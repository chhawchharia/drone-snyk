FROM harness0.harness.io/oci/docker_artifacts/hhi/harnessos-alpine:1@sha256:290a37c85b4284d2c11fa0778e5da79643d912a1f5a57d6f701b77644503b7e7 AS hhi-ca-certificates
RUN apk add --no-cache 'ca-certificates>=20261020-r0'

FROM harness0.harness.io/oci/docker_artifacts/hhi/harnessos-alpine:1@sha256:290a37c85b4284d2c11fa0778e5da79643d912a1f5a57d6f701b77644503b7e7 AS alpine
RUN apk add -U --no-cache 'ca-certificates>=20261020-r0'

FROM docker:20.10.12-dind@sha256:6f2ae4a5fd85ccf85cdd829057a34ace894d25d544e5e4d9f2e7109297fedf8d
COPY --from=hhi-ca-certificates /etc/ssl/certs/ca-certificates.crt /etc/hhi/certs/ca-certificates.crt
ENV SSL_CERT_FILE=/etc/hhi/certs/ca-certificates.crt SSL_CERT_DIR=/etc/hhi/certs
RUN apk --update add \
    curl

# required to install scanning
RUN mkdir -p ~/.docker/cli-plugins && \
    curl https://github.com/docker/scan-cli-plugin/releases/download/v0.12.0/docker-scan_linux_amd64 -L -s -S -o ~/.docker/cli-plugins/docker-scan &&\
    chmod +x ~/.docker/cli-plugins/docker-scan

ENV GODEBUG netdns=go
ENV DOCKER_HOST=unix:///var/run/docker.sock

COPY --from=alpine /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

ADD release/linux/amd64/plugin /bin/
ENTRYPOINT ["/usr/local/bin/dockerd-entrypoint.sh", "/bin/plugin"]
COPY --from=hhi-ca-certificates /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt
