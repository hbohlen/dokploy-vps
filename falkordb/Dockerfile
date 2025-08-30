FROM node:18-bullseye AS browser-build

# Build FalkorDB Browser
WORKDIR /app
RUN apt-get update && apt-get install -y git
RUN git clone https://github.com/FalkorDB/falkordb-browser.git .
RUN npm install && npm run build

FROM falkordb/falkordb:latest

# Install nginx and bash to serve the browser
RUN apt-get update && apt-get install -y nginx bash && rm -rf /var/lib/apt/lists/*

# Copy browser build
COPY --from=browser-build /app/.next/standalone /var/www/html

# Create nginx config for browser (HTTP only - Traefik handles SSL termination)
RUN mkdir -p /etc/nginx/sites-available /etc/nginx/conf.d && \
    echo 'server {\
        listen 80;\
        server_name _;\
        root /var/www/html;\
        index index.html;\
\
        location / {\
            try_files $uri $uri/ /index.html;\
        }\
\
        location /api {\
            proxy_pass http://127.0.0.1:6379;\
            proxy_set_header Host $host;\
            proxy_set_header X-Real-IP $remote_addr;\
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;\
            proxy_set_header X-Forwarded-Proto $scheme;\
        }\
    }' > /etc/nginx/sites-available/default && \
    ln -sf /etc/nginx/sites-available/default /etc/nginx/conf.d/default.conf
# Startup script
RUN echo '#!/bin/bash \n\
nginx -g "daemon off;" & \n\
exec falkordb-server --loadmodule /usr/lib/redis/modules/falkordb.so --port 6379 --bind 0.0.0.0 --protected-mode no' > /start.sh && chmod +x /start.sh

EXPOSE 80 6379

CMD ["/start.sh"]
