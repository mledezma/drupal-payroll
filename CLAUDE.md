# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Drupal 11 Payroll & Employee Management System** set up with DDEV for local development. The application serves as a centralized database to store, organize, and retrieve employee data efficiently, enabling easy access when offering employee services to contractors and enterprises.

**Business Purpose:**
The application serves as a centralized employee database to help present employee profiles to contractors and enterprises, supporting business decisions with organized employee data.

**Core Functionality:**
- **Employee Management** - Store detailed employee profiles with personal and payroll information
- **Data Organization** - Custom content type with structured fields for employee data
- **Search & Filtering** - Views-based employee listings with sorting and filtering capabilities
- **User Access Control** - Role-based permissions for Admin, Manager, and Viewer roles
- **Clean UI** - User-friendly interface for non-technical users

**Employee Data Structure:**
<!-- - Full Name, Date of Birth, Identification Number
- Work Permit Status/Details, Email, Phone Number
- Profile Photo, Salary Paid (actual), Contract Salary (agreed) -->

The project focuses on modern frontend development using TailwindCSS, SCSS, vanilla JavaScript, Webpack builds, and Single Directory Components (SDC) with performance optimization.

## Development Environment

**DDEV Configuration:**
- Project type: `drupal11`
- PHP version: `8.4`
- Web server: `nginx-fpm`
- Database: MariaDB `11.4`
- Document root: `web/`
- Project URL: `https://drupal-payroll.ddev.site`

## Frontend Technology Stack

**Core Technologies:**
- **TailwindCSS** - Utility-first CSS framework for rapid UI development
- **SCSS/Sass** - CSS preprocessing for advanced styling and theming
- **Vanilla JavaScript** - Modern ES6+ JavaScript without heavy frameworks
- **Webpack** - Module bundling, asset compilation, and optimization
- **Single Directory Components (SDC)** - Drupal's component-based architecture

## Common Development Commands

**DDEV Environment:**
```bash
ddev start                    # Start the development environment
ddev stop                     # Stop containers
ddev restart                  # Restart containers
ddev ssh                      # SSH into the web container
ddev describe                 # Show project URLs and status
```

**Drupal Management (via Drush):**
```bash
ddev drush cr                 # Clear all caches
ddev drush cex                # Export configuration
ddev drush cim                # Import configuration
ddev drush updb               # Run database updates
ddev drush uli                # Generate one-time login link
ddev export-db --file=backup_$(date +%Y%m%d).sql.gz   # Database backup with timestamp
ddev drush status             # Show Drupal status
```

**Frontend Development:**
```bash
ddev npm install              # Install Node.js dependencies
ddev npm run build            # Build production assets
ddev npm run dev              # Development build with watch mode
ddev npm run lint             # Run CSS/JS linting
```

**Composer Dependencies:**
```bash
ddev composer install        # Install dependencies
ddev composer require drupal/module_name  # Add new module
ddev composer update          # Update all dependencies
```

## Project Structure

```
/
├── web/                      # Document root
│   ├── themes/custom/        # Custom themes with SDC components
│   ├── modules/custom/       # Custom modules
│   └── sites/default/        # Site-specific configuration
├── config/                   # Configuration Management files (exported configs)
├── .ddev/                    # DDEV configuration
├── vendor/                   # Composer dependencies
└── composer.json             # PHP dependency definitions
```

## Frontend Development Architecture

**Single Directory Components Structure:**
```
/web/themes/custom/theme_name/components/
├── card/
│   ├── card.component.yml    # Component definition & props
│   ├── card.twig            # Template with TailwindCSS
│   ├── card.scss            # Custom SCSS styles
│   └── card.js              # JavaScript behavior
└── navigation/
    ├── navigation.component.yml
    ├── navigation.twig
    ├── navigation.scss
    └── navigation.js
```

**Asset Management:**
- Source files in `src/` directory (SCSS, JS, images)
- Compiled assets output to `assets/` directory
- Webpack handles compilation, optimization, and cache busting
- TailwindCSS integrated with PostCSS processing

## Development Workflow

**Component Development:**
1. Create SDC component directory structure
2. Define component schema in `.component.yml`
3. Build semantic HTML template with TailwindCSS utilities
4. Add custom SCSS for styles beyond Tailwind
5. Implement JavaScript behavior if needed
6. Test responsiveness and accessibility

**Performance-First Approach:**
- Always optimize for Core Web Vitals (LCP, FID, CLS)
- Implement critical CSS for above-the-fold content
- Use lazy loading for images and below-the-fold content
- Code split JavaScript for better caching
- Monitor performance with Chrome DevTools and Lighthouse

## Available Skills

This project has custom skills available for specialized guidance:

- **drupal-expert** - General Drupal development expertise
  - Research-first approach to finding existing solutions
  - Best practices for module and theme development
  - Configuration management workflows

- **drupal-frontend-dev** - Frontend-specific Drupal development
  - TailwindCSS integration and optimization
  - Single Directory Components (SDC) development
  - Webpack build configuration and asset management
  - Performance optimization strategies
  - Modern JavaScript patterns in Drupal context
  - Responsive design and accessibility implementation

## Application Development Requirements

**Content Modeling:**
- **Employee Content Type** with fields:
  - Text: Full Name, Identification Number, Phone Number
  - Date: Date of Birth, Entry Day
  - Email: Email Address
  - List: Work Permit Status (Valid, Expired, Pending, Not Required)
  - Image: Profile Photo (stored in public://employee-photos)
  - Decimal: Salary Paid, Contract Salary (with $ prefix, 2 decimal places)
  - Taxonomy Reference: Contract Company (references Companies vocabulary)
  - Date Range: Contract Duration (contract start and end dates)
  - File: Contract Document (stored in date-organized directories)

- **Companies Taxonomy Vocabulary** for managing company entities that employees can be contracted to

**Data Management:**
- Use Drupal Views for employee listings (sortable, filterable tables)
- Enable searching by name, ID, or email
- Support sorting by salary, name, or date of birth
- Multiple view displays: full list, filtered lists (salary range, work permit status)

**User Roles & Permissions:**
- **Admin**: Full access (create, edit, delete, configure)
- **Manager**: Can create and edit employee records

**Technical Standards:**
- Follow Drupal best practices for content modeling
- Use configuration management (config export/import)
- Leverage core modules: Views, Field UI, Image, File, Taxonomy, Datetime Range
- Create reusable, clean structures
- Utilize taxonomy for structured data relationships

**UI/UX Requirements:**
- Clean admin interface for non-technical users
- Clear field labels and logical grouping
- Simple navigation between employee records
- Responsive design for various screen sizes

## Development Guidelines

**Performance Priorities:**
- Target Core Web Vitals scores (LCP < 2.5s, FID < 100ms, CLS < 0.1)
- Implement critical CSS inline delivery
- Use efficient asset loading strategies (preload, prefetch)
- Optimize images with modern formats and lazy loading

**Code Quality:**
- Follow Drupal coding standards for PHP
- Use ESLint and Stylelint for frontend code quality
- Implement semantic HTML with proper ARIA attributes
- Write mobile-first responsive CSS with TailwindCSS

**Testing Requirements:**
- Test components across target browsers
- Validate accessibility with screen readers
- Check responsive behavior at all breakpoints
- Performance audit with Lighthouse before deployment

## Development Notes

- Xdebug disabled by default for performance - enable with `ddev xdebug`
- Twig debugging should be enabled in development
- CSS/JS aggregation disabled during development
- Use `ddev logs` to check container logs when debugging issues
- Always clear Drupal cache after frontend changes affecting render arrays
