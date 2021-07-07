FROM alpine:latest AS builder

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories \
    && echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
    && apk update --no-cache \
    && apk upgrade --no-cache \
    && apk add curl --no-cache --update \
    && ARGO_VERSION=$(curl -sSL "https://api.github.com/repos/argoproj/argo-cd/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/') \
    && curl -sSL -o /usr/local/bin/argocd https://github.com/argoproj/argo-cd/releases/download/$ARGO_VERSION/argocd-linux-amd64 \
    && chmod +x /usr/local/bin/argocd \
    && HELM_VERSION=$(curl -sSL "https://github.com/kubernetes/helm/releases" | sed -n '/Latest release<\/a>/,$p' | grep -oE 'v[0-9]+\.[0-9]+\.[0-9]+' | head -1) \
    && curl https://get.helm.sh/helm-$HELM_VERSION-linux-amd64.tar.gz | tar xzv \
    && mv linux-amd64/helm /usr/local/bin/helm \
    && curl https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/linux/oc.tar.gz -o /tmp/oc.tar.gz \
    && tar -xzvf /tmp/oc.tar.gz -C /bin \
    && rm /tmp/oc.tar.gz \
    && KUBECTL_VERSION=$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt) \
    && curl -LO https://storage.googleapis.com/kubernetes-release/release/$KUBECTL_VERSION/bin/linux/amd64/kubectl \
    && chmod +x ./kubectl \
    && mv ./kubectl /usr/local/bin/kubectl \
    && HADOLINT_VERSION=$(curl -sSL "https://github.com/hadolint/hadolint/releases" | sed -n '/Latest release<\/a>/,$p' | grep -oE 'v[0-9]+\.[0-9]+\.[0-9]+' | head -1) \
    && curl -L https://github.com/hadolint/hadolint/releases/download/$HADOLINT_VERSION/hadolint-Linux-x86_64 -o /usr/local/bin/hadolint \
    && chmod +x /usr/local/bin/hadolint

FROM alpine:latest

LABEL maintainer="Frederic Spiers <fredspiers@gmail.com>" \
      component="CI/CD tools"

ENV TZ="Europe/Paris"

COPY --from=builder /bin/oc /bin/
COPY --from=builder /usr/local/bin/argocd /usr/local/bin/helm /usr/local/bin/kubectl /usr/local/bin/hadolint /usr/local/bin/

RUN echo "http://dl-cdn.alpinelinux.org/alpine/edge/main" >> /etc/apk/repositories \
    && echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
    && apk update --no-cache \
    && apk upgrade --no-cache \  
    && apk add --no-cache --update \
    libc6-compat \
    podman \
    skopeo \
    git \
    wget \
    curl \
    gettext \
    bash \
    jq \
    yq \
    python3 \
    py3-pip \
    && rm -f /var/lib/containers/storage/libpod/bolt_state.db \
    && sed -i "s/driver = \"overlay\"/driver = \"vfs\"/" /etc/containers/storage.conf \
    && pip3 install awxkit flake8 pytest \
    && apk add buildah --repository=http://dl-cdn.alpinelinux.org/alpine/edge/testing/
