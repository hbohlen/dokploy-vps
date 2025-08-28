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
- **Status**: [TO BE DISCOVERED]
- **Installation Path**: [TO BE DISCOVERED]
- **Configuration Files**: [TO BE DISCOVERED]

### Traefik
- **Configuration Path**: [TO BE DISCOVERED]
- **Dynamic Routing**: [TO BE DISCOVERED]
- **Middleware Setup**: [TO BE DISCOVERED]
- **Certificate Management**: [TO BE DISCOVERED]

### Docker Environment
- **Network Setup**: [TO BE DISCOVERED]
- **Running Services**: [TO BE DISCOVERED]
- **Volume Configuration**: [TO BE DISCOVERED]

## Security Best Practices

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
- **Cannot reach services**: Verify Tailscale connection to `100.114.180.23`
- **SSH access denied**: Use `hbohlen` user, not `root`
- **SSL certificate issues**: Check Traefik Let's Encrypt configuration
- **Service routing problems**: Review Traefik dynamic configuration

### Debug Commands
```bash
# Test Tailscale connectivity
ping 100.114.180.23
tailscale status

# SSH access
ssh hbohlen@hbohlen.systems

# Check service status
docker ps
docker logs <container-name>
```

### Log Locations
- **Traefik Access Logs**: [TO BE DISCOVERED]
- **Dokploy Logs**: [TO BE DISCOVERED]
- **Docker Logs**: `docker logs <container>`
- **System Logs**: `journalctl -u docker`

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
