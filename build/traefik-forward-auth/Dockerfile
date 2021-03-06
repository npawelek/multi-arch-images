FROM golang:1.14-alpine as build

ARG VERSION=v2.2.0
ARG TARGETPLATFORM

ENV CGO_ENABLED=0 \
    GOOS=linux \
    GO111MODULE=on

RUN apk add --no-cache git

WORKDIR /go/src/github.com/thomseddon/traefik-forward-auth

RUN export GOOS=$(echo ${TARGETPLATFORM} | cut -d / -f1) && \
    export GOARCH=$(echo ${TARGETPLATFORM} | cut -d / -f2) && \
    export GOARM=$(echo ${TARGETPLATFORM} | cut -d / -f3 | cut -c2-) && \
    git clone --depth 1 -b ${VERSION} https://github.com/thomseddon/traefik-forward-auth.git . && \
    go build -a -installsuffix nocgo -o /traefik-forward-auth ./cmd

# # Use distroless as minimal base image to package the manager binary
# # Refer to https://github.com/GoogleContainerTools/distroless for more details
FROM gcr.io/distroless/static:nonroot

# COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=build --chown=nonroot /traefik-forward-auth /

USER nonroot:nonroot
