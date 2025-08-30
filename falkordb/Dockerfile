# Use Alpine for much smaller base image (~170MB vs 900MB)
FROM node:18-alpine AS browser-build

# Build FalkorDB Browser
WORKDIR /app

# Install git (much lighter on Alpine)
RUN apk add --no-cache git

# Clone and build with production optimizations
RUN git clone --depth=1 https://github.com/FalkorDB/falkordb-browser.git . && \
    npm ci --production --silent && \
    npm run build

# Use official FalkorDB image as base
FROM falkordb/falkordb:latest

# Install only bash (skip nginx - Next.js serves itself)
RUN apt-get update && apt-get install -y --no-install-recommends bash && \
    rm -rf /var/lib/apt/lists/*

# Copy only production files from browser build
COPY --from=browser-build /app/.next/standalone /app
COPY --from=browser-build /app/.next/static /app/.next/static
COPY --from=browser-build /app/public /app/public

# Simple startup script - Next.js on port 3000, FalkorDB on 6379
RUN echo '#!/bin/bash\ncd /app && node server.js &\nexec falkordb-server --loadmodule /usr/lib/redis/modules/falkordb.so --port 6379 --bind 0.0.0.0 --protected-mode no' > /start.sh && chmod +x /start.sh

EXPOSE 3000 6379

CMD ["/start.sh"]