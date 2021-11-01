FROM debian:buster-slim AS musl
ARG MUSL_URL="https://musl.libc.org/releases/musl-1.2.2.tar.gz"
ENV DEBIAN_FRONTEND=noninteractive
RUN apt update \
    && apt install -y build-essential autoconf automake cmake curl git wget unzip zip \
    && apt clean && rm -rf /var/lib/apt/lists/* /var/log/*
RUN wget -O - "${MUSL_URL}" | tar xz -C /tmp -f - \
    && cd /tmp/musl-* && ./configure && make -j $(nproc) && make install && rm -rf /tmp/musl-*
RUN rm "/usr/lib/$(uname -m)-linux-gnu/libc.so" && ln -s "/lib/$(uname -m)-linux-gnu/libc.so.6" "/usr/lib/$(uname -m)-linux-gnu/libc.so"
ENV CC=/usr/local/musl/bin/musl-gcc
