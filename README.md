# hexo Installation Guide

hexo is a free and open-source blog framework. Hexo provides fast, simple, and powerful blog framework

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for sites
  - Network: Build tool
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default hexo port)
  - Dev server on 4000
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install hexo
sudo dnf install -y hexo

# Enable and start service
sudo systemctl enable --now hexo

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
hexo --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install hexo
sudo apt install -y hexo

# Enable and start service
sudo systemctl enable --now hexo

# Configure firewall
sudo ufw allow N/A

# Verify installation
hexo --version
```

### Arch Linux

```bash
# Install hexo
sudo pacman -S hexo

# Enable and start service
sudo systemctl enable --now hexo

# Verify installation
hexo --version
```

### Alpine Linux

```bash
# Install hexo
apk add --no-cache hexo

# Enable and start service
rc-update add hexo default
rc-service hexo start

# Verify installation
hexo --version
```

### openSUSE/SLES

```bash
# Install hexo
sudo zypper install -y hexo

# Enable and start service
sudo systemctl enable --now hexo

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
hexo --version
```

### macOS

```bash
# Using Homebrew
brew install hexo

# Start service
brew services start hexo

# Verify installation
hexo --version
```

### FreeBSD

```bash
# Using pkg
pkg install hexo

# Enable in rc.conf
echo 'hexo_enable="YES"' >> /etc/rc.conf

# Start service
service hexo start

# Verify installation
hexo --version
```

### Windows

```bash
# Using Chocolatey
choco install hexo

# Or using Scoop
scoop install hexo

# Verify installation
hexo --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/hexo

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
hexo --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable hexo

# Start service
sudo systemctl start hexo

# Stop service
sudo systemctl stop hexo

# Restart service
sudo systemctl restart hexo

# Check status
sudo systemctl status hexo

# View logs
sudo journalctl -u hexo -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add hexo default

# Start service
rc-service hexo start

# Stop service
rc-service hexo stop

# Restart service
rc-service hexo restart

# Check status
rc-service hexo status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'hexo_enable="YES"' >> /etc/rc.conf

# Start service
service hexo start

# Stop service
service hexo stop

# Restart service
service hexo restart

# Check status
service hexo status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start hexo
brew services stop hexo
brew services restart hexo

# Check status
brew services list | grep hexo
```

### Windows Service Manager

```powershell
# Start service
net start hexo

# Stop service
net stop hexo

# Using PowerShell
Start-Service hexo
Stop-Service hexo
Restart-Service hexo

# Check status
Get-Service hexo
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream hexo_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name hexo.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name hexo.example.com;

    ssl_certificate /etc/ssl/certs/hexo.example.com.crt;
    ssl_certificate_key /etc/ssl/private/hexo.example.com.key;

    location / {
        proxy_pass http://hexo_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName hexo.example.com
    Redirect permanent / https://hexo.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName hexo.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/hexo.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/hexo.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend hexo_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/hexo.pem
    redirect scheme https if !{ ssl_fc }
    default_backend hexo_backend

backend hexo_backend
    balance roundrobin
    server hexo1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R hexo:hexo /etc/hexo
sudo chmod 750 /etc/hexo

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status hexo

# View logs
sudo journalctl -u hexo -f

# Monitor resource usage
top -p $(pgrep hexo)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/hexo"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/hexo-backup-$DATE.tar.gz" /etc/hexo /var/lib/hexo

echo "Backup completed: $BACKUP_DIR/hexo-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop hexo

# Restore from backup
tar -xzf /backup/hexo/hexo-backup-*.tar.gz -C /

# Start service
sudo systemctl start hexo
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u hexo -n 100
sudo tail -f /var/log/hexo/hexo.log

# Check configuration
hexo --version

# Check permissions
ls -la /etc/hexo
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep hexo)

# Check disk I/O
iotop -p $(pgrep hexo)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  hexo:
    image: hexo:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/hexo
      - ./data:/var/lib/hexo
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update hexo

# Debian/Ubuntu
sudo apt update && sudo apt upgrade hexo

# Arch Linux
sudo pacman -Syu hexo

# Alpine Linux
apk update && apk upgrade hexo

# openSUSE
sudo zypper update hexo

# FreeBSD
pkg update && pkg upgrade hexo

# Always backup before updates
tar -czf /backup/hexo-pre-update-$(date +%Y%m%d).tar.gz /etc/hexo

# Restart after updates
sudo systemctl restart hexo
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/hexo

# Clean old logs
find /var/log/hexo -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/hexo
```

## Additional Resources

- Official Documentation: https://docs.hexo.org/
- GitHub Repository: https://github.com/hexo/hexo
- Community Forum: https://forum.hexo.org/
- Best Practices Guide: https://docs.hexo.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
