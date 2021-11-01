ARG PREFIX="/opt/sshpass"
ARG PROXY

ARG SSHPASS_URL="http://sourceforge.net/projects/sshpass/files/sshpass/1.09/sshpass-1.09.tar.gz"

FROM moycat/musl:latest AS build
ARG PREFIX
ARG PROXY
ARG SSHPASS_URL

ENV http_proxy="${PROXY}" https_proxy="${PROXY}"

RUN wget -O - ${SSHPASS_URL} | tar xz -C /tmp -f - \
    && cd /tmp/sshpass-* && CFLAGS="-static" ./configure --prefix="${PREFIX}" && make -j $(nproc) && make install \
    && strip "${PREFIX}/bin/sshpass" && rm -rf "${PREFIX}/share" /tmp/sshpass-*

FROM scratch
ARG PREFIX

COPY --from=build "${PREFIX}" /
