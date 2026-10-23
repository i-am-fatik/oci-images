FROM golang:1.26.10-alpine3.24@sha256:27f2d3b83cfc5803c8078f8e730af0f7c431bf067afaec8f60a36fb763187cba AS build
ARG TOR_CONTROLLER_VERSION=0.10.0
ARG TOR_CONTROLLER_COMMIT=f60092be56eeaf5eb033fc3d2fac2e3d53eadf80
RUN apk add --no-cache git
RUN git clone --depth 1 --branch "$TOR_CONTROLLER_VERSION" https://github.com/bugfest/tor-controller /src \
 && test "$(git -C /src rev-parse HEAD)" = "$TOR_CONTROLLER_COMMIT"
WORKDIR /src
RUN go get \
      golang.org/x/net@v0.61.0 \
      golang.org/x/crypto@v0.58.0 \
      golang.org/x/sys@v0.49.0 \
      golang.org/x/text@v0.43.0 \
      golang.org/x/term@v0.47.0 \
      golang.org/x/oauth2@v0.37.0 \
      golang.org/x/time@v0.16.0 \
      google.golang.org/protobuf@v1.36.12 \
      github.com/prometheus/client_golang@v1.20.5 \
      github.com/golang-jwt/jwt/v4@v4.5.2 \
      github.com/klauspost/compress@v1.20.3 \
      github.com/sirupsen/logrus@v1.9.4 \
 && go mod tidy \
 && CGO_ENABLED=0 go build -trimpath -ldflags="-s -w" -o /manager main.go

FROM gcr.io/distroless/static-debian12:nonroot@sha256:afa5c872c891853ca7fcf1f12c3edb23f7eeef36189728842dd51042ff57f7ab
WORKDIR /app
COPY --from=build /manager /app/manager
USER 1001
ENTRYPOINT ["/app/manager"]
