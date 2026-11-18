# Build stage
FROM node:20.18.0-alpine3.20 AS builder

WORKDIR /app

COPY package.json package-lock.json* ./

RUN if [ -f package-lock.json ]; then npm ci; else npm install; fi

COPY . .

RUN npm run build

# Production stage
FROM nginxinc/nginx-unprivileged:1.27.2-alpine

COPY --chown=101:101 nginx.conf /etc/nginx/conf.d/default.conf
COPY --chown=101:101 --from=builder /app/dist /usr/share/nginx/html

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=5s --retries=3 \
  CMD ["wget", "-q", "--spider", "http://127.0.0.1:8080/"]

CMD ["nginx", "-g", "daemon off;"]
