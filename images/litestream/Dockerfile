FROM golang:1.26.10-alpine3.24@sha256:27f2d3b83cfc5803c8078f8e730af0f7c431bf067afaec8f60a36fb763187cba AS build
ARG LITESTREAM_VERSION=v0.5.17
ARG LITESTREAM_COMMIT=ccd326c175b583b5e82893a6078f06dcef5fba3f
RUN apk add --no-cache git gcc musl-dev
RUN git clone --depth 1 --branch "$LITESTREAM_VERSION" https://github.com/benbjohnson/litestream /src \
 && test "$(git -C /src rev-parse HEAD)" = "$LITESTREAM_COMMIT"
WORKDIR /src
RUN go get \
      golang.org/x/net@v0.61.0 \
      golang.org/x/crypto@v0.58.0 \
      golang.org/x/text@v0.43.0 \
      golang.org/x/oauth2@v0.37.0 \
      golang.org/x/sync@v0.24.0 \
      golang.org/x/sys@v0.49.0 \
      google.golang.org/grpc@v1.84.0 \
      google.golang.org/protobuf@v1.36.12 \
      github.com/klauspost/compress@v1.20.3 \
 && go mod tidy \
 && CGO_ENABLED=1 go build -trimpath \
      -ldflags "-s -w -X 'main.Version=${LITESTREAM_VERSION}' -extldflags '-static'" \
      -tags osusergo,netgo,sqlite_omit_load_extension \
      -o /litestream ./cmd/litestream

FROM alpine:3.24.3@sha256:b3413572228e34bd1c9245f5565e6cd127005655e8a7899aef33d8e83255c9e6
COPY --from=build /litestream /usr/local/bin/litestream
USER 1000:1000
ENTRYPOINT ["/usr/local/bin/litestream"]
