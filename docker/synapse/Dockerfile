FROM centos

# Maintainer
MAINTAINER Sven Anderson <sven@redhat.com>

# Git branch to build from
ARG BV_SYN=master

RUN set -ex ;\
    export LIBRARY_PATH=/lib:/usr/lib `# https://github.com/python-pillow/Pillow/issues/1763` ;\
    export MATRIX_UID=991 ;\
    export MATRIX_GID=991 ;\
    groupadd -r -g $MATRIX_GID matrix ;\
    useradd -r -d /data -M -u $MATRIX_UID -g matrix matrix ;\
    yum install -y epel-release ;\
    yum upgrade -y ;\
    yum install -y \
        git \
        gcc \
        make \
        python-pip \
        python-devel \
        libffi-devel \
        openssl-devel \
    ;\
    pip install -U pip ;\
    pip install -U \
        setuptools \
    ;\
    git clone --branch $BV_SYN --depth 1 https://github.com/matrix-org/synapse.git /tmp/synapse ;\
    cd /tmp/synapse ;\
    pip install -U . ;\
    GIT_SYN=$(git ls-remote https://github.com/matrix-org/synapse $BV_SYN | cut -f 1) ;\
    echo "synapse: $BV_SYN ($GIT_SYN)" >> /synapse.version ;\
    cd / ;\
    rm -rf /tmp/synapse ;\
    yum clean all ;\
    yum autoremove -y \
        git \
        gcc \
        make \
        python-devel \
        openssl-devel \
        `#libffi-devel # https://bugs.centos.org/view.php?id=10828` \
    ;\
    yum clean -y all ;\
    rm -rf /var/cache/yum/* /root/.cache/pip

# startup configuration
USER matrix
VOLUME ["/data", "/config"]
CMD ["/usr/bin/python", "-B", "-m", "synapse.app.homeserver", "-c", "/config/homeserver.yaml"]
EXPOSE 8008 8448
