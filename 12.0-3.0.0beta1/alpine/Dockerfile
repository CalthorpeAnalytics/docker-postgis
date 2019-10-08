FROM postgres:12.0-alpine

ENV POSTGIS_VERSION 3.0.0beta1
ENV POSTGIS_SHA256 6bfd5c86beb6109d547d11da643415ea02666612496e03f29eb0025ded7b9155

RUN set -ex \
    \
    && apk add --no-cache --virtual .fetch-deps \
    ca-certificates \
    openssl \
    tar \
    \
    && wget -O postgis.tar.gz "https://github.com/postgis/postgis/archive/$POSTGIS_VERSION.tar.gz" \
    && echo "$POSTGIS_SHA256 *postgis.tar.gz" | sha256sum -c - \
    && mkdir -p /usr/src/postgis \
    && tar \
    --extract \
    --file postgis.tar.gz \
    --directory /usr/src/postgis \
    --strip-components 1 \
    && rm postgis.tar.gz \
    \
    && apk add --no-cache --virtual .deps \
    --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing \
    --repository http://dl-cdn.alpinelinux.org/alpine/edge/main \
    autoconf \
    automake \
    g++ \
    gdal \
    gdal-dev \
    geos \
    geos-dev \
    json-c \
    json-c-dev \
    libtool \
    libxml2-dev \
    make \
    perl \
    proj \
    proj-dev \
    protobuf-c \
    protobuf-c-dev \
    && cd /usr/src/postgis \
    && ./autogen.sh \
    # configure options taken from:
    # https://anonscm.debian.org/cgit/pkg-grass/postgis.git/tree/debian/rules?h=jessie
    && ./configure \
    && make \
    && make install \
    && cd / \
    && rm -rf /usr/src/postgis \
    && apk del .fetch-deps .deps

COPY ./initdb-postgis.sh /docker-entrypoint-initdb.d/postgis.sh
COPY ./update-postgis.sh /usr/local/bin
