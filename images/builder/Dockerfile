#ARG CILIUM_RUNTIME_IMAGE=docker.io/errordeveloper/cilium-runtime:1d8b75fddd0ae3348c48392d12b6d6084e239c5b-dev-wip
ARG COMPILERS_IMAGE=docker.io/cilium/image-compilers:818f48aa3eada1d23018f02f3c43a169c51eea1a@sha256:2f71fc0c9bdbb9a430ccb8cd86cb1fecc63acff464fc253fe3cd3748488e0e65
ARG GOLANG_IMAGE=golang:1.14.4@sha256:d39a459086c75920390c9b9d42bbc7c52fef1fe7666e7633a02508c607c889d4

#FROM ${COMPILERS_IMAGE} as compilers-dist
FROM ${GOLANG_IMAGE} as golang-dist

#FROM ${CILIUM_RUNTIME_IMAGE}
FROM ${COMPILERS_IMAGE}
LABEL maintainer="maintainer@cilium.io"

#COPY --from=compilers-dist / /

COPY --from=golang-dist /usr/local/go /usr/local/go
RUN mkdir -p /go
ENV GOROOT /usr/local/go
ENV GOPATH /go
ENV PATH "${GOROOT}/bin:${GOPATH}/bin:${PATH}"

COPY build-go-deps.sh /tmp/build-go-deps.sh
RUN /tmp/build-go-deps.sh

WORKDIR /go/src/github.com/cilium/cilium
