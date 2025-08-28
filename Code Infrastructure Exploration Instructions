Code Infrastructure Exploration Instructions

## Your Mission

You are exploring a DigitalOcean VPS that runs Dokploy with Tailscale networking. Your job is to:

1. **Read and understand** the current AGENTS.md file
2. **Run the infrastructure checklist** systematically 
3. **Update AGENTS.md** as you discover actual configurations
4. **Document everything** you find for future development

## Step-by-Step Process

### Phase 1: Understand Current Documentation
```bash
# First, read the current AGENTS.md to understand what's expected
cat AGENTS.md

# Read the checklist to understand what you'll be exploring
cat docs/infrastructure-checklist.md
```

### Phase 2: System Overview Discovery
Run the **System Overview** section from the checklist and immediately update AGENTS.md with findings:

```bash
# After running system overview commands, update AGENTS.md with:
# - Actual user privileges and sudo capabilities
# - System specs and resource usage
# - Active services discovered
```

**Update Rule**: Replace `[TO BE DISCOVERED]` sections in AGENTS.md with actual findings as you go.

### Phase 3: Docker Environment Mapping
Run the **Docker Environment** commands and update AGENTS.md with:
- Running containers and their purposes
- Docker networks (especially dokploy-network details)
- Current volume usage and configuration

### Phase 4: Dokploy Discovery
Run the **Dokploy Configuration** commands and update AGENTS.md with:
- Exact installation location
- Configuration file locations
- Environment variables and settings
- How services are managed

### Phase 5: Traefik Deep Dive  
Run the **Traefik Deep Dive** commands and update AGENTS.md with:
- Configuration file contents and explanations
- Current routing rules and patterns
- Middleware configurations
- Certificate management setup

### Phase 6: Security and Permissions Analysis
Run security-focused commands and update AGENTS.md with:
- Actual file permissions on critical components
- Socket configurations and access
- Current firewall status
- Security considerations discovered

### Phase 7: Network and Service Analysis
Complete the network analysis and update AGENTS.md with:
- Active services and ports
- Network connectivity patterns
- Current SSL certificate status
- Service health and performance

## Update Guidelines

### What to Update in AGENTS.md
1. **Replace all `[TO BE DISCOVERED]` markers** with actual findings
2. **Add specific file paths** where configurations are found
3. **Include actual command examples** that work on this system
4. **Document any unexpected discoveries** or deviations from assumptions
5. **Add troubleshooting info** based on what you learn

### Documentation Standards
- **Be Specific**: Include exact paths, commands, and configurations
- **Be Current**: Document what actually exists, not what should exist
- **Be Helpful**: Add context and explanations for future developers
- **Be Accurate**: Verify information before adding it to AGENTS.md

### Example Update Pattern
Instead of:
```
- **Configuration Path**: [TO BE DISCOVERED]
```

Update to:
```
- **Configuration Path**: `/etc/dokploy/traefik/traefik.yml`
- **Dynamic Configs**: `/etc/dokploy/traefik/dynamic/` (dokploy.yml, middlewares.yml)
- **Certificate Storage**: `/etc/dokploy/traefik/dynamic/acme.json`
```

## Critical Requirements

### File Safety
- **NEVER** display private keys or sensitive credentials
- **DO** document where they're stored securely
- **VERIFY** permissions on sensitive files

### System Safety
- **READ** configurations, don't modify them during exploration
- **TEST** connectivity carefully 
- **DOCUMENT** any changes you need to make for testing

### Documentation Quality
- **EXPLAIN** complex configurations in simple terms
- **PROVIDE** working examples for common tasks
- **ANTICIPATE** future developer questions
- **ORGANIZE** information logically in AGENTS.md

## Success Criteria

You'll know you're done when:
- [ ] All `[TO BE DISCOVERED]` markers are replaced with actual information
- [ ] AGENTS.md accurately reflects the current infrastructure
- [ ] Future developers can understand and work with the setup
- [ ] Troubleshooting sections contain real, actionable advice
- [ ] Security considerations are documented based on actual configuration

## Final Step

After completing the exploration:
```bash
# Commit your updated documentation
git add AGENTS.md docs/
git commit -m "Infrastructure discovery complete - update AGENTS.md with actual configuration"
git push
```

## Remember

Your goal is to transform AGENTS.md from a template into a living document that accurately describes this specific VPS infrastructure. Be thorough, be accurate, and document everything that future developers (including yourself) will need to know.
