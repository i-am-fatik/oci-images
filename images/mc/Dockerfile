FROM golang:1.26.10-alpine3.24@sha256:27f2d3b83cfc5803c8078f8e730af0f7c431bf067afaec8f60a36fb763187cba AS build
ARG MC_VERSION=RELEASE.2025-08-13T08-35-41Z
ARG MC_COMMIT=7394ce0dd2a80935aded936b09fa12cbb3cb8096
RUN apk add --no-cache git
RUN git clone --depth 1 --branch "$MC_VERSION" https://github.com/minio/mc /src \
 && test "$(git -C /src rev-parse HEAD)" = "$MC_COMMIT"
WORKDIR /src
RUN go get \
      golang.org/x/net@v0.61.0 \
      golang.org/x/crypto@v0.58.0 \
      golang.org/x/text@v0.43.0 \
      golang.org/x/sys@v0.49.0 \
      google.golang.org/grpc@v1.84.0 \
      github.com/klauspost/compress@v1.20.3 \
      github.com/prometheus/prometheus@v0.311.3 \
 && go mod tidy \
 && CGO_ENABLED=0 MC_RELEASE=RELEASE go build -trimpath -tags kqueue \
      -ldflags "$(MC_RELEASE=RELEASE go run buildscripts/gen-ldflags.go)" -o /mc .

FROM alpine:3.24.3@sha256:be2656061b536c1883c4034790e21d0a09c5aaaf51968b89368a3c0371ea2c9a
RUN apk add --no-cache ca-certificates
COPY --from=build /mc /usr/bin/mc
ENTRYPOINT ["mc"]
