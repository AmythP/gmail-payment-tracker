# Gmail Payment Tracker

![Python](https://img.shields.io/badge/python-3.8+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Gmail API](https://img.shields.io/badge/Gmail-API-red.svg)

Automatically extract payment amounts from Gmail no-reply emails and track them in Excel and Google Sheets. Perfect for freelancers, small businesses, and anyone who needs to track payments received via email notifications.

## 🚀 Features

- **Automatic Email Scanning**: Searches Gmail for payment-related emails from no-reply addresses
- **Smart Amount Extraction**: Uses regex patterns to extract payment amounts in multiple currencies (USD, INR, etc.)
- **Dual Export**: Saves data to both Excel and Google Sheets automatically
- **Date Tracking**: Captures the exact date and time of payment receipt
- **Multiple Currency Support**: Detects and tracks USD, INR, and other currencies
- **Configurable Patterns**: Customize regex patterns for different payment providers
- **Comprehensive Logging**: Track all operations with detailed logs

## 💻 Prerequisites

- Python 3.8 or higher
- Gmail account
- Google Cloud account (free tier works fine)
- Basic knowledge of Python

## 📦 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/AmythP/gmail-payment-tracker.git
cd gmail-payment-tracker
```

### 2. Create Virtual Environment

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Set Up Gmail API

#### Step 1: Create Google Cloud Project

1. Go to [Google Cloud Console](https://console.cloud.google.com/)
2. Create a new project
3. Name it "Gmail Payment Tracker"

#### Step 2: Enable Gmail API

1. Go to **APIs & Services > Library**
2. Search for "Gmail API"
3. Click **Enable**

#### Step 3: Create OAuth Credentials

1. Go to **APIs & Services > Credentials**
2. Click **Create Credentials > OAuth client ID**
3. Configure consent screen if prompted
4. Select **Desktop app** as application type
5. Download the credentials file
6. Rename it to `credentials.json` and place in project directory

### 5. Set Up Google Sheets API (Optional)

If you want to sync to Google Sheets:

1. Enable Google Sheets API in the same project
2. Create a **Service Account**
3. Download service account JSON
4. Rename to `service_credentials.json`

## 🛠️ Configuration

Edit `config.json` to customize the tracker:

```json
{
  "gmail": {
    "search_query": "from:noreply payment OR amount OR paid"
  },
  "payment_patterns": [
    "\\$[\\d,]+\\.[\\d]{2}",
    "₹[\\d,]+\\.?[\\d]*",
    "Rs\\.?\\s*[\\d,]+\\.?[\\d]*"
  ]
}
```

### Customize Payment Patterns

Add regex patterns for your specific payment providers:

```json
"payment_patterns": [
  "received \\$[\\d,]+\\.[\\d]{2}",
  "paid ₹[\\d,]+",
  "transaction of Rs[\\d,]+"
]
```

### Customize Email Search

Modify the search query to target specific senders:

```json
"search_query": "from:noreply@paypal.com OR from:noreply@stripe.com payment"
```

## 🏃 Usage

### Basic Usage

Run the tracker:

```bash
python gmail_payment_tracker.py
```

On first run:
1. A browser window will open
2. Sign in to your Gmail account
3. Grant permissions
4. A `token.json` file will be created for future use

### Programmatic Usage

```python
from gmail_payment_tracker import GmailPaymentTracker

# Initialize tracker
tracker = GmailPaymentTracker()

# Search for payments
payments = tracker.search_payment_emails(max_results=50)

# Save to Excel
tracker.save_to_excel(payments)

# Save to Google Sheets
tracker.save_to_google_sheets(payments)
```

### Custom Search

```python
# Modify search query in code
tracker.config['gmail']['search_query'] = 'from:noreply@razorpay.com payment'
payments = tracker.search_payment_emails()
```

## 📁 Project Structure

```
gmail-payment-tracker/
├── gmail_payment_tracker.py  # Main application
├── requirements.txt          # Python dependencies
├── config.json               # Configuration
├── credentials.json          # Gmail OAuth credentials (you create)
├── token.json                # Auth token (auto-generated)
├── service_credentials.json  # Sheets service account (optional)
├── README.md                 # This file
├── .gitignore                # Git ignore patterns
└── payment_data/             # Excel files (auto-created)
    └── payment_tracker.xlsx  # Payment data
```

## 📊 Output Format

### Excel File

The Excel file contains:

| Date | Sender | Subject | Amount | Currency | Email Preview |
|------|--------|---------|--------|----------|---------------|
| 2025-11-11 10:30:00 | noreply@paypal.com | Payment Received | $50.00 | USD | You have received... |

### Google Sheets

Same format as Excel, with:
- Auto-formatted headers (green background)
- Updated in real-time
- Shareable with team members

## 🔍 Supported Payment Patterns

The tracker recognizes:

### Amount Formats
- `$50.00` (USD with decimals)
- `₹500` or `₹500.50` (INR)
- `Rs. 500` or `Rs 500.50`
- `INR 500` or `USD 50.00`
- `amount: 500`
- `paid $50`
- `received ₹500`

### Email Sources
- PayPal: `noreply@paypal.com`
- Stripe: `noreply@stripe.com`
- Razorpay: `noreply@razorpay.com`
- PayU: `noreply@payu.in`
- Bank notifications
- Custom payment gateways

## ⚙️ Advanced Features

### Schedule Automatic Tracking

Use cron (Linux/Mac) or Task Scheduler (Windows):

```bash
# Run every day at 9 AM
0 9 * * * cd /path/to/gmail-payment-tracker && python gmail_payment_tracker.py
```

### Filter by Date Range

```python
tracker.config['gmail']['search_query'] = 'from:noreply payment after:2025/11/01'
```

### Export to CSV

```python
import pandas as pd

payments = tracker.search_payment_emails()
df = pd.DataFrame(payments)
df.to_csv('payments.csv', index=False)
```

## 👥 Use Cases

1. **Freelancers**: Track PayPal/Stripe payments automatically
2. **Small Businesses**: Monitor daily payment receipts
3. **Accountants**: Collect payment data for bookkeeping
4. **E-commerce**: Track payment gateway notifications
5. **Personal Finance**: Monitor all incoming payments
6. **Tax Preparation**: Annual payment history for tax filing

## 🔒 Privacy & Security

- **OAuth 2.0**: Secure authentication with Gmail
- **Read-Only Access**: Only reads emails, never sends or modifies
- **Local Storage**: Credentials stored locally on your machine
- **No Cloud Processing**: All processing happens on your computer
- **.gitignore**: Credentials automatically excluded from git

### Security Best Practices

1. Never share `credentials.json` or `token.json`
2. Review OAuth permissions before granting
3. Revoke access anytime from Google Account settings
4. Use service accounts for production deployments

## 🐛 Troubleshooting

### Gmail Authentication Error

**Problem**: "Credentials file not found"

**Solution**: 
1. Download OAuth credentials from Google Cloud Console
2. Rename to `credentials.json`
3. Place in project root directory

### No Payments Found

**Problem**: "No payment emails found"

**Solution**:
1. Check your `search_query` in config.json
2. Verify emails exist in your Gmail
3. Try broader search: `payment OR amount OR paid`

### Amount Not Extracted

**Problem**: Email found but amount is blank

**Solution**:
1. Check email content manually
2. Add custom regex pattern to `payment_patterns`
3. Look at log file for debugging info

### Google Sheets Error

**Problem**: "Spreadsheet not found"

**Solution**:
1. Create service account credentials
2. Share spreadsheet with service account email
3. Grant Editor permissions

## 📝 License

MIT License

## 👤 Author

**Amrut Prakash**
- GitHub: [@AmythP](https://github.com/AmythP)
- Location: Indore, India

## 🤝 Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create a feature branch
3. Commit your changes
4. Push to the branch
5. Open a Pull Request

## ⭐ Show Your Support

Give a ⭐ if this project helped you!

## 📞 Support

For issues or questions:
- Open an issue on GitHub
- Check existing issues for solutions

## 📝 Changelog

### Version 1.0.0 (2025-11-11)
- Initial release
- Gmail email extraction
- Payment amount detection
- Excel export
- Google Sheets integration
- Multi-currency support
- Comprehensive logging

---

**Disclaimer**: This tool accesses your Gmail using OAuth 2.0. Ensure you trust the code before granting permissions. All processing happens locally on your machine.