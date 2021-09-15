FROM jenkins/jenkins:2.289.2-lts

ENV JENKINS_USER admin
ENV JENKINS_PASS %PASSWORD%

# Skip initial setup
ENV JAVA_OPTS -Djenkins.install.runSetupWizard=false

# Jenkins runs all grovy files from init.groovy.d dir
# use this for creating default admin user
COPY default-user.groovy /usr/share/jenkins/ref/init.groovy.d/

# Pre-install plugins
COPY plugins.txt /usr/share/jenkins/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/plugins.txt

# Pre-install colony plugin
COPY colony.hpi /usr/share/jenkins/ref/plugins/colony.hpi

# Create job
RUN mkdir -p /usr/share/jenkins/ref/jobs/demo/
COPY config.xml /usr/share/jenkins/ref/jobs/demo/config.xml

# Install aws cli and npm
USER root 
RUN apt-get update && apt-get install -y awscli
RUN apt-get install -y curl \
  && curl -sL https://deb.nodesource.com/setup_10.x | bash - \
  && apt-get install -y nodejs \
  && curl -L https://www.npmjs.com/install.sh | sh

# Install dotnet core and dependencies
RUN wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
RUN dpkg -i packages-microsoft-prod.deb
RUN rm packages-microsoft-prod.deb


# Install the .Net Core framework, set the path, and show the version of core installed.
RUN apt-get install -y apt-transport-https
RUN apt-get update
RUN apt-get install -y dotnet-sdk-3.1 && \
    export PATH=$PATH:$HOME/dotnet && \
    dotnet --version
RUN apt-get update
RUN apt-get install -y aspnetcore-runtime-3.1

USER jenkins

# Copy jenkins.yaml
COPY jenkins.yaml /usr/share/jenkins/ref/jenkins.yaml
