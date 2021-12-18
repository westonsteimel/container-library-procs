ARG VERSION="0.11.12"
ARG REVISION="e8f3b925250d244196a696681b8d5e73b792d166"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "v${VERSION}" https://github.com/dalance/procs /build/procs \
    && cd /build/procs \
    && git reset --hard "${REVISION}" \
    && NOCOMPRESS=1 /home/builder/build.sh

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
COPY --from=builder /build/artifacts/procs /usr/local/bin/procs

USER procs

ENTRYPOINT ["/usr/local/bin/procs"]
CMD ["--help"]

LABEL org.opencontainers.image.title="procs" \
    org.opencontainers.image.description="procs in Docker" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"

