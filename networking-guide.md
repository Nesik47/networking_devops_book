# Networking для DevOps інженерів
## Повний практичний гід

---

# Зміст

1. [Розуміння мереж](#1-розуміння-мереж)
2. [OSI модель / TCP модель](#2-osi-модель--tcp-модель)
3. [Розуміння IP протоколу](#3-розуміння-ip-протоколу)
4. [Розуміння портів](#4-розуміння-портів)
5. [Основи мережевої безпеки](#5-основи-мережевої-безпеки)
6. [Інструменти для мережевого моніторингу](#6-інструменти-для-мережевого-моніторингу)
7. [Безпечна передача даних між серверами](#7-безпечна-передача-даних-між-серверами)
8. [DNS](#8-dns)
9. [HTTP поглиблене вивчення](#9-http-поглиблене-вивчення)

---

# 1. Розуміння мереж

## 1.1 Що таке мережа?

**Мережа** - це з'єднання двох або більше пристроїв, які можуть обмінюватися даними.

**Основні компоненти:**
- Вузли (комп'ютери, сервери, принтери)
- З'єднання (кабелі, WiFi)
- Протоколи (правила обміну)
- Мережеве обладнання (роутери, комутатори)

**Типи за розміром:**
- **PAN** - Персональна (Bluetooth)
- **LAN** - Локальна (офіс, дім)
- **MAN** - Міська
- **WAN** - Глобальна (Інтернет)

## 1.2 Що таке Інтернет?

**Інтернет** - глобальна мережа мереж, що з'єднує мільярди пристроїв через TCP/IP протоколи.

**Ключові концепції:**
- Децентралізація
- Packet Switching
- ISP (провайдери)
- Backbone (високошвидкісні з'єднання)

## 1.3 Мережеві топології

**Bus (Шина):** Всі до одного кабелю
**Star (Зірка):** Всі до центрального комутатора ⭐ Найпопулярніша
**Ring (Кільце):** Коло з'єднань
**Mesh (Повнозв'язна):** Кожен з кожним
**Hybrid:** Комбінація типів

**Домашня мережа:**
```
Internet → [Modem/Router] → WiFi/Ethernet → Пристрої
```

**Команди для дослідження:**
```bash
ip addr show          # Показати IP
nmap -sn 192.168.1.0/24  # Знайти пристрої
traceroute google.com    # Шлях до сервера
```

## 1.4 Протоколи

**IP** - адресація та маршрутизація
**TCP** - надійна доставка (HTTP, SSH, FTP)
**UDP** - швидка доставка без гарантій (DNS, streaming)
**ICMP** - діагностика (ping)
**ARP** - IP → MAC
**DHCP** - автоматичні IP адреси
**DNS** - домени → IP

---

# 2. OSI модель / TCP модель

## 2.1 Порівняння моделей

```
OSI (7 рівнів)          TCP/IP (4 рівні)
------------------      -----------------
7. Application          4. Application
6. Presentation         (HTTP, FTP, SSH)
5. Session              
------------------      -----------------
4. Transport            3. Transport
                        (TCP, UDP)
------------------      -----------------
3. Network              2. Internet
                        (IP, ICMP)
------------------      -----------------
2. Data Link            1. Network Access
1. Physical             (Ethernet, WiFi)
```

## 2.2 Рівні OSI детально

### Рівень 1: Physical
- Біти через фізичне середовище
- Кабелі, WiFi, хаби
- Характеристики: bandwidth, роз'єми

### Рівень 2: Data Link
- Надійна передача між сусідами
- **MAC адреси** (00:1A:2B:3C:4D:5E)
- **Switch** працює тут
- Ethernet фрейми

```bash
ip link show  # Показати MAC
```

### Рівень 3: Network
- Маршрутизація через мережі
- **IP адреси**
- **Router** працює тут
- Визначає шлях пакетів

```bash
ip route show      # Таблиця маршрутизації
traceroute google.com
```

### Рівень 4: Transport
- Доставка процес-до-процесу
- **Порти** (80, 443, 22)
- TCP (надійний) vs UDP (швидкий)

**TCP three-way handshake:**
```
Client → Server: SYN
Client ← Server: SYN-ACK
Client → Server: ACK
```

```bash
netstat -an  # З'єднання
ss -tuln     # Listening порти
```

### Рівень 5: Session
- Встановлення сесій
- Checkpoints
- Менш важливий в сучасних додатках

### Рівень 6: Presentation
- Форматування даних
- Шифрування (SSL/TLS)
- Стиснення
- JPEG, PNG, ASCII

### Рівень 7: Application
- Взаємодія з користувачем
- HTTP, FTP, SSH, DNS, SMTP
- Більшість DevOps інструментів тут

## Діагностика по рівнях

```bash
# Layer 1-2: Інтерфейс
ip link show

# Layer 3: Ping
ping 8.8.8.8

# Layer 4: Порт
nc -zv server.com 80

# Layer 7: HTTP
curl -I https://google.com
```

---

# 3. Розуміння IP протоколу

## 3.1 IPv4 vs IPv6

**IPv4:**
- 32 біти: 192.168.1.1
- ~4.3 мільярда адрес
- Проблема вичерпання
- Використовує NAT

**IPv6:**
- 128 біт: 2001:0db8:85a3::8a2e:0370:7334
- ~340 ундециліонів адрес
- Спрощений header
- Вбудована безпека

```bash
ping6 google.com
ip -6 addr show
```

## 3.2 Публічні vs Приватні IP

**Приватні діапазони (RFC 1918):**
```
10.0.0.0/8          (16M адрес)
172.16.0.0/12       (1M адрес)
192.168.0.0/16      (65K адрес)
```

**Спеціальні:**
- 127.0.0.1 - localhost
- 169.254.0.0/16 - link-local (APIPA)
- 0.0.0.0 - будь-яка адреса

**Дізнатись публічну IP:**
```bash
curl ifconfig.me
curl ipinfo.io
```

### NAT (Network Address Translation)

Багато приватних IP → одна публічна IP

```
Дім: 192.168.1.10, 192.168.1.11
  ↓
Router NAT: 203.0.113.42
  ↓
Інтернет
```

**Типи NAT:**
- Static NAT (1:1)
- Dynamic NAT (пул адрес)
- PAT/NAT Overload (найпопулярніший)

## 3.3 CIDR та Subnetting

**CIDR нотація:** 192.168.1.0/24

**/24** = маска 255.255.255.0 = 254 хости

**Формула:**
```
Хостів = 2^(32 - prefix) - 2

/24: 2^8 - 2 = 254
/25: 2^7 - 2 = 126
/26: 2^6 - 2 = 62
```

**Таблиця CIDR:**
```
/8   → 16M хостів
/16  → 65K хостів
/24  → 254 хости  ← типова мала мережа
/25  → 126 хостів
/26  → 62 хости
/27  → 30 хостів
/30  → 2 хости (point-to-point)
/32  → 1 хост
```

**Приклад поділу /24:**
```
192.168.1.0/24 → поділити на 4

192.168.1.0/26   (1-62)
192.168.1.64/26  (65-126)
192.168.1.128/26 (129-190)
192.168.1.192/26 (193-254)
```

**Інструменти:**
```bash
ipcalc 192.168.1.0/24
```

---

# 4. Розуміння портів

## 4.1 Навіщо порти?

**Порт** - 16-bit число (0-65535) для ідентифікації процесу.

**Аналогія:**
- IP адреса = адреса будинку
- Порт = номер квартири

**Структура з'єднання:**
```
192.168.1.10:50234 → 142.250.185.46:443
(ваш комп)              (Google HTTPS)
```

## 4.2 Well-known ports (0-1023)

**Веб:**
```
20/21  - FTP
22     - SSH
23     - Telnet (НЕ використовувати!)
25     - SMTP
53     - DNS
80     - HTTP
110    - POP3
143    - IMAP
443    - HTTPS
```

**Бази даних:**
```
1433   - MS SQL Server
3306   - MySQL/MariaDB
5432   - PostgreSQL
6379   - Redis
27017  - MongoDB
```

**DevOps:**
```
2375/2376  - Docker
3000       - Grafana
5000       - Docker Registry
6443       - Kubernetes API
8080       - Jenkins
9090       - Prometheus
```

**Перевірка портів:**
```bash
# Локальні
netstat -tuln
ss -tuln

# Віддалені
nc -zv google.com 80
nmap -p 80,443 google.com

# Який процес використовує порт
lsof -i :80
```

## 4.3 Ephemeral ports (49152-65535)

Тимчасові порти для клієнтських з'єднань.

**Діапазон:**
```bash
# Linux
cat /proc/sys/net/ipv4/ip_local_port_range
# 32768-60999

# Змінити
echo "10000 65000" > /proc/sys/net/ipv4/ip_local_port_range
```

**Проблема вичерпання портів:**
```
Симптоми: "Address already in use"
Причини: Багато з'єднань, TIME_WAIT

Рішення:
sysctl -w net.ipv4.tcp_tw_reuse=1
sysctl -w net.ipv4.tcp_fin_timeout=30
```

---

# 5. Основи мережевої безпеки

## 5.1 Firewalls

### iptables (Linux)

**Базові правила:**
```bash
# Дозволити SSH
iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Дозволити HTTP/HTTPS
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Дозволити встановлені з'єднання
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Заблокувати все інше
iptables -A INPUT -j DROP

# Rate limiting (anti-DDoS)
iptables -A INPUT -p tcp --dport 80 -m limit --limit 25/minute -j ACCEPT
```

**Управління:**
```bash
iptables -L -v -n           # Показати правила
iptables -D INPUT 5         # Видалити правило
iptables -F                 # Очистити все
iptables-save > rules.v4    # Зберегти
```

### UFW (Ubuntu)

```bash
ufw enable
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw allow from 192.168.1.0/24 to any port 3306
ufw status verbose
```

### firewalld (CentOS/RHEL)

```bash
firewall-cmd --zone=public --add-service=http --permanent
firewall-cmd --zone=public --add-port=8080/tcp --permanent
firewall-cmd --reload
firewall-cmd --list-all
```

## 5.2 Шифрування

### Симетричне (AES)
Один ключ для шифрування та дешифрування.

```bash
# OpenSSL
openssl enc -aes-256-cbc -salt -in file.txt -out file.enc
openssl enc -aes-256-cbc -d -in file.enc -out file.txt
```

### Асиметричне (RSA, Ed25519)
Пара ключів: публічний + приватний.

**SSH ключі:**
```bash
# Генерувати (Ed25519 - рекомендовано)
ssh-keygen -t ed25519 -C "your_email@example.com"

# Копіювати на сервер
ssh-copy-id user@server.com

# Права
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_ed25519
```

### SSL/TLS

**Let's Encrypt:**
```bash
# Встановити Certbot
apt install certbot python3-certbot-nginx

# Отримати сертифікат
certbot --nginx -d example.com

# Оновити
certbot renew
```

**Nginx HTTPS:**
```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_protocols TLSv1.3 TLSv1.2;
    
    location / {
        proxy_pass http://localhost:8080;
    }
}
```

### VPN

**WireGuard:**
```bash
# Генерувати ключі
wg genkey | tee privatekey | wg pubkey > publickey

# /etc/wireguard/wg0.conf
[Interface]
PrivateKey = SERVER_PRIVATE_KEY
Address = 10.0.0.1/24
ListenPort = 51820

[Peer]
PublicKey = CLIENT_PUBLIC_KEY
AllowedIPs = 10.0.0.2/32

# Запустити
wg-quick up wg0
```

---

# 6. Інструменти для мережевого моніторингу

## 6.1 Базові інструменти

### ping
```bash
ping google.com
ping -c 4 google.com       # 4 пакети
ping -s 1000 google.com    # Розмір пакета
```

### traceroute
```bash
traceroute google.com
traceroute -T -p 80 google.com  # TCP
```

### netstat / ss
```bash
# Активні з'єднання
netstat -tuln
ss -tuln

# Статистика
netstat -s
ss -s

# Процеси
netstat -tulnp
ss -tulnp
```

### nmap
```bash
nmap 192.168.1.1           # Scan хоста
nmap 192.168.1.0/24        # Scan підмережі
nmap -p 80,443 192.168.1.1 # Конкретні порти
nmap -p- 192.168.1.1       # Всі порти
nmap -sV 192.168.1.1       # Версії сервісів
nmap -O 192.168.1.1        # Визначити ОС
```

### tcpdump
```bash
tcpdump -i eth0
tcpdump -i eth0 -w capture.pcap
tcpdump -r capture.pcap
tcpdump -i eth0 port 80
tcpdump -i eth0 'tcp port 80 and host 192.168.1.10'
```

### nc (netcat)
```bash
nc -zv google.com 80       # Перевірка порту
nc -l 8080                 # TCP сервер
nc server_ip 8080          # Підключитись

# Передача файлу
nc -l 8080 > file.txt      # Отримувач
nc server_ip 8080 < file.txt  # Відправник
```

### curl
```bash
curl https://api.example.com
curl -I https://google.com  # Headers
curl -v https://google.com  # Verbose
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John"}'
```

### dig
```bash
dig google.com
dig google.com +short
dig @8.8.8.8 google.com
dig google.com MX
dig google.com AAAA
dig google.com +trace
```

## 6.2 Моніторинг

### iftop (bandwidth)
```bash
iftop -i eth0
iftop -P -i eth0  # Показати порти
```

### nethogs (по процесах)
```bash
nethogs eth0
```

### iperf3 (тест bandwidth)
```bash
# Сервер
iperf3 -s

# Клієнт
iperf3 -c server_ip
iperf3 -c server_ip -u -b 100M  # UDP
```

### mtr (ping + traceroute)
```bash
mtr google.com
mtr --report google.com
```

## 6.3 Troubleshooting сценарії

**Сайт не відкривається:**
```bash
dig example.com +short     # DNS працює?
ping example.com           # Доступний?
nc -zv example.com 80      # Порт відкритий?
curl -I https://example.com
```

**Повільна мережа:**
```bash
ping -c 100 google.com     # Latency?
mtr --report google.com    # Packet loss?
nethogs eth0               # Хто споживає?
```

**Не можу підключитись:**
```bash
ping server_ip             # Доступний?
nc -zv server_ip 22        # Порт відкритий?
iptables -L -v -n          # Firewall блокує?
systemctl status sshd      # Сервіс працює?
```

---

# 7. Безпечна передача даних між серверами

## 7.1 SSH

**Підключення:**
```bash
ssh user@server.com
ssh -p 2222 user@server.com
ssh -i ~/.ssh/id_rsa user@server.com
ssh -v user@server.com  # Debug
```

**SSH config (~/.ssh/config):**
```
Host myserver
    HostName server.example.com
    User deploy
    Port 2222
    IdentityFile ~/.ssh/id_rsa_deploy

Host internal
    HostName 10.0.1.10
    ProxyJump bastion.com
```

**SSH тунелі:**
```bash
# Local port forwarding
ssh -L 8080:localhost:80 user@server.com
# localhost:8080 → server:80

# Remote port forwarding
ssh -R 8080:localhost:80 user@server.com
# server:8080 → local:80

# SOCKS proxy
ssh -D 8080 user@server.com
```

## 7.2 SCP

```bash
scp file.txt user@server:/path/
scp user@server:/path/file.txt .
scp -r directory/ user@server:/path/
scp -P 2222 file.txt user@server:/path/
```

## 7.3 SFTP

```bash
sftp user@server.com

# Команди:
ls, cd, pwd        # Сервер
lls, lcd, lpwd     # Локально
get file.txt       # Завантажити
put file.txt       # Відправити
```

## 7.4 rsync

```bash
# Базова синхронізація
rsync -avz /local/ user@server:/remote/

# З прогресом
rsync -avzP /local/ user@server:/remote/

# Видаляти на призначенні
rsync -avz --delete /local/ user@server:/remote/

# Виключити файли
rsync -avz --exclude '*.log' /local/ user@server:/remote/

# Через SSH порт
rsync -avz -e "ssh -p 2222" /local/ user@server:/remote/
```

---

# 8. DNS

## 8.1 Розуміння DNS

**DNS** - перетворює домени (google.com) в IP (142.250.185.46).

**Ієрархія:**
```
. (Root)
  ↓
.com
  ↓
google.com
  ↓
www.google.com
```

**DNS lookup процес:**
```
1. Браузер → Local cache
2. OS → Recursive resolver (8.8.8.8)
3. Recursive → Root servers
4. Recursive → TLD servers (.com)
5. Recursive → Authoritative DNS (ns1.google.com)
6. Відповідь: google.com = 142.250.185.46
```

## 8.2 Основні DNS records

### A Record
Домен → IPv4
```
example.com.  IN  A  203.0.113.10
```

### AAAA Record
Домен → IPv6
```
example.com.  IN  AAAA  2001:0db8::1
```

### CNAME Record
Alias для домену
```
www.example.com.  IN  CNAME  example.com.
```

### MX Record
Mail сервери
```
example.com.  IN  MX  10  mail.example.com.
```

### TXT Record
Довільний текст
```
example.com.  IN  TXT  "v=spf1 include:_spf.google.com ~all"
```

### NS Record
Name servers
```
example.com.  IN  NS  ns1.example.com.
```

## 8.3 DevOps use cases

**Service Discovery:**
```
myservice.default.svc.cluster.local
```

**Load Balancing:**
```
example.com.  IN  A  203.0.113.1
example.com.  IN  A  203.0.113.2
example.com.  IN  A  203.0.113.3
```

**Blue-Green Deployments:**
```
www.example.com.  IN  CNAME  blue.example.com.
# Switch to:
www.example.com.  IN  CNAME  green.example.com.
```

**Internal DNS:**
```
db-master.internal  → 10.0.1.10
cache.internal      → 10.0.2.20
```

---

# 9. HTTP поглиблене вивчення

## 9.1 HTTP Methods

### GET - Читання
```bash
curl -X GET "https://api.example.com/users?page=1"
```
Характеристики: безпечний, ідемпотентний, кешується

### POST - Створення
```bash
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John"}'
```
Характеристики: не безпечний, не ідемпотентний

### PUT - Повне оновлення
```bash
curl -X PUT https://api.example.com/users/123 \
  -d '{"name":"John Updated"}'
```
Характеристики: ідемпотентний, повна заміна

### PATCH - Часткове оновлення
```bash
curl -X PATCH https://api.example.com/users/123 \
  -d '{"name":"John"}'
```
Оновлює тільки вказані поля

### DELETE - Видалення
```bash
curl -X DELETE https://api.example.com/users/123
```
Характеристики: ідемпотентний

## 9.2 Response Codes

**2xx - Success:**
- 200 OK
- 201 Created
- 204 No Content

**3xx - Redirection:**
- 301 Moved Permanently
- 302 Found
- 304 Not Modified

**4xx - Client Error:**
- 400 Bad Request
- 401 Unauthorized
- 403 Forbidden
- 404 Not Found
- 429 Too Many Requests

**5xx - Server Error:**
- 500 Internal Server Error
- 502 Bad Gateway
- 503 Service Unavailable
- 504 Gateway Timeout

## 9.3 Authentication

### Basic Auth
```bash
curl -u user:password https://api.example.com
```

### Bearer Token (JWT)
```bash
curl -H "Authorization: Bearer TOKEN" https://api.example.com
```

### API Keys
```bash
curl -H "X-API-Key: abc123" https://api.example.com
```

## 9.4 REST API

**RESTful URLs:**
```
GET    /api/users           # Список
GET    /api/users/123       # Один
POST   /api/users           # Створити
PUT    /api/users/123       # Оновити
DELETE /api/users/123       # Видалити
```

**Response format:**
```json
{
  "data": [...],
  "meta": {
    "total": 100,
    "page": 1,
    "per_page": 20
  },
  "links": {
    "next": "/api/users?page=2"
  }
}
```

## 9.5 HTTPS

**Port:** 443

**TLS Handshake:**
```
1. Client → Server: ClientHello
2. Server → Client: Certificate
3. Client перевіряє сертифікат
4. Обмін ключами
5. Зашифрована комунікація
```

**Security Headers:**
```
Strict-Transport-Security: max-age=31536000
Content-Security-Policy: default-src 'self'
X-Frame-Options: DENY
```

---

# Заключення

## Ключові Takeaways

1. **OSI/TCP моделі** - основа troubleshooting
2. **IP та subnetting** - критично для cloud
3. **Ports** - розуміти well-known та ephemeral
4. **Firewall** - безпека не optional
5. **SSH** - безпечна робота з серверами
6. **DNS** - service discovery в DevOps
7. **HTTP/HTTPS** - основа API та веб-сервісів

## Практичні навички

```bash
# Діагностика в одному скрипті
ping -c 3 $HOST
traceroute $HOST
dig $HOST +short
nc -zv $HOST 80
curl -I https://$HOST
```

## Наступні кроки

- Налаштуйте власний DNS
- Створіть VPN між серверами
- Розгорніть API з TLS
- Налаштуйте моніторинг мережі
- Практикуйте troubleshooting

## Корисні ресурси

- https://www.cloudflare.com/learning/
- https://www.digitalocean.com/community/tutorials
- https://wiki.archlinux.org/title/Network_configuration

---

**Написано для DevOps інженерів. Практично. Готово до використання.** 🚀

---

**Версія:** 1.0.1  
**Дата:** 2025  
**Автор:** Nestor Yakymets 
