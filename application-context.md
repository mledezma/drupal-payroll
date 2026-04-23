# 🧾 Drupal 11 Payroll & Employee Management System – Specification

## 🎯 Objective
Build a **Drupal 11 web application** to manage employee records and payroll-related information. The system will act as a centralized database to store, organize, and retrieve employee data efficiently, enabling easy access when offering employee services to contractors and enterprises.

---

## 🧩 Core Features

### 1. Employee Management
Create a system to store detailed employee profiles with the following fields:

- Full Name
- Date of Birth
- Identification Number (ID)
- Work Permit Status / Details
- Email Address
- Phone Number
- Profile Photo
- Salary Paid (actual salary)
- Contract Salary (agreed salary)

---

### 2. Data Storage & Structure
- Implement a **custom content type** called `Employee`.
- Use appropriate field types:
  - Text (Name, ID)
  - Date (Date of Birth)
  - Email
  - Telephone
  - Image (Profile Photo)
  - Decimal (Salary fields)
- Ensure validation rules:
  - Required fields where necessary
  - Proper formats (email, phone, numeric values)

---

### 3. Employee Listing & Queries
- Use **Drupal Views** to:
  - Display employees in a sortable and filterable table
  - Allow searching by name, ID, or email
  - Enable sorting by salary, name, or date of birth

- Provide multiple views:
  - Full employee list
  - Filtered lists (e.g., by salary range or work permit status)

---

### 4. User Interface
- Create a clean admin interface for:
  - Adding new employees
  - Editing existing records
  - Viewing employee details

- Ensure usability for non-technical users:
  - Clear labels
  - Logical grouping of fields
  - Simple navigation

---

### 5. Access Control
Define roles and permissions:

- **Admin**
  - Full access (create, edit, delete, configure)

- **Manager** (optional)
  - Can create and edit employee records

- **Viewer** (optional)
  - Read-only access

---

### 6. Purpose & Use Case
The application should:

- Serve as a **centralized employee database**
- Allow quick retrieval of employee information
- Help present employee profiles to contractors and enterprises
- Support business decisions with organized employee data

---

## ⚙️ Technical Requirements

- Drupal Version: **Drupal 11**
- Use core modules whenever possible:
  - Views
  - Field UI
  - Image

- Follow Drupal best practices:
  - Configuration management (config export/import)
  - Clean content modeling
  - Reusable structures

---

## 🚀 Optional Enhancements (Future Scope)

- Payroll calculations:
  - Taxes
  - Deductions
  - Net salary

- Data export:
  - CSV
  - Excel

- API support:
  - REST or GraphQL integration

- Additional features:
  - Employee status (active/inactive)
  - Document uploads (contracts, permits)