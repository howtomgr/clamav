# ClamAV Installation Guide

ClamAV is a free and open-source Antivirus. Open source antivirus engine

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
  - CPU: 2 cores minimum (4+ cores recommended)
  - RAM: 2GB minimum (4GB+ recommended)
  - Storage: 1GB for installation
  - Network: 3310 ports
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 3310 (default clamav port)
- **Dependencies**:
  - clamav-freshclam, clamav-update
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

# Install clamav
sudo dnf install -y clamav clamav-freshclam, clamav-update

# Enable and start service
sudo systemctl enable --now clamav-daemon

# Configure firewall
sudo firewall-cmd --permanent --add-service=clamav
sudo firewall-cmd --reload

# Verify installation
clamav --version || systemctl status clamav-daemon
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install clamav
sudo apt install -y clamav clamav-freshclam, clamav-update

# Enable and start service
sudo systemctl enable --now clamav-daemon

# Configure firewall
sudo ufw allow 3310

# Verify installation
clamav --version || systemctl status clamav-daemon
```

### Arch Linux

```bash
# Install clamav
sudo pacman -S clamav

# Enable and start service
sudo systemctl enable --now clamav-daemon

# Verify installation
clamav --version || systemctl status clamav-daemon
```

### Alpine Linux

```bash
# Install clamav
apk add --no-cache clamav

# Enable and start service
rc-update add clamav-daemon default
rc-service clamav-daemon start

# Verify installation
clamav --version || rc-service clamav-daemon status
```

### openSUSE/SLES

```bash
# Install clamav
sudo zypper install -y clamav clamav-freshclam, clamav-update

# Enable and start service
sudo systemctl enable --now clamav-daemon

# Configure firewall
sudo firewall-cmd --permanent --add-service=clamav
sudo firewall-cmd --reload

# Verify installation
clamav --version || systemctl status clamav-daemon
```

### macOS

```bash
# Using Homebrew
brew install clamav

# Start service
brew services start clamav

# Verify installation
clamav --version
```

### FreeBSD

```bash
# Using pkg
pkg install clamav

# Enable in rc.conf
echo 'clamav-daemon_enable="YES"' >> /etc/rc.conf

# Start service
service clamav-daemon start

# Verify installation
clamav --version || service clamav-daemon status
```

### Windows

```powershell
# Using Chocolatey
choco install clamav

# Or using Scoop
scoop install clamav

# Verify installation
clamav --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory if needed
sudo mkdir -p /etc/clamav

# Set up basic configuration
sudo tee /etc/clamav/clamav.conf << 'EOF'
# ClamAV Configuration
MaxThreads 12, MaxConnectionQueueLength 30
EOF

# Test configuration
sudo clamav -t || sudo clamav-daemon configtest

# Reload service
sudo systemctl reload clamav-daemon
```

### Security Hardening

```bash
# Set appropriate permissions
sudo chown -R clamav:clamav /etc/clamav
sudo chmod 750 /etc/clamav

# Enable security features
# See security section for detailed hardening steps
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable clamav-daemon

# Start service
sudo systemctl start clamav-daemon

# Stop service
sudo systemctl stop clamav-daemon

# Restart service
sudo systemctl restart clamav-daemon

# Reload configuration
sudo systemctl reload clamav-daemon

# Check status
sudo systemctl status clamav-daemon

# View logs
sudo journalctl -u clamav-daemon -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add clamav-daemon default

# Start service
rc-service clamav-daemon start

# Stop service
rc-service clamav-daemon stop

# Restart service
rc-service clamav-daemon restart

# Check status
rc-service clamav-daemon status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'clamav-daemon_enable="YES"' >> /etc/rc.conf

# Start service
service clamav-daemon start

# Stop service
service clamav-daemon stop

# Restart service
service clamav-daemon restart

# Check status
service clamav-daemon status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start clamav
brew services stop clamav
brew services restart clamav

# Check status
brew services list | grep clamav
```

### Windows Service Manager

```powershell
# Start service
net start clamav-daemon

# Stop service
net stop clamav-daemon

# Using PowerShell
Start-Service clamav-daemon
Stop-Service clamav-daemon
Restart-Service clamav-daemon

# Check status
Get-Service clamav-daemon
```

## Advanced Configuration

### Performance Optimization

```bash
# Configure performance settings
cat >> /etc/clamav/clamav.conf << 'EOF'
MaxThreads 12, MaxConnectionQueueLength 30
EOF

# Apply system tuning
sudo sysctl -w net.core.somaxconn=65535
sudo sysctl -w net.ipv4.tcp_max_syn_backlog=65535

# Restart service
sudo systemctl restart clamav-daemon
```

### Clustering and High Availability

```bash
# Configure clustering (if supported)
# See official documentation for cluster setup

# Basic load balancing setup example
# Configure multiple instances on different ports
```

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream clamav_backend {
    server 127.0.0.1:3310;
    server 127.0.0.1:{default_port}1 backup;
}

server {
    listen 80;
    server_name clamav.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name clamav.example.com;

    ssl_certificate /etc/ssl/certs/clamav.example.com.crt;
    ssl_certificate_key /etc/ssl/private/clamav.example.com.key;

    location / {
        proxy_pass http://clamav_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # WebSocket support (if needed)
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName clamav.example.com
    Redirect permanent / https://clamav.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName clamav.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/clamav.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/clamav.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:3310/
    ProxyPassReverse / http://127.0.0.1:3310/
    
    # WebSocket support (if needed)
    RewriteEngine on
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{HTTP:Connection} upgrade [NC]
    RewriteRule ^/?(.*) "ws://127.0.0.1:3310/$1" [P,L]
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend clamav_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/clamav.pem
    redirect scheme https if !{ ssl_fc }
    default_backend clamav_backend

backend clamav_backend
    balance roundrobin
    option httpchk GET /health
    server clamav1 127.0.0.1:3310 check
    server clamav2 127.0.0.1:{default_port}1 check backup
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R clamav:clamav /etc/clamav
sudo chmod 750 /etc/clamav

# Configure firewall
sudo firewall-cmd --permanent --add-service=clamav
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on

# Configure fail2ban
sudo tee /etc/fail2ban/jail.d/clamav.conf << 'EOF'
[clamav]
enabled = true
port = 3310
filter = clamav
logpath = /var/log/clamav/*.log
maxretry = 5
bantime = 3600
EOF
```

### SSL/TLS Configuration

```bash
# Generate SSL certificates
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/clamav.key \
    -out /etc/ssl/certs/clamav.crt

# Configure SSL in clamav
# See official documentation for SSL configuration
```

## Database Setup

### PostgreSQL Backend (if applicable)

```bash
# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE clamav_db;
CREATE USER clamav_user WITH ENCRYPTED PASSWORD 'secure_password';
GRANT ALL PRIVILEGES ON DATABASE clamav_db TO clamav_user;
EOF

# Configure clamav to use PostgreSQL
# See official documentation for database configuration
```

### MySQL/MariaDB Backend (if applicable)

```bash
# Create database and user
sudo mysql << EOF
CREATE DATABASE clamav_db;
CREATE USER 'clamav_user'@'localhost' IDENTIFIED BY 'secure_password';
GRANT ALL PRIVILEGES ON clamav_db.* TO 'clamav_user'@'localhost';
FLUSH PRIVILEGES;
EOF
```

## Performance Optimization

### System Tuning

```bash
# Kernel parameters
sudo tee -a /etc/sysctl.conf << EOF
net.core.somaxconn = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.core.netdev_max_backlog = 5000
vm.swappiness = 10
EOF

sudo sysctl -p

# ClamAV specific tuning
MaxThreads 12, MaxConnectionQueueLength 30
```

### Resource Limits

```bash
# Configure system limits
sudo tee -a /etc/security/limits.conf << EOF
clamav soft nofile 65535
clamav hard nofile 65535
clamav soft nproc 32768
clamav hard nproc 32768
EOF
```

## Monitoring

### Prometheus Integration

```yaml
# prometheus.yml configuration
scrape_configs:
  - job_name: 'clamav'
    static_configs:
      - targets: ['localhost:3310']
    metrics_path: '/metrics'
```

### Health Checks

```bash
# Basic health check script
#!/bin/bash
if systemctl is-active --quiet clamav-daemon; then
    echo "ClamAV is running"
    exit 0
else
    echo "ClamAV is not running"
    exit 1
fi
```

### Log Monitoring

```bash
# Configure log rotation
sudo tee /etc/logrotate.d/clamav << 'EOF'
/var/log/clamav/*.log {
    daily
    rotate 14
    compress
    delaycompress
    missingok
    notifempty
    create 0640 clamav clamav
    postrotate
        systemctl reload clamav-daemon > /dev/null 2>&1 || true
    endscript
}
EOF
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# ClamAV backup script
BACKUP_DIR="/backup/clamav"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"

# Stop service (if required)
systemctl stop clamav-daemon

# Backup configuration
tar -czf "$BACKUP_DIR/clamav-config-$DATE.tar.gz" /etc/clamav

# Backup data (adjust paths as needed)
tar -czf "$BACKUP_DIR/clamav-data-$DATE.tar.gz" /var/lib/clamav

# Start service
systemctl start clamav-daemon

# Clean old backups (keep 30 days)
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +30 -delete

echo "Backup completed: $BACKUP_DIR"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop clamav-daemon

# Restore configuration
sudo tar -xzf /backup/clamav/clamav-config-*.tar.gz -C /

# Restore data
sudo tar -xzf /backup/clamav/clamav-data-*.tar.gz -C /

# Set permissions
sudo chown -R clamav:clamav /etc/clamav
sudo chown -R clamav:clamav /var/lib/clamav

# Start service
sudo systemctl start clamav-daemon
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u clamav-daemon -n 100
sudo tail -f /var/log/clamav/*.log

# Check configuration
sudo clamav -t || sudo clamav-daemon configtest

# Check permissions
ls -la /etc/clamav
ls -la /var/lib/clamav
```

2. **Connection refused**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 3310
sudo netstat -tlnp | grep 3310

# Check firewall
sudo firewall-cmd --list-all
sudo iptables -L -n

# Test connection
telnet localhost 3310
nc -zv localhost 3310
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep clamd)
htop -p $(pgrep clamd)

# Check connections
ss -ant | grep :3310 | wc -l

# Monitor I/O
iotop -p $(pgrep clamd)
```

### Debug Mode

```bash
# Run in debug mode
sudo clamav -d
# or
sudo clamav-daemon debug

# Increase log verbosity
# Edit configuration to enable debug logging
```

## Integration Examples

### Docker Compose

```yaml
version: '3.8'
services:
  clamav:
    image: clamav:latest
    container_name: clamav
    ports:
      - "3310:3310"
    volumes:
      - ./config:/etc/clamav
      - ./data:/var/lib/clamav
    environment:
      - clamav_CONFIG=/etc/clamav/clamav.conf
    restart: unless-stopped
    networks:
      - clamav_net

networks:
  clamav_net:
    driver: bridge
```

### Kubernetes Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: clamav
spec:
  replicas: 3
  selector:
    matchLabels:
      app: clamav
  template:
    metadata:
      labels:
        app: clamav
    spec:
      containers:
      - name: clamav
        image: clamav:latest
        ports:
        - containerPort: 3310
        volumeMounts:
        - name: config
          mountPath: /etc/clamav
      volumes:
      - name: config
        configMap:
          name: clamav-config
---
apiVersion: v1
kind: Service
metadata:
  name: clamav
spec:
  selector:
    app: clamav
  ports:
  - port: 3310
    targetPort: 3310
  type: LoadBalancer
```

### Ansible Playbook

```yaml
---
- name: Install and configure ClamAV
  hosts: all
  become: yes
  tasks:
    - name: Install clamav
      package:
        name: clamav
        state: present
    
    - name: Configure clamav
      template:
        src: clamav.conf.j2
        dest: /etc/clamav/clamav.conf
        owner: clamav
        group: clamav
        mode: '0640'
      notify: restart clamav
    
    - name: Start and enable clamav
      systemd:
        name: clamav-daemon
        state: started
        enabled: yes
  
  handlers:
    - name: restart clamav
      systemd:
        name: clamav-daemon
        state: restarted
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update clamav

# Debian/Ubuntu
sudo apt update && sudo apt upgrade clamav

# Arch Linux
sudo pacman -Syu clamav

# Alpine Linux
apk update && apk upgrade clamav

# openSUSE
sudo zypper update clamav

# FreeBSD
pkg update && pkg upgrade clamav

# Always backup before updates
tar -czf /backup/clamav-pre-update-$(date +%Y%m%d).tar.gz /etc/clamav

# Restart after updates
sudo systemctl restart clamav-daemon
```

### Regular Maintenance Tasks

```bash
# Clean logs
find /var/log/clamav -name "*.log" -mtime +30 -delete

# Verify integrity
sudo clamav --verify || sudo clamav-daemon check

# Update databases (if applicable)
sudo clamav-update-db

# Optimize performance
sudo clamav-optimize

# Check for security updates
sudo clamav --security-check
```

## Additional Resources

- Official Documentation: https://docs.clamav.org/
- GitHub Repository: https://github.com/clamav/clamav
- Community Forum: https://forum.clamav.org/
- Wiki: https://wiki.clamav.org/
- Comparison vs Sophos, ESET, Kaspersky, Bitdefender: https://docs.clamav.org/comparison

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
