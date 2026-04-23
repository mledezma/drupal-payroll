# Application Features & Requirements

This document outlines the core features and technical requirements for the Drupal 11 Payroll & Employee Management System.

## Core Functionality Overview

The application serves as a centralized employee database designed to:
- Store comprehensive employee profiles
- Enable efficient data organization and retrieval
- Support contractor and enterprise services
- Provide role-based access to employee information

## Employee Data Structure

### Employee Content Type

The system uses a custom **Employee** content type with the following structured fields:

#### Text Fields
- **Full Name** - Employee's complete legal name
- **Identification Number** - Unique employee identifier (SSN, employee ID, etc.)
- **Phone Number** - Primary contact number

#### Specialized Fields
- **Date of Birth** - Date field for age calculations and compliance
- **Email Address** - Email field with validation for communication
- **Profile Photo** - Image field stored in `public://employee-photos`

#### Financial Fields
- **Salary Paid** - Decimal field with $ prefix, 2 decimal places for actual salary
- **Contract Salary** - Decimal field with $ prefix, 2 decimal places for contracted amount

#### Status Fields
- **Work Permit Status** - List field with options:
  - Valid
  - Expired  
  - Pending
  - Not Required

### Field Configuration Requirements

```yaml
# Example field configuration
field_full_name:
  type: string
  label: "Full Name"
  required: true
  max_length: 255

field_identification_number:
  type: string
  label: "Identification Number"
  required: true
  max_length: 50
  
field_email:
  type: email
  label: "Email Address"
  required: true

field_salary_paid:
  type: decimal
  label: "Salary Paid"
  precision: 10
  scale: 2
  prefix: "$"

field_work_permit_status:
  type: list_string
  label: "Work Permit Status"
  allowed_values:
    valid: "Valid"
    expired: "Expired"
    pending: "Pending"
    not_required: "Not Required"
```

## Data Management Features

### Employee Listings (Views)

The system uses Drupal Views to create multiple employee listing displays:

#### Full Employee List
- **Sortable columns**: Name, ID, Salary, Date of Birth
- **Filterable by**: Work permit status, salary range
- **Search functionality**: Name, ID, or email search
- **Pagination**: Configurable items per page

#### Filtered Views
- **By Salary Range**: High earners, entry level, etc.
- **By Work Permit Status**: Valid permits, expired permits, pending
- **Recent Additions**: Newest employee records first

#### View Display Options
```yaml
employee_list:
  display_modes:
    - table: Sortable data table
    - card: Grid layout with employee cards
    - list: Compact list view
  
  filters:
    - work_permit_status
    - salary_range
    - date_range
    
  sorting:
    - name (default)
    - salary_paid
    - date_of_birth
    - created_date
```

### Search Capabilities

- **Global search** across name, identification number, and email
- **Advanced filters** for refined results
- **Quick filters** for common searches (e.g., "Valid work permits")
- **Saved searches** for frequently used filter combinations

## User Roles & Permissions

### Role Definitions

#### Admin Role
- **Full system access**
- Create, edit, delete employee records
- Manage user accounts and permissions
- Configure views and content types
- Access all administrative functions
- Export/import employee data

#### Manager Role
- **Employee data management**
- Create and edit employee records
- View all employee information
- Access to reporting features
- Cannot delete records or manage users
- Limited administrative access

#### Viewer Role
- **Read-only access**
- View employee listings and profiles
- Use search and filtering features
- Generate reports (view only)
- Cannot modify any data
- No administrative access

### Permission Matrix

| Action | Admin | Manager | Viewer |
|--------|-------|---------|--------|
| Create Employee | ✅ | ✅ | ❌ |
| Edit Employee | ✅ | ✅ | ❌ |
| Delete Employee | ✅ | ❌ | ❌ |
| View Employee | ✅ | ✅ | ✅ |
| Manage Users | ✅ | ❌ | ❌ |
| Configure System | ✅ | ❌ | ❌ |
| Export Data | ✅ | ✅ | ❌ |

## User Interface Requirements

### Design Principles
- **Clean, professional appearance** suitable for business use
- **Intuitive navigation** for non-technical users
- **Responsive design** for various screen sizes
- **Accessibility compliance** (WCAG AA standards)

### Key UI Components

#### Employee Profile Display
```twig
{# Employee profile card component #}
<div class="employee-profile bg-white rounded-lg shadow-md p-6">
  <div class="flex items-start space-x-4">
    <img src="{{ profile_photo }}" class="w-16 h-16 rounded-full object-cover" alt="{{ full_name }}">
    <div class="flex-1">
      <h2 class="text-xl font-semibold text-gray-900">{{ full_name }}</h2>
      <p class="text-gray-600">ID: {{ identification_number }}</p>
      <p class="text-gray-600">{{ email }}</p>
    </div>
    <div class="text-right">
      <span class="work-permit-badge work-permit-badge--{{ work_permit_status|lower }}">
        {{ work_permit_status }}
      </span>
    </div>
  </div>
  
  <div class="mt-4 grid grid-cols-2 gap-4 text-sm">
    <div>
      <span class="font-medium">Phone:</span> {{ phone }}
    </div>
    <div>
      <span class="font-medium">Date of Birth:</span> {{ date_of_birth|date('M j, Y') }}
    </div>
    <div>
      <span class="font-medium">Salary Paid:</span> ${{ salary_paid|number_format(2) }}
    </div>
    <div>
      <span class="font-medium">Contract Salary:</span> ${{ contract_salary|number_format(2) }}
    </div>
  </div>
</div>
```

#### Employee Listing Table
- **Sortable headers** with visual indicators
- **Inline editing** for quick updates (Manager+ roles)
- **Bulk actions** for multiple employee operations
- **Export options** (CSV, PDF)

### Form Design
- **Logical field grouping** (Personal Info, Contact, Employment)
- **Real-time validation** with helpful error messages
- **Progress indication** for multi-step forms
- **Auto-save** functionality for long forms

## Reporting Features

### Standard Reports
- **Employee Directory** - Complete listing with contact info
- **Salary Analysis** - Compensation breakdowns and statistics
- **Work Permit Status** - Compliance tracking report
- **New Hires Report** - Recent additions to the database

### Export Options
- **CSV format** for spreadsheet import
- **PDF format** for formatted reports
- **Filtered exports** based on current view settings

## Data Validation & Security

### Input Validation
- **Email format validation** for email fields
- **Phone number formatting** with international support
- **Salary field validation** (positive numbers, proper formatting)
- **Date validation** for date of birth field

### Data Security
- **Role-based access control** enforced at all levels
- **Audit logging** for data changes
- **Secure file uploads** for profile photos
- **Data encryption** for sensitive information

### Privacy Considerations
- **GDPR compliance** features for data export/deletion
- **Consent management** for photo usage
- **Data retention policies** configuration
- **Anonymous reporting** options

## Integration Capabilities

### API Endpoints
- **REST API** for employee data access
- **JSON API** for frontend applications
- **Custom endpoints** for specialized integrations

### Import/Export
- **CSV import** for bulk employee data entry
- **Excel compatibility** for existing HR systems
- **Data validation** during import process
- **Error reporting** for failed imports

## Performance Requirements

### Database Optimization
- **Indexed fields** for search performance
- **Query optimization** for large datasets
- **Caching strategies** for frequently accessed data

### Frontend Performance
- **Lazy loading** for employee photos
- **Pagination** for large lists
- **AJAX filtering** without page reloads
- **Progressive enhancement** for older browsers

## Technical Implementation Standards

### Content Modeling Best Practices
- Follow Drupal field naming conventions
- Use appropriate field types for data validation
- Implement field dependencies and conditional logic
- Create reusable field configurations

### Configuration Management
- All customizations in version-controlled configuration
- Environment-specific configuration handling
- Automated deployment of configuration changes
- Configuration validation and testing

### Module Dependencies
- **Core modules**: Field UI, Views, Image, User
- **Contributed modules**: As needed for specific functionality
- **Custom modules**: For specialized business logic

## Next Steps

- Review [Development Environment Setup](Development-Environment-Setup.md) to begin development
- Check [Frontend Development Guide](Frontend-Development-Guide.md) for UI implementation
- Follow [Development Workflow](Development-Workflow.md) for contribution process