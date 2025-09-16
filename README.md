# rethinkdb Installation Guide

rethinkdb is a free and open-source realtime database for modern applications. RethinkDB pushes JSON to your apps in realtime with changefeeds, serving as an open-source alternative to Firebase Realtime Database

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
  - CPU: 2+ cores recommended
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 10GB+ for data
  - Network: HTTP and native protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 28015 (default rethinkdb port)
  - Port 8080 for admin UI
- **Dependencies**:
  - Python 3.6+ (for driver)
  - GCC/Clang compiler (for building from source)
  - libcurl, libprotobuf, libncurses5
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

# Install rethinkdb
sudo dnf install -y rethinkdb

# Enable and start service
sudo systemctl enable --now rethinkdb

# Configure firewall
sudo firewall-cmd --permanent --add-port=28015/tcp
sudo firewall-cmd --reload

# Verify installation
rethinkdb --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install rethinkdb
sudo apt install -y rethinkdb

# Enable and start service
sudo systemctl enable --now rethinkdb

# Configure firewall
sudo ufw allow 28015

# Verify installation
rethinkdb --version
```

### Arch Linux

```bash
# Install rethinkdb
sudo pacman -S rethinkdb

# Enable and start service
sudo systemctl enable --now rethinkdb

# Verify installation
rethinkdb --version
```

### Alpine Linux

```bash
# Install rethinkdb
apk add --no-cache rethinkdb

# Enable and start service
rc-update add rethinkdb default
rc-service rethinkdb start

# Verify installation
rethinkdb --version
```

### openSUSE/SLES

```bash
# Install rethinkdb
sudo zypper install -y rethinkdb

# Enable and start service
sudo systemctl enable --now rethinkdb

# Configure firewall
sudo firewall-cmd --permanent --add-port=28015/tcp
sudo firewall-cmd --reload

# Verify installation
rethinkdb --version
```

### macOS

```bash
# Using Homebrew
brew install rethinkdb

# Start service
brew services start rethinkdb

# Verify installation
rethinkdb --version
```

### FreeBSD

```bash
# Using pkg
pkg install rethinkdb

# Enable in rc.conf
echo 'rethinkdb_enable="YES"' >> /etc/rc.conf

# Start service
service rethinkdb start

# Verify installation
rethinkdb --version
```

### Windows

```bash
# Using Chocolatey
choco install rethinkdb

# Or using Scoop
scoop install rethinkdb

# Verify installation
rethinkdb --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/rethinkdb

# Set up basic configuration
cat > /etc/rethinkdb/rethinkdb.conf << 'EOF'
# RethinkDB configuration file
bind=all
driver-port=28015
http-port=8080
cluster-port=29015

# Cache size in MB
cache-size=2048

# Directory configuration
directory=/var/lib/rethinkdb/default
log-file=/var/log/rethinkdb/rethinkdb.log

# Security
no-http-admin=no
EOF

# Test configuration
rethinkdb --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable rethinkdb

# Start service
sudo systemctl start rethinkdb

# Stop service
sudo systemctl stop rethinkdb

# Restart service
sudo systemctl restart rethinkdb

# Check status
sudo systemctl status rethinkdb

# View logs
sudo journalctl -u rethinkdb -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add rethinkdb default

# Start service
rc-service rethinkdb start

# Stop service
rc-service rethinkdb stop

# Restart service
rc-service rethinkdb restart

# Check status
rc-service rethinkdb status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'rethinkdb_enable="YES"' >> /etc/rc.conf

# Start service
service rethinkdb start

# Stop service
service rethinkdb stop

# Restart service
service rethinkdb restart

# Check status
service rethinkdb status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start rethinkdb
brew services stop rethinkdb
brew services restart rethinkdb

# Check status
brew services list | grep rethinkdb
```

### Windows Service Manager

```powershell
# Start service
net start rethinkdb

# Stop service
net stop rethinkdb

# Using PowerShell
Start-Service rethinkdb
Stop-Service rethinkdb
Restart-Service rethinkdb

# Check status
Get-Service rethinkdb
```

## Advanced Configuration

### Cluster Configuration
```bash
# /etc/rethinkdb/rethinkdb.conf
# Multi-node cluster setup

# Node name
server-name=node1

# Join existing cluster
join=192.168.1.10:29015
join=192.168.1.11:29015

# Cluster discovery
canonical-address=192.168.1.12

# Replication settings
replicas=3
shards=8
```

### Performance Tuning
```bash
# Memory and cache settings
cache-size=4096  # 4GB cache
io-threads=64    # Number of I/O threads

# Query engine settings  
max-concurrent-queries=1000
query-cache-size=512  # MB

# Network settings
max-connections=10000
connection-timeout=20
```

### Security Configuration
```bash
# Enable authentication
auth-key=your-secret-auth-key

# TLS/SSL configuration
tls-min-protocol=TLSv1.2
tls-ciphers=ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256
tls-cert-file=/etc/rethinkdb/cert.pem
tls-key-file=/etc/rethinkdb/key.pem

# Bind to specific interface
bind=127.0.0.1
bind-http=127.0.0.1
bind-cluster=10.0.0.5
```

### Storage Configuration  
```bash
# Storage engine settings
direct-io=yes
page-cache-size=2048  # MB

# Write durability
durability=hard  # or soft for better performance

# Compaction settings
compaction-concurrency=2
compaction-interval=3600  # seconds
```

### Python Driver Configuration
```python
import rethinkdb as r

# Connection pool configuration
conn = r.connect(
    host='localhost',
    port=28015,
    db='mydb',
    auth_key='your-auth-key',
    timeout=20,
    ssl={
        'ca_certs': '/path/to/ca.pem',
        'certfile': '/path/to/client-cert.pem', 
        'keyfile': '/path/to/client-key.pem'
    }
)

# Connection pool for production
from rethinkdb import net

pool = r.ConnectionPool(
    host='localhost',
    port=28015,
    max_idle=10,
    max_open=50,
    timeout=20
)
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream rethinkdb_backend {
    server 127.0.0.1:28015;
}

server {
    listen 80;
    server_name rethinkdb.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name rethinkdb.example.com;

    ssl_certificate /etc/ssl/certs/rethinkdb.example.com.crt;
    ssl_certificate_key /etc/ssl/private/rethinkdb.example.com.key;

    location / {
        proxy_pass http://rethinkdb_backend;
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
    ServerName rethinkdb.example.com
    Redirect permanent / https://rethinkdb.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName rethinkdb.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/rethinkdb.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/rethinkdb.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:28015/
    ProxyPassReverse / http://127.0.0.1:28015/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend rethinkdb_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/rethinkdb.pem
    redirect scheme https if !{ ssl_fc }
    default_backend rethinkdb_backend

backend rethinkdb_backend
    balance roundrobin
    server rethinkdb1 127.0.0.1:28015 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R rethinkdb:rethinkdb /etc/rethinkdb
sudo chmod 750 /etc/rethinkdb

# Configure firewall
sudo firewall-cmd --permanent --add-port=28015/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

### Initial Database Creation
```python
import rethinkdb as r

# Connect to RethinkDB
conn = r.connect(host='localhost', port=28015)

# Create database
r.db_create('myapp').run(conn)

# Create tables with indexes
r.db('myapp').table_create('users', primary_key='id').run(conn)
r.db('myapp').table('users').index_create('email').run(conn)
r.db('myapp').table('users').index_create('created_at').run(conn)

# Create table with custom settings
r.db('myapp').table_create('logs', 
    primary_key='id',
    durability='soft',  # Better write performance
    shards=3,
    replicas=2
).run(conn)
```

### Changefeeds Example
```python
# Real-time updates
for change in r.table('users').changes().run(conn):
    print(change)

# Filtered changefeed
for change in r.table('users')\
    .filter(r.row['status'] == 'active')\
    .changes()\
    .run(conn):
    print(change['new_val'])
```

### Geospatial Queries
```python  
# Create geo index
r.table('locations').index_create('coordinates', geo=True).run(conn)

# Find nearby locations
r.table('locations').get_nearest(
    r.point(-73.9857, 40.7484),  # NYC coordinates
    index='coordinates',
    max_dist=1000,  # meters
    unit='m'
).run(conn)
```

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
sudo systemctl status rethinkdb

# View logs
sudo journalctl -u rethinkdb -f

# Monitor resource usage
top -p $(pgrep rethinkdb)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/rethinkdb"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/rethinkdb-backup-$DATE.tar.gz" /etc/rethinkdb /var/lib/rethinkdb

echo "Backup completed: $BACKUP_DIR/rethinkdb-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop rethinkdb

# Restore from backup
tar -xzf /backup/rethinkdb/rethinkdb-backup-*.tar.gz -C /

# Start service
sudo systemctl start rethinkdb
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u rethinkdb -n 100
sudo tail -f /var/log/rethinkdb/rethinkdb.log

# Check configuration
rethinkdb --version

# Check permissions
ls -la /etc/rethinkdb
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 28015

# Test connectivity
telnet localhost 28015

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep rethinkdb)

# Check disk I/O
iotop -p $(pgrep rethinkdb)

# Check connections
ss -an | grep 28015
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  rethinkdb:
    image: rethinkdb:latest
    ports:
      - "28015:28015"
    volumes:
      - ./config:/etc/rethinkdb
      - ./data:/var/lib/rethinkdb
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update rethinkdb

# Debian/Ubuntu
sudo apt update && sudo apt upgrade rethinkdb

# Arch Linux
sudo pacman -Syu rethinkdb

# Alpine Linux
apk update && apk upgrade rethinkdb

# openSUSE
sudo zypper update rethinkdb

# FreeBSD
pkg update && pkg upgrade rethinkdb

# Always backup before updates
tar -czf /backup/rethinkdb-pre-update-$(date +%Y%m%d).tar.gz /etc/rethinkdb

# Restart after updates
sudo systemctl restart rethinkdb
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/rethinkdb

# Clean old logs
find /var/log/rethinkdb -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/rethinkdb
```

## Additional Resources

- Official Documentation: https://docs.rethinkdb.org/
- GitHub Repository: https://github.com/rethinkdb/rethinkdb
- Community Forum: https://forum.rethinkdb.org/
- Best Practices Guide: https://docs.rethinkdb.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
