FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive

# Install nano and git (for demonstration)
RUN apt-get update && \
    apt-get install -y \ 
    nano \
    git \
    build-essential \
    python3 \
    python3-pip \
    python3-venv \
    iputils-ping \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Create a symbolic link so 'py' calls 'python3'
RUN ln -s /usr/bin/python3 /usr/bin/py

# Optionally, set up any config pointing to the proxy 
# (though you might prefer to do this in the docker-compose.yml via environment variables).
# For example:
RUN git config --global https.proxy http://github-proxy:3128
RUN git config --global http.proxy http://github-proxy:3128
    

WORKDIR /opt
CMD [ "tail", "-f", "/dev/null" ]