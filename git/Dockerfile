ARG PREFIX="/opt/git"
ARG PROXY

ARG GIT_URL="https://github.com/git/git/archive/refs/tags/v2.33.1.tar.gz"
ARG CURL_URL="https://github.com/curl/curl/releases/download/curl-7_79_1/curl-7.79.1.tar.gz"
ARG EXPAT_URL="https://github.com/libexpat/libexpat/releases/download/R_2_4_1/expat-2.4.1.tar.gz"
ARG OPENSSL_URL="https://www.openssl.org/source/openssl-1.1.1l.tar.gz"
ARG ZLIB_URL="https://www.zlib.net/zlib-1.2.11.tar.gz"

FROM moycat/musl:latest AS build
ARG PREFIX
ARG PROXY
ARG GIT_URL
ARG CURL_URL
ARG EXPAT_URL
ARG OPENSSL_URL
ARG ZLIB_URL

ENV http_proxy="${PROXY}" https_proxy="${PROXY}"

RUN wget -O - ${ZLIB_URL} | tar xz -C /tmp -f - \
    && cd /tmp/zlib-* && ./configure --static && make -j $(nproc) && make install && rm -rf /tmp/zlib-*
RUN wget -O - ${OPENSSL_URL} | tar xz -C /tmp -f - \
    && cd /tmp/openssl-* && ./Configure no-afalgeng no-tests no-shared linux-$(uname -m) -DOPENSSL_NO_SECURE_MEMORY && make -j $(nproc) && make install_sw && rm -rf /tmp/openssl-*
RUN wget -O - ${EXPAT_URL} | tar xz -C /tmp -f - \
    && cd /tmp/expat-* && ./configure --enable-shared=no && make -j $(nproc) && make install && rm -rf /tmp/expat-*
RUN wget -O - ${CURL_URL} | tar xz -C /tmp -f - \
    && cd /tmp/curl-* && ./configure --enable-shared=no --with-openssl=/usr/local --with-zlib=/usr/local && make -j $(nproc) && make install && rm -rf /tmp/curl-*

RUN wget -O - ${GIT_URL} | tar xz -C /tmp -f - \
    && cd /tmp/git-* && make configure \
    && ./configure --prefix="${PREFIX}" --with-zlib=/usr/local --with-openssl=/usr/local --with-curl=/usr/local --with-expat=/usr/local CPPFLAGS="-DNO_DEFLATE_BOUND" CFLAGS="-static" LDFLAGS="-L/usr/local/lib" LIBS="-lz -lssl -lcrypto" \
    && make -j $(nproc) libgit.a xdiff/lib.a && cp libgit.a /usr/local/lib/libgit.a && cp xdiff/lib.a /usr/local/lib/libxdiff.a \
    && make -j $(nproc) LIBS="-lgit -lxdiff -lz -lssl -lcrypto" NO_GETTEXT=1 NO_TCLTK=1 NO_PERL=1 NO_PYTHON=1 \
    && make install -j $(nproc) LIBS="-lgit -lxdiff -lz -lssl -lcrypto" NO_GETTEXT=1 NO_TCLTK=1 NO_PERL=1 NO_PYTHON=1 INSTALL_SYMLINKS=1 INSTALL_STRIP=-s \
    && rm -rf cd /tmp/git-*

FROM scratch
ARG PREFIX

COPY --from=build "${PREFIX}" /
