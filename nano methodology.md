# Methodology & Commands Log

## DNS Reconnaissance

```bash
# SPF Record Discovery
dig TXT itskillscentre.com.ng +short

# Resolving included domain
dig A websitewelcome.com +short

# Reverse DNS Lookup (PTR Record)
dig -x 192.185.93.75 +short
```

## Origin IP Verification

```bash
# Direct IP access
curl -I http://192.185.93.75/

# Access with Host Header
curl -I -H "Host: itskillscentre.com.ng" http://192.185.93.75/

# Forced resolution via curl
curl --resolve itskillscentre.com.ng:80:192.185.93.75 -I http://itskillscentre.com.ng/
```

## Application Testing

```bash
# WAF Bypass Attempts
curl -X PURGE https://itskillscentre.com.ng/
curl -H "X-Original-URL: /admin" https://itskillscentre.com.ng/

# Sensitive File Discovery
curl -I https://itskillscentre.com.ng/.env
curl -I https://itskillscentre.com.ng/robots.txt
```
