# Copyright Broadcom, Inc. All Rights Reserved.
# SPDX-License-Identifier: APACHE-2.0

FROM docker.io/bitnami/minideb:bookworm AS builder

ARG DOWNLOADS_URL="downloads.bitnami.com/files/stacksmith"
ARG TARGETARCH

ENV OS_ARCH="${TARGETARCH:-amd64}"

COPY prebuildfs /
SHELL ["/bin/bash", "-o", "errexit", "-o", "nounset", "-o", "pipefail", "-c"]

# Install required system packages and dependencies
RUN install_packages ca-certificates curl
RUN mkdir -p /tmp/bitnami/pkg/cache/ ; cd /tmp/bitnami/pkg/cache/ || exit 1 ; \
    COMPONENTS=( \
      "sealed-secrets-kubeseal-0.29.0-3-linux-${OS_ARCH}-debian-12" \
    ) ; \
    for COMPONENT in "${COMPONENTS[@]}"; do \
      if [ ! -f "${COMPONENT}.tar.gz" ]; then \
        curl -SsLf "https://${DOWNLOADS_URL}/${COMPONENT}.tar.gz" -O ; \
        curl -SsLf "https://${DOWNLOADS_URL}/${COMPONENT}.tar.gz.sha256" -O ; \
      fi ; \
      sha256sum -c "${COMPONENT}.tar.gz.sha256" ; \
      tar -zxf "${COMPONENT}.tar.gz" -C /opt/bitnami --strip-components=2 --no-same-owner ; \
      rm -rf "${COMPONENT}".tar.gz{,.sha256} ; \
    done
RUN uninstall_packages curl

######

FROM scratch

ARG DOWNLOADS_URL="downloads.bitnami.com/files/stacksmith"
ARG TARGETARCH

ENV OS_ARCH="${TARGETARCH:-amd64}"

LABEL com.vmware.cp.artifact.flavor="sha256:c50c90cfd9d12b445b011e6ad529f1ad3daea45c26d20b00732fae3cd71f6a83" \
      org.opencontainers.image.base.name="scratch" \
      org.opencontainers.image.created="2025-06-06T05:05:47Z" \
      org.opencontainers.image.description="Application packaged by Broadcom, Inc." \
      org.opencontainers.image.ref.name="0.29.0-debian-12-r5" \
      org.opencontainers.image.title="sealed-secrets-kubeseal" \
      org.opencontainers.image.vendor="Broadcom, Inc." \
      org.opencontainers.image.version="0.29.0"

COPY rootfs /
COPY --from=builder /opt/bitnami/sealed-secrets-kubeseal/.spdx-sealed-secrets-kubeseal.spdx /opt/bitnami/sealed-secrets-kubeseal/.spdx-sealed-secrets-kubeseal.spdx
COPY --from=builder /opt/bitnami/sealed-secrets-kubeseal/bin/kubeseal /opt/bitnami/sealed-secrets-kubeseal/bin/kubeseal
COPY --from=builder /opt/bitnami/sealed-secrets-kubeseal/licenses /opt/bitnami/sealed-secrets-kubeseal/licenses

ENV APP_VERSION="0.29.0" \
    BITNAMI_APP_NAME="sealed-secrets-kubeseal" \
    PATH="/opt/bitnami/sealed-secrets-kubeseal/bin:$PATH"

USER 1001

ENTRYPOINT [ "kubeseal" ]
