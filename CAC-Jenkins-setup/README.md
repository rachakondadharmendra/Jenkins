
# Quick Jenkins Setup with Configuration as Code (CAC)

In this guide, we'll walk you through setting up Jenkins with Configuration as Code (CAC) on an Ubuntu Server 22.04 for a streamlined experience. CAC enables you to manage Jenkins configuration using human-readable code, facilitating easier maintenance, versioning, and replication across various environments.


## Prerequisites

Before we begin, ensure you have the following installed on your system:

- Docker
- Git (Optional, if you want to version control your configuration)

## Step 1: Create the Required Files

For the Jenkins CAC setup, we need two files:

1. `plugins.txt`: This file contains a list of plugins that will be automatically installed during Jenkins boot.
2. `casc.yaml`: This file holds the configuration for your entire Jenkins server.

### Generating the `plugins.txt` File

1. Access your Jenkins instance at `http://<jenkins-public-ip>:8080/script`.
2. Run the following script and copy the output, then save it in a `plugins.txt` file.

```groovy
def plugins = new ArrayList(Jenkins.instance.pluginManager.plugins)
plugins.sort { it.getShortName() }.each{
  plugin -> 
     println ("${plugin.getShortName()}:${plugin.getVersion()}")
}
```

### Generating the `casc.yaml` File

1. Install the "Configuration as Code" plugin in your Jenkins instance.
2. Go to "Manage Jenkins" and select "Configuration as Code".
3. Click on "View config" to see your current configuration.
   
**Warning:** Do not modify the configuration file directly, as it may disrupt your entire Jenkins server.

Copy the desired configurations, such as tools, system settings, and credentials, into a new `casc.yaml` file.

The `casc.yaml` file is divided into several sections:

- **Credentials:** This section stores all the credentials used by Jenkins. Credential values can be passed as environment variables or stored in vaults.
- **System Configuration:** This section includes default node configurations, administrator credentials, and authorization strategies.
- **Global Settings:** This section defines global configurations for various Jenkins components, such as email settings, GitHub configurations, and timeout settings.
- **Tools Configuration:** This section configures the tools used by Jenkins, such as Git, JDK, Maven, and SonarQube.

### Credentials

The credentials section stores sensitive information securely, such as usernames, passwords, and tokens.

```yaml
credentials:
  system:
    domainCredentials:
    - credentials:
      - usernamePassword:
          description: "github_admin_creds"
          id: "github_admin_creds"
          password: ${GITHUB_ADMIN_ACCESS_TOKEN}
          scope: GLOBAL
          username: "rachakondadharmendra"
```

### System Configuration (Including Admin Username/Password)

The system configuration encompasses default node configurations along with admin credentials for Jenkins.

```yaml
jenkins:
  systemMessage: " \n\n Welcome to Jenkins with CAC Integration! 🚀 \n This system is integrated with CAC for enhanced security.Ready to streamline your workflows? Let's get started! \n\n"
  labelAtoms:
  - name: "built-in"
  markupFormatter: "plainText"
  mode: NORMAL
  myViewsTabBar: "standard"
  nodeMonitors:
  - "architecture"
  - "clock"
  - diskSpace:
      freeSpaceThreshold: "1GiB"
      freeSpaceWarningThreshold: "2GiB"
  - "swapSpace"
  - tmpSpace:
      freeSpaceThreshold: "2GiB"
      freeSpaceWarningThreshold: "3GiB"
  - "responseTime"
  numExecutors: 2
  primaryView:
    all:
      name: "all"
  projectNamingStrategy: "standard"
  quietPeriod: 5
  remotingSecurity:
    enabled: true
  scmCheckoutRetryCount: 0   
  securityRealm:
    local:
      allowsSignup: false
      users:
       - id: ${JENKINS_ADMIN_ID}
         password: ${JENKINS_ADMIN_PASSWORD}
  authorizationStrategy:
    globalMatrix:
      permissions:
        - "Overall/Administer:${JENKINS_ADMIN_ID}"
        - "Overall/Read:authenticated"
  remotingSecurity:
    enabled: true        
security:
  queueItemAuthenticator:
    authenticators:
    - global:
        strategy: triggeringUsersAuthorizationStrategy
```

### Global Setting/Configuration

This section defines global configurations for Jenkins, covering various aspects like email notifications, SCM settings, and timeouts.

```yaml
unclassified:
  buildDiscarders:
    configuredBuildDiscarders:
    - "jobBuildDiscarder"
  buildStepOperation:
    enabled: false
  email-ext:
    adminRequiredForTemplateTesting: false
    allowUnregisteredEnabled: false
    charset: "UTF-8"
    debugMode: false
    defaultBody: |-
      $PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:

      Check console output at $BUILD_URL to view the results.
    defaultContentType: "text/plain"
    defaultSubject: "$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!"
    defaultTriggerIds:
    - "hudson.plugins.emailext.plugins.trigger.FailureTrigger"
    maxAttachmentSize: -1
    maxAttachmentSizeMb: -1
    precedenceBulk: false
    watchingEnabled: false
  enrichedSummaryConfig:
    enrichedSummaryEnabled: false
    httpClientDelayBetweenRetriesInSeconds: 1
    httpClientMaxRetries: 3
    httpClientTimeoutInSeconds: 1
  fingerprints:
    fingerprintCleanupDisabled: false
    storage: "file"
  gitHubConfiguration:
    apiRateLimitChecker: ThrottleForNormalize
  gitHubPluginConfig:
    hookUrl: "${JENKINS_URL}/github-webhook/"
  globalTimeOutConfiguration:
    operations:
    - "abortOperation"
    overwriteable: false
  injectionConfig:
    allowUntrusted: false
    checkForBuildAgentErrors: false
    enabled: false
    enforceUrl: false
    injectCcudExtension: false
    injectMavenExtension: false
  junitTestResultStorage:
    storage: "file"
  location:
    adminAddress: "address not configured yet <nobody@nowhere>"
    url: "${JENKINS_URL}"
  mailer:
    charset: "UTF-8"
    useSsl: false
    useTls: false
  pollSCM:
    pollingThreadCount: 10
  scmGit:
    addGitTagAction: false
    allowSecondFetch: false
    createAccountBasedOnEmail: false
    disableGitToolChooser: false
    hideCredentials: false
    showEntireCommitSummaryInChanges: false
    useExistingAccountWithSameEmail: false

  timestamper:
    allPipelines: false
    elapsedTimeFormat: "'<b>'HH:mm:ss.S'</b> '"
    systemTimeFormat: "'<b>'HH:mm:ss'</b> '"
```

### Tools Configuration

This section manages the configurations for tools required by Jenkins, such as Git, JDK, Maven, and SonarQube.

```yamltool:
  git:
    installations:
    - home: "git"
      name: "Default"
  jdk:
    installations:
    - name: "jdk-11"
      properties:
      - installSource:
          installers:
          - adoptOpenJdkInstaller:
              id: "jdk-18.0.2.1+1"
  mavenGlobalConfig:
    globalSettingsProvider: "standard"
    settingsProvider: "standard"
  sonarRunnerInstallation:
    installations:
    - name: "sonarqube"
      properties:
      - installSource:
          installers:
          - sonarRunnerInstaller:
              id: "5.0.1.3006"
```

Additional configurations may exist depending on the plugins and Jenkins version being used.
## Step 2: Create the Dockerfile and .env File

Next, create a Dockerfile to build your custom Jenkins image and a `.env` file to pass environment variables.

### Dockerfile

```dockerfile
# Base Jenkins image
FROM jenkins/jenkins:lts
USER root

# Install required packages
RUN apt-get update && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    gnupg-agent \
    lsb-release \
    software-properties-common
    
# Setup Docker repository
RUN mkdir -m 0755 -p /etc/apt/keyrings
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
RUN echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
  
# Install Docker
RUN apt-get update && apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Set Jenkins environment variables
ENV JAVA_OPTS=-Djenkins.install.runSetupWizard=false
ENV CASC_JENKINS_CONFIG=/var/jenkins_home/casc.yaml

# Copy and install plugins
COPY plugins.txt /usr/share/jenkins/ref/plugins.txt 
RUN curl -sSfL -o /usr/share/jenkins/ref/pln.jar https://github.com/jenkinsci/plugin-installation-manager-tool/releases/download/2.12.15/jenkins-plugin-manager-2.12.15.jar
RUN java -jar /usr/share/jenkins/ref/pln.jar -f /usr/share/jenkins/ref/plugins.txt
RUN rm /usr/share/jenkins/ref/pln.jar

# Copy configuration file
COPY casc.yaml /var/jenkins_home/casc.yaml

EXPOSE 8080
```

**Explanation of Dockerfile:**

- The Dockerfile starts with the base Jenkins image and sets the user to root to execute commands requiring root privileges.
- It installs necessary packages and sets up the Docker repository.
- Docker is installed and Jenkins environment variables are set.
- Plugins are copied and installed, and the configuration file is copied into the Jenkins container.

### .env File

```dotenv
# Environment variables for Jenkins setup
GITHUB_ADMIN_ACCESS_TOKEN=sample_lettersasdfk234oaiera_of_git
JENKINS_ADMIN_ID=ADMIN
JENKINS_ADMIN_PASSWORD=ADMIN_PASSWORD
JENKINS_URL=http://localhost:8080/
```

**Explanation of .env File:**

- The .env file contains environment variables used during the setup of Jenkins.
- `GITHUB_ADMIN_ACCESS_TOKEN` is used for GitHub authentication.
- `JENKINS_ADMIN_ID` and `JENKINS_ADMIN_PASSWORD` set the admin credentials for Jenkins.
- `JENKINS_URL` specifies the URL at which Jenkins will be accessible.

Feel free to modify the `JENKINS_ADMIN_ID` and `JENKINS_ADMIN_PASSWORD` values according to your preferences.

## Step 3: Build and Run the Docker Image

With all the files in place, it's time to build and run the Docker image.

### Build the Docker image:

```bash
docker build -t jenkins_cac:v1.0 .
```

### Run the Docker container:

```bash
docker run --name jenkins -d -p 8080:8080 -p 5000:5000 \
 -v jenkins_home:/var/jenkins_home \
 -v /var/run/docker.sock:/var/run/docker.sock \
 --privileged \
 --user root \
 --restart always \
 --env-file .env \
 jenkins_cac:v1.0
```

This command starts the Jenkins container with the specified configurations and environment variables.

## Conclusion

Congratulations! You have successfully set up Jenkins with Configuration as Code (CAC). You can now access your Jenkins instance at http://localhost:8080 and start automating your build and deployment processes with ease.

Remember to version control your `plugins.txt` and `casc.yaml` files to maintain consistency across different environments and enable collaboration within your team.

Happy coding!
