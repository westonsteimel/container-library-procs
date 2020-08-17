ARG SOURCE
ARG REVISION
ARG CREATED
ARG VERSION
ARG GIT_REF="${VERSION}"

FROM westonsteimel/rust-static-builder:stable as builder

ARG GIT_REF

RUN git clone --depth 1 --branch "${GIT_REF}" https://github.com/dalance/procs /build/procs \
    && cd /build/procs \
    && /home/builder/build.sh

FROM alpine:latest as config

RUN addgroup procs \
    && adduser -G procs -s /bin/sh -D procs

FROM scratch

ARG VERSION
ARG SOURCE
ARG REVISION
ARG CREATED

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/procs/target/x86_64-unknown-linux-musl/release/procs /usr/local/bin/procs

USER procs

ENV TERM xterm-256color

ENTRYPOINT ["/usr/local/bin/procs"]
CMD ["--help"]

LABEL org.opencontainers.image.title="procs" \
    org.opencontainers.image.description="procs in Docker" \
    org.opencontainers.image.source="${SOURCE}" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.created="${CREATED}" \
    org.opencontainers.image.version="${VERSION}"

