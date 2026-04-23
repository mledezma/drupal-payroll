# Troubleshooting Guide

This guide covers common issues and solutions for the Drupal 11 Payroll & Employee Management System.

## Development Environment Issues

### DDEV Problems

#### DDEV Won't Start

**Symptoms:**
- `ddev start` fails with container errors
- Database connection errors
- Web service not accessible

**Solutions:**

1. **Check Docker status:**
   ```bash
   docker ps
   docker system info
   
   # Restart Docker if needed
   sudo systemctl restart docker  # Linux
   # Or restart Docker Desktop on Mac/Windows
   ```

2. **Clean DDEV environment:**
   ```bash
   ddev poweroff
   ddev clean
   ddev start
   ```

3. **Check port conflicts:**
   ```bash
   ddev describe
   netstat -tulpn | grep :80
   netstat -tulpn | grep :443
   
   # Change ports if needed
   ddev config --web-extra-ports=8080,8443
   ```

4. **Reset DDEV completely:**
   ```bash
   ddev delete -Oy
   ddev config
   ddev start
   ```

#### Database Connection Issues

**Symptoms:**
- "Database connection failed" errors
- Can't access database from Drupal
- Drush commands fail with DB errors

**Solutions:**

1. **Check database status:**
   ```bash
   ddev describe
   ddev logs db
   
   # Test database connection
   ddev mysql
   ```

2. **Import fresh database:**
   ```bash
   ddev import-db --src=backup.sql
   # Or
   ddev drush sql:drop
   ddev drush site:install
   ```

3. **Check database credentials:**
   ```bash
   ddev describe
   # Verify credentials in web/sites/default/settings.php
   ```

### Drupal Configuration Issues

#### Configuration Import Failures

**Symptoms:**
- `drush cim` fails with errors
- Configuration status shows mismatches
- New features don't appear after deployment

**Common Errors and Solutions:**

1. **UUID mismatches:**
   ```bash
   # Error: "Site UUID in source storage does not match the target storage."
   
   # Solution: Update site UUID
   ddev drush config:set system.site uuid [UUID_FROM_CONFIG]
   
   # Or export current config to match site
   ddev drush cex
   ```

2. **Missing modules:**
   ```bash
   # Error: "Module 'module_name' is not installed"
   
   # Solution: Install missing modules
   ddev composer require drupal/module_name
   ddev drush en module_name
   ddev drush cim
   ```

3. **Configuration conflicts:**
   ```bash
   # Check what's different
   ddev drush config:status
   
   # Import specific configurations
   ddev drush cim --partial --source=config/sync
   
   # Or force import (use with caution)
   ddev drush cim --skip-modules=problematic_module
   ```

#### Database Update Failures

**Symptoms:**
- `drush updb` fails partway through
- Database schema out of sync
- Features not working after code updates

**Solutions:**

1. **Run updates in parts:**
   ```bash
   # Check what updates are pending
   ddev drush updatedb --dry-run
   
   # Run entity updates separately
   ddev drush updatedb --entity-updates
   
   # Run hook updates
   ddev drush updatedb --no-entity-updates
   ```

2. **Clear caches before and after:**
   ```bash
   ddev drush cr
   ddev drush updb
   ddev drush cr
   ```

3. **Recover from failed update:**
   ```bash
   # Check database for incomplete updates
   ddev drush sql:query "SELECT * FROM key_value WHERE name LIKE '%update%'"
   
   # Clear update status (use carefully)
   ddev drush sql:query "DELETE FROM key_value WHERE collection = 'post_update'"
   
   # Re-run updates
   ddev drush updb
   ```

### Frontend Build Issues

#### Asset Build Failures

**Symptoms:**
- `npm run build` fails with errors
- CSS/JS files not generated
- Styles not appearing on site

**Solutions:**

1. **Clear npm cache and reinstall:**
   ```bash
   ddev ssh
   rm -rf node_modules package-lock.json
   npm cache clean --force
   exit
   
   ddev npm install
   ddev npm run build
   ```

2. **Check for syntax errors:**
   ```bash
   ddev npm run lint
   
   # Fix specific file issues
   ddev npm run lint:css
   ddev npm run lint:js
   ```

3. **Debug webpack build:**
   ```bash
   ddev npm run build -- --verbose
   
   # Or check webpack config
   ddev npm run debug
   ```

#### TailwindCSS Not Working

**Symptoms:**
- Tailwind classes not applying
- Custom component styles missing
- Build completes but no styles generated

**Solutions:**

1. **Check TailwindCSS configuration:**
   ```javascript
   // tailwind.config.js
   module.exports = {
     content: [
       './web/themes/custom/*/components/**/*.{twig,js}',
       './web/themes/custom/*/templates/**/*.twig',
       './src/**/*.{js,scss}'
     ],
     // ... rest of config
   };
   ```

2. **Verify SCSS imports:**
   ```scss
   // main.scss
   @import 'tailwindcss/base';
   @import 'tailwindcss/components';
   @import 'tailwindcss/utilities';
   ```

3. **Clear all caches:**
   ```bash
   ddev npm run build
   ddev drush cr
   ddev drush cc theme-registry
   ```

## Application-Specific Issues

### Employee Management Problems

#### Employee Creation Fails

**Symptoms:**
- Form submission errors
- Required field validation issues
- Image upload failures

**Solutions:**

1. **Check field configurations:**
   ```bash
   ddev drush field:list employee
   
   # Check specific field settings
   ddev drush config:get field.field.node.employee.field_salary_paid
   ```

2. **File upload issues:**
   ```bash
   # Check file permissions
   ls -la web/sites/default/files/
   
   # Fix permissions
   chmod -R 755 web/sites/default/files/
   chown -R $USER web/sites/default/files/
   ```

3. **Validate form structure:**
   ```bash
   # Check for custom validation
   grep -r "employee_form" web/modules/custom/
   
   # Clear form cache
   ddev drush cc theme-registry
   ```

#### Search Functionality Not Working

**Symptoms:**
- No search results returned
- Search form not submitting
- Database errors in search

**Solutions:**

1. **Check Views configuration:**
   ```bash
   ddev drush views:list | grep employee
   
   # Check specific view
   ddev drush config:get views.view.employee_search
   ```

2. **Rebuild search indexes:**
   ```bash
   # If using Search API
   ddev drush search-api:rebuild-tracker
   ddev drush search-api:index
   
   # If using core search
   ddev drush search:reindex
   ```

3. **Database index issues:**
   ```bash
   ddev mysql
   SHOW INDEX FROM node__field_full_name;
   
   # Create index if missing
   CREATE FULLTEXT INDEX idx_employee_search ON node__field_full_name (field_full_name_value);
   ```

### Performance Issues

#### Slow Page Load Times

**Symptoms:**
- Pages take > 3 seconds to load
- High database query counts
- Memory exhaustion errors

**Solutions:**

1. **Enable Drupal debugging:**
   ```php
   // settings.local.php
   $config['system.performance']['css']['preprocess'] = FALSE;
   $config['system.performance']['js']['preprocess'] = FALSE;
   $config['system.logging']['error_level'] = 'verbose';
   
   // Enable Twig debugging
   $settings['twig_debug'] = TRUE;
   $settings['twig_auto_reload'] = TRUE;
   $settings['twig_cache'] = FALSE;
   ```

2. **Profile database queries:**
   ```bash
   # Enable database logging
   ddev drush config:set system.logging.database_logging 1
   
   # Check slow queries
   ddev mysql -e "SHOW PROCESSLIST;"
   ddev mysql -e "SELECT * FROM mysql.slow_log ORDER BY start_time DESC LIMIT 10;"
   ```

3. **Optimize Views queries:**
   ```bash
   # Check Views query performance
   ddev drush views:analyze employee_list
   
   # Enable Views query debugging
   # Admin → Development → Views → Settings → Show SQL query
   ```

#### Memory Limit Errors

**Symptoms:**
- "Fatal error: Allowed memory size exhausted"
- Page crashes during operation
- Import/export operations fail

**Solutions:**

1. **Increase PHP memory limit:**
   ```bash
   # Check current limit
   ddev exec php -ini | grep memory_limit
   
   # Increase in .ddev/config.yaml
   web_environment:
     - PHP_MEMORY_LIMIT=512M
   
   ddev restart
   ```

2. **Optimize data processing:**
   ```php
   // Process data in batches
   $batch_size = 100;
   $employees = \Drupal::entityQuery('node')
     ->condition('type', 'employee')
     ->range(0, $batch_size)
     ->execute();
   ```

3. **Clear caches more frequently:**
   ```bash
   ddev drush cr
   
   # Set up automated cache clearing
   ddev drush cron
   ```

## Production Issues

### Site Down/Inaccessible

**Symptoms:**
- 500 Internal Server Error
- Database connection errors
- Site in maintenance mode

**Emergency Solutions:**

1. **Check error logs:**
   ```bash
   tail -f /var/log/nginx/error.log
   tail -f /var/log/php/error.log
   ddev logs
   ```

2. **Enable maintenance mode:**
   ```bash
   ddev drush state:set system.maintenance_mode 1
   ```

3. **Quick fixes:**
   ```bash
   # Clear all caches
   ddev drush cr
   
   # Check database connection
   ddev drush status
   
   # Restore from backup if needed
   ddev import-db --src=backup.sql
   ```

### Configuration Issues After Deployment

**Symptoms:**
- New features not appearing
- Permission errors
- Module conflicts

**Solutions:**

1. **Force configuration import:**
   ```bash
   ddev drush cim --skip-modules=problematic_module
   
   # Or import specific config
   ddev drush config:import --partial --source=config/sync
   ```

2. **Check module status:**
   ```bash
   ddev drush pml | grep -i employee
   
   # Enable missing modules
   ddev drush en module_name
   ```

3. **Rebuild permissions:**
   ```bash
   ddev drush rebuild
   
   # Update database
   ddev drush updb
   ```

## Security Issues

### Suspicious Activity

**Signs to watch for:**
- Unusual login attempts
- Modified files outside deployment
- Unexpected database changes
- High server resource usage

**Response Steps:**

1. **Immediate actions:**
   ```bash
   # Enable maintenance mode
   ddev drush state:set system.maintenance_mode 1
   
   # Check recent logins
   ddev drush watchdog:show --type=user
   
   # Review file changes
   find . -type f -mtime -1 | grep -v "sites/default/files"
   ```

2. **Investigation:**
   ```bash
   # Check access logs
   grep -i "POST" /var/log/nginx/access.log | tail -100
   
   # Review database changes
   ddev mysql -e "SELECT * FROM watchdog WHERE type='system' ORDER BY timestamp DESC LIMIT 20;"
   ```

3. **Recovery:**
   ```bash
   # Update all passwords
   ddev drush user:password admin "new_secure_password"
   
   # Update Drupal core and modules
   ddev composer update drupal/core-recommended --with-dependencies
   ddev drush updb
   ```

## Getting Help

### Debug Information to Collect

When asking for help, provide:

1. **System information:**
   ```bash
   ddev describe
   ddev drush status
   php -v
   node --version
   ```

2. **Error details:**
   ```bash
   ddev logs | tail -50
   ddev drush watchdog:show --severity=Error --count=10
   ```

3. **Configuration status:**
   ```bash
   ddev drush config:status
   ddev drush pml | grep -i payroll
   ```

### Log Locations

**DDEV Environment:**
- Container logs: `ddev logs`
- Drupal logs: `ddev drush watchdog:show`
- PHP errors: Check container logs

**Production Environment:**
- Web server: `/var/log/nginx/error.log`
- PHP: `/var/log/php/error.log`  
- Drupal: Database watchdog table or syslog

### Common Commands for Quick Diagnosis

```bash
# Check overall system health
ddev describe
ddev drush status

# Clear all caches
ddev drush cr

# Check for configuration issues
ddev drush config:status

# Check for pending updates
ddev drush updatedb --dry-run

# View recent errors
ddev drush watchdog:show --severity=Error --count=5

# Test database connection
ddev mysql -e "SELECT COUNT(*) FROM users;"

# Check file permissions
ls -la web/sites/default/
```

### When to Escalate

Contact senior developers or system administrators when:

- Data corruption is suspected
- Security breach indicators
- System-wide performance degradation
- Database corruption
- Issues affecting multiple environments
- Critical functionality completely broken

For additional guidance, refer to:
- [Development Workflow](Development-Workflow.md) for process issues
- [Deployment Guide](Deployment-Guide.md) for production problems
- [Frontend Development Guide](Frontend-Development-Guide.md) for UI/asset issues