# 🧾 Drupal 11 Payroll & Employee Management System

A comprehensive web application built on Drupal 11 to manage employee records and payroll-related information. This system serves as a centralized database for storing, organizing, and retrieving employee data efficiently, enabling easy access when offering employee services to contractors and enterprises.

## ✨ Features

### Core Functionality
- **Employee Management** - Store detailed employee profiles with personal and payroll information
- **Data Organization** - Custom content type with structured fields for employee data
- **Search & Filtering** - Views-based employee listings with sorting and filtering capabilities
- **User Access Control** - Role-based permissions for different user types
- **Clean UI** - User-friendly interface designed for non-technical users

### Employee Data Structure
- Personal Information: Full Name, Date of Birth, Identification Number
- Contact Details: Email Address, Phone Number
- Work Information: Work Permit Status/Details, Profile Photo
- Payroll Data: Salary Paid (actual), Contract Salary (agreed)

## 🛠️ Technology Stack

### Backend
- **Drupal 11.3.8** - Content management framework
- **PHP 8.4** - Server-side programming
- **MariaDB 11.8** - Database management
- **Composer** - Dependency management

### Frontend
- **TailwindCSS** - Utility-first CSS framework
- **SCSS/Sass** - CSS preprocessing
- **Vanilla JavaScript** - Modern ES6+ without heavy frameworks
- **Webpack** - Module bundling and asset compilation
- **Single Directory Components (SDC)** - Component-based architecture

### Development Environment
- **DDEV** - Local development environment
- **Git** - Version control
- **Drush** - Drupal command-line tool

## 🚀 Getting Started

### Prerequisites
- [DDEV](https://docs.ddev.com/) installed on your system
- [Composer](https://getcomposer.org/) (included with DDEV)
- [Git](https://git-scm.com/) for version control

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/mledezma/drupal-payroll.git
   cd drupal-payroll
   ```

2. **Start DDEV environment**
   ```bash
   ddev start
   ```

3. **Install dependencies**
   ```bash
   ddev composer install
   ```

4. **Access the application**
   - **Website**: https://drupal-payroll.ddev.site
   - **Admin Login**: Run `ddev drush uli` to get a one-time login link

### Quick Setup Commands
```bash
# Start development environment
ddev start

# Install Drush (if not already installed)
ddev composer require drush/drush

# Install Drupal (if needed)
ddev drush site:install --account-name=admin --account-pass=admin -y

# Generate admin login link
ddev drush uli --no-browser
```

## 📁 Project Structure

```
/
├── web/                      # Document root
│   ├── themes/custom/        # Custom themes with SDC components
│   ├── modules/custom/       # Custom modules
│   ├── sites/default/        # Site configuration
│   └── core/                 # Drupal core (managed by Composer)
├── .ddev/                    # DDEV configuration
├── .agents/skills/           # Custom Claude Code skills
├── vendor/                   # Composer dependencies
├── composer.json             # PHP dependencies
├── CLAUDE.md                 # Claude Code configuration
├── application-context.md    # Project specification
└── README.md                 # This file
```

## 🔧 Development Workflow

### Daily Development Commands

**DDEV Environment Management:**
```bash
ddev start                    # Start containers
ddev stop                     # Stop containers
ddev restart                  # Restart containers
ddev ssh                      # SSH into web container
```

**Drupal Management:**
```bash
ddev drush cr                 # Clear all caches
ddev drush cex                # Export configuration
ddev drush cim                # Import configuration
ddev drush updb               # Run database updates
ddev drush status             # Show Drupal status
```

**Frontend Development:**
```bash
ddev npm install              # Install Node.js dependencies
ddev npm run build            # Build production assets
ddev npm run dev              # Development build with watch
```

### Content Type Development
The system uses a custom **Employee** content type with the following fields:
- Text fields: Full Name, Identification Number, Phone Number
- Date field: Date of Birth
- Email field: Email Address
- List field: Work Permit Status (Valid, Expired, Pending, Not Required)
- Image field: Profile Photo
- Decimal fields: Salary Paid, Contract Salary

## 👥 User Roles & Permissions

### Admin
- Full access to all system features
- Can create, edit, delete, and configure all content
- Manages user accounts and permissions

### Manager
- Can create and edit employee records
- Cannot access system configuration
- Has access to all employee data

### Viewer
- Read-only access to employee data
- Cannot create or modify records
- Limited to viewing and searching employees

## 🎯 Core Features Implementation

### Employee Management
- Custom content type with structured fields
- Form validation and required field enforcement
- Image upload handling for profile photos
- Salary field formatting with currency symbols

### Data Views & Filtering
- **Employee Listing**: Sortable table view of all employees
- **Search Functionality**: Search by name, ID, or email
- **Advanced Filtering**: Filter by salary range, work permit status
- **Sorting Options**: Sort by name, date of birth, or salary amounts

### User Interface
- Clean, responsive admin interface
- Logical field grouping and clear labels
- Mobile-friendly design with TailwindCSS
- Accessible navigation for all user types

## 🚧 Future Enhancements

### Phase 2 Features (Planned)
- **Payroll Calculations**: Automated tax calculations and deductions
- **Data Export**: CSV and Excel export functionality
- **API Integration**: REST or GraphQL endpoints
- **Document Management**: Contract and permit file uploads
- **Employee Status**: Active/inactive employee tracking
- **Reporting**: Advanced reporting and analytics

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📝 License

This project is licensed under the GPL-2.0-or-later License - see the [LICENSE.txt](LICENSE.txt) file for details.

## 🆘 Support & Documentation

### Development Help
- Run `/help` in Claude Code for development assistance
- Use the custom `drupal-expert` skill for Drupal-specific guidance
- Use the `drupal-frontend-dev` skill for frontend development

### Useful Links
- [Drupal 11 Documentation](https://www.drupal.org/docs/user_guide/en/index.html)
- [DDEV Documentation](https://docs.ddev.com/)
- [TailwindCSS Documentation](https://tailwindcss.com/docs)
- [Single Directory Components](https://www.drupal.org/docs/develop/theming-drupal/using-single-directory-components)

## 📊 Project Status

- ✅ Initial Drupal 11 setup complete
- ✅ DDEV development environment configured
- ✅ Custom skills and documentation in place
- ⏳ Employee content type implementation
- ⏳ User roles and permissions setup
- ⏳ Views and filtering implementation
- ⏳ Frontend theme development

---

**Built with ❤️ using Drupal 11 and modern frontend technologies**