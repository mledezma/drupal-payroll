# Development Workflow

This guide outlines the development workflow and contribution process for the Drupal 11 Payroll & Employee Management System.

## Git Workflow

### Branch Strategy

We use a **feature branch workflow** based on the `master` branch:

- `master` - Main development branch, always deployable
- `feature/[description]` - Feature development branches
- `bugfix/[description]` - Bug fix branches
- `hotfix/[description]` - Critical production fixes

### Naming Conventions

**Branch Names:**
```
feature/employee-search-functionality
feature/salary-reporting-dashboard
bugfix/employee-photo-upload-issue
hotfix/critical-security-patch
```

**Commit Messages:**
Follow conventional commit format:
```
feat: add employee search functionality
fix: resolve photo upload validation error
docs: update API documentation
style: improve employee card component styling
refactor: optimize employee query performance
test: add unit tests for employee model
```

## Development Process

### 1. Setting Up New Work

```bash
# Start from master branch
git checkout master
git pull origin master

# Create feature branch
git checkout -b feature/employee-advanced-search

# Start DDEV environment
ddev start
ddev composer install
ddev npm install
```

### 2. Development Cycle

#### Backend Changes (Drupal)

1. **Configuration Management**
   ```bash
   # Export current configuration
   ddev drush cex
   
   # Make changes via UI or code
   # ...
   
   # Export new configuration
   ddev drush cex
   
   # Clear caches
   ddev drush cr
   ```

2. **Custom Module Development**
   ```bash
   # Generate module scaffolding
   ddev drush generate module
   
   # Create custom functionality
   # Edit files in web/modules/custom/
   
   # Clear caches after changes
   ddev drush cr
   ```

3. **Database Updates**
   ```bash
   # If schema changes are made
   ddev drush updb
   
   # Test configuration import
   ddev drush cim
   ```

#### Frontend Changes

1. **Component Development**
   ```bash
   # Start development build with watch
   ddev npm run dev
   
   # Create new component
   mkdir web/themes/custom/payroll_theme/components/new-component
   
   # Develop component files
   # - component.yml
   # - component.twig  
   # - component.example.scss
   # - component.example.js
   ```

2. **Asset Compilation**
   ```bash
   # Development build
   ddev npm run dev
   
   # Production build
   ddev npm run build
   
   # Lint code
   ddev npm run lint
   ```

3. **Clear Drupal Caches**
   ```bash
   # Clear theme registry after template changes
   ddev drush cc theme-registry
   
   # Clear all caches
   ddev drush cr
   ```

### 3. Testing Your Changes

#### Manual Testing Checklist

**Functionality Testing:**
- [ ] Feature works as expected in primary use case
- [ ] Edge cases handled appropriately  
- [ ] Error states display helpful messages
- [ ] Form validation works correctly

**Cross-browser Testing:**
- [ ] Chrome (latest)
- [ ] Firefox (latest)
- [ ] Safari (latest)
- [ ] Edge (latest)

**Responsive Testing:**
- [ ] Mobile (320px-768px)
- [ ] Tablet (768px-1024px)
- [ ] Desktop (1024px+)

**Accessibility Testing:**
- [ ] Keyboard navigation works
- [ ] Screen reader compatibility
- [ ] Color contrast compliance
- [ ] Focus indicators visible

**Performance Testing:**
- [ ] Lighthouse performance audit
- [ ] Core Web Vitals check
- [ ] Asset size optimization

#### Automated Testing

```bash
# Run PHP tests (if available)
ddev exec vendor/bin/phpunit

# Run JavaScript tests
ddev npm test

# Run CSS/JS linting
ddev npm run lint
```

### 4. Code Review Process

#### Self-Review Checklist

Before requesting review:

**Code Quality:**
- [ ] Follows Drupal coding standards
- [ ] No commented-out code or debug statements
- [ ] Functions and variables have clear names
- [ ] Complex logic is documented

**Security:**
- [ ] User input is properly sanitized
- [ ] No SQL injection vulnerabilities
- [ ] Proper access control checks
- [ ] Sensitive data is protected

**Performance:**
- [ ] Database queries are optimized
- [ ] Images are optimized and responsive
- [ ] CSS/JS assets are minimized
- [ ] Caching is implemented where appropriate

#### Preparing Pull Request

1. **Clean up commit history** (if needed):
   ```bash
   # Interactive rebase to squash commits
   git rebase -i master
   ```

2. **Update from master**:
   ```bash
   git checkout master
   git pull origin master
   git checkout feature/your-branch
   git rebase master
   ```

3. **Final testing**:
   ```bash
   ddev drush cim  # Test configuration import
   ddev drush cr   # Clear caches
   ddev npm run build  # Build production assets
   ```

4. **Create pull request** with:
   - Clear title describing the change
   - Detailed description of what was implemented
   - Screenshots for UI changes
   - Testing instructions
   - Related issue numbers

### 5. Pull Request Template

```markdown
## Summary
Brief description of changes made.

## Changes Made
- [ ] Added employee search functionality
- [ ] Updated employee card component
- [ ] Added new database fields
- [ ] Updated configuration exports

## Testing
- Manual testing completed on Chrome, Firefox, Safari
- Responsive testing on mobile and desktop
- Accessibility testing with keyboard navigation
- Performance testing shows no regression

## Screenshots
[Include screenshots for UI changes]

## Related Issues
Fixes #123
Related to #456

## Checklist
- [ ] Code follows project standards
- [ ] Tests added/updated as needed
- [ ] Documentation updated
- [ ] Configuration exported
- [ ] Assets compiled for production
```

## Code Standards

### PHP/Drupal Standards

Follow [Drupal Coding Standards](https://www.drupal.org/docs/develop/standards):

```php
<?php

namespace Drupal\payroll_employee\Controller;

use Drupal\Core\Controller\ControllerBase;
use Drupal\Core\Database\Connection;
use Symfony\Component\DependencyInjection\ContainerInterface;

/**
 * Employee controller for payroll system.
 */
class EmployeeController extends ControllerBase {

  /**
   * The database connection.
   *
   * @var \Drupal\Core\Database\Connection
   */
  protected $database;

  /**
   * Constructs a new EmployeeController.
   *
   * @param \Drupal\Core\Database\Connection $database
   *   The database connection.
   */
  public function __construct(Connection $database) {
    $this->database = $database;
  }

  /**
   * {@inheritdoc}
   */
  public static function create(ContainerInterface $container) {
    return new static(
      $container->get('database')
    );
  }

}
```

### CSS/SCSS Standards

```scss
// Use BEM methodology for custom classes
.employee-card {
  @apply bg-white rounded-lg shadow-md p-6;
  
  &__header {
    @apply flex items-center justify-between mb-4;
  }
  
  &__title {
    @apply text-lg font-semibold text-gray-900;
  }
  
  &--highlighted {
    @apply border-l-4 border-blue-500;
  }
}

// Mobile-first responsive design
.employee-grid {
  @apply grid grid-cols-1 gap-4;
  
  @screen md {
    @apply grid-cols-2;
  }
  
  @screen lg {
    @apply grid-cols-3;
  }
}
```

### JavaScript Standards

```javascript
/**
 * Employee search functionality.
 */
class EmployeeSearch {
  constructor(element) {
    this.element = element;
    this.searchInput = element.querySelector('.search-input');
    this.resultsContainer = element.querySelector('.search-results');
    this.init();
  }

  init() {
    this.bindEvents();
    this.setupDebouncing();
  }

  bindEvents() {
    this.searchInput.addEventListener('input', (e) => {
      this.handleSearch(e.target.value);
    });
  }

  async handleSearch(query) {
    if (query.length < 3) {
      this.clearResults();
      return;
    }

    try {
      const results = await this.fetchResults(query);
      this.displayResults(results);
    } catch (error) {
      console.error('Search failed:', error);
      this.displayError('Search failed. Please try again.');
    }
  }
}
```

## Configuration Management

### Workflow for Configuration Changes

1. **Make changes via Drupal UI**
2. **Export configuration**:
   ```bash
   ddev drush cex
   ```
3. **Commit configuration files**:
   ```bash
   git add config/
   git commit -m "feat: add employee search view configuration"
   ```
4. **Test configuration import**:
   ```bash
   ddev drush cim
   ```

### Configuration Files to Track

Always commit these configuration changes:
- `config/sync/` - All exported configuration
- Custom module configurations
- View configurations
- Field configurations
- Content type definitions

## Deployment Process

### Pre-deployment Checklist

- [ ] All tests passing
- [ ] Configuration exported and committed
- [ ] Assets built for production
- [ ] Database updates tested
- [ ] Performance impact assessed

### Deployment Commands

```bash
# Update codebase
git pull origin master

# Install/update dependencies  
ddev composer install --no-dev
ddev npm ci

# Build production assets
ddev npm run build

# Run database updates
ddev drush updb

# Import configuration
ddev drush cim

# Clear caches
ddev drush cr

# Enable production settings
# - CSS/JS aggregation
# - Disable development modules
# - Configure caching
```

## Troubleshooting Development Issues

### Common Issues and Solutions

**Configuration Import Fails:**
```bash
# Check for configuration conflicts
ddev drush config:status

# Force import specific configs
ddev drush cim --partial

# Reset to clean state if needed
ddev drush sql:drop
ddev drush site:install
```

**Frontend Build Issues:**
```bash
# Clear npm cache
ddev npm cache clean --force

# Reinstall dependencies
rm -rf node_modules package-lock.json
ddev npm install

# Check for syntax errors
ddev npm run lint
```

**Performance Issues:**
```bash
# Check database queries
ddev drush watchdog:show

# Profile with Xdebug
ddev xdebug on

# Clear all caches
ddev drush cr
```

## Getting Help

### Documentation Resources

- [Drupal 11 Documentation](https://www.drupal.org/docs/drupal-apis)
- [DDEV Documentation](https://ddev.readthedocs.io/)
- [TailwindCSS Documentation](https://tailwindcss.com/docs)

### Internal Resources

- Check the [Troubleshooting](Troubleshooting.md) guide
- Review [Frontend Development Guide](Frontend-Development-Guide.md)
- Consult [Application Features](Application-Features.md) for requirements

### When to Ask for Help

- Stuck on an issue for more than 2 hours
- Unsure about architectural decisions
- Need clarification on requirements
- Performance issues that can't be resolved