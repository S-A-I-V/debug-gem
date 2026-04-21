---
tags: [nfc, error, maf, ssl, certificates]
date: 2026-04-20
severity: 🟠 High
status: resolved
---
# Blank Page After Login SSL Rejection

## Error
No visible error. Page loads but is completely blank. DevTools Network tab shows red failed `.js` requests.

## Root Cause
MAF serves microfrontend assets over HTTPS with self-signed certificates. Browsers block these by default. Each port is treated as a separate origin and needs its own exception.

## Fix
```
1. Open DevTools (F12 or Cmd+Opt+I)
2. Go to Network tab
3. Find red/failed .js requests
4. For EACH one:
   a. Right-click → "Open in new tab"
   b. Click "Advanced" → "Proceed to localhost (unsafe)"
   c. Close the tab
5. Repeat for every unique port (8080, 3000, 3001, etc.)
6. Go back to main app tab → Refresh
```

## Time to Fix
~2 min (per browser session).

## Connected To
- [[MAF-CLI]]
- [[NFC-SSL]]
