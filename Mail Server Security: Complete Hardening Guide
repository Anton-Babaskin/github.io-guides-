# 🔒 Mail Server Security: Complete Hardening Guide

> **Language**: English | [Русский](mail-server-security-guide.ru.md)

## ⚠️ Important Notice

This guide is designed for **bare-metal Dovecot + Postfix installations** on clean Linux servers. If you're using pre-configured mail solutions like:

- **iRedMail** - Most security features are already configured
- **Mail-in-a-Box** - Includes automated security setup  
- **Zimbra** - Has built-in security modules
- **Other packaged solutions**

**Check your existing configuration first!** Many of these settings may already be in place. Use this guide to:
- Verify your current security posture
- Fill any gaps in your setup
- Understand what each component does
- Troubleshoot existing configurations

---

## 🧪 Testing & Diagnostics Tools

### Essential Online Testing Tools

**Mail-Tester.com** - Complete email deliverability testing
```
https://www.mail-tester.com/
```
- Send test email to provided address
- Get detailed spam score analysis
- Check SPF, DKIM, DMARC status
- Review content filtering results
- **Usage**: Send from your server to test@mail-tester.com

**MXToolbox.com** - DNS and mail server diagnostics
```
https://mxtoolbox.com/
```
Key tools:
- **MX Lookup** - Verify MX records
- **SPF Record Lookup** - Test SPF configuration
- **DKIM Lookup** - Validate DKIM records
- **DMARC Lookup** - Check DMARC policy
- **Blacklist Check** - Test if your IP is blacklisted
- **SMTP Test** - Verify SMTP connectivity

**Additional Testing Resources:**
```
# DMARC Analyzer
https://www.dmarcanalyzer.com/

# SSL/TLS Testing  
https://www.ssllabs.com/ssltest/

# Spam Database Checks
https://multirbl.valli.org/

# Email Authentication Tester
https://emailsecuritygrader.com/
```

### Command Line Testing

**Test SPF Record:**
```bash
# Install testing tools
apt install swaks mailutils dig

# Test SPF
dig TXT example.com | grep spf
nslookup -type=TXT example.com

# Send test email with SWAKS
swaks --to test@mail-tester.com --from admin@yourdomain.com --server your.mail.server.com
```

**Test DKIM:**
```bash
# Check DKIM record
dig TXT mail._domainkey.yourdomain.com

# Test DKIM signing
opendkim-testkey -d yourdomain.com -s mail -vvv
```

**Test Mail Flow:**
```bash
# Check if mail is being processed
tail -f /var/log/mail.log

# Test local delivery
echo "Test message" | mail -s "Test" user@yourdomain.com

# Check queue
mailq
postqueue -p
```

---

Production-tested security configurations to protect your mail infrastructure from attacks, spam, and data breaches.

## 📋 Table of Contents

- [DNS Authentication (SPF, DKIM, DMARC)](#dns-authentication)
- [TLS/SSL Configuration](#tlsssl-configuration)
- [Rate Limiting & Brute Force Protection](#rate-limiting--brute-force-protection)
- [Postgrey - Greylisting Protection](#postgrey---greylisting-protection)
- [Advanced Spam Filtering](#advanced-spam-filtering)
- [Connection Security](#connection-security)
- [Testing & Diagnostics Tools](#testing--diagnostics-tools)
- [Monitoring & Alerting](#monitoring--alerting)
- [Firewall Configuration](#firewall-configuration)

---

## 🛡️ DNS Authentication

### SPF (Sender Policy Framework)

**DNS Record Setup:**
```dns
example.com. IN TXT "v=spf1 mx a ip4:192.168.1.100 include:_spf.google.com ~all"
```

**Key parameters:**
- `mx` - allow MX servers
- `a` - allow A record servers  
- `ip4:x.x.x.x` - specific IP addresses
- `include:` - include other SPF records
- `~all` - soft fail (recommended for testing)
- `-all` - hard fail (production)

**Testing SPF:**
```bash
# Check SPF record
dig TXT example.com

# Test SPF validation
spfquery -ip=192.168.1.100 -sender=user@example.com -helo=mail.example.com
```

### DKIM (DomainKeys Identified Mail)

**Generate DKIM keys:**
```bash
# Install opendkim
apt install opendkim opendkim-tools

# Generate keys
mkdir -p /etc/opendkim/keys/example.com
cd /etc/opendkim/keys/example.com
opendkim-genkey -s mail -d example.com
chown opendkim:opendkim mail.private
```

**OpenDKIM configuration `/etc/opendkim.conf`:**
```config
# Basic settings
Syslog                  yes
UMask                   002
OversignHeaders         From
TrustAnchorFile         /usr/share/dns/root.key
UserID                  opendkim

# Security settings
Canonicalization        relaxed/simple
Mode                    sv
SubDomains              no
AutoRestart             yes
Background              yes
SignatureAlgorithm      rsa-sha256

# Keys and domains
KeyTable                /etc/opendkim/key.table
SigningTable            /etc/opendkim/signing.table
ExternalIgnoreList      /etc/opendkim/trusted.hosts
InternalHosts           /etc/opendkim/trusted.hosts

# Socket
Socket                  local:/var/run/opendkim/opendkim.sock
```

**Key table `/etc/opendkim/key.table`:**
```
mail._domainkey.example.com example.com:mail:/etc/opendkim/keys/example.com/mail.private
```

**Signing table `/etc/opendkim/signing.table`:**
```
*@example.com mail._domainkey.example.com
```

**Trusted hosts `/etc/opendkim/trusted.hosts`:**
```
127.0.0.1
localhost
192.168.1.0/24
example.com
mail.example.com
```

**DNS DKIM Record:**
```dns
mail._domainkey.example.com. IN TXT "v=DKIM1; k=rsa; p=YOUR_PUBLIC_KEY_HERE"
```

**Postfix integration `/etc/postfix/main.cf`:**
```config
# DKIM
smtpd_milters = local:opendkim/opendkim.sock
non_smtpd_milters = local:opendkim/opendkim.sock
milter_protocol = 6
milter_default_action = accept
milter_connect_macros = i j {daemon_name} v {if_name} _
```

### DMARC (Domain-based Message Authentication)

**DNS DMARC Record:**
```dns
_dmarc.example.com. IN TXT "v=DMARC1; p=quarantine; rua=mailto:dmarc@example.com; ruf=mailto:dmarc@example.com; fo=1; adkim=s; aspf=s"
```

**DMARC parameters:**
- `p=none` - monitoring only
- `p=quarantine` - suspicious emails to spam
- `p=reject` - block failing emails
- `rua=` - aggregate reports
- `ruf=` - forensic reports
- `fo=1` - generate reports on any failure
- `adkim=s` - strict DKIM alignment
- `aspf=s` - strict SPF alignment

---

## 🔐 TLS/SSL Configuration

### Modern TLS Setup

**Postfix TLS configuration `/etc/postfix/main.cf`:**
```config
# TLS Security
smtpd_tls_security_level = encrypt
smtp_tls_security_level = may
smtpd_tls_auth_only = yes
smtp_tls_note_starttls_offer = yes

# Certificate files
smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem
smtpd_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

# TLS protocols and ciphers
smtpd_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtp_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtpd_tls_ciphers = high
smtp_tls_ciphers = high
smtpd_tls_exclude_ciphers = aNULL, eNULL, EXPORT, DES, RC4, MD5, PSK, SRP, CAMELLIA, SEED

# TLS session cache
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

# Additional security
smtpd_tls_received_header = yes
smtpd_tls_loglevel = 1
smtp_tls_loglevel = 1

# DANE TLSA
smtp_dns_support_level = dnssec
smtp_tls_policy_maps = hash:/etc/postfix/tls_policy
```

**Dovecot TLS configuration `/etc/dovecot/conf.d/10-ssl.conf`:**
```config
ssl = required
ssl_cert = </etc/letsencrypt/live/mail.example.com/fullchain.pem
ssl_key = </etc/letsencrypt/live/mail.example.com/privkey.pem
ssl_ca = </etc/ssl/certs/ca-certificates.crt

ssl_protocols = !SSLv3 !TLSv1 !TLSv1.1
ssl_cipher_list = ECDHE-RSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA384
ssl_prefer_server_ciphers = yes

ssl_dh = </etc/dovecot/dh.pem
```

**Generate strong DH parameters:**
```bash
openssl dhparam -out /etc/dovecot/dh.pem 2048
chown dovecot:dovecot /etc/dovecot/dh.pem
chmod 600 /etc/dovecot/dh.pem
```

---

## 🚧 Rate Limiting & Brute Force Protection

### Postfix Rate Limiting

**Anvil settings `/etc/postfix/main.cf`:**
```config
# Connection limits
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 100
smtpd_client_recipient_rate_limit = 300
smtpd_client_event_limit_exceptions = ${mynetworks}

# Error limits
smtpd_error_sleep_time = 5s
smtpd_soft_error_limit = 2
smtpd_hard_error_limit = 5
smtpd_junk_command_limit = 2

# Recipient limits
smtpd_recipient_limit = 100
message_size_limit = 25600000
```

### Fail2ban Configuration

**Install and configure fail2ban:**
```bash
apt install fail2ban
```

**Postfix jail `/etc/fail2ban/jail.d/postfix.conf`:**
```ini
[postfix-sasl]
enabled = true
port = smtp,465,submission,imap,imaps,pop3,pop3s
filter = postfix-sasl
logpath = /var/log/mail.log
maxretry = 3
bantime = 3600

[postfix-rbl]
enabled = true
port = smtp,465,submission
filter = postfix-rbl
logpath = /var/log/mail.log
maxretry = 1
bantime = 86400

[dovecot]
enabled = true
port = pop3,pop3s,imap,imaps,submission,465,sieve
filter = dovecot
logpath = /var/log/mail.log
maxretry = 3
bantime = 3600
```

**Custom postfix-rbl filter `/etc/fail2ban/filter.d/postfix-rbl.conf`:**
```ini
[Definition]
failregex = reject: RCPT from .*\[<HOST>\]: 554 5\.7\.1 Service unavailable; Client host \[.*\] blocked
            reject: RCPT from .*\[<HOST>\]: 554 5\.7\.1 .*blocked.*
ignoreregex =
```

---

## ⏱️ Postgrey - Greylisting Protection

### Installation and Setup

```bash
apt install postgrey
```

**Postgrey configuration `/etc/default/postgrey`:**
```bash
POSTGREY_OPTS="--inet=10023 --delay=300 --max-age=35 --retry-window=2 --greylist-text=Greylisted"
POSTGREY_TEXT="Your message has been greylisted. Please try again in 5 minutes."
```

**Postfix integration `/etc/postfix/main.cf`:**
```config
smtpd_recipient_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    check_policy_service inet:127.0.0.1:10023,
    permit
```

**Whitelist management `/etc/postgrey/whitelist_clients`:**
```
# Major email providers
google.com
gmail.com  
outlook.com
hotmail.com
yahoo.com
aol.com

# Business services
salesforce.com
mailchimp.com
constantcontact.com
```

**Auto-whitelist script:**
```bash
#!/bin/bash
# /usr/local/bin/postgrey-whitelist-update.sh

# Download fresh whitelists
wget -O /tmp/whitelist_clients.new "https://postgrey.schweikert.ch/pub/postgrey_whitelist_clients"

if [ -s /tmp/whitelist_clients.new ]; then
    cp /tmp/whitelist_clients.new /etc/postgrey/whitelist_clients_auto
    systemctl reload postgrey
    logger "Postgrey whitelist updated successfully"
else
    logger "Failed to update postgrey whitelist"
fi

rm -f /tmp/whitelist_clients.new
```

---

## 🗑️ Advanced Spam Filtering

### SpamAssassin Integration

**Install SpamAssassin:**
```bash
apt install spamassassin spamc
```

**SpamAssassin configuration `/etc/spamassassin/local.cf`:**
```config
# Required score to mark as spam
required_score 5.0

# Network tests
skip_rbl_checks 0
use_network_test 1

# Bayes learning
use_bayes 1
use_bayes_rules 1
bayes_auto_learn 1
bayes_auto_learn_threshold_nonspam 0.1
bayes_auto_learn_threshold_spam 12.0

# Custom rules
score BAYES_00 -1.9
score BAYES_05 -1.9
score BAYES_20 -0.8
score BAYES_40 -0.4
score BAYES_60 0.4
score BAYES_80 0.8
score BAYES_95 3.17
score BAYES_99 5.0

# RBL checks
score RCVD_IN_DNSWL_NONE 0 0 0 0
score RCVD_IN_DNSWL_LOW -0.7 0 -0.7 -0.7
score RCVD_IN_DNSWL_MED -1.4 0 -1.4 -1.4
score RCVD_IN_DNSWL_HI -5 0 -5 -5
```

**Postfix SpamAssassin integration via Amavis:**
```bash
apt install amavisd-new
```

**Amavis configuration `/etc/amavis/conf.d/15-content_filter_mode`:**
```perl
use strict;

# Enable spam and virus checking
@bypass_virus_checks_maps = (
   \%bypass_virus_checks, \@bypass_virus_checks_acl, \$bypass_virus_checks_re);
@bypass_spam_checks_maps = (
   \%bypass_spam_checks, \@bypass_spam_checks_acl, \$bypass_spam_checks_re);

1;  # ensure a defined return
```

### ClamAV Antivirus

**Install and configure ClamAV:**
```bash
apt install clamav clamav-daemon
freshclam
```

**ClamAV configuration `/etc/clamav/clamd.conf`:**
```config
LocalSocket /var/run/clamav/clamd.ctl
FixStaleSocket true
LocalSocketGroup clamav
LocalSocketMode 666
User clamav
TCPSocket 3310
TCPAddr 127.0.0.1
MaxThreads 20
ReadTimeout 180
MaxFileSize 25M
MaxScanSize 100M
MaxRecursion 16
MaxFiles 10000
```

---

## 🌐 Connection Security

### SMTP Security Restrictions

**Advanced SMTP restrictions `/etc/postfix/main.cf`:**
```config
# Recipient restrictions
smtpd_recipient_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_recipient,
    reject_unknown_recipient_domain,
    reject_unauth_destination,
    reject_rbl_client zen.spamhaus.org,
    reject_rbl_client bl.spamcop.net,
    reject_rbl_client cbl.abuseat.org,
    check_policy_service inet:127.0.0.1:10023,
    permit

# Sender restrictions
smtpd_sender_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_sender,
    reject_unknown_sender_domain,
    reject_unlisted_sender,
    permit

# HELO restrictions
smtpd_helo_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_non_fqdn_helo_hostname,
    reject_invalid_helo_hostname,
    reject_unknown_helo_hostname,
    permit

# Data restrictions
smtpd_data_restrictions =
    reject_unauth_pipelining,
    permit
```

### SASL Authentication

**Dovecot SASL configuration `/etc/dovecot/conf.d/10-master.conf`:**
```config
service auth {
  unix_listener /var/spool/postfix/private/auth {
    mode = 0666
    user = postfix
    group = postfix
  }
}
```

**Postfix SASL integration `/etc/postfix/main.cf`:**
```config
# SASL Authentication
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous, noplaintext
smtpd_sasl_tls_security_options = noanonymous
```

---

## 📊 Monitoring & Alerting

### Log Monitoring Script

**Mail security monitor `/usr/local/bin/mail-security-monitor.sh`:**
```bash
#!/bin/bash

LOGFILE="/var/log/mail.log"
ALERT_EMAIL="admin@example.com"
TEMP_DIR="/tmp/mail-monitor"
LOCKFILE="/var/run/mail-monitor.lock"

# Check if already running
if [ -f "$LOCKFILE" ]; then
    exit 1
fi
echo $$ > "$LOCKFILE"

mkdir -p "$TEMP_DIR"

# Check for authentication failures
AUTH_FAILURES=$(tail -n 1000 "$LOGFILE" | grep -c "authentication failed")
if [ "$AUTH_FAILURES" -gt 50 ]; then
    echo "HIGH: $AUTH_FAILURES authentication failures in last 1000 log lines" | \
    mail -s "ALERT: High Authentication Failures" "$ALERT_EMAIL"
fi

# Check for RBL blocks
RBL_BLOCKS=$(tail -n 1000 "$LOGFILE" | grep -c "blocked using")
if [ "$RBL_BLOCKS" -gt 100 ]; then
    echo "INFO: $RBL_BLOCKS RBL blocks in last 1000 log lines" | \
    mail -s "INFO: High RBL Activity" "$ALERT_EMAIL"
fi

# Check mail queue size
QUEUE_SIZE=$(mailq | tail -1 | awk '{print $5}')
if [ "$QUEUE_SIZE" -gt 1000 ]; then
    echo "WARNING: Mail queue size is $QUEUE_SIZE messages" | \
    mail -s "ALERT: Large Mail Queue" "$ALERT_EMAIL"
fi

# Check disk space
DISK_USAGE=$(df /var/spool/postfix | tail -1 | awk '{print $5}' | sed 's/%//')
if [ "$DISK_USAGE" -gt 80 ]; then
    echo "WARNING: Mail spool disk usage is $DISK_USAGE%" | \
    mail -s "ALERT: High Disk Usage" "$ALERT_EMAIL"
fi

# Cleanup
rm -f "$LOCKFILE"
```

### Monitoring with Zabbix/Prometheus

**Postfix exporter for Prometheus:**
```yaml
# docker-compose.yml for postfix-exporter
version: '3'
services:
  postfix-exporter:
    image: kumina/postfix-exporter:latest
    ports:
      - "9154:9154"
    volumes:
      - /var/log:/var/log:ro
    command:
      - '--postfix.logfile_path=/var/log/mail.log'
```

**Key metrics to monitor:**
- Authentication failures per hour
- Queue size and age
- TLS connection percentage
- Spam/virus detection rates
- Delivery success rates
- Disk space usage

---

## 🔥 Firewall Configuration

### UFW Configuration

```bash
# Basic rules
ufw default deny incoming
ufw default allow outgoing

# SSH (change port if needed)
ufw allow 22/tcp

# Email services
ufw allow 25/tcp   # SMTP
ufw allow 465/tcp  # SMTPS
ufw allow 587/tcp  # Submission
ufw allow 993/tcp  # IMAPS
ufw allow 995/tcp  # POP3S

# Optional: HTTP/HTTPS for webmail
ufw allow 80/tcp
ufw allow 443/tcp

# Rate limiting for SMTP
ufw limit 25/tcp
ufw limit 587/tcp

# Enable firewall
ufw --force enable
```

### Advanced iptables Rules

```bash
#!/bin/bash
# /usr/local/bin/mail-firewall.sh

# Flush existing rules
iptables -F
iptables -t nat -F
iptables -t mangle -F

# Default policies
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# Loopback
iptables -I INPUT 1 -i lo -j ACCEPT

# Established connections
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# SSH with rate limiting
iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --set
iptables -A INPUT -p tcp --dport 22 -m state --state NEW -m recent --update --seconds 60 --hitcount 4 -j DROP
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# SMTP with connection limiting
iptables -A INPUT -p tcp --dport 25 -m connlimit --connlimit-above 10 --connlimit-mask 32 -j DROP
iptables -A INPUT -p tcp --dport 25 -m state --state NEW -m recent --set --name smtp
iptables -A INPUT -p tcp --dport 25 -m state --state NEW -m recent --update --seconds 60 --hitcount 30 --name smtp -j DROP
iptables -A INPUT -p tcp --dport 25 -j ACCEPT

# Other mail ports
iptables -A INPUT -p tcp --dport 465 -j ACCEPT  # SMTPS
iptables -A INPUT -p tcp --dport 587 -j ACCEPT  # Submission
iptables -A INPUT -p tcp --dport 993 -j ACCEPT  # IMAPS
iptables -A INPUT -p tcp --dport 995 -j ACCEPT  # POP3S

# Log dropped packets
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7

# Save rules
iptables-save > /etc/iptables/rules.v4
```

---

## 🔍 Security Checklist

### Daily Tasks
- [ ] Check authentication failure logs
- [ ] Monitor mail queue size
- [ ] Review fail2ban reports
- [ ] Verify certificate expiration

### Weekly Tasks  
- [ ] Update spam rules and signatures
- [ ] Review and update whitelists
- [ ] Check disk space usage
- [ ] Analyze mail traffic patterns

### Monthly Tasks
- [ ] Review and update firewall rules
- [ ] Update DMARC policy based on reports
- [ ] Security patches and updates
- [ ] Test backup and restore procedures

---

## 🚨 Incident Response

### Compromised Account Response
```bash
# 1. Disable account immediately
doveadm auth test username@domain.com

# 2. Check for suspicious activity
grep "username@domain.com" /var/log/mail.log | tail -100

# 3. Reset password and force logout
doveadm kick username@domain.com

# 4. Check outbound mail queue for spam
mailq | grep username@domain.com
```

### Spam Outbreak Response
```bash
# 1. Temporary rate limiting
postconf -e "smtpd_client_message_rate_limit = 10"
postfix reload

# 2. Enable stricter RBL checking
postconf -e "smtpd_recipient_restrictions = permit_mynetworks, permit_sasl_authenticated, reject_unauth_destination, reject_rbl_client zen.spamhaus.org, reject_rbl_client bl.spamcop.net, permit"

# 3. Flush suspicious messages
postsuper -d ALL deferred
```

This guide provides comprehensive security hardening for production mail servers. Always test changes in a development environment first and maintain regular backups.
