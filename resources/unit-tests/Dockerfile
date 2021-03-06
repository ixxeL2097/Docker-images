FROM alpine:latest AS builder

RUN apk update --no-cache \
    && apk add curl --no-cache --update \
    && HADOLINT_VERSION=$(curl -sSL "https://github.com/hadolint/hadolint/releases" | sed -n '/Latest release<\/a>/,$p' | grep -oE 'v[0-9]+\.[0-9]+\.[0-9]+' | head -1) \
    && curl -L "https://github.com/hadolint/hadolint/releases/download/$HADOLINT_VERSION/hadolint-Linux-x86_64" -o /usr/local/bin/hadolint \
    && chmod +x /usr/local/bin/hadolint

FROM alpine:latest

LABEL maintainer="Frederic Spiers <fredspiers@gmail.com>" \
      component="CI/CD tools"

ENV TZ="Europe/Paris" \
    IMG_VERSION="1.0.0"

COPY --from=builder /usr/local/bin/hadolint /usr/local/bin/

WORKDIR /usr/app

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories \
    && echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
    && apk update --no-cache \
    && apk upgrade --no-cache \  
    && apk add --no-cache --update \
    curl \
    bash \
    jq \
    python3 \
    py3-pip \
    skopeo \
    shellcheck \
    nodejs \
    npm

RUN pip3 install --no-cache-dir flake8 pytest pytest-cov coverage

RUN npm install jslint eslint jshint