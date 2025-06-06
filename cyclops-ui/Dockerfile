FROM node:20.10.0-alpine as builder
WORKDIR /app

# Install dependencies

COPY package.json ./
COPY yarn.lock ./

RUN #npm config set scripts-prepend-node-path true

RUN yarn install

# Copy source, static and config files (node_modules is ignored)
COPY . .

ARG version=0.0.0
ENV VITE_APP_VERSION=$version

# Build application
ENV NODE_OPTIONS=--max-old-space-size=1536
RUN yarn build

FROM nginx:stable-alpine as app

WORKDIR /usr/share/nginx/html

RUN apk add --no-cache gettext

COPY nginx-entrypoint.sh /

# Copy env file
COPY env.js ./

# Copy app files
COPY --from=0 app/build/ ./

# Copy reverse proxy configuration
COPY ./nginx/default.conf /default-nginx.conf

ARG VERSION
ENV REACT_APP_VERSION=$VERSION

ENTRYPOINT [ "sh", "/nginx-entrypoint.sh" ]
