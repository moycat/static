ARG PREFIX="/opt/openssh"
ARG PROXY

ARG OPENSSH_URL="https://cloudflare.cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-8.8p1.tar.gz"
ARG OPENSSL_URL="https://www.openssl.org/source/openssl-1.1.1l.tar.gz"
ARG ZLIB_URL="https://www.zlib.net/zlib-1.2.11.tar.gz"

FROM moycat/musl:latest AS build
ARG PREFIX
ARG PROXY
ARG OPENSSH_URL
ARG OPENSSL_URL
ARG ZLIB_URL

ENV http_proxy="${PROXY}" https_proxy="${PROXY}"

RUN wget -O - ${ZLIB_URL} | tar xz -C /tmp -f - \
    && cd /tmp/zlib-* && ./configure --static && make -j $(nproc) && make install && rm -rf /tmp/zlib-*
RUN wget -O - ${OPENSSL_URL} | tar xz -C /tmp -f - \
    && cd /tmp/openssl-* && ./Configure no-afalgeng no-tests no-shared linux-$(uname -m) -DOPENSSL_NO_SECURE_MEMORY && make -j $(nproc) && make install_sw && rm -rf /tmp/openssl-*

RUN wget -O - ${OPENSSH_URL} | tar xz -C /tmp -f - && cd /tmp/openssh-* \
    && LDFLAGS="-static" ./configure --with-ssl-dir=/usr/local --with-zlib=/usr/local --prefix="${PREFIX}" \
    && make -j $(nproc) && make install-nokeys \
    && rm -rf "${PREFIX}/etc/moduli" "${PREFIX}/etc/sshd_config" "${PREFIX}/libexec/sftp-server" "${PREFIX}/sbin" "${PREFIX}/share" \
    && rm -rf /tmp/openssh-*

FROM scratch
ARG PREFIX

COPY --from=build "${PREFIX}" /
