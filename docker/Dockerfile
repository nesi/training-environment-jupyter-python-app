FROM ubuntu:22.04

# install system dependencies
# TODO: move ldap-utils, libnss-ldapd, libpam-ldapd, nscd, nslcd to base image??
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        git \
        dnsutils \
        locales \
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
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_NZ -c -f UTF-8 -A /usr/share/locale/locale.alias en_NZ.UTF-8

ENV LANG en_NZ.utf8

# install kubectl, required for running on the k8s cluster
ARG KUBECTL_VERSION=v1.28.5
RUN curl -LO https://dl.k8s.io/release/$KUBECTL_VERSION/bin/linux/amd64/kubectl \
    && mv kubectl /usr/local/bin/kubectl \
    && chmod +x /usr/local/bin/kubectl

# copy in ood k8s utils
ARG UTILS_HASH=6298fb01f7a7c66a8454e3f0fd74437a32491423
RUN git clone https://github.com/nesi/training-environment-k8s-utils.git /opt/ood-k8s-utils \
    && cd /opt/ood-k8s-utils \
    && git checkout $UTILS_HASH \
    && chmod +x /opt/ood-k8s-utils/files/* \
    && mv /opt/ood-k8s-utils/files/* /bin/ \
    && rm -rf /opt/ood-k8s-utils

# copy the repo source (e.g. notebooks) to the container image and install dependencies
ARG PYTHON_HASH="0fa4a0383caf0b32fb41b5e4a071edeab07d46c8"
RUN git clone https://github.com/nesi/intro-python.git /opt/intro-python \
    && cd /opt/intro-python \
    && git checkout $PYTHON_HASH \
    && pip3 --no-cache-dir install -r requirements.txt \
    && pip3 --no-cache-dir install jupyterlab \
    && rm -rf /opt/intro-python/.git

# fallback: fetch the gapminder data straight from the Carpentries lesson repos
# in case the pinned intro-python commit predates data/gapminder*.csv. The clone
# above ships these files, so -nc (no-clobber) makes this a no-op on a current
# pin. Upstream repos are pinned by commit so the build stays reproducible.
ARG SWC_PYTHON_GAPMINDER_HASH=5d886a81763f0c113f5560368311c44f41eea02d
ARG SWC_R_GAPMINDER_HASH=61dff93a6e329945d7c2a7520054e633c0ab3b62
RUN mkdir -p /opt/intro-python/data \
    && cd /opt/intro-python/data \
    && for f in gapminder_all.csv \
                gapminder_gdp_africa.csv \
                gapminder_gdp_americas.csv \
                gapminder_gdp_asia.csv \
                gapminder_gdp_europe.csv \
                gapminder_gdp_oceania.csv ; do \
           wget -nv -nc "https://raw.githubusercontent.com/swcarpentry/python-novice-gapminder/$SWC_PYTHON_GAPMINDER_HASH/episodes/data/$f" ; \
       done \
    && { [ -f gapminder.csv ] || wget -nv -O gapminder.csv \
        "https://raw.githubusercontent.com/swcarpentry/r-novice-gapminder/$SWC_R_GAPMINDER_HASH/episodes/data/gapminder_data.csv" ; } \
    && chown -R root:root /opt/intro-python/data \
    && chmod -R o+rX /opt/intro-python/data
