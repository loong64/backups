ARG ALPINE_VERSION=3.23
ARG ALPINE_IMAGE=ghcr.io/loong64/alpine:3.24

FROM --platform=$BUILDPLATFORM golang:1.27.3-alpine${ALPINE_VERSION} AS builder
ARG TARGETOS
ARG TARGETARCH

WORKDIR /app

COPY packages/restic-proxy/go.mod packages/restic-proxy/go.sum ./
RUN go mod download

COPY packages/restic-proxy/ ./
RUN CGO_ENABLED=0 GOOS=$TARGETOS GOARCH=$TARGETARCH go build -o /restic-proxy .

FROM ${ALPINE_IMAGE}

RUN apk add --no-cache dumb-init \
    && addgroup -g 1000 restic-proxy && adduser -u 1000 -G restic-proxy -s /bin/sh -D restic-proxy

USER restic-proxy

COPY --from=builder /restic-proxy /usr/local/bin/restic-proxy

ENV RESTIC_PROXY_HOST=0.0.0.0
ENV RESTIC_PROXY_PORT=1434
ENV LOG_FORMAT=json

EXPOSE 1434
CMD ["dumb-init", "restic-proxy"]
