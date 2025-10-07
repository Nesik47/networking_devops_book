# 📚 Networking для DevOps інженерів

Повний практичний гід з мережевих технологій для DevOps-спеціалістів. Від основ до просунутих концепцій з реальними прикладами команд та сценаріїв troubleshooting.

## 📖 Зміст

1. **Розуміння мереж**
   - Що таке мережа та Інтернет
   - Мережеві топології
   - Основні протоколи (TCP, UDP, ICMP, DNS)

2. **OSI модель / TCP модель**
   - Порівняння моделей
   - Детальний розбір кожного рівня
   - Практична діагностика по рівнях

3. **IP протокол**
   - IPv4 vs IPv6
   - Публічні vs Приватні IP
   - CIDR та Subnetting
   - NAT

4. **Порти**
   - Well-known ports (0-1023)
   - Ephemeral ports
   - Практичні приклади

5. **Мережева безпека**
   - Firewalls (iptables, UFW, firewalld)
   - Шифрування (SSH, SSL/TLS)
   - VPN (WireGuard, OpenVPN)

6. **Інструменти моніторингу**
   - Базові: ping, traceroute, netstat, ss
   - Просунуті: nmap, tcpdump, Wireshark
   - Troubleshooting сценарії

7. **Безпечна передача даних**
   - SSH, SCP, SFTP
   - rsync
   - SSH тунелі

8. **DNS**
   - Як працює DNS
   - Типи DNS records (A, AAAA, CNAME, MX, TXT)
   - DevOps use cases

9. **HTTP/HTTPS**
   - HTTP методи (GET, POST, PUT, DELETE)
   - Response codes
   - Authentication (Basic, Bearer, OAuth)
   - REST API
   - TLS/SSL

## 🚀 Швидкий старт

### Перевірка мережевого з'єднання
```bash
# Базова діагностика
ping -c 4 google.com
traceroute google.com
dig google.com +short
curl -I https://google.com
```

### Моніторинг портів
```bash
# Локальні відкриті порти
ss -tuln

# Сканування віддаленого хоста
nmap -p 80,443 example.com
```

### SSH підключення
```bash
# Базове підключення
ssh user@server.com

# З тунелем
ssh -L 8080:localhost:80 user@server.com
```

## 💡 Для кого ця книга

- ✅ DevOps інженери
- ✅ SRE спеціалісти
- ✅ System Administrators
- ✅ Backend розробники
- ✅ Студенти IT-спеціальностей

## 🛠️ Що всередині

- 📝 Понад 200 практичних команд
- 🔧 Реальні troubleshooting сценарії
- 🎯 DevOps use cases
- ⚡ Best practices
- 🔒 Security рекомендації

## 🔗 Корисні ресурси

- [Cloudflare Learning](https://www.cloudflare.com/learning/)
- [DigitalOcean Tutorials](https://www.digitalocean.com/community/tutorials)
- [Arch Linux Network Wiki](https://wiki.archlinux.org/title/Network_configuration)

## 🤝 Внесок

Знайшли помилку або хочете додати щось корисне? Створюйте Issue або Pull Request!

## ⭐ Підтримка

Якщо ця книга допомогла вам, поставте зірку ⭐ цьому репозиторію!

---

**Версія:** 1.0 | **Мова:** Українська 🇺🇦
