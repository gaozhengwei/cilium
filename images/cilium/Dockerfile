# syntax=docker/dockerfile:1.1-experimental
ARG CILIUM_BUILDER_IMAGE=docker.io/errordeveloper/cilium-builder:019538f3bd3275e8d8369e2c5823efbe0f3a4f06-dev
ARG CILIUM_RUNTIME_IMAGE=docker.io/errordeveloper/cilium-runtime:201594d798c0dafdc5969e81ca21fa2761103b7f-dev

#FROM quay.io/cilium/hubble:v0.6.0 as hubble

FROM --platform=linux/amd64 ${CILIUM_BUILDER_IMAGE} as builder

ARG NOSTRIP
ARG LOCKDEBUG

RUN --mount=type=bind,readwrite,target=/go/src/github.com/cilium/cilium --mount=target=/root/.cache,type=cache --mount=target=/go/pkg/mod,type=cache \
  make NOSTRIP=$NOSTRIP LOCKDEBUG=$LOCKDEBUG PKG_BUILD=1 \
  SKIP_DOCS=true DESTDIR=/out/linux/amd64 clean-container build-container install-container

RUN --mount=type=bind,readwrite,target=/go/src/github.com/cilium/cilium --mount=target=/root/.cache,type=cache --mount=target=/go/pkg/mod,type=cache \
  env GOARCH=arm64 CC=aarch64-linux-gnu-gcc \
  make NOSTRIP=$NOSTRIP LOCKDEBUG=$LOCKDEBUG PKG_BUILD=1 \
  HOST_CC=aarch64-linux-gnu-gcc HOST_STRIP=aarch64-linux-gnu-strip \
  SKIP_DOCS=true DESTDIR=/out/linux/arm64 clean-container build-container install-container

FROM ${CILIUM_RUNTIME_IMAGE}
ARG TARGETPLATFORM
LABEL maintainer="maintainer@cilium.io"

COPY --from=builder /out/${TARGETPLATFORM} /

#COPY --from=hubble /usr/bin/hubble /usr/bin/hubble
#COPY --from=builder /go/src/github.com/cilium/cilium/plugins/cilium-cni/cni-install.sh /cni-install.sh
#COPY --from=builder /go/src/github.com/cilium/cilium/plugins/cilium-cni/cni-uninstall.sh /cni-uninstall.sh
#COPY --from=builder /go/src/github.com/cilium/cilium/contrib/packaging/docker/init-container.sh /init-container.sh

WORKDIR /home/cilium

RUN groupadd -f cilium \
    && echo ". /etc/profile.d/bash_completion.sh" >> /etc/bash.bashrc

CMD ["/usr/bin/cilium"]
