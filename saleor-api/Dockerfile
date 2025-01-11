### Build and install packages
FROM python:3.12


RUN apt update -y && apt install -y gettext && apt clean && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
WORKDIR /app
RUN pip install poetry==1.8.4
RUN poetry config virtualenvs.create false
COPY poetry.lock pyproject.toml /app/
RUN poetry install --no-root

### Final image
# FROM python:3.12-slim

RUN groupadd -r saleor && useradd -r -g saleor saleor

# Pillow dependencies
RUN apt update -y \
  && apt install -y \
  libffi8 \
  libgdk-pixbuf2.0-0 \
  liblcms2-2 \
  libopenjp2-7 \
  libssl3 \
  libtiff6 \
  libwebp7 \
  libpq5 \
  shared-mime-info \
  mime-support \
  && apt clean \
  && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /app/media /app/static \
  && chown -R saleor:saleor /app/

# COPY --from=build-python /usr/local/lib/python3.12/site-packages/ /usr/local/lib/python3.12/site-packages/
# COPY --from=build-python /usr/local/bin/ /usr/local/bin/
COPY . /app
WORKDIR /app

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}
RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input

EXPOSE 8000
ENV PYTHONUNBUFFERED=1

CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]
