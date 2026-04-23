# Drupal 11 Payroll & Employee Management System

Welcome to the **Drupal 11 Payroll & Employee Management System** documentation wiki. This application serves as a centralized database to store, organize, and retrieve employee data efficiently, enabling easy access when offering employee services to contractors and enterprises.

## 🎯 Business Purpose

The application serves as a centralized employee database to help present employee profiles to contractors and enterprises, supporting business decisions with organized employee data.

## 🚀 Core Functionality

- **Employee Management** - Store detailed employee profiles with personal and payroll information
- **Data Organization** - Custom content type with structured fields for employee data  
- **Search & Filtering** - Views-based employee listings with sorting and filtering capabilities
- **User Access Control** - Role-based permissions for Admin, Manager, and Viewer roles
- **Clean UI** - User-friendly interface for non-technical users

## 🛠️ Technology Stack

- **Backend**: Drupal 11 with PHP 8.4
- **Database**: MariaDB 11.8
- **Frontend**: TailwindCSS, SCSS, Vanilla JavaScript, Webpack
- **Components**: Single Directory Components (SDC)
- **Development**: DDEV local environment

## 📚 Quick Navigation

- [Development Environment Setup](Development-Environment-Setup.md)
- [Frontend Development Guide](Frontend-Development-Guide.md) 
- [Application Features & Requirements](Application-Features.md)
- [Development Workflow](Development-Workflow.md)
- [Deployment Guide](Deployment-Guide.md)
- [Troubleshooting](Troubleshooting.md)

## 🏃‍♂️ Quick Start

1. **Clone the repository**
   ```bash
   git clone [repository-url]
   cd drupal-payroll
   ```

2. **Start DDEV environment**
   ```bash
   ddev start
   ddev composer install
   ddev npm install
   ```

3. **Access the application**
   - Website: `https://drupal-payroll.ddev.site`
   - Admin: `ddev drush uli` (generates one-time login)

## 👥 User Roles

- **Admin**: Full access (create, edit, delete, configure)
- **Manager**: Can create and edit employee records  
- **Viewer**: Read-only access to employee data

## 🎨 Frontend Architecture

This project focuses on modern frontend development using:
- **TailwindCSS** for utility-first styling
- **Single Directory Components (SDC)** for component architecture
- **Webpack** for asset compilation and optimization
- **Performance-first approach** targeting Core Web Vitals

## 📞 Support

For development questions or issues, please check the [Troubleshooting](Troubleshooting.md) guide or create an issue in the repository.