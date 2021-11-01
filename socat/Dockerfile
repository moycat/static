ARG PREFIX="/opt/socat"
ARG PROXY

ARG SOCAT_URL="http://www.dest-unreach.org/socat/download/socat-1.7.4.2.tar.gz"

FROM moycat/musl:latest AS build
ARG PREFIX
ARG PROXY
ARG SOCAT_URL

ENV http_proxy="${PROXY}" https_proxy="${PROXY}"

RUN wget -O - ${SOCAT_URL} | tar xz -C /tmp -f - \
    && cd /tmp/socat-* && CFLAGS="-static" ./configure --prefix="${PREFIX}" && make -j $(nproc) && make install \
    && strip "${PREFIX}/bin/socat" && rm -rf "${PREFIX}/share" /tmp/socat-*

FROM scratch
ARG PREFIX

COPY --from=build "${PREFIX}" /
