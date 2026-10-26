FROM docker:20.10.12-dind@sha256:6f2ae4a5fd85ccf85cdd829057a34ace894d25d544e5e4d9f2e7109297fedf8d

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
