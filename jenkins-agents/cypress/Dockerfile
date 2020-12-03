FROM quay.io/openshift/origin-jenkins-agent-base:4.5

USER root
COPY contrib/openshift/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo

ENV NODEJS_VERSION=12 \
    NPM_CONFIG_PREFIX=$HOME/.npm-global \
    PATH=$HOME/node_modules/.bin/:$HOME/.npm-global/bin/:$PATH

RUN curl --silent --location https://rpm.nodesource.com/setup_${NODEJS_VERSION}.x | bash -

RUN INSTALL_PKGS="nodejs git bc gettext java-11-openjdk-headless lsof rsync tar unzip which zip bzip2 jq xorg-x11-server-Xvfb gtk2-devel gtk3-devel libnotify-devel GConf2 libXScrnSaver alsa-lib" && \
    DISABLES="--disablerepo=rhel-server-extras --disablerepo=rhel-server --disablerepo=rhel-fast-datapath --disablerepo=rhel-server-optional --disablerepo=rhel-server-ose --disablerepo=rhel-server-rhscl" && \
    # DISABLES="" && \
    yum $DISABLES -y update && \
    yum $DISABLES install -y --setopt=tsflags=nodocs --disablerepo='rhel-*' \
      $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y && \
    rm -rf /var/cache/yum && \
    npm install --unsafe-perm -g npm-audit-html npm-audit-ci-wrapper sonar-scanner && \
    chown root:root /home/jenkins -R && \
    chmod 775 /home/jenkins/.config -R && \
    chmod 775 /home/jenkins/.npm -R

# Copy the entrypoint
ADD contrib/bin/* /usr/local/bin/

# Run the Jenkins JNLP client
ENTRYPOINT ["/usr/bin/go-init", "-main", "/usr/local/bin/run-jnlp-client"]

USER 1001

