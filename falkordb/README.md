# FalkorDB Deployment with HTTPS

## Overview
This Dockerfile has been configured to work with Traefik reverse proxy for HTTPS termination. The container serves HTTP only (port 80), and Traefik handles SSL certificates and HTTPS routing.

## Key Changes Made
1. **Removed SSL configuration from nginx** - No more hardcoded certificate paths
2. **Simplified nginx to HTTP only** - Listens only on port 80
3. **Added proper proxy headers** - For compatibility with Traefik reverse proxy
4. **Removed port 443 exposure** - Only exposes ports 80 (web) and 6379 (FalkorDB)

## Deployment Pattern
When deployed through Dokploy:
1. Container runs on HTTP port 80 internally
2. Traefik routes HTTPS traffic from `falkordb.hbohlen.systems` to the container's port 80
3. SSL certificates are automatically managed by Traefik using Let's Encrypt DNS-01 challenge
4. Users access the service securely via `https://falkordb.hbohlen.systems`

## Volume Recommendations
For data persistence, use the Dokploy volume pattern:
```
../files/falkordb-data:/var/lib/falkordb/data
```

This ensures FalkorDB data survives container restarts and redeployments.

## Traefik Integration
The container is designed to work behind Traefik with:
- Automatic HTTPS termination
- Let's Encrypt certificate management
- Proper forwarded headers for client IP tracking
- HTTP to HTTPS redirect handled by Traefik