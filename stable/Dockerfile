ARG GIT_TAG="v0.10.3"
ARG PROCS_VERSION="${GIT_TAG}"

FROM westonsteimel/rust-static-builder:stable as builder

ARG GIT_TAG

RUN git clone --depth 1 --branch "${GIT_TAG}" https://github.com/dalance/procs /build/procs \
    && cd /build/procs \
    && /home/builder/build.sh

FROM alpine:latest as config

RUN addgroup procs \
    && adduser -G procs -s /bin/sh -D procs

FROM scratch

ARG PROCS_VERSION

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/procs/target/x86_64-unknown-linux-musl/release/procs /usr/local/bin/procs

USER procs

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/procs"]
CMD ["--help"]

LABEL org.opencontainers.image.title="procs" \
    org.opencontainers.image.description="procs in Docker" \
    org.opencontainers.image.version="${PROCS_VERSION}" \
    version="${PROCS_VERSION}"

