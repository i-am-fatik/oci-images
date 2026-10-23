FROM alpine:3.24.3@sha256:be2656061b536c1883c4034790e21d0a09c5aaaf51968b89368a3c0371ea2c9a AS source
ARG SILENT_LINK_WATCHDOG_VERSION=edge-2eacdd8
ARG SILENT_LINK_WATCHDOG_COMMIT=2eacdd8897d9e832f36b772a0138c92f162a794a
RUN apk add --no-cache git \
 && git clone https://github.com/nktrjsk/silent-link-watchdog /src \
 && git -C /src checkout -q "$SILENT_LINK_WATCHDOG_COMMIT"

FROM python:3.12.15-slim@sha256:a0f57064cbdcd6285fa45f51237ab0b35ea8ebaed06a1f5973e89e19dfb86704
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    DB_PATH=/data/silentlink.db \
    PORT=8080 \
    SILENTLINK_SKIP_DOTENV=1
WORKDIR /app
RUN apt-get update && apt-get upgrade -y && rm -rf /var/lib/apt/lists/*
COPY --from=source /src/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt \
        "psycopg[binary]>=3.1" "psycopg_pool>=3.1"
COPY --from=source /src/app ./app
RUN mkdir -p /data && useradd -u 10001 -r app && chown -R app /data
USER app
EXPOSE 8080
CMD ["python", "-m", "app"]
