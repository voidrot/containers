ARG CNPG_IMAGE=ghcr.io/cloudnative-pg/postgresql:18-standard-trixie
ARG PG_MAJOR=18
ARG PARADEDB_VERSION=v0.23.2
ARG CARGO_PGRX_VERSION=0.18.0

FROM ${CNPG_IMAGE} AS pg_search_builder

USER root
ARG PG_MAJOR
ARG PARADEDB_VERSION
ARG CARGO_PGRX_VERSION
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    ca-certificates \
    clang \
    curl \
    git \
    libclang-dev \
    pkg-config \
    postgresql-server-dev-${PG_MAJOR} \
  && rm -rf /var/lib/apt/lists/*

RUN curl --proto '=https' --tlsv1.2 -fsSL https://sh.rustup.rs | sh -s -- -y --profile minimal --default-toolchain stable

ENV PATH=/root/.cargo/bin:${PATH}

RUN cargo install --locked cargo-pgrx --version ${CARGO_PGRX_VERSION} \
  && cargo pgrx init --pg${PG_MAJOR}=/usr/lib/postgresql/${PG_MAJOR}/bin/pg_config

WORKDIR /src
RUN git clone --depth=1 --branch ${PARADEDB_VERSION} https://github.com/paradedb/paradedb.git

WORKDIR /src/paradedb/pg_search
RUN cargo pgrx package --profile=release-with-debug --pg-config /usr/lib/postgresql/${PG_MAJOR}/bin/pg_config

FROM ${CNPG_IMAGE}

USER root
ARG PG_MAJOR
ARG PARADEDB_VERSION
ARG CNPG_IMAGE

LABEL org.opencontainers.image.title="CNPG PostgreSQL 18 with common extensions" \
      org.opencontainers.image.description="CloudNativePG PostgreSQL 18 image extended with pg_cron, pg_search, pg_partman, pgvector, pg_trgm, and hypopg" \
      org.opencontainers.image.source="https://github.com/voidrot/containers" \
      org.opencontainers.image.vendor="voidrot" \
      org.opencontainers.image.base.name="${CNPG_IMAGE}" \
      io.voidrot.postgresql.major="18" \
      io.voidrot.paradedb.version="${PARADEDB_VERSION}"

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN apt-get update && apt-get install -y --no-install-recommends \
    postgresql-${PG_MAJOR}-cron \
    postgresql-${PG_MAJOR}-hypopg \
    postgresql-${PG_MAJOR}-partman \
    postgresql-${PG_MAJOR}-pgvector \
  && rm -rf /var/lib/apt/lists/*

COPY --from=pg_search_builder /src/paradedb/target/release-with-debug/pg_search-pg18/usr/lib/postgresql/18/lib/* /usr/lib/postgresql/18/lib/
COPY --from=pg_search_builder /src/paradedb/target/release-with-debug/pg_search-pg18/usr/share/postgresql/18/extension/* /usr/share/postgresql/18/extension/

USER postgres
