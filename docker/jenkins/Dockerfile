# Stage 1: Préparation de l'environnement
FROM jenkins/jenkins:2.426.3-jdk17 AS preparation

USER root

RUN apt-get update && \
    apt-get install -y lsb-release curl

# Stage 2: Configuration de Docker
FROM preparation AS configure-docker

RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
    https://download.docker.com/linux/debian/gpg && \
    echo "deb [arch=$(dpkg --print-architecture) \
    signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
    https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io && \
    rm -rf /var/lib/apt/lists/*

# Stage 3: Configuration de Jenkins
FROM configure-docker AS configure-jenkins

USER jenkins

RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"

# Stage 4: Configuration des permissions et autres ajustements
FROM configure-jenkins AS configure-permissions

USER root

# Ajoutez ici les commandes nécessaires pour ajuster les permissions de /var/run/docker.sock
# Exemple :
RUN chmod 666 /var/run/docker.sock

# Supprimez les fichiers temporaires et nettoyez le cache apt
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Stage final
FROM configure-permissions

USER jenkins

# Ajoutez ici les dernières étapes si nécessaire
# Par exemple, ajouter le script d'entrée
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh

ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
