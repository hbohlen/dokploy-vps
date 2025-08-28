FROM node:18-alpine as browser-build

# Build FalkorDB Browser
WORKDIR /app
RUN apk add --no-cache git
RUN git clone https://github.com/FalkorDB/falkordb-browser.git .
RUN npm install && npm run build

FROM falkordb/falkordb:latest

# Install nginx to serve the browser
RUN apt-get update && apt-get install -y nginx && rm -rf /var/lib/apt/lists/*

# Copy browser build
COPY --from=browser-build /app/build /var/www/html

# Create nginx config for browser
RUN echo 'server { \
    listen 80; \
    server_name _; \
    root /var/www/html; \
    index index.html; \
    \
    location / { \
        try_files $uri $uri/ /index.html; \
    } \
    \
    location /api { \
        proxy_pass http://localhost:6379; \
        proxy_set_header Host $host; \
        proxy_set_header X-Real-IP $remote_addr; \
    } \
}' > /etc/nginx/sites-available/default

# Startup script
RUN echo '#!/bin/bash \n\
nginx & \n\
exec falkordb-server --loadmodule /usr/lib/redis/modules/falkordb.so --port 6379 --bind 0.0.0.0 --protected-mode no' > /start.sh && chmod +x /start.sh

EXPOSE 80 6379

CMD ["/start.sh"]
