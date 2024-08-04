FROM ubuntu:22.04

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        git \
        ldap-utils \
        libnss-ldapd \
        libpam-ldapd \
        less \
        nano \
        nodejs \
        nscd \
        nslcd \
        python-is-python3 \
        python3 \
        python3-pip \
        rsync \
        unzip \
        vim \
        wget \
        zip \
    && rm -rf /var/lib/apt/lists/*

# download the required date files into the container image
#RUN wget -nv https://ndownloader.figshare.com/files/2292172 \
#        -O /var/lib/surveys.csv \
#    && chown -R root:root /var/lib/surveys.csv \
#    && chmod -R o+rX /var/lib/surveys.csv \

# copy the repo source (e.g. notebooks) to the container image and install dependencies
ARG PYTHON_HASH="fde72796f9369202dee57afa53e680f6f435ef1d"
RUN git clone https://github.com/nesi/intro-python.git /opt/intro-python \
    && cd /opt/intro-python \
    && git checkout $PYTHON_HASH \
    && pip3 --no-cache-dir install -r requirements.txt \
    && pip3 --no-cache-dir install jupyterlab \
    && rm -rf /opt/intro-python/.git
