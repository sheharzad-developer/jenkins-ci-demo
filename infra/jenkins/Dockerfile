FROM jenkins/jenkins:lts
USER root

# Docker CLI (docker.io pulls in the CLI; we only ever talk to the host's
# socket, bind-mounted in at container run time, not the bundled daemon)
# plus Node/npm so pipeline steps can run `npm install` / `npm test`.
RUN apt-get update && \
    apt-get install -y --no-install-recommends docker-cli nodejs npm && \
    rm -rf /var/lib/apt/lists/*

# /var/run/docker.sock on the host is owned by root:root (mode 660);
# add jenkins to the root group so it can read/write the socket
RUN usermod -aG root jenkins

USER jenkins
