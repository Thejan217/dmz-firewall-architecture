# Security Policies

## Access Control & Passwords

- OPNsense web GUI access restricted to the LAN zone only;
  blocked from WAN and DMZ by firewall rule.
- Minimum 12-character passwords: upper, lower, numeric, special
  characters. Default credentials changed on deployment.
- 90-day password rotation; last 5 passwords cannot be reused.
- Account lockout after 5 failed login attempts.
- Principle of least privilege: Nginx runs under a restricted
  service account, no root privileges.
- Physical/host access to the virtualised environment restricted
  to authorised personnel; VMware Workstation itself is
  password-protected.

## Logging, Backup & Data Security

- Logging enabled on all security-relevant rules (DMZ→LAN block,
  WAN→DMZ NAT forward). Logs retained 90 days minimum.
- Weekly full VM snapshots (OPNsense + web server); monthly
  OPNsense config export. Backups stored on a separate drive.
- Data in transit: HTTPS/TLS 1.2+ required in production
  (documented as a current gap — see README limitations).
- Suspected intrusions reviewed within 24 hours; suspicious
  traffic escalated immediately.
- Security patches applied within 7 days; critical patches
  within 24 hours.
