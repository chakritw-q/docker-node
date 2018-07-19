FROM alpine:3.7

ENV NODE_MAJOR_VERSION 10
ENV NODE_VERSION ${NODE_MAJOR_VERSION}.6.0

USER 0

RUN echo "## Installing prerequisites {"; \
    apk update; \
    apk add --no-cache \
    sudo libstdc++ wget curl tar bzip2 gnupg; \
    echo "## }"
    
RUN addgroup -g 1000 docker && \
    adduser -u 1000 -G docker -s /bin/sh -D docker; \
    echo '%docker ALL=(ALL) NOPASSWD:ALL' >>/etc/sudoers
    
RUN echo "## Installing build tools {"; \
    export buildDeps = ' \
        binutils-gold \
        g++ gcc make \
        libgcc \
        linux-headers \
        python \
    '; \
    echo ${buildDeps}; \
    apk add --no-cache --virtual .build-deps ${buildDeps}; \
    echo "## }"
    
# gpg keys listed at https://github.com/nodejs/node#release-team
RUN echo "## Installing NodeJS sources"; \
    set -ex; \
    for key in \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    56730D5401028683275BD23C23EFEFE93C4CFFFE \
    77984A986EBC2AA786BC0F66B01FBB92821C587A \
    8FCCA13FEF1D0C2E91008E09770F7A9A5AE15600 \
    ; do \
        gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys "$key" || \
        gpg --keyserver hkp://ipv4.pool.sks-keyservers.net --recv-keys "$key" || \
        gpg --keyserver hkp://pgp.mit.edu:80 --recv-keys "$key" ; \
    done; \
    curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION.tar.xz" && \
    curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" && \
    gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc && \
    grep " node-v$NODE_VERSION.tar.xz\$" SHASUMS256.txt | sha256sum -c - && \
    tar -xf "node-v$NODE_VERSION.tar.xz" && \
    cd "node-v$NODE_VERSION" && \
    ./configure && \
    make -j$(getconf _NPROCESSORS_ONLN) && \
    make install; \
    cd .. \
    rm -Rf "node-v$NODE_VERSION"; \
    && rm "node-v$NODE_VERSION.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt; \
    echo "## }"

ENV YARN_VERSION 1.7.0

RUN echo "## Installing Yarn {"; \
    set -ex; \
    apk add --no-cache --virtual .build-deps-yarn; \
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
    apk del .build-deps-yarn; \
    echo "## }"

RUN echo "## Installing Extra tools {"; \
    apk add --no-cache nano vim; \
    echo "## }"

RUN echo "## Uninstalling build tools {"; \
    echo ${buildDeps}; \
    apk del .build-tools; \
    echo "## }"
    
EXPOSE 8080

USER 1000

CMD [ "node" ]
