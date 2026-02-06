# OMRIX Project Summary

## Overview

**OMRIX** is a production-ready derivatives market analysis and signal platform built with PHP 8+, MySQL, HTML, CSS, and JavaScript. It integrates with the Deriv API to provide probability-based market analysis with integrated risk control.

---

## Project Structure

```
omrix/
├── api/                        # API endpoints
│   └── analyze.php            # Market analysis API endpoint
│
├── config/                     # Configuration files
│   ├── config.php             # Main configuration with .env loader
│   └── database.php           # PDO database connection manager
│
├── database/                   # Database files
│   └── schema.sql             # Complete MySQL schema with tables, views, triggers
│
├── includes/                   # PHP library files
│   ├── auth/
│   │   └── Auth.php           # Authentication system (login/register/logout)
│   ├── deriv/
│   │   ├── DerivConnector.php # Deriv API integration with encryption
│   │   └── Analyzer.php       # Technical analysis engine
│   └── security/
│       ├── CSRF.php           # CSRF protection
│       ├── Encryption.php     # AES-256-GCM encryption
│       ├── RateLimiter.php    # Rate limiting for API/auth
│       └── SessionManager.php # Secure session management
│
├── logs/                       # Application logs (writable)
├── cache/                      # Cache files (writable)
├── sessions/                   # Session files (writable)
├── uploads/                    # User uploads (writable)
│
├── public/                     # Web root (document root)
│   ├── css/
│   │   └── omrix.css          # Complete stylesheet (dark/gold theme)
│   ├── index.php              # Landing page
│   ├── login.php              # Login page
│   ├── register.php           # Registration page
│   ├── dashboard.php          # User dashboard
│   ├── analyzer.php           # Market analyzer interface
│   ├── connect.php            # Deriv account connection
│   ├── logout.php             # Logout handler
│   └── .htaccess              # Apache configuration with security headers
│
├── .env.example               # Environment configuration template
├── .gitignore                 # Git ignore rules
└── README.md                  # Comprehensive documentation
```

---

## Key Features Implemented

### 1. Frontend
- ✅ Responsive landing page with professional dark/gold theme
- ✅ Secure login & registration pages
- ✅ User dashboard with stats and quick actions
- ✅ Interactive market analyzer display
- ✅ Deriv account connection interface
- ✅ Clean, professional fintech UI (no gambling visuals)

### 2. Authentication & Security
- ✅ Secure user registration with password validation
- ✅ Password hashing using Argon2id (PHP 8+)
- ✅ PHP session-based authentication with hardening
- ✅ CSRF protection on all forms
- ✅ Rate limiting for login attempts (5 per 30 min)
- ✅ Session hardening (HttpOnly, Secure, SameSite cookies)
- ✅ Account lockout after failed attempts
- ✅ 2FA readiness (database structure)

### 3. Database (MySQL)
- ✅ Users table with UUID, status tracking
- ✅ Encrypted Deriv API token storage (AES-256-GCM)
- ✅ User settings table
- ✅ Trade history / analysis logs (read-only)
- ✅ Audit logs for security tracking
- ✅ Rate limiting table
- ✅ Proper indexing and normalization
- ✅ Views for reporting (user_activity_summary, daily_trading_stats)
- ✅ Triggers for automated actions

### 4. Deriv Integration
- ✅ API token-based authentication (NOT username/password)
- ✅ Token encryption before database storage
- ✅ Backend-only API communication (PHP)
- ✅ Demo account support first
- ✅ Clear permission scopes
- ✅ Token revocation capability

### 5. Analyzer System
- ✅ PHP backend analyzer endpoint
- ✅ Fetches market data from Deriv API
- ✅ Multi-indicator logic (RSI, EMA, Bollinger Bands, ATR)
- ✅ JSON output with:
  - Symbol, Direction bias (LONG/SHORT/NEUTRAL)
  - Probability score (0-100%)
  - Risk level (LOW/MEDIUM/HIGH)
- ✅ Frontend JavaScript to fetch and display analysis

### 6. Trading Logic (Safe Mode)
- ✅ No guaranteed profits language
- ✅ No hidden auto-trading
- ✅ Demo trading emphasized
- ✅ Backend validation of trade rules
- ✅ Comprehensive legal disclaimers

---

## Security Features

| Feature | Implementation |
|---------|---------------|
| Password Hashing | Argon2id with 64MB memory cost |
| Token Encryption | AES-256-GCM with unique IV per token |
| Session Security | HttpOnly, Secure, SameSite=Strict cookies |
| CSRF Protection | Token-based with 1-hour expiry |
| Rate Limiting | Per-IP tracking with lockout |
| SQL Injection | PDO prepared statements |
| XSS Protection | htmlspecialchars output encoding |
| Security Headers | X-XSS-Protection, X-Frame-Options, etc. |

---

## Installation Steps

1. **Upload files** to web server
2. **Create database** and import `database/schema.sql`
3. **Copy `.env.example` to `.env`** and configure:
   - Database credentials
   - Encryption keys (generate with `openssl rand -base64 32`)
   - Deriv App ID
4. **Set permissions**:
   ```bash
   chmod -R 755 /var/www/html/omrix
   chmod -R 775 /var/www/html/omrix/{logs,cache,sessions,uploads}
   chmod 600 /var/www/html/omrix/.env
   ```
5. **Configure Apache** to point to `public/` directory
6. **Enable mod_rewrite** and required modules
7. **Set up SSL** (Let's Encrypt recommended)

---

## Configuration

### Environment Variables (.env)

```bash
# Required
DB_HOST=localhost
DB_NAME=omrix
DB_USER=omrix_user
DB_PASS=your_secure_password
ENCRYPTION_KEY=your-32-byte-key-here!!
ENCRYPTION_KEY_DERIV=your-deriv-key-here!!

# Optional
OMRIX_ENV=production
DERIV_APP_ID=your_app_id
APP_URL=https://yourdomain.com
```

### Security Settings

```php
DERIV_DEMO_ONLY = true;      // Force demo accounts only
FEATURE_REAL_TRADING = false; // Disable real trading
LOGIN_LOCKOUT_ATTEMPTS = 5;   // Failed attempts before lockout
SESSION_LIFETIME = 86400;     // 24 hours
```

---

## API Endpoints

### Analyze Market
```
GET /api/analyze.php?symbol=frxEURUSD&timeframe=1h
```

**Response:**
```json
{
  "success": true,
  "symbol": "frxEURUSD",
  "direction_bias": "LONG",
  "probability_score": 72,
  "risk_level": "MEDIUM",
  "indicators": {
    "rsi": 52.34,
    "ema_fast": 1.08450,
    "ema_slow": 1.08320,
    "bb_upper": 1.08750,
    "bb_lower": 1.08200,
    "atr": 0.00120
  },
  "analysis_summary": "Market showing uptrend conditions...",
  "timestamp": 1704067200
}
```

---

## Technical Indicators Used

| Indicator | Purpose |
|-----------|---------|
| RSI (14) | Overbought/Oversold detection |
| EMA (12, 26) | Trend direction and momentum |
| Bollinger Bands | Volatility and price extremes |
| ATR (14) | Volatility measurement |

---

## Legal & Compliance

- ✅ Risk disclaimer on all pages
- ✅ No "guaranteed profit" language
- ✅ No misleading claims
- ✅ Demo trading emphasized
- ✅ Clear terms of service structure
- ✅ Privacy policy structure
- ✅ API usage disclaimer

---

## Browser Support

- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

---

## Performance

- Database connection pooling (PDO persistent)
- Analysis result caching (30 seconds)
- Gzip compression enabled
- Browser caching for static assets
- Optimized SQL queries with proper indexing

---

## Future Enhancements (Structure Ready)

- Two-factor authentication (2FA)
- Email notifications
- Real-time WebSocket data
- Advanced charting integration
- Mobile app API
- Multi-language support

---

## License & Disclaimer

This is proprietary software. All rights reserved.

**Risk Disclaimer**: Trading derivatives involves substantial risk of loss and is not suitable for all investors. Past performance is not indicative of future results. OMRIX provides analytical tools only and does not guarantee profits.

---

## Support

For technical support or inquiries, contact: support@omrix.com
