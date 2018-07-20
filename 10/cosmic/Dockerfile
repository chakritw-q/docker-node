FROM ubuntu:cosmic

ENV NODE_MAJOR_VERSION 10
ENV NODE_VERSION ${NODE_MAJOR_VERSION}.6.0

ARG LINUX_HEADERS_VERSION="4.15.0-23"

USER 0

RUN echo "## Installing prerequisites {"; \
    set -ex; \
    dpkgArch=`dpkg --print-architecture` && \
    export dpkgArch; \
    apt-get upgrade; \
    apt-get dist-upgrade; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
    sudo curl netbase wget tar bzip2 gnupg ca-certificates; \
    #rm -rf /var/lib/apt/lists/*; \
    echo "## }"
    
RUN groupadd --gid 1000 docker && \
    useradd --uid 1000 --gid docker --shell /bin/bash --create-home docker && \
    echo '%docker ALL=(ALL) NOPASSWD:ALL' >>/etc/sudoers
    
RUN echo "## Installing build tools {"; \
    set -ex; \
    buildDeps=" \
        gcc g++ make patch autoconf automake binutils \
        libgcc-7-dev \
        linux-headers-${LINUX_HEADERS_VERSION}-generic \
        python \
    "; \
    export buildDeps; \
    echo ${buildDeps}; \
    apt-get install -y --no-install-recommends ${buildDeps}; \
    echo "## }"
    
RUN echo "## Installing NodeJS ${NODE_MAJOR_VERSION}.x packages for Debian/Ubuntu {"; \
    curl -sL https://deb.nodesource.com/setup_${NODE_MAJOR_VERSION}.x | bash -; \
    apt-get install -y nodejs build-essential; \
    echo "## }"
    
# gpg keys listed at https://github.com/nodejs/node#release-team
#RUN echo "## Installing NodeJS sources"; \
#    set -ex; \
#    for key in \
#    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
#    FD3A5288F042B6850C66B31F09FE44734EB7990E \
#    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
#    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
#    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
#    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
#    56730D5401028683275BD23C23EFEFE93C4CFFFE \
#    77984A986EBC2AA786BC0F66B01FBB92821C587A \
#    ; do \
#        gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
#        gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
#        gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
#    done; \
#    ARCH= && \
#    case "${dpkgArch##*-}" in \
#        amd64) ARCH='x64';; \
#        ppc64el) ARCH='ppc64le';; \
#        s390x) ARCH='s390x';; \
#        arm64) ARCH='arm64';; \
#        armhf) ARCH='armv7l';; \
#        i386) ARCH='x86';; \
#        *) echo "unsupported architecture"; exit 1 ;; \
#    esac; \
#    curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz" && \
#    curl -SLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" && \
#    gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc && \
#    grep " node-v$NODE_VERSION-linux-$ARCH.tar.xz\$" SHASUMS256.txt | sha256sum -c - && \
#    tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner && \
#    rm "node-v$NODE_VERSION-linux-$ARCH.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt && \
#    ln -s /usr/local/bin/node /usr/local/bin/nodejs; \
#    echo "## }"
    
ENV YARN_VERSION 1.7.0

RUN echo "## Installing Yarn {"; \
    set -ex; \
    for key in \
    6A010C5166006599AA17F08146C2130DFD2497F5 \
    ; do \
        gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
        gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
        gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
    done; \
    curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" && \
    curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz.asc" && \
    gpg --batch --verify yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz && \
    mkdir -p /opt && \
    tar -xzf yarn-v$YARN_VERSION.tar.gz -C /opt/ && \
    ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn && \
    ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg && \
    rm yarn-v$YARN_VERSION.tar.gz.asc yarn-v$YARN_VERSION.tar.gz; \
    echo "## }"

RUN echo "## Installing Extra tools {"; \
    apt-get install -y --no-install-recommends nano vim inetutils-tools inetutils-traceroute; \
    echo "## }"

RUN echo "## Uninstalling build tools {"; \
    echo ${buildDeps}; \
    apt-get remove -y ${buildDeps}; \
    echo "## }"
    
EXPOSE 8080

USER 1000

CMD [ "node" ]
