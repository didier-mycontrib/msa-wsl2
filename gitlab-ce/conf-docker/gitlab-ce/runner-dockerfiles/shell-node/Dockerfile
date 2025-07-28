FROM gitlab/gitlab-runner:ubuntu-v16.6.1
ARG NODE_MAJOR=22

# ca-certificates because curl uses certificates from ca-certificates
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates curl gnupg && \
    # Installing nodejs
    mkdir -p /etc/apt/keyrings && \
    curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg && \
    echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list && \
    apt-get update && apt-get install nodejs -y && apt-get clean && rm -rf /var/lib/apt/lists/*


# Set SHELL flags for RUN commands to allow -e and pipefail

SHELL ["/bin/bash", "-eo", "pipefail", "-c"]

RUN echo "PATH=${PATH}" >> /etc/environment 

#install docker-ce on this runner in order to build and push image from gitlab-ci jobs
RUN apt update && apt install -y --no-install-recommends apt-transport-https ca-certificates curl software-properties-common &&\
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg &&\
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null &&\
    apt update && apt-cache policy docker-ce && apt install -y --no-install-recommends docker-ce &&\
    mkdir -p ~/.docker/cli-plugins/ &&\
    curl -SL https://github.com/docker/compose/releases/download/v2.29.7/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose &&\
    chmod +x ~/.docker/cli-plugins/docker-compose

RUN npm install -g typescript && npm install -g http-server && npm install -g start-server-and-test

#NB: xvfb and libglib-2.0.so is a required dependency of cypress
#NB: library list ok for ubuntu 20 (or 22?) not for 24 
RUN apt-get update && \
  apt-get install --no-install-recommends -y \
  libgtk2.0-0 \
  libgtk-3-0 \
  libnotify-dev \
  libgconf-2-4 \
  libgbm-dev \
  libnss3 \
  libxss1 \
  libasound2 \
  libxtst6 \
  procps \
  xauth \
  xvfb
  
RUN npx cypress install # Install Cypress binary into image

