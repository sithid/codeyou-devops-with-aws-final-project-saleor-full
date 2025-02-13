######################################################################################################################################################
# Dockerfile: Saleor-Api
# Purpose: Builds the docker image of the saleor-api for use in Code:You's DevOps w/ AWS Final Project which utilizes Saleor to demonstrate
#   students understanding of Docker, CI/CD pipelines, Github Workflows, and AWS deployment.
#
# Written By: James D. Glosser (demonicurges05@gmail.com)
#
# Notes:
#   I was confused about the following parts of the directions:
#       * TODO: needs more context. Configure Poetry to install dependencies directly in the system environment (disable virtual environments).    
#       * Weird build errors from SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input
#           - Fixed by copying all from ./saleor-api.
#
# What I find confusing or vague is solely my own experience and may have been made so on purpose as part of the assignment.
######################################################################################################################################################

FROM python:3.12

RUN apt-get update && apt-get install -y \
    gettext \
    libffi8 \
    libgdk-pixbuf2.0-0 \
    liblcms2-2 \
    libopenjp2-7 \
    libssl3 \
    libtiff6 \
    libwebp7 \
    shared-mime-info \
    mime-support \
    libpq5 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY . .

RUN pip install poetry==1.8.4
RUN poetry config virtualenvs.create false
COPY poetry.lock pyproject.toml /app/
RUN poetry install --no-root

RUN groupadd -g 1001 saleor
RUN useradd -u 1001 -g saleor -ms /bin/bash saleor
RUN chown -R saleor:saleor /app
USER saleor

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/static/}
RUN SECRET_KEY=dummy STATIC_URL=${STATIC_URL} python3 manage.py collectstatic --no-input

EXPOSE 8000

CMD ["gunicorn", "--bind", ":8000", "--workers", "4", "--worker-class", "saleor.asgi.gunicorn_worker.UvicornWorker", "saleor.asgi:application"]