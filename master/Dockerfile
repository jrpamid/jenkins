FROM jenkins/jenkins:2.273-alpine

# switch to root user, just ncase base image has another user 
USER root

ENV USER=jenkins \
    GROUP=cicd \
    JENKINS_VERSION="2.273" \
    LANG=en_US.UTF-8 \
    JENKINS_HOME=/var/lib/jenkins \
    TINI_VERSION v0.19.0 \
    JAVA_OPTS="-Djenkins.install.runSetupWizard=false"

ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/local/bin/tini

# Setup Jenkins User
RUN apk add --update shadow \
    && groupadd -g 1000 ${GROUP} \
    && usermod -a -G ${GROUP} ${USER} \
    && echo "${USER} ALL=NOPASSWD: ALL" >> /etc/sudoers \
    && chmod +x /usr/local/bin/tini 

# swicth to jenkins user
USER jenkins

COPY resources/default-user.groovy /usr/share/jenkins/ref/init.groovy.d/default-user.groovy
COPY resources/plugins-list.txt /usr/local/bin/plugins-list.txt

# installing the required jenkins plugins
RUN /usr/local/bin/install-plugins.sh $(cat /usr/local/bin/plugins-list.txt)

# ports 8080 for Jenkins web interface & 50000 for Jenkins Slave agents
EXPOSE 8080 50000

LABEL AUTHOR=jrpamid.cloudops@gmail.com \
      DESCRIPTION="Jenkins Master & Slave for CI-CD"

# use tini program as a init 
ENTRYPOINT ["/usr/local/bin/tini","--"]
