FROM ubi8/ubi

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en

ENV PYTHONUNBUFFERED 1
ENV PIP_NO_CACHE_DIR off
ENV PIP_DEFAULT_TIMEOUT=60

ENV GALAXY_VENV /usr/share/galaxy/venv
ENV DJANGO_SETTINGS_MODULE galaxy.settings.development
ENV C_FORCE_ROOT 1

# Install node.js and yarn
RUN dnf -y install --setopt=tsflags=nodocs nodejs rsync \
    && curl -sL -o '/etc/yum.repos.d/yarn.repo' 'https://dl.yarnpkg.com/rpm/yarn.repo' \
    && dnf -y install --setopt=tsflags=nodocs yarn \
    && dnf -y clean all \
    && rm -rf /var/cache/yum

# Install packages
RUN dnf -y install \
        gcc git make \
        python3 python3-devel \
        tmux vim \
    && ln -sf /usr/bin/python3 /usr/bin/python \
    && dnf -y clean all \
    && rm -rf /var/cache/yum

# Create directories structure
RUN mkdir -p /galaxy \
             /usr/share/galaxy \
             /var/lib/galaxy/media \
             /var/tmp/galaxy/imports \
             /var/tmp/galaxy/uploads \
             /var/run/galaxy

VOLUME ["/var/lib/galaxy", "/var/tmp/galaxy"]

# Install node dependencies
RUN yarn global add @angular/cli@6.1.2 \
    && ng set --global packageManager=yarn

# Create virtual environment and install python dependencies
COPY requirements/dev-requirements.txt /tmp/requirements.txt
RUN python3.6 -m venv "${GALAXY_VENV}" \
    && "${GALAXY_VENV}/bin/pip" install -U \
        'pip' \
        'wheel' \
        'setuptools' \
    && "${GALAXY_VENV}/bin/pip" install -r /tmp/requirements.txt

COPY scripts/docker/dev/entrypoint.sh /entrypoint

WORKDIR /galaxy
ENTRYPOINT ["/entrypoint"]
CMD ["start", "tmux"]
