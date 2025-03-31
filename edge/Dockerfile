ARG VERSION="master"
ARG REVISION="de7bac2a98bd90f72e4a52d0b606c9e41ea9ef02"

FROM docker.io/rust:alpine as builder

ARG VERSION
ARG REVISION

RUN apk update && apk add --no-cache \
    git \
    ca-certificates \
    build-base \
    musl-utils \
    musl-dev

RUN git clone --branch "${VERSION}" https://github.com/dalance/procs /build/procs \
    && cd /build/procs \
    && git reset --hard "${REVISION}" \
    && RUSTFLAGS='-C link-arg=-s' cargo build --release --bins --target-dir /build/

FROM alpine:latest as config

RUN addgroup procs \
    && adduser -G procs -s /bin/sh -D procs

FROM scratch

ARG VERSION
ARG REVISION
ENV PROCS_VERSION="${VERSION}" \
    PROCS_REVISION="${REVISION}" \
    TERM="xterm-256color"

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/release/procs /usr/local/bin/procs

USER procs

ENTRYPOINT ["/usr/local/bin/procs"]
CMD ["--help"]

LABEL org.opencontainers.image.title="procs" \
    org.opencontainers.image.description="procs in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"

