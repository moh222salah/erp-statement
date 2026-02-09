# ERPNext Financial System - Project Structure

## 📁 Complete File Structure

```
erp_statement/
├── 📄 setup.py                          # Package installation configuration
├── 📄 requirements.txt                  # Python dependencies
├── 📄 license.txt                       # MIT License
├── 📄 .gitignore                        # Git ignore rules
├── 📄 README.md                         # Project documentation
├── 📄 INSTALLATION.md                   # Installation & usage guide
│
├── 📁 erp_statement/                    # Main application module
│   ├── 📄 __init__.py                   # Module initialization
│   ├── 📄 hooks.py                      # Frappe hooks configuration
│   ├── 📄 install.py                    # Installation scripts
│   ├── 📄 tasks.py                      # Scheduled tasks
│   │
│   ├── 📁 api/                          # API endpoints
│   │   ├── 📄 __init__.py
│   │   ├── 📄 statement.py              # Core statement API
│   │   ├── 📄 sales_invoice.py          # Sales invoice handlers
│   │   ├── 📄 purchase_invoice.py       # Purchase invoice handlers
│   │   ├── 📄 payment_entry.py          # Payment entry handlers
│   │   └── 📄 journal_entry.py          # Journal entry handlers
│   │
│   ├── 📁 utils/                        # Utility functions
│   │   ├── 📄 __init__.py
│   │   └── 📄 helpers.py                # Helper utilities
│   │
│   ├── 📁 public/                       # Frontend assets
│   │   ├── 📁 css/
│   │   │   ├── 📄 erp_statement.css     # Main styles
│   │   │   └── 📄 web.css               # Web portal styles
│   │   ├── 📁 js/
│   │   │   ├── 📄 erp_statement.js      # Main JavaScript
│   │   │   ├── 📄 web.js                # Web portal JavaScript
│   │   │   ├── 📄 sales_invoice.js      # Sales invoice customizations
│   │   │   ├── 📄 purchase_invoice.js   # Purchase invoice customizations
│   │   │   └── 📄 payment_entry.js      # Payment entry customizations
│   │   └── 📁 images/
│   │       └── 📄 logo.png              # App logo
│   │
│   ├── 📁 templates/                    # HTML templates
│   │   ├── 📁 pages/
│   │   │   └── 📄 statement.html        # Statement page template
│   │   └── 📁 includes/
│   │       ├── 📄 statement_report.html # Statement report template
│   │       └── 📄 summary_cards.html    # Summary cards template
│   │
│   ├── 📁 doctype/                      # Custom DocTypes
│   │   ├── 📁 account_statement/
│   │   │   ├── 📄 account_statement.json
│   │   │   ├── 📄 account_statement.py
│   │   │   └── 📄 account_statement.js
│   │   └── 📁 financial_report/
│   │       ├── 📄 financial_report.json
│   │       ├── 📄 financial_report.py
│   │       └── 📄 financial_report.js
│   │
│   ├── 📁 fixtures/                     # Data fixtures
│   │   └── 📄 custom_fields.json        # Custom field definitions
│   │
│   └── 📁 patches/                      # Database patches
│       └── 📄 v1_0/
│           └── 📄 update_custom_fields.py
│
└── 📁 {erp_statement,erp_statement/     # Additional directories
    └── (auto-created structure)
```

## 📝 File Descriptions

### Root Level Files

#### setup.py
Python package setup configuration for installation via pip/bench.

**Key Functions:**
- Package metadata
- Dependencies management
- Installation scripts

#### requirements.txt
Lists all Python dependencies required by the application.

**Main Dependencies:**
- frappe >= 14.0.0
- erpnext >= 14.0.0
- pandas >= 1.5.0
- openpyxl >= 3.0.10
- reportlab >= 3.6.0

#### license.txt
MIT License - Open source license allowing free use and modification.

#### .gitignore
Specifies files and directories to be ignored by Git version control.

---

### Main Module Files

#### erp_statement/__init__.py
Module initialization file that defines the version.

```python
__version__ = '1.0.0'
```

#### erp_statement/hooks.py
**Core configuration file for Frappe app integration.**

**Key Sections:**
- App metadata (name, title, description)
- CSS/JS includes
- DocType customizations
- Document event hooks
- Scheduled tasks configuration
- Web routes
- Fixtures

**Example Hook:**
```python
doc_events = {
    "Sales Invoice": {
        "on_submit": "erp_statement.api.sales_invoice.on_submit"
    }
}
```

#### erp_statement/install.py
**Installation and setup functions.**

**Key Functions:**
- `before_install()` - Pre-installation checks
- `after_install()` - Post-installation setup
- `create_custom_fields_for_statement()` - Create custom fields
- `create_default_accounts()` - Setup default accounts
- `create_default_reports()` - Create system reports

#### erp_statement/tasks.py
**Scheduled background tasks.**

**Main Tasks:**
- `update_exchange_rates()` - Daily currency updates
- `generate_daily_reports()` - Daily report generation
- `generate_monthly_statements()` - Monthly statement creation
- `cleanup_old_logs()` - Log file maintenance
- `send_scheduled_reports()` - Automated report emails
- `backup_financial_data()` - Data backup operations

---

### API Module

#### api/statement.py
**Core API for account statement operations.**

**Main Functions:**

```python
@frappe.whitelist()
def get_account_statement(account, from_date, to_date, **kwargs):
    """
    Get detailed account statement with all transactions
    
    Returns:
        dict: Statement data with transactions and summary
    """

@frappe.whitelist()
def get_balance(account, date=None, **kwargs):
    """
    Get current balance for an account
    
    Returns:
        dict: Balance information
    """

@frappe.whitelist()
def generate_monthly_statement(company, month_date):
    """
    Generate comprehensive monthly statement
    
    Returns:
        dict: Monthly statement data
    """

@frappe.whitelist()
def export_statement(account, from_date, to_date, format="pdf"):
    """
    Export account statement in various formats
    
    Returns:
        str: File path or download link
    """
```

**Helper Functions:**
- `get_opening_balance()` - Calculate opening balance
- `get_description()` - Format entry descriptions
- `group_entries_by_voucher()` - Group transactions

#### api/sales_invoice.py
Event handlers for Sales Invoice document.

**Functions:**
- `on_submit()` - Handle invoice submission
- `on_cancel()` - Handle invoice cancellation
- `update_statement_for_invoice()` - Update statement
- `reverse_statement_for_invoice()` - Reverse entries

#### api/purchase_invoice.py
Event handlers for Purchase Invoice document.

#### api/payment_entry.py
Event handlers for Payment Entry document.

#### api/journal_entry.py
Event handlers for Journal Entry document.

---

### Utils Module

#### utils/helpers.py
**Comprehensive utility functions.**

**Categories:**

1. **Date & Fiscal Functions:**
   ```python
   get_fiscal_year(date, company)
   validate_dates(from_date, to_date)
   calculate_age(date, reference_date)
   get_aging_category(age_days)
   ```

2. **Currency Functions:**
   ```python
   get_account_currency(account)
   get_company_currency(company)
   convert_currency(amount, from_currency, to_currency, date)
   get_exchange_rate(from_currency, to_currency, date)
   format_currency(amount, currency, decimals)
   ```

3. **Voucher Functions:**
   ```python
   get_voucher_details(voucher_type, voucher_no)
   get_party_details(party_type, party)
   ```

4. **General Utilities:**
   ```python
   sanitize_number(value)
   truncate_string(text, length)
   get_default_company()
   ```

---

### Frontend Assets

#### public/css/erp_statement.css
**Main stylesheet with comprehensive styling.**

**Features:**
- CSS custom properties (variables)
- Statement container styles
- Summary card animations
- Responsive table design
- Filter and button styles
- Loading animations
- Dark mode support
- Print styles
- Mobile responsive design

**Color Scheme:**
```css
--primary-color: #2E86AB
--secondary-color: #A23B72
--accent-color: #F18F01
--success-color: #6A994E
--danger-color: #C73E1D
```

#### public/js/erp_statement.js
**Main JavaScript functionality.**

**Key Objects:**

1. **erp_statement** (Main module)
   - `init()` - Initialize system
   - `get_statement()` - Fetch statement data
   - `export_statement()` - Export functionality
   - `get_balance()` - Get account balance
   - `show_statement_dialog()` - Display statement dialog
   - `generate_statement()` - Generate statement
   - `render_statement()` - Render statement view

2. **erp_statement.statement_report**
   - `refresh()` - Refresh report view
   - Custom button handlers

3. **erp_statement.utils**
   - `format_number()` - Number formatting
   - `parse_number()` - Parse numeric values
   - `get_voucher_color()` - Voucher type colors
   - `show_loading()` - Loading indicator
   - `hide_loading()` - Hide loading

---

### Templates

#### templates/pages/statement.html
Main statement page template for web view.

#### templates/includes/statement_report.html
Statement report template with data binding.

#### templates/includes/summary_cards.html
Reusable summary card components.

---

## 🔧 Configuration Files

### Custom Fields Configuration

Located in `fixtures/custom_fields.json`:

```json
{
  "doctype": "Custom Field",
  "filters": [
    ["name", "in", [
      "Sales Invoice-statement_account",
      "Purchase Invoice-statement_account",
      "Payment Entry-statement_reference"
    ]]
  ]
}
```

### Scheduled Tasks Configuration

In `hooks.py`:

```python
scheduler_events = {
    "daily": [
        "erp_statement.tasks.update_exchange_rates",
        "erp_statement.tasks.generate_daily_reports"
    ],
    "monthly": [
        "erp_statement.tasks.generate_monthly_statements"
    ]
}
```

---

## 🚀 Key Features by File

### Statement Generation
- **File:** `api/statement.py`
- **Function:** `get_account_statement()`
- **Features:** 
  - Opening/closing balance calculation
  - Transaction filtering
  - Party-wise statements
  - Voucher grouping

### Data Export
- **File:** `api/statement.py`
- **Function:** `export_statement()`
- **Formats:** PDF, Excel, CSV

### Scheduled Reports
- **File:** `tasks.py`
- **Functions:** 
  - `generate_daily_reports()`
  - `generate_monthly_statements()`

### Real-time Balance
- **File:** `api/statement.py`
- **Function:** `get_balance()`
- **Usage:** Dashboard widgets, quick checks

---

## 📦 Installation Flow

1. **setup.py** → Package definition
2. **requirements.txt** → Dependency installation
3. **hooks.py** → App registration
4. **install.py** → Custom setup
5. **Assets** → Build and deploy

---

## 🔄 Data Flow

```
User Request
    ↓
Frontend (JS/CSS)
    ↓
API Layer (statement.py)
    ↓
Business Logic
    ↓
Database (ERPNext)
    ↓
Response Processing
    ↓
Frontend Display
```

---

## 🎯 Usage Examples

### Generate Statement
```python
from erp_statement.api.statement import get_account_statement

statement = get_account_statement(
    account="Debtors - ABC",
    from_date="2025-01-01",
    to_date="2025-01-31"
)
```

### Get Balance
```python
from erp_statement.api.statement import get_balance

balance = get_balance(
    account="Debtors - ABC",
    date="2025-01-31"
)
```

### Export Statement
```python
from erp_statement.api.statement import export_statement

file_path = export_statement(
    account="Debtors - ABC",
    from_date="2025-01-01",
    to_date="2025-01-31",
    format="pdf"
)
```

---

## 📊 Database Schema

### Custom Fields Added

**Sales Invoice:**
- `statement_account` (Link to Account)

**Purchase Invoice:**
- `statement_account` (Link to Account)

**Payment Entry:**
- `statement_reference` (Data field)

---

## 🔐 Security

### API Whitelist
All API functions use `@frappe.whitelist()` decorator for security.

### Permission System
Integrated with ERPNext role-based permissions.

### Data Validation
Input sanitization in `utils/helpers.py`.

---

## 📈 Performance Optimization

### Caching
- Use Frappe's `frappe.cache()` for frequently accessed data
- Cache exchange rates and account balances

### Query Optimization
- Indexed database queries
- Efficient GL Entry filtering
- Batch processing for multiple accounts

### Asset Optimization
- Minified CSS/JS in production
- Lazy loading for large datasets
- Pagination for long statements

---

## 🧪 Testing

### Test Files Structure
```
erp_statement/
└── tests/
    ├── __init__.py
    ├── test_statement.py
    ├── test_api.py
    ├── test_helpers.py
    └── fixtures.py
```

### Running Tests
```bash
bench --site [site-name] run-tests --app erp_statement
```

---

## 📝 Documentation Files

- **README.md** - Main project documentation
- **INSTALLATION.md** - Installation and usage guide
- **PROJECT_STRUCTURE.md** - This file
- **API_REFERENCE.md** - API documentation (to be created)
- **CHANGELOG.md** - Version history (to be created)

---

## 🔄 Update & Maintenance

### Version Updates
1. Update `__version__` in `__init__.py`
2. Add patches in `patches/` directory
3. Update `CHANGELOG.md`
4. Run migrations: `bench migrate`

### Adding Features
1. Create new API functions in `api/`
2. Add frontend code in `public/`
3. Update `hooks.py` if needed
4. Write tests
5. Update documentation

---

## 🤝 Contributing

### Development Setup
```bash
# Clone repo
git clone https://github.com/moh222salah/erp-statement.git

# Install in development mode
bench get-app /path/to/erp_statement

# Install on site
bench --site dev.local install-app erp_statement
```

### Code Style
- Python: PEP 8
- JavaScript: ESLint
- CSS: BEM methodology

---

**Built with ❤️ by Mohammed Salah**
**For ERPNext Community**
