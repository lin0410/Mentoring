# Stage 1: Préparation de l'environnement
FROM jenkins/jenkins:2.426.3-jdk17 
USER root

RUN apt-get update && \
    apt-get install -y lsb-release curl


RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg && \
    echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io && \
    rm -rf /var/lib/apt/lists/*

# Supprimez les fichiers temporaires et nettoyez le cache apt
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*
# RUN usermod -aG 1001 jenkins
RUN usermod -aG docker jenkins
# RUN chown root:docker /var/run/docker.sock


USER jenkins
