FROM python:3.14-slim

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
	&& apt-get install -y --no-install-recommends curl ca-certificates gnupg build-essential \
	&& curl -fsSL https://deb.nodesource.com/setup_22.x | bash - \
	&& apt-get install -y --no-install-recommends nodejs \
	&& pip install --no-cache-dir uvicorn uvloop \
	&& groupadd --gid 1000 mcpo || true \
	&& useradd --uid 1000 --gid 1000 --create-home --shell /bin/bash mcpo || true \
	&& apt-get purge -y --auto-remove curl gnupg build-essential \
	&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

WORKDIR /mcpo
RUN chown mcpo:mcpo /mcpo

USER mcpo

CMD ["/bin/bash"]
