# Deployment Guide

## Prerequisites
- Docker & Docker Compose
- Git
- (Production) VPS with public IP
- (Production) Domain name & SSL certificate

## Development Deployment

### 1. Clone Repository
```bash
git clone https://github.com/natbkgift/flowbiz-template-service.git
cd flowbiz-template-service
```

### 2. Configure Environment
```bash
cp .env.example .env
# Edit .env with your settings
```

### 3. Start Services
```bash
docker compose up --build
```

### 4. Verify
```bash
curl http://localhost:8000/healthz
curl http://localhost:8000/v1/meta
```

## Production Deployment

### 1. Server Setup
```bash
# SSH into your VPS
ssh user@your-server.com

# Install Docker
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

### 2. Deploy Application
```bash
git clone https://github.com/natbkgift/flowbiz-template-service.git
cd flowbiz-template-service

# Create production environment file
cp .env.example .env
nano .env
# Set:
# APP_ENV=prod
# FLOWBIZ_BUILD_SHA=$(git rev-parse HEAD)
```

### 3. Start Production Stack
```bash
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
```

### 4. Configure SSL (Let's Encrypt)
```bash
# Install certbot
sudo apt-get install certbot python3-certbot-nginx

# Obtain certificate
sudo certbot --nginx -d yourdomain.com

# Update nginx/snippets/security_headers.conf
# Uncomment HSTS header
```

### 5. Verify Production
```bash
curl http://yourdomain.com/healthz
curl http://yourdomain.com/v1/meta
```

## Monitoring

### Health Checks
```bash
# Docker health status
docker compose ps

# Application logs
docker compose logs -f api

# Nginx logs
docker compose logs -f nginx
```

### Endpoints
- Health: `GET /healthz`
- Metadata: `GET /v1/meta`

## Updates

### Rolling Update
```bash
# Pull latest changes
git pull origin main

# Rebuild and restart
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build

# Verify
curl http://yourdomain.com/healthz
```

### Rollback
```bash
# Checkout previous version
git checkout <previous-commit>

# Rebuild
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --build
```

## Troubleshooting

### Service Won't Start
```bash
# Check logs
docker compose logs api

# Check environment
docker compose exec api env | grep -E "APP_|FLOWBIZ_"

# Rebuild from scratch
docker compose down -v
docker compose up --build
```

### Port Already in Use
```bash
# Find process using port
sudo lsof -i :8000

# Change port in .env
APP_PORT=8001
```

### Nginx Issues
```bash
# Test configuration
docker compose exec nginx nginx -t

# Reload configuration
docker compose exec nginx nginx -s reload
```

## Security Checklist

- [ ] SSL certificate installed
- [ ] HSTS header enabled
- [ ] Firewall configured (ports 80, 443 only)
- [ ] Regular security updates
- [ ] Log monitoring enabled
- [ ] Backup strategy defined

## Performance

### Resource Limits
Add to docker-compose.prod.yml:
```yaml
services:
  api:
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 512M
```

### Scaling
```bash
# Scale API replicas
docker compose -f docker-compose.yml -f docker-compose.prod.yml up -d --scale api=3
```
