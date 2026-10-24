FROM harness0.harness.io/oci/docker_artifacts/hhi/harnessos-alpine:1@sha256:290a37c85b4284d2c11fa0778e5da79643d912a1f5a57d6f701b77644503b7e7 AS hhi-ca-certificates
RUN apk add --no-cache 'ca-certificates>=20261020-r0'

FROM docker:20.10.12-dind@sha256:6f2ae4a5fd85ccf85cdd829057a34ace894d25d544e5e4d9f2e7109297fedf8d
COPY --from=hhi-ca-certificates /usr/share/ca-certificates/ /usr/share/ca-certificates/
COPY --from=hhi-ca-certificates /etc/ca-certificates.conf /etc/ca-certificates.conf
RUN rm -f /usr/share/ca-certificates/mozilla/AAA_Certificate_Services.crt /usr/share/ca-certificates/mozilla/Comodo_AAA_Services_root.crt /usr/share/ca-certificates/mozilla/emSign_ECC_Root_CA_-_G3.crt \
    && printf '%s\n' '!mozilla/AAA_Certificate_Services.crt' '!mozilla/Comodo_AAA_Services_root.crt' '!mozilla/emSign_ECC_Root_CA_-_G3.crt' >> /etc/ca-certificates.conf \
    && update-ca-certificates --fresh
RUN apk --update add \
    curl

# required to install scanning
RUN mkdir -p ~/.docker/cli-plugins && \
    curl https://github.com/docker/scan-cli-plugin/releases/download/v0.12.0/docker-scan_linux_amd64 -L -s -S -o ~/.docker/cli-plugins/docker-scan &&\
    chmod +x ~/.docker/cli-plugins/docker-scan

ENV GODEBUG netdns=go
ENV DOCKER_HOST=unix:///var/run/docker.sock


ADD release/linux/amd64/plugin /bin/
ENTRYPOINT ["/usr/local/bin/dockerd-entrypoint.sh", "/bin/plugin"]
