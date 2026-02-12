# Multi-stage build for React application

# Build stage
FROM node:22-alpine AS build

WORKDIR /app

# Add backend URL as build-time environment variable
ARG REACT_APP_API_URL=http://bank-api-service:80
ENV REACT_APP_API_URL=$REACT_APP_API_URL

COPY package*.json ./
RUN npm ci --only=production
COPY . .

RUN npm run build

# Production stage
FROM nginx:alpine AS production

COPY --from=build /app/build /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf.template

RUN echo '#!/bin/sh' > /docker-entrypoint.sh && \
    echo 'envsubst "\$PORT" < /etc/nginx/conf.d/default.conf.template > /etc/nginx/conf.d/default.conf' >> /docker-entrypoint.sh && \
    echo 'exec nginx -g "daemon off;"' >> /docker-entrypoint.sh && \
    chmod +x /docker-entrypoint.sh

ENV PORT=80
EXPOSE $PORT
CMD ["/docker-entrypoint.sh"]
