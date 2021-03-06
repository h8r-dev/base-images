FROM debian:bullseye-slim

RUN apt-get update \
  && apt install --no-install-recommends -y \
    jq \
    git \
    tar \
    curl \
    gzip \
    unzip \
    gnupg \
    dirmngr \
    xz-utils \
    libatomic1 \
    ca-certificates \
  && apt-get remove --purge --auto-remove -y && rm -rf /var/lib/apt/lists/*

# install yq
ENV YQ_VERSION 4.25.1
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSL --compressed "https://github.com/mikefarah/yq/releases/download/v$YQ_VERSION/yq_linux_${ARCH}" -o /usr/local/bin/yq \
  && curl -fsSLO --compressed "https://github.com/mikefarah/yq/releases/download/v$YQ_VERSION/checksums" \
  && echo "$(<checksums grep yq_linux_${ARCH} | grep -v tar.gz | awk '{print$19}') /usr/local/bin/yq" | sha256sum -c - \
  && chmod +x /usr/local/bin/yq \
  && rm checksums \
  && yq --version

# install terraform
ENV TF_VERSION 1.1.9
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSLO --compressed "https://releases.hashicorp.com/terraform/$TF_VERSION/terraform_${TF_VERSION}_linux_$ARCH.zip" \
  && curl -fsSLO "https://releases.hashicorp.com/terraform/$TF_VERSION/terraform_${TF_VERSION}_SHA256SUMS" \
  && grep " terraform_${TF_VERSION}_linux_$ARCH.zip\$" "terraform_${TF_VERSION}_SHA256SUMS" | sha256sum -c - \
  && unzip -d /usr/local/bin "terraform_${TF_VERSION}_linux_$ARCH.zip" \
  && rm "terraform_${TF_VERSION}_linux_$ARCH.zip" "terraform_${TF_VERSION}_SHA256SUMS" \
  && terraform version

# install github-cli
ENV GH_VERSION 2.11.3
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSLO --compressed "https://github.com/cli/cli/releases/download/v$GH_VERSION/gh_${GH_VERSION}_linux_$ARCH.tar.gz" \
  && curl -fsSLO "https://github.com/cli/cli/releases/download/v$GH_VERSION/gh_${GH_VERSION}_checksums.txt" \
  && grep " gh_${GH_VERSION}_linux_$ARCH.tar.gz\$" "gh_${GH_VERSION}_checksums.txt" | sha256sum -c - \
  && tar -xzf "gh_${GH_VERSION}_linux_$ARCH.tar.gz" --no-same-owner \
  && mv "gh_${GH_VERSION}_linux_$ARCH/bin/gh" /usr/local/bin/gh \
  && rm "gh_${GH_VERSION}_linux_$ARCH.tar.gz" "gh_${GH_VERSION}_checksums.txt" \
  && rm -rf "gh_${GH_VERSION}_linux_$ARCH" \
  && gh version

# argocd cli
ENV ARGOCD_VERSION 2.3.3
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSL --compressed "https://github.com/argoproj/argo-cd/releases/download/v$ARGOCD_VERSION/argocd-linux-$ARCH" -o /usr/local/bin/argocd \
  # && curl -fsSLO --compressed "https://github.com/argoproj/argo-cd/releases/download/v$ARGOCD_VERSION/argocd-linux-$ARCH.sha256" \
  # && echo "$(cat argocd-linux-$ARCH.sha256) /usr/local/bin/argocd" | sha256sum -c - \
  && chmod +x /usr/local/bin/argocd \
  # && rm argocd-linux-$ARCH.sha256 \
  && argocd version --client

# install kubectl
ENV KUBECTL_VERSION 1.23.7
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSL --compressed "https://dl.k8s.io/release/v$KUBECTL_VERSION/bin/linux/$ARCH/kubectl" -o /usr/local/bin/kubectl \
  && curl -fsSLO --compressed "https://dl.k8s.io/release/v$KUBECTL_VERSION/bin/linux/$ARCH/kubectl.sha256" \
  && echo "$(cat kubectl.sha256) /usr/local/bin/kubectl" | sha256sum -c - \
  && chmod +x /usr/local/bin/kubectl \
  && rm kubectl.sha256 \
  && kubectl version --client

# install helm
ENV HELM_VERSION 3.9.0
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSLO --compressed "https://get.helm.sh/helm-v$HELM_VERSION-linux-$ARCH.tar.gz" \
  && curl -fsSLO --compressed "https://get.helm.sh/helm-v$HELM_VERSION-linux-$ARCH.tar.gz.sha256sum" \
  && grep " helm-v$HELM_VERSION-linux-$ARCH.tar.gz\$" "helm-v$HELM_VERSION-linux-$ARCH.tar.gz.sha256sum" | sha256sum -c - \
  && tar -xzf "helm-v$HELM_VERSION-linux-$ARCH.tar.gz" --no-same-owner \
  && mv "linux-$ARCH/helm" /usr/local/bin/helm \
  && rm "helm-v$HELM_VERSION-linux-$ARCH.tar.gz" "helm-v$HELM_VERSION-linux-$ARCH.tar.gz.sha256sum" \
  && rm -rf "linux-$ARCH" \
  && helm version

# install kubeseal
ENV KUBESECAL_VERSION 0.17.5
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && mkdir kubeseal-tmp \
  && curl -fsSLO --compressed "https://github.com/bitnami-labs/sealed-secrets/releases/download/v${KUBESECAL_VERSION}/kubeseal-${KUBESECAL_VERSION}-linux-${ARCH}.tar.gz" \
  && curl -fsSLO --compressed "https://github.com/bitnami-labs/sealed-secrets/releases/download/v${KUBESECAL_VERSION}/sealed-secrets_${KUBESECAL_VERSION}_checksums.txt" \
  && grep " kubeseal-${KUBESECAL_VERSION}-linux-${ARCH}.tar.gz\$" "sealed-secrets_${KUBESECAL_VERSION}_checksums.txt" | sha256sum -c - \
  && tar zxf "kubeseal-${KUBESECAL_VERSION}-linux-${ARCH}.tar.gz" -C kubeseal-tmp \
  && mv kubeseal-tmp/kubeseal /usr/local/bin/kubeseal \
  && rm "kubeseal-${KUBESECAL_VERSION}-linux-${ARCH}.tar.gz" "sealed-secrets_${KUBESECAL_VERSION}_checksums.txt" \
  && rm -rf kubeseal-tmp \
  && kubeseal --version

# install nodejs
ENV NODE_VERSION 16.15.0
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='x64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz" \
  && curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt" \
  && grep " node-v$NODE_VERSION-linux-$ARCH.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
  && rm "node-v$NODE_VERSION-linux-$ARCH.tar.xz" SHASUMS256.txt \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && node --version \
  && npm --version \
  && npm install -g yarn \
  && npm install -g @vue/cli \
  && npm cache clean --force

# install nodejs
ENV GOMPLATE_VERSION 3.11.1
RUN ARCH= && dpkgArch="$(dpkg --print-architecture)" \
  && case "${dpkgArch##*-}" in \
    amd64) ARCH='amd64';; \
    arm64) ARCH='arm64';; \
    *) echo "unsupported architecture"; exit 1 ;; \
  esac \
  && curl -fsSLO --compressed https://github.com/hairyhenderson/gomplate/releases/download/v${GOMPLATE_VERSION}/gomplate_linux-${ARCH} \
  && mv gomplate_linux-${ARCH} /usr/local/bin/gomplate \
  && chmod +x /usr/local/bin/gomplate
