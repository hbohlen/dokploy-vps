# Use minimal Alpine Node.js for browser build
FROM node:18-alpine AS browser-build

WORKDIR /app

# Install minimal dependencies for git clone
RUN apk add --no-cache git

# Clone and build with optimizations
RUN git clone --depth=1 --single-branch https://github.com/FalkorDB/falkordb-browser.git . && \
    # Install all dependencies (needed for build), with optimizations
    npm ci --no-audit --no-fund --silent && \
    # Build with higher memory limit and optimizations for low-resource environments
    NODE_OPTIONS="--max-old-space-size=1024" NEXT_TELEMETRY_DISABLED=1 npm run build && \
    # Clean up to reduce final image size - remove everything except built files
    rm -rf .git src components pages styles public/icons node_modules package*.json && \
    # Remove source maps and other non-essential files to save space
    find .next -name "*.map" -delete && \
    find . -name "*.md" -delete && \
    find . -name "*.json" ! -path "./.next/*" -delete

# Use official FalkorDB image as base (already optimized)
FROM falkordb/falkordb:latest

# Don't install bash - use sh which is already available
# Copy only essential production files
COPY --from=browser-build /app/.next/standalone /app
COPY --from=browser-build /app/.next/static /app/.next/static
COPY --from=browser-build /app/public /app/public

# Use sh instead of bash for smaller footprint
RUN echo '#!/bin/sh\ncd /app && node server.js &\nexec falkordb-server --loadmodule /usr/lib/redis/modules/falkordb.so --port 6379 --bind 0.0.0.0 --protected-mode no' > /start.sh && \
    chmod +x /start.sh

EXPOSE 3000 6379

CMD ["/start.sh"]