# AGENTS.md

## Infrastructure Overview

This repository documents and manages a DigitalOcean VPS running Dokploy with Tailscale networking. All services are secured behind Tailscale and only accessible through the Tailnet.

## VPS Details

- **Provider**: DigitalOcean Droplet
- **Domain**: `hbohlen.systems`
- **Tailnet IP**: `100.114.180.23`
- **SSH Access**: `hbohlen@hbohlen.systems` (port 22, root login disabled)
- **Root Access**: DigitalOcean Console only
- **Services**: Dokploy with Traefik reverse proxy

## System Specifications

- **OS**: Ubuntu 24.04.3 LTS (Noble Numbat)
- **Kernel**: 6.8.0-78-generic
- **Architecture**: x86_64
- **Memory**: 2GB RAM
- **Storage**: 48GB SSD (26% used)
- **CPU**: 1 vCPU (load average: ~0.4)
- **Uptime**: ~22 hours
- **User Account**: `hbohlen` with full sudo privileges
- **Active Services**: Docker, Tailscale, SSH, systemd-networkd, and others

## Access Requirements

### Prerequisites
- Must be connected to Tailscale network
- SSH access via `hbohlen` user account
- Domain resolves to Tailnet IP for security

### Service Access
- **Dokploy Dashboard**: `https://hbohlen.systems` (Tailnet only)
- **Future Services**: Will be deployed as `<service>.hbohlen.systems`

## Network Security

### Firewall Configuration (DigitalOcean)
- SSH (22/TCP): Open to all IPs (user access only)
- HTTP (80/TCP): Restricted to Tailscale range `100.64.0.0/10`
- HTTPS (443/TCP): Restricted to Tailscale range `100.64.0.0/10`
- Tailscale UDP (41641): Open for mesh connectivity

### Domain Configuration
- A record: `hbohlen.systems` → `100.114.180.23` (Tailnet IP)
- Wildcard support for subdomains via Traefik
- Let's Encrypt certificates managed automatically

## Development Workflow

### Infrastructure Exploration
1. SSH into VPS: `ssh hbohlen@hbohlen.systems`
2. Navigate to project: `cd /opt/dokploy-vps`
3. Run infrastructure checklist to discover current setup
4. Document findings in `docs/exploration-results.md`
5. Update this AGENTS.md with discovered configurations

### Service Deployment
1. Access Dokploy dashboard at `https://hbohlen.systems`
2. Deploy containers through Dokploy interface
3. Traefik handles automatic routing and SSL
4. Services inherit Tailscale access restrictions

### Container Development
- Build and test containers locally or on VPS
- Push to `ghcr.io/hbohlen/containers/<service>`
- Deploy via Dokploy with proper domain routing
- Monitor via Traefik access logs

## Current Configuration Status

### Dokploy
- **Status**: Running as Docker container `dokploy.1.*` on port 3000
- **Installation Path**: `/etc/dokploy` (configuration directory)
- **Configuration Files**:
  - Main config: `/etc/dokploy/traefik/dynamic/dokploy.yml`
  - Applications: `/etc/dokploy/applications/`
  - Compose files: `/etc/dokploy/compose/`
  - Logs: `/etc/dokploy/logs/`
  - SSH keys: `/etc/dokploy/ssh/`
  - Volume backups: `/etc/dokploy/volume-backups/`
- **Database**: PostgreSQL (internal container on port 5432)
- **Cache**: Redis (internal container on port 6379)
- **Web Access**: `https://hbohlen.systems` (via Traefik with Let's Encrypt SSL)

### Traefik
- **Configuration Path**: `/etc/dokploy/traefik/traefik.yml`
- **Dynamic Routing**: `/etc/dokploy/traefik/dynamic/` (dokploy.yml, middlewares.yml)
- **Middleware Setup**: HTTPS redirect middleware configured
- **Certificate Management**:
  - Let's Encrypt ACME with HTTP-01 challenge
  - Email: `bohlenhayden@gmail.com`
  - Storage: `/etc/dokploy/traefik/dynamic/acme.json`
  - Automatic renewal enabled
- **Access Logs**: JSON format at `/etc/dokploy/traefik/dynamic/access.log`
- **Entry Points**: HTTP (80), HTTPS (443) with HTTP/3 support

### Docker Environment
- **Network Setup**: Overlay network `dokploy-network` (10.0.1.0/24) in Docker Swarm mode
- **Running Services**:
  - `dokploy-traefik`: Traefik reverse proxy (ports 80, 443, 8080)
  - `dokploy-redis`: Redis database (port 6379 internal)
  - `dokploy`: Dokploy main application (port 3000)
  - `dokploy-postgres`: PostgreSQL database (port 5432 internal)
- **Volume Configuration**:
  - `../files/` pattern recommended for persistent data across deployments
  - `dokploy-docker-config`: Docker configuration storage
  - `dokploy-postgres-database`: PostgreSQL data persistence
  - `redis-data-volume`: Redis data persistence
  - `projects-langflow-ygqerf_*`: Langflow project volumes (stopped)
  - **Best Practice**: Use `../files/service-name:/container/path` for data persistence

### Dokploy Volume Best Practices
- **Recommended Pattern**: Use `../files/` relative paths instead of host bind mounts
- **Persistence**: Data survives container restarts and redeployments
- **Example**: `../files/falkordb-data:/var/lib/falkordb/data`
- **Storage Location**: Files stored in `/opt/dokploy-vps/files/` on the host
- **Avoid**: Direct host paths like `/folder:/path` (cleaned up on redeployment)

### Network & Service Status
- **Active Ports**:
  - `22/tcp`: SSH (OpenSSH)
  - `80/tcp`: HTTP (Traefik)
  - `443/tcp`: HTTPS (Traefik) with HTTP/3 support
  - `3000/tcp`: Dokploy dashboard
  - `8080/tcp`: Traefik dashboard
  - `2377/tcp`: Docker Swarm management
  - `7946/tcp`: Docker Swarm node communication
  - `41641/udp`: Tailscale
- **SSL/TLS**: Active with Let's Encrypt certificates (TLS 1.3, AES-128-GCM)
- **Service Health**: All core services running and accessible
- **External Access**: Restricted to Tailscale network (100.64.0.0/10)

## Security Best Practices

### Current Security Status
- **Firewall**: UFW active with Tailscale access and restricted HTTP/HTTPS to Tailnet (100.64.0.0/10)
- **SSH**: Root login disabled, key-based authentication enabled, password authentication disabled
- **File Permissions**:
  - Dokploy config: `/etc/dokploy/` (777 - world writable, concerning)
  - SSH keys: `/etc/dokploy/ssh/` (700 - secure)
  - SSL certificates: `/etc/dokploy/traefik/dynamic/acme.json` (600 - secure)
  - Docker socket: `/var/run/docker.sock` (660, root:docker - appropriate)
- **User Access**: `hbohlen` user has sudo and docker group membership
- **Tailscale**: Active and properly configured for network security

### Access Control
- Never expose services outside Tailscale without explicit firewall rules
- Use `sudo` for privileged operations from `hbohlen` account
- Emergency root access only via DigitalOcean Console
- Monitor access logs for unexpected traffic

### Certificate Management
- All certificates managed via Let's Encrypt
- Contact email: `bohlenhayden@gmail.com`
- Automatic renewal via Traefik
- Test new domains with staging certificates first

## Troubleshooting

### Common Issues
- **Cannot reach services**: Verify Tailscale connection to `100.114.180.23` and check UFW firewall status
- **SSH access denied**: Use `hbohlen` user (not root), ensure SSH key is properly configured
- **SSL certificate issues**: Check `/etc/dokploy/traefik/dynamic/acme.json` and Traefik logs
- **Service routing problems**: Review `/etc/dokploy/traefik/dynamic/dokploy.yml` configuration
- **Dokploy not accessible**: Check if `dokploy.1.*` container is running: `docker ps`
- **Database connection issues**: Verify `dokploy-postgres.1.*` and `dokploy-redis.1.*` containers

### Debug Commands
```bash
# Test Tailscale connectivity
ping 100.114.180.23
tailscale status

# SSH access
ssh hbohlen@hbohlen.systems

# Check service status
docker ps
docker logs dokploy.1.*  # Dokploy main app
docker logs dokploy-traefik  # Traefik proxy

# Check network connectivity
curl -I https://hbohlen.systems
curl -I http://localhost:8080  # Traefik dashboard

# Check firewall status
sudo ufw status

# Check listening ports
sudo ss -tlnp | grep LISTEN

# Check Dokploy configuration
sudo cat /etc/dokploy/traefik/dynamic/dokploy.yml

# Check system resources
df -h  # Disk usage
free -h  # Memory usage
uptime  # System load
```

### Log Locations
- **Traefik Access Logs**: `/etc/dokploy/traefik/dynamic/access.log` (JSON format)
- **Dokploy Logs**: `/etc/dokploy/logs/` (per-project logs)
- **Docker Logs**: `docker logs <container>` or `docker logs dokploy.1.*` for Dokploy
- **System Logs**: `journalctl -u docker` or `journalctl -u tailscaled`

## Project Goals

### Current Objectives
1. **Infrastructure Documentation**: Complete mapping of current VPS setup
2. **FalkorDB Deployment**: Build and deploy graph database with browser interface
3. **Best Practices**: Establish patterns for future container deployments

### Future Expansion
- Additional database services
- Development and staging environments
- Monitoring and alerting setup
- Backup and disaster recovery

---

*This document should be updated as infrastructure is explored and new services are deployed. Run the infrastructure checklist to discover current configurations and update sections marked [TO BE DISCOVERED].*
