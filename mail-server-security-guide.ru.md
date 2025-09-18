# 🔒 Безопасность почтового сервера: Полное руководство

> **Язык**: [English](mail-server-security-guide.md) | Русский

## ⚠️ Важное замечание

Это руководство предназначено для **"голых" установок Dovecot + Postfix** на чистых Linux серверах. Если вы используете готовые почтовые решения типа:

- **iRedMail** - Большинство настроек безопасности уже сконфигурировано
- **Mail-in-a-Box** - Включает автоматическую настройку безопасности
- **Zimbra** - Имеет встроенные модули безопасности
- **Другие готовые решения**

**Сначала проверьте существующую конфигурацию!** Многие из этих настроек могут уже быть на месте. Используйте это руководство чтобы:
- Проверить текущий уровень безопасности
- Заполнить пробелы в настройке
- Понять как работает каждый компонент
- Устранить проблемы в существующих конфигурациях

---

Проверенные в production настройки безопасности для защиты почтовой инфраструктуры от атак, спама и утечек данных.

## 📋 Содержание

- [DNS-аутентификация (SPF, DKIM, DMARC)](#dns-аутентификация)
- [Настройка TLS/SSL](#настройка-tlsssl)
- [Ограничение скорости и защита от брутфорса](#ограничение-скорости-и-защита-от-брутфорса)
- [Postgrey - защита через greylisting](#postgrey---защита-через-greylisting)
- [Продвинутая фильтрация спама](#продвинутая-фильтрация-спама)
- [Безопасность соединений](#безопасность-соединений)
- [Инструменты тестирования и диагностики](#инструменты-тестирования-и-диагностики)
- [Мониторинг и уведомления](#мониторинг-и-уведомления)
- [Настройка файрвола](#настройка-файрвола)

---

## 🛡️ DNS-аутентификация

### SPF (Sender Policy Framework)

**Настройка DNS записи:**
```dns
example.com. IN TXT "v=spf1 mx a ip4:192.168.1.100 include:_spf.google.com ~all"
```

**Ключевые параметры:**
- `mx` - разрешить MX серверы
- `a` - разрешить A записи
- `ip4:x.x.x.x` - конкретные IP адреса
- `include:` - включить другие SPF записи
- `~all` - мягкий отказ (рекомендуется для тестирования)
- `-all` - жесткий отказ (для production)

**Тестирование SPF:**
```bash
# Проверка SPF записи
dig TXT example.com

# Тест валидации SPF
spfquery -ip=192.168.1.100 -sender=user@example.com -helo=mail.example.com
```

### DKIM (DomainKeys Identified Mail)

**Генерация DKIM ключей:**
```bash
# Установка opendkim
apt install opendkim opendkim-tools

# Генерация ключей
mkdir -p /etc/opendkim/keys/example.com
cd /etc/opendkim/keys/example.com
opendkim-genkey -s mail -d example.com
chown opendkim:opendkim mail.private
```

**Конфигурация OpenDKIM `/etc/opendkim.conf`:**
```config
# Основные настройки
Syslog                  yes
UMask                   002
OversignHeaders         From
TrustAnchorFile         /usr/share/dns/root.key
UserID                  opendkim

# Настройки безопасности
Canonicalization        relaxed/simple
Mode                    sv
SubDomains              no
AutoRestart             yes
Background              yes
SignatureAlgorithm      rsa-sha256

# Ключи и домены
KeyTable                /etc/opendkim/key.table
SigningTable            /etc/opendkim/signing.table
ExternalIgnoreList      /etc/opendkim/trusted.hosts
InternalHosts           /etc/opendkim/trusted.hosts

# Socket
Socket                  local:/var/run/opendkim/opendkim.sock
```

**Таблица ключей `/etc/opendkim/key.table`:**
```
mail._domainkey.example.com example.com:mail:/etc/opendkim/keys/example.com/mail.private
```

**Таблица подписей `/etc/opendkim/signing.table`:**
```
*@example.com mail._domainkey.example.com
```

**Доверенные хосты `/etc/opendkim/trusted.hosts`:**
```
127.0.0.1
localhost
192.168.1.0/24
example.com
mail.example.com
```

**DNS DKIM запись:**
```dns
mail._domainkey.example.com. IN TXT "v=DKIM1; k=rsa; p=ВАШ_ПУБЛИЧНЫЙ_КЛЮЧ_ЗДЕСЬ"
```

**Интеграция с Postfix `/etc/postfix/main.cf`:**
```config
# DKIM
smtpd_milters = local:opendkim/opendkim.sock
non_smtpd_milters = local:opendkim/opendkim.sock
milter_protocol = 6
milter_default_action = accept
milter_connect_macros = i j {daemon_name} v {if_name} _
```

### DMARC (Domain-based Message Authentication)

**DNS DMARC запись:**
```dns
_dmarc.example.com. IN TXT "v=DMARC1; p=quarantine; rua=mailto:dmarc@example.com; ruf=mailto:dmarc@example.com; fo=1; adkim=s; aspf=s"
```

**Параметры DMARC:**
- `p=none` - только мониторинг
- `p=quarantine` - подозрительные письма в спам
- `p=reject` - блокировать неудачные письма
- `rua=` - агрегированные отчеты
- `ruf=` - форензик отчеты
- `fo=1` - генерировать отчеты при любом сбое
- `adkim=s` - строгое выравнивание DKIM
- `aspf=s` - строгое выравнивание SPF

---

## 🔐 Настройка TLS/SSL

### Современная настройка TLS

**Конфигурация TLS в Postfix `/etc/postfix/main.cf`:**
```config
# Безопасность TLS
smtpd_tls_security_level = encrypt
smtp_tls_security_level = may
smtpd_tls_auth_only = yes
smtp_tls_note_starttls_offer = yes

# Файлы сертификатов
smtpd_tls_cert_file = /etc/letsencrypt/live/mail.example.com/fullchain.pem
smtpd_tls_key_file = /etc/letsencrypt/live/mail.example.com/privkey.pem
smtpd_tls_CAfile = /etc/ssl/certs/ca-certificates.crt

# Протоколы и шифры TLS
smtpd_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtp_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
smtpd_tls_ciphers = high
smtp_tls_ciphers = high
smtpd_tls_exclude_ciphers = aNULL, eNULL, EXPORT, DES, RC4, MD5, PSK, SRP, CAMELLIA, SEED

# Кэш TLS сессий
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

# Дополнительная безопасность
smtpd_tls_received_header = yes
smtpd_tls_loglevel = 1
smtp_tls_loglevel = 1

# DANE TLSA
smtp_dns_support_level = dnssec
smtp_tls_policy_maps = hash:/etc/postfix/tls_policy
```

**Конфигурация TLS в Dovecot `/etc/dovecot/conf.d/10-ssl.conf`:**
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

**Генерация сильных DH параметров:**
```bash
openssl dhparam -out /etc/dovecot/dh.pem 2048
chown dovecot:dovecot /etc/dovecot/dh.pem
chmod 600 /etc/dovecot/dh.pem
```

---

## 🚧 Ограничение скорости и защита от брутфорса

### Ограничения в Postfix

**Настройки Anvil `/etc/postfix/main.cf`:**
```config
# Ограничения соединений
smtpd_client_connection_count_limit = 10
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 100
smtpd_client_recipient_rate_limit = 300
smtpd_client_event_limit_exceptions = ${mynetworks}

# Ограничения ошибок
smtpd_error_sleep_time = 5s
smtpd_soft_error_limit = 2
smtpd_hard_error_limit = 5
smtpd_junk_command_limit = 2

# Ограничения получателей
smtpd_recipient_limit = 100
message_size_limit = 25600000
```

### Настройка Fail2ban

**Установка и настройка fail2ban:**
```bash
apt install fail2ban
```

**Jail для Postfix `/etc/fail2ban/jail.d/postfix.conf`:**
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

---

## ⏱️ Postgrey - защита через greylisting

### Установка и настройка

```bash
apt install postgrey
```

**Конфигурация Postgrey `/etc/default/postgrey`:**
```bash
POSTGREY_OPTS="--inet=10023 --delay=300 --max-age=35 --retry-window=2 --greylist-text=Greylisted"
POSTGREY_TEXT="Ваше сообщение помещено в серый список. Попробуйте повторить отправку через 5 минут."
```

**Интеграция с Postfix `/etc/postfix/main.cf`:**
```config
smtpd_recipient_restrictions =
    permit_mynetworks,
    permit_sasl_authenticated,
    reject_unauth_destination,
    check_policy_service inet:127.0.0.1:10023,
    permit
```

**Управление белым списком `/etc/postgrey/whitelist_clients`:**
```
# Основные email провайдеры
google.com
gmail.com  
outlook.com
hotmail.com
yahoo.com
yandex.ru
mail.ru

# Бизнес сервисы
salesforce.com
mailchimp.com
```

---

## 🗑️ Продвинутая фильтрация спама

### Интеграция SpamAssassin

**Установка SpamAssassin:**
```bash
apt install spamassassin spamc
```

**Конфигурация SpamAssassin `/etc/spamassassin/local.cf`:**
```config
# Требуемый балл для маркировки как спам
required_score 5.0

# Сетевые тесты
skip_rbl_checks 0
use_network_test 1

# Bayes обучение
use_bayes 1
use_bayes_rules 1
bayes_auto_learn 1
bayes_auto_learn_threshold_nonspam 0.1
bayes_auto_learn_threshold_spam 12.0

# Пользовательские правила
score BAYES_00 -1.9
score BAYES_05 -1.9
score BAYES_20 -0.8
score BAYES_40 -0.4
score BAYES_60 0.4
score BAYES_80 0.8
score BAYES_95 3.17
score BAYES_99 5.0
```

### Антивирус ClamAV

**Установка и настройка ClamAV:**
```bash
apt install clamav clamav-daemon
freshclam
```

---

## 🧪 Инструменты тестирования и диагностики

### Основные онлайн инструменты

**Mail-Tester.com** - полное тестирование доставки почты
```
https://www.mail-tester.com/
```
- Отправьте тестовое письмо на предоставленный адрес
- Получите детальный анализ спам-балла
- Проверьте статус SPF, DKIM, DMARC
- Просмотрите результаты фильтрации контента
- **Использование**: отправьте с вашего сервера на test@mail-tester.com

**MXToolbox.com** - диагностика DNS и почтовых серверов
```
https://mxtoolbox.com/
```
Ключевые инструменты:
- **MX Lookup** - проверка MX записей
- **SPF Record Lookup** - тест конфигурации SPF
- **DKIM Lookup** - валидация DKIM записей
- **DMARC Lookup** - проверка политики DMARC
- **Blacklist Check** - тест, не в черном ли списке ваш IP
- **SMTP Test** - проверка SMTP подключения

**Дополнительные ресурсы для тестирования:**
```
# DMARC анализатор
https://www.dmarcanalyzer.com/

# Тестирование SSL/TLS
https://www.ssllabs.com/ssltest/

# Проверка спам-баз
https://multirbl.valli.org/

# Тестер email аутентификации
https://emailsecuritygrader.com/
```

### Тестирование через командную строку

**Тест SPF записи:**
```bash
# Установка инструментов тестирования
apt install swaks mailutils dig

# Тест SPF
dig TXT example.com | grep spf
nslookup -type=TXT example.com

# Отправка тестового письма с SWAKS
swaks --to test@mail-tester.com --from admin@yourdomain.com --server your.mail.server.com
```

**Тест DKIM:**
```bash
# Проверка DKIM записи
dig TXT mail._domainkey.yourdomain.com

# Тест подписи DKIM
opendkim-testkey -d yourdomain.com -s mail -vvv
```

---

## 📊 Мониторинг и уведомления

### Скрипт мониторинга безопасности

**Монитор безопасности почты `/usr/local/bin/mail-security-monitor.sh`:**
```bash
#!/bin/bash

LOGFILE="/var/log/mail.log"
ALERT_EMAIL="admin@example.com"
TEMP_DIR="/tmp/mail-monitor"
LOCKFILE="/var/run/mail-monitor.lock"

# Проверка на запуск
if [ -f "$LOCKFILE" ]; then
    exit 1
fi
echo $$ > "$LOCKFILE"

mkdir -p "$TEMP_DIR"

# Проверка неудачных аутентификаций
AUTH_FAILURES=$(tail -n 1000 "$LOGFILE" | grep -c "authentication failed")
if [ "$AUTH_FAILURES" -gt 50 ]; then
    echo "ВЫСОКИЙ: $AUTH_FAILURES неудачных аутентификаций в последних 1000 строках лога" | \
    mail -s "ТРЕВОГА: Высокое число неудачных аутентификаций" "$ALERT_EMAIL"
fi

# Проверка размера очереди
QUEUE_SIZE=$(mailq | tail -1 | awk '{print $5}')
if [ "$QUEUE_SIZE" -gt 1000 ]; then
    echo "ВНИМАНИЕ: Размер очереди почты $QUEUE_SIZE сообщений" | \
    mail -s "ТРЕВОГА: Большая очередь почты" "$ALERT_EMAIL"
fi

# Очистка
rm -f "$LOCKFILE"
```

---

## 🔥 Настройка файрвола

### Конфигурация UFW

```bash
# Базовые правила
ufw default deny incoming
ufw default allow outgoing

# SSH (измените порт при необходимости)
ufw allow 22/tcp

# Почтовые сервисы
ufw allow 25/tcp   # SMTP
ufw allow 465/tcp  # SMTPS
ufw allow 587/tcp  # Submission
ufw allow 993/tcp  # IMAPS
ufw allow 995/tcp  # POP3S

# Ограничение скорости для SMTP
ufw limit 25/tcp
ufw limit 587/tcp

# Включить файрвол
ufw --force enable
```

---

## 🔍 Чек-лист безопасности

### Ежедневные задачи
- [ ] Проверка логов неудачных аутентификаций
- [ ] Мониторинг размера очереди почты
- [ ] Просмотр отчетов fail2ban
- [ ] Проверка срока действия сертификатов

### Еженедельные задачи
- [ ] Обновление спам-правил и сигнатур
- [ ] Просмотр и обновление белых списков
- [ ] Проверка использования дискового пространства
- [ ] Анализ паттернов почтового трафика

### Ежемесячные задачи
- [ ] Просмотр и обновление правил файрвола
- [ ] Обновление политики DMARC на основе отчетов
- [ ] Установка патчей безопасности и обновлений
- [ ] Тестирование процедур резервного копирования и восстановления

Это руководство предоставляет комплексную защиту для production почтовых серверов. Всегда тестируйте изменения в dev-среде и поддерживайте регулярные резервные копии.
