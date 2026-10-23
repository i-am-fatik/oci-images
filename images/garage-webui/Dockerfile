FROM node:24.22.1-alpine3.24@sha256:06d7adf3e0ef61a13f1d28682726366f97a15aa1888c934d3ecec86c33918b53 AS frontend
ARG GARAGE_WEBUI_VERSION=1.1.0
ARG GARAGE_WEBUI_COMMIT=a6640157c1a757c92aa539149df0d902283e6763
RUN apk add --no-cache git
RUN git clone --depth 1 --branch "$GARAGE_WEBUI_VERSION" https://github.com/khairul169/garage-webui /src \
 && test "$(git -C /src rev-parse HEAD)" = "$GARAGE_WEBUI_COMMIT"
WORKDIR /src
RUN npm install -g pnpm@9.15.9 \
 && pnpm install --frozen-lockfile \
 && pnpm run build

FROM golang:1.26.10-alpine3.24@sha256:27f2d3b83cfc5803c8078f8e730af0f7c431bf067afaec8f60a36fb763187cba AS backend
COPY --from=frontend /src/backend /src/backend
COPY --from=frontend /src/dist /src/backend/ui/dist
WORKDIR /src/backend
RUN go get \
      golang.org/x/net@v0.61.0 \
      golang.org/x/crypto@v0.58.0 \
      golang.org/x/sys@v0.49.0 \
      golang.org/x/text@v0.43.0 \
 && go mod tidy \
 && CGO_ENABLED=0 go build -trimpath -ldflags="-s -w" -tags=prod -o /out/main main.go \
 && mkdir -m 1777 /out/tmp

FROM scratch
COPY --from=backend /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=backend /out/tmp /tmp
COPY --from=backend /out/main /bin/main
ENTRYPOINT ["/bin/main"]
