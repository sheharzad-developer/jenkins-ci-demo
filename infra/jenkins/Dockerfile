FROM jenkins/jenkins:lts
USER root

# Docker CLI (docker.io pulls in the CLI; we only ever talk to the host's
# socket, bind-mounted in at container run time, not the bundled daemon),
# Node/npm for `npm install`/`npm test`, and kubectl for the deploy stage.
RUN apt-get update && \
    apt-get install -y --no-install-recommends docker-cli nodejs npm kubectl curl && \
    rm -rf /var/lib/apt/lists/*

# Helm isn't packaged in Debian's repos; install via the official script
RUN curl --retry 5 --retry-connrefused --retry-delay 5 -fsSL \
        -o /tmp/get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 && \
    chmod +x /tmp/get_helm.sh && \
    /tmp/get_helm.sh && \
    rm /tmp/get_helm.sh

# /var/run/docker.sock on the host is owned by root:root (mode 660);
# add jenkins to the root group so it can read/write the socket
RUN usermod -aG root jenkins

USER jenkins
