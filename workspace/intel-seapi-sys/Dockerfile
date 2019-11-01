# start with musl (https://hub.docker.com/r/muslcc/x86_64)
FROM muslcc/x86_64:x86_64-linux-musl

# choose rust version
ARG RUSTUP_VERSION=1.20.2
ARG RUSTUP_SHA256=44d689d8cf49165f059cafe10a5ce49708a26b0b0641169bc0e39ad9c54930d5
ARG RUST_VERSION=1.38.0

# install rust (from https://github.com/rust-lang/docker-rust/blob/master/Dockerfile-alpine.template)
RUN apk add --no-cache \
        ca-certificates \
        gcc \
        curl

ENV RUSTUP_HOME=/usr/local/rustup \
    CARGO_HOME=/usr/local/cargo \
    PATH=/usr/local/cargo/bin:$PATH \
    RUST_VERSION=$RUST_VERSION

RUN set -eux; \
    url="https://static.rust-lang.org/rustup/archive/$RUSTUP_VERSION/x86_64-unknown-linux-musl/rustup-init"; \
    # for some (likely proxy) reason wget would not work here, using curl instead...
    curl -O "$url"; \
    echo "$RUSTUP_SHA256 *rustup-init" | sha256sum -c -; \
    chmod +x rustup-init; \
    ./rustup-init -y --no-modify-path --default-toolchain $RUST_VERSION; \
    rm rustup-init; \
    chmod -R a+w $RUSTUP_HOME $CARGO_HOME; \
    rustup --version; \
    cargo --version; \
    rustc --version;

# install dependencies needed for intel-seapi-sys build
RUN apk add --no-cache \
    cmake \
    make \
    musl-dev \
    clang-libs
# cmake and make are used by IntelSEAPI during the build
# musl-dev is needed for a pthread.h include
# clang-libs is needed for creating bindgen code in build.rs:bindgen_jitprofiling()

# attempt to build intel-seapi-sys
COPY . /opt/intel-seapi-sys
WORKDIR /opt/intel-seapi-sys
RUN cargo build
