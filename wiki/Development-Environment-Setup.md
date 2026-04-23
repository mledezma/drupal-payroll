# Development Environment Setup

This guide will help you set up your local development environment for the Drupal 11 Payroll & Employee Management System.

## Prerequisites

Before starting, ensure you have the following installed:

- [DDEV](https://ddev.readthedocs.io/en/stable/#installation) (latest version)
- [Docker Desktop](https://www.docker.com/products/docker-desktop) or compatible Docker engine
- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/) (LTS version recommended)

## DDEV Configuration

The project uses DDEV with the following configuration:

- **Project type**: `drupal11`
- **PHP version**: `8.4`
- **Web server**: `nginx-fpm`
- **Database**: MariaDB `11.8`
- **Document root**: `web/`
- **Project URL**: `https://drupal-payroll.ddev.site`

## Initial Setup

### 1. Clone the Repository

```bash
git clone [repository-url]
cd drupal-payroll
```

### 2. Start DDEV Environment

```bash
# Start the development environment
ddev start

# Install PHP dependencies
ddev composer install

# Install Node.js dependencies
ddev npm install
```

### 3. Database Setup

If starting with a fresh database:

```bash
# Import configuration
ddev drush cim

# Run database updates
ddev drush updb

# Clear all caches
ddev drush cr
```

### 4. Build Frontend Assets

```bash
# Development build with watch mode
ddev npm run dev

# Or for production build
ddev npm run build
```

## Common DDEV Commands

### Environment Management
```bash
ddev start                    # Start the development environment
ddev stop                     # Stop containers
ddev restart                  # Restart containers
ddev ssh                      # SSH into the web container
ddev describe                 # Show project URLs and status
```

### Drupal Management (via Drush)
```bash
ddev drush cr                 # Clear all caches
ddev drush cex                # Export configuration
ddev drush cim                # Import configuration
ddev drush updb               # Run database updates
ddev drush uli                # Generate one-time login link
ddev drush sql:dump > backup.sql   # Database backup
ddev drush status             # Show Drupal status
```

### Frontend Development
```bash
ddev npm install              # Install Node.js dependencies
ddev npm run build            # Build production assets
ddev npm run dev              # Development build with watch mode
ddev npm run lint             # Run CSS/JS linting
```

### Composer Dependencies
```bash
ddev composer install        # Install dependencies
ddev composer require drupal/module_name  # Add new module
ddev composer update          # Update all dependencies
```

## Development Tools

### Xdebug

Xdebug is disabled by default for performance. Enable it when needed:

```bash
# Enable Xdebug
ddev xdebug

# Disable Xdebug
ddev xdebug off
```

### Debugging Configuration

For development, ensure these settings are enabled:

- Twig debugging should be enabled
- CSS/JS aggregation should be disabled
- Error display should be enabled

### Logs

Check container logs for debugging:

```bash
ddev logs        # Show all logs
ddev logs web    # Show web server logs
ddev logs db     # Show database logs
```

## Accessing the Application

Once setup is complete:

- **Website**: `https://drupal-payroll.ddev.site`
- **Database**: Available at `127.0.0.1:32768` (port may vary)
- **MailHog**: `https://drupal-payroll.ddev.site:8026` (email testing)

### Admin Access

Generate a one-time login link:

```bash
ddev drush uli
```

## Project Structure

```
/
├── web/                      # Document root
│   ├── themes/custom/        # Custom themes with SDC components
│   ├── modules/custom/       # Custom modules
│   └── sites/default/        # Site-specific configuration
├── .ddev/                    # DDEV configuration
├── vendor/                   # Composer dependencies
├── node_modules/             # Node.js dependencies
├── src/                      # Frontend source files
└── assets/                   # Compiled frontend assets
```

## Environment Variables

Set any required environment variables in `.ddev/config.yaml` or create a `.ddev/config.local.yaml` file for local overrides.

## Next Steps

Once your environment is running:

1. Review the [Application Features](Application-Features.md) to understand the data structure
2. Check out the [Frontend Development Guide](Frontend-Development-Guide.md) for component development
3. Follow the [Development Workflow](Development-Workflow.md) for contribution guidelines

## Troubleshooting

If you encounter issues, check the [Troubleshooting](Troubleshooting.md) guide or run:

```bash
ddev describe    # Check project status
ddev logs        # Review error logs
```