# Config Export and Sanitization Guide

## Export Method
Export each device running configuration directly from CLI using:
- `show running-config`
- Copy/paste to a text file named `<DEVICE>-running-config.txt`

Store files in the correct folder:
- `configs/routers/`
- `configs/core-switches/`
- `configs/distribution-switches/`
- `configs/access-switches/`

## Sanitization Rules
Before publishing, replace all sensitive values with placeholders:
- enable secrets
- local user passwords/secrets
- SNMP community strings
- WPA/WPA2 PSKs
- NTP authentication keys
- FTP credentials
- any private tokens or keys

Example placeholder format:
- `<REDACTED_ENABLE_SECRET>`
- `<REDACTED_USER_SECRET>`
- `<REDACTED_SNMP_STRING>`
