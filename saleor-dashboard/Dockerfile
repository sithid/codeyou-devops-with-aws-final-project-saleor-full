######################################################################################################################################################
# Dockerfile: Saleor-Dashboard
# Purpose: Builds the docker image of the saleor-dashboard for use in Code:You's DevOps w/ AWS Final Project which utilizes Saleor to demonstrate
#   students understanding of Docker, CI/CD pipelines, Github Workflows, and AWS deployment.
#
# Written By: James D. Glosser (demonicurges05@gmail.com)
#
# Notes:
#   I was confused about the following parts of the directions:
#       * CMD/EntryPoint: I was confused about what to use for an entrypoint until I remembered that the runner (nginx) already has one defined.
#           - COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh
#
# What I find confusing or vague is solely my own experience and may have been made so on purpose as part of the assignment.
######################################################################################################################################################

FROM node:18-alpine AS builder

RUN apk add --no-cache bash

WORKDIR /app

COPY package.json package-lock.json ./
ENV CI=1

RUN npm ci --legacy-peer-deps

COPY nginx/ ./nginx/
COPY assets/ ./assets/
COPY locale/ ./locale/
COPY scripts/ ./scripts/
COPY src/ ./src/
COPY vite.config.js tsconfig.json ./
COPY *.d.ts ./
COPY schema.graphql ./
COPY introspection*.json ./
COPY .featureFlags/ ./.featureFlags/

ARG API_URL
ENV API_URL=${API_URL:-http://localhost:8000/graphql/}

ARG APP_MOUNT_URI
ENV APP_MOUNT_URI=${APP_MOUNT_URI:-/dashboard/}

ARG APPS_MARKETPLACE_API_URL
ENV APPS_MARKETPLACE_API_URL=${APPS_MARKETPLACE_API_URL:-https://apps.saleor.io/api/v2/saleor-apps}

ARG APPS_TUNNEL_URL_KEYWORDS
ENV APPS_TUNNEL_URL_KEYWORDS=${APPS_TUNNEL_URL_KEYWORDS}

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-/dashboard/}

ARG SKIP_SOURCEMAPS
ENV SKIP_SOURCEMAPS=${SKIP_SOURCEMAPS:-true}

ARG LOCALE_CODE
ENV LOCALE_CODE=${LOCALE_CODE:-EN}

RUN npm run build

FROM nginx:stable-alpine AS runner

WORKDIR /app

COPY --from=builder /app/build/ /app/
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh

RUN chmod +x /docker-entrypoint.d/50-replace-env-vars.sh

EXPOSE 9000