# Build stage for BerkeleyDB
FROM alpine:3.12 as berkeleydb

RUN apk --no-cache add autoconf
RUN apk --no-cache add automake
RUN apk --no-cache add build-base
RUN apk --no-cache add openssl

ENV BERKELEYDB_VERSION=db-4.8.30.NC
ENV BERKELEYDB_PREFIX=/opt/berkleydb

RUN wget -q https://download.oracle.com/berkeley-db/${BERKELEYDB_VERSION}.tar.gz
RUN tar -xzf *.tar.gz
RUN sed s/__atomic_compare_exchange/__atomic_compare_exchange_db/g -i ${BERKELEYDB_VERSION}/dbinc/atomic.h
RUN mkdir -p ${BERKELEYDB_PREFIX}

WORKDIR /${BERKELEYDB_VERSION}/build_unix

RUN ../dist/configure --enable-cxx --disable-shared --with-pic --prefix=${BERKELEYDB_PREFIX}
RUN make
RUN make install
RUN rm -rf ${BERKELEYDB_PREFIX}/docs

# Build stage for Peercoind
FROM alpine:3.12 as peercoin-build

COPY --from=berkeleydb /opt/ /opt/

RUN apk --no-cache add autoconf \
    automake \
    boost-dev \
    build-base \
    chrpath \
    file \
    libevent-dev \
    openssl \
    openssl-dev \
    libtool \
    linux-headers \
    zeromq-dev \
    protobuf-dev

ENV PEERCOIN_VERSION=0.10.3ppc \
    PEERCOIN_SHA=d84aca363c3038b68c7dcf0081c9833217eaaf4d62fb1cc021f86b5993ba7e1b \
    PEERCOIN_PREFIX=/opt/peercoin

RUN apk --no-cache add git \
    && git clone --single-branch --branch release-0.10 \
    https://github.com/n3srl/peercoin.git tempura

WORKDIR /tempura

# ./configure can't find berkley db unless we do this
RUN ln -s /opt/berkleydb /usr/include/db4.8 \
    && ln -s /opt/berkleydb/include/* /usr/include  \
    && ln -s /opt/berkleydb/lib/* /usr/lib

RUN ./autogen.sh
RUN ./configure \
    --prefix=${PEERCOIN_PREFIX} \
    --mandir=/usr/share/man \
    --disable-tests \
    --disable-bench \
    --disable-ccache \
    --with-gui=no \
    --with-utils \
    --with-libs \
    --with-daemon
RUN make
RUN make install
RUN strip ${PEERCOIN_PREFIX}/bin/peercoin-cli
RUN strip ${PEERCOIN_PREFIX}/bin/peercoin-tx
RUN strip ${PEERCOIN_PREFIX}/bin/peercoind
RUN strip ${PEERCOIN_PREFIX}/lib/libpeercoinconsensus.a
RUN strip ${PEERCOIN_PREFIX}/lib/libpeercoinconsensus.so.0.0.0

# Build stage for compiled artifacts
FROM alpine:3.12

RUN adduser -S peercoin && \
  apk --no-cache add \
    boost \
    boost-program_options \
    libevent \
    openssl \
    libzmq \
    su-exec \
    bash

ENV PPC_DATA=/data
ENV PEERCOIN_PREFIX=/opt/peercoin
ENV PATH=${PEERCOIN_PREFIX}/bin:$PATH
ENV RPC_PASSWORD=ppcpass
ENV RPC_USER=ppcuser

COPY --from=peercoin-build /opt /opt
COPY docker-entrypoint.sh /entrypoint.sh

VOLUME ["${PPC_DATA}"]

EXPOSE 9901 9902 9903 9904

ENTRYPOINT ["/entrypoint.sh"]

CMD ["peercoind"]
