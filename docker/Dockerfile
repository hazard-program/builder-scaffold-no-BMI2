# Sui dev environment: CLI, local node, Node.js. Builds native for host arch (arm64/amd64).
# Ubuntu 24.04 required for Sui binary
FROM docker.io/library/ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    gnupg \
    git \
    jq \
    dos2unix \
    postgresql-client \
    libatomic1 \
    && mkdir -p /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key -o /tmp/nodesource-repo.gpg.key \
    && gpg --batch --quiet --show-keys --with-colons /tmp/nodesource-repo.gpg.key | grep -q "fpr.*:6F71F525282841EEDAF851B42F59B5F99B1BE0B4:" \
    && gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg /tmp/nodesource-repo.gpg.key \
    && rm -f /tmp/nodesource-repo.gpg.key \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_24.x nodistro main" > /etc/apt/sources.list.d/nodesource.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends nodejs \
    && npm install -g pnpm \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Custom Sui CLI compiled without AVX2
COPY sui /usr/local/bin/sui
RUN chmod +x /usr/local/bin/sui \
    && sui --version

WORKDIR /workspace
ENV SUI_CONFIG_DIR=/workspace/.sui

COPY docker/scripts/ /workspace/scripts/
RUN dos2unix /workspace/scripts/*.sh && chmod +x /workspace/scripts/*.sh

RUN sed -i 's|/workspace/builder-scaffold/docker/\.env\.sui|/workspace/.sui/.env.sui|g' /workspace/scripts/entrypoint.sh

ENTRYPOINT ["/workspace/scripts/entrypoint.sh"]
