FROM ghcr.io/cloudnative-pg/postgresql:17.11-202610190957-system-trixie@sha256:ae22bdd6008e02c18180c84192b3afbc95a758a184899f1cb7e85ed9c3ef8128
ARG CNPG_POSTGRESQL_VERSION=17.11-202610190957-system-trixie-1
USER root
RUN pip install --no-cache-dir --break-system-packages --no-deps --upgrade certifi==2026.10.19 \
 && python3 -c "import certifi; assert certifi.__version__ == '2026.10.19', certifi.__version__"
USER 26
