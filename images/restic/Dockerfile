FROM golang:1.26.10-alpine3.24@sha256:27f2d3b83cfc5803c8078f8e730af0f7c431bf067afaec8f60a36fb763187cba AS build
ARG RESTIC_VERSION=v0.19.1
ARG RESTIC_COMMIT=6aa3a516ce654808a1f28f9fa21e9b7c8e6e90bf
RUN apk add --no-cache git
RUN git clone --depth 1 --branch "$RESTIC_VERSION" https://github.com/restic/restic /src \
 && test "$(git -C /src rev-parse HEAD)" = "$RESTIC_COMMIT"
WORKDIR /src
RUN go get \
      golang.org/x/net@v0.61.0 \
      golang.org/x/crypto@v0.58.0 \
      golang.org/x/text@v0.43.0 \
      golang.org/x/sys@v0.49.0 \
      google.golang.org/grpc@v1.84.0 \
      github.com/klauspost/compress@v1.20.3 \
 && go mod tidy \
 && CGO_ENABLED=0 go build -trimpath \
      -ldflags "-s -w -X 'main.version=${RESTIC_VERSION#v}'" \
      -o /restic ./cmd/restic

FROM alpine:3.24.3@sha256:be2656061b536c1883c4034790e21d0a09c5aaaf51968b89368a3c0371ea2c9a
RUN apk add --no-cache ca-certificates jq tzdata
COPY --from=build /restic /usr/bin/restic
COPY --from=build --chmod=0755 /src/docker/entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
