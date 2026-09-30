# Three-Zone DMZ Firewall Architecture

Design and implementation of a segmented network infrastructure for a
fictional e-commerce company, built for a university infrastructure
security module. Deployed and tested in a virtualised environment
using OPNsense, Nginx and Debian.

## Scenario

A fictional online retailer requiring a secure, scalable public web
presence without exposing internal management systems — a standard
requirement for any organisation running public-facing services
alongside internal infrastructure.

## Architecture

Three-zone network model separating traffic by trust level:

| Zone | Address range | Purpose |
|------|--------------|---------|
| WAN  | DHCP (simulated public IP) | Untrusted internet-facing interface |
| DMZ  | 10.10.10.0/24 | Isolated segment hosting the public web server |
| LAN  | 192.168.100.0/24 | Trusted management zone, host-only access |

- **Firewall:** OPNsense (FreeBSD-based, open-source)
- **Web server:** Nginx on Debian, static IP 10.10.10.10
- **Virtualisation:** VMware Workstation, three isolated virtual network segments

Full network diagram: [`network-diagram.png`](network-diagram.png)

## Security design

- **Default-deny on all interfaces** — only explicitly permitted traffic passes
- **DMZ to LAN: blocked.** A compromised web server cannot reach the
  internal management zone
- **DMZ to WAN: allowed (outbound only).** Permits the web server to
  receive OS and security patches
- **WAN inbound: HTTP only (port 80)**, forwarded via NAT to the DMZ
  web server — no other ports or services exposed externally
- **Firewall administration restricted to the LAN zone**, accessible
  only via host-only networking — no remote management surface on
  WAN or DMZ

Full firewall rule sets are in [`firewall-rules/`](firewall-rules).
Security and operational policies (access control, logging, backup,
patch management) are in [`docs/security-policies.md`](docs/security-policies.md).

## Validation testing

The design wasn't assumed to work — it was tested:

| Test | Result |
|------|--------|
| Web server reachable from outside via WAN IP | Pass — 200 OK |
| Nginx service active and starts on boot | Pass |
| DMZ → WAN outbound connectivity (ping to 8.8.8.8) | Pass — 0% packet loss |
| DMZ → LAN block (ping to LAN gateway) | **Pass** — 100% packet loss, confirming isolation |
![DMZ to LAN block test](screenshots/dmz-to-lan-block-test.png)
| External Nmap SYN scan against WAN interface (1,000 ports) | Pass — only port 80 open, 999 filtered |

The last two are the ones that matter most: proving a compromised
DMZ host can't pivot into the internal network, and confirming no
unintended services are exposed to the internet.

## Limitations and production gap

This is a single-node lab deployment, not a production-ready system.
Documented gaps, and what would close them:

- **HTTP only, no TLS** — production would require HTTPS with TLS 1.2+
- **No IDS/IPS** — OPNsense's Suricata plugin would provide traffic
  inspection this deployment lacks
- **No load balancing / high availability** — a single web server
  instance is a single point of failure at any real scale
- **No penetration testing performed** beyond the Nmap scan used for
  verification — a production system would need a proper pentest
  before going live

## Stack

OPNsense · Nginx · Debian · VMware Workstation · Nmap
