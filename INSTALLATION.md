# ERPNext Financial System - Installation & Usage Guide

## 📦 Installation

### Prerequisites

Before installing ERPNext Financial System, ensure you have:

- ERPNext v14 or higher installed
- Python 3.8+
- Node.js 14+
- MariaDB 10.3+
- Redis 5+
- Frappe Bench

### Step 1: Get the App

```bash
# Navigate to your bench directory
cd ~/frappe-bench

# Get the app from GitHub
bench get-app https://github.com/moh222salah/erp-statement.git

# Or if developing locally
bench get-app /path/to/erp_statement
```

### Step 2: Install on Site

```bash
# Install app on your site
bench --site [your-site-name] install-app erp_statement

# Example:
bench --site mycompany.local install-app erp_statement
```

### Step 3: Run Migrations

```bash
# Migrate database
bench --site [your-site-name] migrate

# Clear cache
bench --site [your-site-name] clear-cache

# Build assets
bench build --app erp_statement
```

### Step 4: Restart Services

```bash
# Restart bench
bench restart

# Or restart specific services
sudo supervisorctl restart all
```

## 🚀 Quick Start

### 1. Access the System

After installation, log in to your ERPNext site:

```
https://your-site.com
```

Navigate to: **Accounting > Reports > Account Statement Analytics**

### 2. Generate Your First Statement

```python
# Using Python Console in ERPNext
import frappe
from erp_statement.api.statement import get_account_statement

# Generate statement
statement = get_account_statement(
    account="Debtors - ABC",
    from_date="2025-01-01",
    to_date="2025-01-31"
)

print(statement)
```

### 3. Using the Web Interface

1. Go to **Accounting > Account Statement**
2. Select an account
3. Choose date range
4. Click "Generate Statement"
5. Export as PDF, Excel, or CSV

## 📊 API Usage

### Python API Examples

#### Get Account Statement

```python
import frappe
from erp_statement.api.statement import get_account_statement

# Basic usage
statement = get_account_statement(
    account="Debtors - Company",
    from_date="2025-01-01",
    to_date="2025-01-31"
)

# With filters
statement = get_account_statement(
    account="Debtors - Company",
    from_date="2025-01-01",
    to_date="2025-01-31",
    party_type="Customer",
    party="Customer ABC",
    show_opening_entries=True,
    show_cancelled_entries=False
)

# Access data
print(f"Opening Balance: {statement['opening_balance']}")
print(f"Total Debit: {statement['total_debit']}")
print(f"Total Credit: {statement['total_credit']}")
print(f"Closing Balance: {statement['closing_balance']}")

# Loop through entries
for entry in statement['entries']:
    print(f"{entry['date']} - {entry['voucher_no']} - {entry['debit']}")
```

#### Get Account Balance

```python
from erp_statement.api.statement import get_balance

# Get current balance
balance = get_balance(
    account="Debtors - Company",
    date="2025-01-31"
)

print(f"Balance: {balance['balance']} {balance['balance_type']}")
```

#### Generate Monthly Statement

```python
from erp_statement.api.statement import generate_monthly_statement

# Generate for specific month
monthly_statement = generate_monthly_statement(
    company="Your Company",
    month_date="2025-01-15"  # Any date in the month
)

print(f"Total Receivable: {monthly_statement['summary']['total_receivable']}")
print(f"Total Payable: {monthly_statement['summary']['total_payable']}")
```

### REST API Examples

#### Get Statement via REST

```bash
# Using curl
curl -X GET "https://your-site.com/api/method/erp_statement.api.statement.get_account_statement" \
  -H "Authorization: token YOUR_API_KEY:YOUR_API_SECRET" \
  -d "account=Debtors - Company" \
  -d "from_date=2025-01-01" \
  -d "to_date=2025-01-31"
```

```javascript
// Using JavaScript/Fetch
fetch('https://your-site.com/api/method/erp_statement.api.statement.get_account_statement', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json',
        'Authorization': 'token YOUR_API_KEY:YOUR_API_SECRET'
    },
    body: JSON.stringify({
        account: "Debtors - Company",
        from_date: "2025-01-01",
        to_date: "2025-01-31"
    })
})
.then(response => response.json())
.then(data => console.log(data.message));
```

```python
# Using Python requests
import requests

url = "https://your-site.com/api/method/erp_statement.api.statement.get_account_statement"
headers = {
    "Authorization": "token YOUR_API_KEY:YOUR_API_SECRET",
    "Content-Type": "application/json"
}
data = {
    "account": "Debtors - Company",
    "from_date": "2025-01-01",
    "to_date": "2025-01-31"
}

response = requests.post(url, json=data, headers=headers)
statement = response.json()['message']
```

## 🛠️ Configuration

### Custom Fields

The app automatically creates these custom fields:

1. **Sales Invoice**: `statement_account`
2. **Purchase Invoice**: `statement_account`
3. **Payment Entry**: `statement_reference`

### Scheduled Tasks

Configure in `hooks.py`:

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

### Permissions

Set up role permissions:

```bash
# Give roles access to Account Statement
bench --site [site-name] set-config developer_mode 1
```

Then go to:
**Setup > Permissions > Role Permissions Manager**

## 🎨 Customization

### Custom CSS

Add to `public/css/custom.css`:

```css
.statement-container {
    background: #f0f0f0;
}

.summary-card {
    border-radius: 20px;
}
```

### Custom JavaScript

Add to `public/js/custom.js`:

```javascript
erp_statement.custom = {
    before_generate: function(filters) {
        // Custom logic before generating statement
        console.log("Generating statement with filters:", filters);
    },
    
    after_generate: function(statement) {
        // Custom logic after statement generation
        console.log("Statement generated:", statement);
    }
};
```

## 📱 Mobile Access

### Progressive Web App (PWA)

The system is PWA-ready. Users can:

1. Open the site in mobile browser
2. Click "Add to Home Screen"
3. Access like a native app

### Mobile API

```javascript
// Mobile-optimized API call
erp_statement.mobile.get_statement({
    account: "Debtors - Company",
    from_date: "2025-01-01",
    to_date: "2025-01-31",
    compress: true  // Compress response for mobile
});
```

## 🔧 Troubleshooting

### Common Issues

#### 1. App Not Showing After Installation

```bash
# Clear cache
bench --site [site-name] clear-cache

# Rebuild
bench build --app erp_statement

# Restart
bench restart
```

#### 2. API Not Working

```bash
# Check if app is installed
bench --site [site-name] list-apps

# Check migrations
bench --site [site-name] migrate
```

#### 3. Permission Denied

```bash
# Check file permissions
sudo chown -R frappe:frappe ~/frappe-bench/apps/erp_statement

# Set correct permissions
find ~/frappe-bench/apps/erp_statement -type d -exec chmod 755 {} \;
find ~/frappe-bench/apps/erp_statement -type f -exec chmod 644 {} \;
```

### Debug Mode

Enable debug mode:

```bash
# In site_config.json
{
    "developer_mode": 1,
    "db_name": "your_db",
    "db_password": "your_password"
}
```

View logs:

```bash
# Tail logs
tail -f ~/frappe-bench/logs/worker.error.log
tail -f ~/frappe-bench/logs/web.error.log

# Check specific app logs
tail -f ~/frappe-bench/logs/erp_statement.log
```

## 🧪 Testing

### Run Tests

```bash
# Run all tests
bench --site [site-name] run-tests --app erp_statement

# Run specific test
bench --site [site-name] run-tests --app erp_statement --module erp_statement.tests.test_statement

# Run with coverage
bench --site [site-name] run-tests --app erp_statement --coverage
```

### Create Test Data

```python
# Create test invoices and payments
from erp_statement.tests.fixtures import create_test_data

create_test_data()
```

## 📚 Advanced Usage

### Batch Processing

```python
# Process multiple accounts
from erp_statement.api.statement import get_account_statement

accounts = ["Debtors - ABC", "Debtors - XYZ", "Creditors - PQR"]

statements = {}
for account in accounts:
    statements[account] = get_account_statement(
        account=account,
        from_date="2025-01-01",
        to_date="2025-01-31"
    )

# Aggregate data
total_receivable = sum([s['closing_balance'] for s in statements.values() 
                       if s['balance_type'] == 'DR'])
```

### Custom Reports

```python
# Create custom report query
from erp_statement.utils.helpers import get_fiscal_year

def custom_aging_report(company):
    fiscal_year = get_fiscal_year(company=company)
    
    query = """
        SELECT 
            party,
            SUM(CASE WHEN age <= 30 THEN outstanding ELSE 0 END) as '0-30',
            SUM(CASE WHEN age > 30 AND age <= 60 THEN outstanding ELSE 0 END) as '31-60',
            SUM(CASE WHEN age > 60 AND age <= 90 THEN outstanding ELSE 0 END) as '61-90',
            SUM(CASE WHEN age > 90 THEN outstanding ELSE 0 END) as '90+'
        FROM (
            -- Your custom query here
        ) as aged_data
        GROUP BY party
    """
    
    return frappe.db.sql(query, as_dict=True)
```

### Export Automation

```python
# Automate monthly exports
from erp_statement.api.statement import export_statement
from frappe.utils import get_first_day, get_last_day, today

def auto_export_monthly_statements():
    month_start = get_first_day(today())
    month_end = get_last_day(today())
    
    accounts = frappe.get_all("Account", 
                             filters={"account_type": "Receivable"},
                             pluck="name")
    
    for account in accounts:
        export_statement(
            account=account,
            from_date=month_start,
            to_date=month_end,
            format="pdf"
        )
```

## 🔐 Security Best Practices

### 1. API Keys

Always use API keys for external access:

```python
# Generate API key
user = frappe.get_doc("User", "user@example.com")
api_key = user.api_key
api_secret = frappe.generate_hash()
user.api_secret = api_secret
user.save()
```

### 2. Rate Limiting

Configure in `hooks.py`:

```python
rate_limit = {
    "window": 86400,  # 24 hours
    "limit": 1000     # 1000 requests per day
}
```

### 3. Data Encryption

Enable encryption for sensitive fields:

```python
from frappe.utils.password import encrypt, decrypt

encrypted_data = encrypt(sensitive_data)
decrypted_data = decrypt(encrypted_data)
```

## 📖 Additional Resources

- [ERPNext Documentation](https://docs.erpnext.com)
- [Frappe Framework Documentation](https://frappeframework.com/docs)
- [GitHub Repository](https://github.com/moh222salah/erp-statement)
- [Report Issues](https://github.com/moh222salah/erp-statement/issues)

## 💡 Tips & Tricks

1. **Performance**: Use date ranges wisely - smaller ranges = faster queries
2. **Caching**: Enable Redis caching for frequently accessed statements
3. **Backup**: Always backup before major updates
4. **Testing**: Test in development environment first
5. **Monitoring**: Set up monitoring for scheduled tasks

## 🤝 Support

For support:
- 📧 Email: moh222salah@example.com
- 💬 GitHub Issues: [Create an issue](https://github.com/moh222salah/erp-statement/issues)
- 📚 Wiki: [Documentation](https://github.com/moh222salah/erp-statement/wiki)
