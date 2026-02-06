# OMRIX - Derivatives Market Analysis Platform

A professional, secure, and ethical derivatives market analysis and signal platform that integrates with the Deriv API. Built with PHP 8+, MySQL, and modern web technologies.

## Features

- **Probability-Based Analysis**: Multi-indicator technical analysis (RSI, EMA, Bollinger Bands, ATR)
- **Secure API Integration**: AES-256-GCM encrypted token storage
- **Risk Assessment**: Every signal includes LOW/MEDIUM/HIGH risk evaluation
- **Demo Account First**: Practice trading before risking real capital
- **Professional UI**: Dark theme with gold accents, fully responsive design
- **Security First**: CSRF protection, rate limiting, password hashing with Argon2id

## Requirements

- PHP 8.0 or higher
- MySQL 5.7 or higher (MariaDB 10.3+)
- Apache with mod_rewrite enabled
- OpenSSL extension
- PDO MySQL extension
- mbstring extension

## Installation

### 1. Clone/Download the Project

```bash
cd /var/www/html
git clone <repository-url> omrix
cd omrix
```

### 2. Create Database

```bash
mysql -u root -p
```

```sql
CREATE DATABASE omrix CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'omrix_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON omrix.* TO 'omrix_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

### 3. Import Database Schema

```bash
mysql -u omrix_user -p omrix < database/schema.sql
```

### 4. Configure Environment

Create a `.env` file in the project root (outside public directory):

```bash
# Environment
OMRIX_ENV=production

# Database
DB_HOST=localhost
DB_NAME=omrix
DB_USER=omrix_user
DB_PASS=your_secure_password

# Security (generate with: openssl rand -base64 32)
ENCRYPTION_KEY=your-32-byte-encryption-key-here!!
ENCRYPTION_KEY_DERIV=your-deriv-encryption-key-here!!

# Application
APP_URL=https://yourdomain.com

# Deriv API
DERIV_APP_ID=your_deriv_app_id
```

**Important**: Generate unique encryption keys:
```bash
openssl rand -base64 32
```

### 5. Set Permissions

```bash
# Set ownership
chown -R www-data:www-data /var/www/html/omrix

# Set permissions
chmod -R 755 /var/www/html/omrix
chmod -R 775 /var/www/html/omrix/logs
chmod -R 775 /var/www/html/omrix/cache

# Protect sensitive files
chmod 600 /var/www/html/omrix/.env
```

### 6. Configure Web Server

#### Apache

Point your document root to the `public` directory:

```apache
<VirtualHost *:80>
    ServerName yourdomain.com
    DocumentRoot /var/www/html/omrix/public
    
    <Directory /var/www/html/omrix/public>
        AllowOverride All
        Require all granted
    </Directory>
    
    ErrorLog ${APACHE_LOG_DIR}/omrix_error.log
    CustomLog ${APACHE_LOG_DIR}/omrix_access.log combined
</VirtualHost>
```

Enable required modules:
```bash
a2enmod rewrite
a2enmod headers
a2enmod deflate
a2enmod expires
systemctl restart apache2
```

### 7. SSL Certificate (Recommended)

Use Let's Encrypt for free SSL:
```bash
certbot --apache -d yourdomain.com
```

## Security Considerations

### Encryption Keys
- Never commit encryption keys to version control
- Rotate keys periodically
- Store keys in environment variables only

### Database Security
- Use strong, unique passwords
- Restrict database user privileges
- Enable SSL connections if possible

### Application Security
- Keep PHP and dependencies updated
- Enable HTTPS in production
- Configure proper firewall rules
- Regular security audits

## Usage

### User Registration
1. Visit `/register.php`
2. Create an account with strong password
3. Verify email (if enabled)

### Connecting Deriv Account
1. Log in to your Deriv account
2. Go to Account Settings → API Token
3. Create a new token with `read` and `trade` scopes
4. In OMRIX, go to "Connect" page
5. Paste your token and select account type (Demo recommended)

### Running Analysis
1. Go to the Analyzer page
2. Select a symbol (e.g., EUR/USD)
3. Choose a timeframe
4. Click "Analyze"
5. Review the signal, probability, and risk level

## Project Structure

```
omrix/
├── api/                    # API endpoints
│   └── analyze.php        # Market analysis API
├── config/                # Configuration files
│   ├── config.php        # Main configuration
│   └── database.php      # Database connection
├── database/              # Database files
│   └── schema.sql        # Database schema
├── includes/              # PHP includes
│   ├── auth/             # Authentication
│   │   └── Auth.php
│   ├── deriv/            # Deriv API
│   │   ├── DerivConnector.php
│   │   └── Analyzer.php
│   └── security/         # Security utilities
│       ├── CSRF.php
│       ├── Encryption.php
│       ├── RateLimiter.php
│       └── SessionManager.php
├── logs/                  # Log files (writable)
├── cache/                 # Cache files (writable)
├── public/               # Web root
│   ├── css/              # Stylesheets
│   ├── js/               # JavaScript files
│   ├── images/           # Image assets
│   ├── index.php         # Landing page
│   ├── login.php         # Login page
│   ├── register.php      # Registration page
│   ├── dashboard.php     # User dashboard
│   ├── analyzer.php      # Market analyzer
│   ├── connect.php       # Deriv connection
│   ├── logout.php        # Logout handler
│   └── .htaccess         # Apache config
└── README.md             # This file
```

## API Documentation

### Analyze Endpoint

**URL**: `/api/analyze.php`

**Method**: GET

**Parameters**:
- `symbol` (required): Market symbol (e.g., `frxEURUSD`, `R_10`)
- `timeframe` (optional): Timeframe (default: `1h`, options: `1m`, `5m`, `15m`, `30m`, `1h`, `4h`, `1d`)

**Response**:
```json
{
  "success": true,
  "symbol": "frxEURUSD",
  "timeframe": "1h",
  "current_price": 1.08542,
  "direction_bias": "LONG",
  "probability_score": 72,
  "risk_level": "MEDIUM",
  "confidence_score": 65,
  "indicators": {
    "rsi": 52.34,
    "ema_fast": 1.08450,
    "ema_slow": 1.08320,
    "bb_upper": 1.08750,
    "bb_lower": 1.08200,
    "atr": 0.00120
  },
  "suggested_stop_loss": 1.08342,
  "suggested_take_profit": 1.08842,
  "analysis_summary": "Market showing uptrend conditions...",
  "timestamp": 1704067200
}
```

## Rate Limiting

- Login attempts: 5 per 30 minutes per IP
- Registration: 3 per hour per IP
- API calls: 60 per minute per IP

## Legal Disclaimer

**IMPORTANT**: Trading derivatives involves substantial risk of loss and is not suitable for all investors. Past performance is not indicative of future results. OMRIX provides analytical tools only and does not guarantee profits. You should carefully consider whether trading is appropriate for you in light of your experience, objectives, and financial resources.

## License

This project is proprietary software. All rights reserved.

## Support

For support inquiries, please contact: support@omrix.com

## Changelog

### Version 1.0.0
- Initial release
- User authentication system
- Deriv API integration
- Market analyzer with technical indicators
- Secure token encryption
- Rate limiting and CSRF protection
