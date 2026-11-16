FROM ubuntu:26.04 AS builder

ARG BIRD_VERSION=2.15.1
ARG BIRD_PROTOCOLS=bmp,bgp,rpki,static

RUN apt update -y  &&  \
    apt install -y --no-install-recommends --no-install-suggests \
        autoconf \
        bison \
        ca-certificates \
        curl \
        flex \
        gcc \
        libncurses-dev \
        libreadline-dev \
        libssh-dev \
        m4 \
        make \
    && rm -rf /var/lib/apt/lists/*

# Sources come from the upstream GitLab archive: https://bird.network.cz/download/
# returns HTTP 403. A git archive has no ./configure, hence `autoreconf -i`.
RUN curl -fsSL -o /tmp/bird-${BIRD_VERSION}.tar.gz \
        https://gitlab.nic.cz/labs/bird/-/archive/v${BIRD_VERSION}/bird-v${BIRD_VERSION}.tar.gz
RUN tar xzf /tmp/bird-${BIRD_VERSION}.tar.gz -C /tmp

RUN cd /tmp/bird-v${BIRD_VERSION} \
    && autoreconf -i \
    && ./configure \
        --prefix=/usr/ \
        --sysconfdir=/etc/bird/ \
        --localstatedir=/var/ \
        --enable-libssh \
        --with-protocols=${BIRD_PROTOCOLS} \
    && make && make install \
    && { find /usr/sbin/bird* -type f -executable -exec strip --strip-all "{}" +; }

FROM ubuntu:26.04

RUN set -eux \
    && apt-get update -y \
    && apt-get install -y --no-install-recommends --no-install-suggests \
        iproute2 \
        libtinfo6 \
        libreadline8 \
        libssh-4 \
    && rm -rf /var/lib/apt/lists/* /var/log/*

COPY --from=builder /usr/sbin/bird* /usr/sbin/
COPY --from=builder /etc/bird/ /etc/bird/

EXPOSE 179/tcp
CMD ["bird", "-f", "-R"]
