# Deployment Guide

This guide covers deployment processes and production configuration for the Drupal 11 Payroll & Employee Management System.

## Deployment Overview

The application supports multiple deployment environments:
- **Development** - Local DDEV environment
- **Staging** - Pre-production testing environment  
- **Production** - Live application environment

## Environment Configuration

### Environment Variables

Key environment variables to configure:

```bash
# Database Configuration
DB_HOST=localhost
DB_NAME=drupal_payroll
DB_USER=drupal
DB_PASS=secure_password

# Drupal Configuration  
DRUPAL_HASH_SALT=random_hash_salt_string
DRUPAL_ENV=production

# File System
PUBLIC_FILES_PATH=/path/to/public/files
PRIVATE_FILES_PATH=/path/to/private/files

# Performance
CACHE_BACKEND=redis
REDIS_HOST=localhost
REDIS_PORT=6379

# Security
TRUSTED_HOST_PATTERNS='^example\.com$'
```

### settings.php Configuration

**Production settings.php additions:**
```php
<?php

// Environment-specific database configuration
if (getenv('DRUPAL_ENV') === 'production') {
  $databases['default']['default'] = [
    'database' => getenv('DB_NAME'),
    'username' => getenv('DB_USER'),
    'password' => getenv('DB_PASS'),
    'host' => getenv('DB_HOST'),
    'port' => '3306',
    'driver' => 'mysql',
    'prefix' => '',
    'collation' => 'utf8mb4_general_ci',
  ];
}

// Hash salt
$settings['hash_salt'] = getenv('DRUPAL_HASH_SALT');

// File paths
$settings['file_public_path'] = 'sites/default/files';
$settings['file_private_path'] = getenv('PRIVATE_FILES_PATH');

// Trusted host configuration
$settings['trusted_host_patterns'] = [
  getenv('TRUSTED_HOST_PATTERNS'),
];

// Performance settings
$config['system.performance']['css']['preprocess'] = TRUE;
$config['system.performance']['js']['preprocess'] = TRUE;
$config['system.performance']['cache']['page']['max_age'] = 86400;

// Logging
$config['system.logging']['error_level'] = 'hide';

// Cache backend
if (getenv('CACHE_BACKEND') === 'redis') {
  $settings['redis.connection']['interface'] = 'PhpRedis';
  $settings['redis.connection']['host'] = getenv('REDIS_HOST');
  $settings['redis.connection']['port'] = getenv('REDIS_PORT');
  $settings['cache']['default'] = 'cache.backend.redis';
}
```

## Pre-deployment Checklist

### Code Preparation

- [ ] All feature branches merged to master
- [ ] Configuration exported and committed
- [ ] Database update hooks tested
- [ ] Frontend assets built for production
- [ ] Dependencies updated and locked
- [ ] Security updates applied

### Testing Requirements

- [ ] All automated tests passing
- [ ] Manual testing completed
- [ ] Performance testing results acceptable
- [ ] Security scan completed
- [ ] Accessibility testing passed
- [ ] Cross-browser testing completed

### Configuration Verification

- [ ] Configuration import tested on staging
- [ ] Database schema updates verified
- [ ] File permissions configured
- [ ] Environment variables set
- [ ] SSL certificates configured
- [ ] Backup procedures tested

## Deployment Process

### Standard Deployment Steps

#### 1. Backup Current Environment

```bash
# Database backup
mysqldump -u $DB_USER -p$DB_PASS $DB_NAME > backup_$(date +%Y%m%d_%H%M%S).sql

# File system backup
tar -czf files_backup_$(date +%Y%m%d_%H%M%S).tar.gz /path/to/drupal/sites/default/files

# Code backup (if not using Git)
tar -czf code_backup_$(date +%Y%m%d_%H%M%S).tar.gz /path/to/drupal
```

#### 2. Deploy Code Changes

```bash
# Pull latest changes
git checkout master
git pull origin master

# Install/update dependencies
composer install --no-dev --optimize-autoloader

# Install frontend dependencies and build assets
npm ci
npm run build
```

#### 3. Database Updates

```bash
# Put site in maintenance mode
drush state:set system.maintenance_mode 1

# Clear caches before updates
drush cache:rebuild

# Run database updates
drush updatedb

# Import configuration changes
drush config:import

# Clear caches after updates
drush cache:rebuild

# Take site out of maintenance mode
drush state:set system.maintenance_mode 0
```

#### 4. Post-deployment Verification

```bash
# Verify site is accessible
curl -I https://your-domain.com

# Check for errors in logs
drush watchdog:show --severity=Error

# Verify configuration is correct
drush config:status

# Test critical functionality
# - Employee creation
# - Search functionality  
# - User authentication
# - File uploads
```

### Zero-downtime Deployment

For production systems requiring zero downtime:

#### 1. Blue-Green Deployment Setup

```bash
# Deploy to staging environment (green)
rsync -av /current/production/ /staging/green/

# Update green environment
cd /staging/green
git pull origin master
composer install --no-dev
npm run build
drush updb
drush cim
drush cr

# Test green environment
# Run automated tests
# Perform manual verification

# Switch traffic to green environment
# Update load balancer configuration
# Verify traffic is flowing correctly

# Keep blue environment as rollback option
```

#### 2. Database Migration Strategy

```bash
# For schema changes requiring downtime
# Create maintenance window
# Export data if needed
# Apply schema changes
# Import data
# Verify integrity
# Resume normal operations
```

## Production Optimization

### Performance Configuration

#### Drupal Performance Settings

```php
// Enable CSS/JS aggregation
$config['system.performance']['css']['preprocess'] = TRUE;
$config['system.performance']['js']['preprocess'] = TRUE;

// Page caching
$config['system.performance']['cache']['page']['max_age'] = 3600;

// Views caching
$config['views.settings']['ui']['cache'] = TRUE;
```

#### Database Optimization

```sql
-- Optimize employee table indexes
CREATE INDEX idx_employee_status ON node__field_work_permit_status (field_work_permit_status_value);
CREATE INDEX idx_employee_salary ON node__field_salary_paid (field_salary_paid_value);
CREATE INDEX idx_employee_created ON node_field_data (created);

-- Optimize search queries
CREATE FULLTEXT INDEX idx_employee_search ON node__field_full_name (field_full_name_value);
```

#### Web Server Configuration

**Nginx configuration example:**
```nginx
server {
    listen 443 ssl http2;
    server_name your-domain.com;
    root /var/www/drupal/web;
    
    # SSL configuration
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/private.key;
    
    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
    
    # Static asset caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
    
    # Drupal configuration
    location / {
        try_files $uri /index.php?$query_string;
    }
    
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php-fpm;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```

### Security Hardening

#### File Permissions

```bash
# Set proper ownership
chown -R www-data:www-data /var/www/drupal

# Set directory permissions
find /var/www/drupal -type d -exec chmod 755 {} \;

# Set file permissions
find /var/www/drupal -type f -exec chmod 644 {} \;

# Protect sensitive files
chmod 600 /var/www/drupal/web/sites/default/settings.php
chmod 600 /var/www/drupal/web/sites/default/settings.local.php
```

#### Security Headers

```nginx
# Security headers
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-Content-Type-Options "nosniff" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';" always;
```

## Monitoring and Maintenance

### Application Monitoring

#### Health Check Endpoints

Create custom health check endpoint:
```php
/**
 * Health check controller.
 */
public function healthCheck() {
  $checks = [
    'database' => $this->checkDatabase(),
    'files' => $this->checkFiles(),
    'cache' => $this->checkCache(),
  ];
  
  return new JsonResponse($checks);
}
```

#### Log Monitoring

```bash
# Monitor Drupal logs
tail -f /var/log/drupal/drupal.log

# Monitor web server logs
tail -f /var/log/nginx/access.log
tail -f /var/log/nginx/error.log

# Monitor PHP logs
tail -f /var/log/php/error.log
```

### Automated Backups

#### Database Backup Script

```bash
#!/bin/bash
# backup-database.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/database"
DB_NAME="drupal_payroll"

# Create backup directory
mkdir -p $BACKUP_DIR

# Perform backup
mysqldump -u $DB_USER -p$DB_PASS $DB_NAME > $BACKUP_DIR/db_backup_$DATE.sql

# Compress backup
gzip $BACKUP_DIR/db_backup_$DATE.sql

# Remove backups older than 7 days
find $BACKUP_DIR -name "*.sql.gz" -mtime +7 -delete

echo "Database backup completed: db_backup_$DATE.sql.gz"
```

#### File Backup Script

```bash
#!/bin/bash
# backup-files.sh

DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/backups/files"
FILES_DIR="/var/www/drupal/web/sites/default/files"

# Create backup directory
mkdir -p $BACKUP_DIR

# Perform backup
tar -czf $BACKUP_DIR/files_backup_$DATE.tar.gz -C $FILES_DIR .

# Remove backups older than 30 days
find $BACKUP_DIR -name "*.tar.gz" -mtime +30 -delete

echo "Files backup completed: files_backup_$DATE.tar.gz"
```

### Automated Deployment

#### CI/CD Pipeline Example (GitHub Actions)

```yaml
# .github/workflows/deploy.yml
name: Deploy to Production

on:
  push:
    branches: [master]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup PHP
        uses: shivammathur/setup-php@v2
        with:
          php-version: '8.4'
          
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'
          
      - name: Install dependencies
        run: |
          composer install --no-dev --optimize-autoloader
          npm ci
          
      - name: Build assets
        run: npm run build
        
      - name: Deploy to server
        uses: easingthemes/ssh-deploy@v2.1.5
        env:
          SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
          REMOTE_HOST: ${{ secrets.REMOTE_HOST }}
          REMOTE_USER: ${{ secrets.REMOTE_USER }}
          SOURCE: "./"
          TARGET: "/var/www/drupal"
          
      - name: Run deployment script
        uses: appleboy/ssh-action@v0.1.4
        with:
          host: ${{ secrets.REMOTE_HOST }}
          username: ${{ secrets.REMOTE_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            cd /var/www/drupal
            drush updb -y
            drush cim -y
            drush cr
```

## Rollback Procedures

### Quick Rollback Process

```bash
# 1. Identify rollback point
git log --oneline -n 10

# 2. Create rollback branch
git checkout -b rollback-$(date +%Y%m%d_%H%M%S)

# 3. Revert to previous version
git reset --hard [previous-commit-hash]

# 4. Deploy rolled-back version
# Follow standard deployment process

# 5. Restore database backup if needed
mysql -u $DB_USER -p$DB_PASS $DB_NAME < backup_previous.sql

# 6. Verify rollback success
curl -I https://your-domain.com
drush status
```

### Emergency Rollback

For critical issues requiring immediate action:

```bash
# Enable maintenance mode
drush state:set system.maintenance_mode 1

# Restore from backup
mysql -u $DB_USER -p$DB_PASS $DB_NAME < emergency_backup.sql

# Clear all caches
drush cr

# Disable maintenance mode
drush state:set system.maintenance_mode 0

# Verify system health
drush status
```

## Troubleshooting Deployment Issues

### Common Issues and Solutions

**Configuration Import Failures:**
```bash
# Check configuration status
drush config:status

# Identify specific issues
drush config:import --dry-run

# Force import with overrides
drush config:import --skip-modules=module_name
```

**Database Update Failures:**
```bash
# Check update status
drush updatedb --dry-run

# Run specific updates
drush updatedb --entity-updates

# Clear caches and retry
drush cr && drush updatedb
```

**File Permission Issues:**
```bash
# Reset permissions
chmod -R 755 /var/www/drupal
chmod -R 644 /var/www/drupal/web/sites/default/files
chown -R www-data:www-data /var/www/drupal
```

## Post-deployment Tasks

### Verification Checklist

- [ ] Site loads without errors
- [ ] Admin dashboard accessible
- [ ] Employee CRUD operations work
- [ ] Search functionality operational
- [ ] File uploads working
- [ ] User authentication functional
- [ ] Performance metrics acceptable
- [ ] SSL certificate valid
- [ ] Backup procedures verified

### Performance Monitoring

Set up monitoring for:
- Page load times
- Database query performance
- Server resource usage
- Error rates
- User experience metrics

For ongoing maintenance and monitoring, refer to the [Troubleshooting](Troubleshooting.md) guide.