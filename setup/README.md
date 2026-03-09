# Установка и настройка среды

Этот раздел содержит пошаговые инструкции по установке и настройке всех компонентов симулятора.

## Структура раздела

- [network-setup.md](network-setup.md) — настройка виртуальных сетей
- [kali-setup.md](kali-setup.md) — установка и настройка Kali Linux
- [windows-setup.md](windows-setup.md) — установка и настройка Windows
- [ids-setup.md](ids-setup.md) — развёртывание ViPNet IDS NS VA

## Шаг 1. Настройка виртуальных сетей

Создайте три виртуальных коммутатора (vSwitch) в VMware Workstation через Virtual Network Editor:

| Сеть | Назначение | Адресация | Интерфейс VMware |
|------|-----------|-----------|------------------|
| DMZ | Зона атакующего (Kali Linux) | 192.168.10.0/24 | VMnet1 |
| LAN | Внутренняя сеть (Windows) | 192.168.20.0/24 | VMnet2 |
| Management | Управление IDS | 192.168.30.0/24 | VMnet3 |

> **Важно:** DHCP и NAT отключены. Promiscuous Mode включён для интерфейса IDS.

## Шаг 2. Установка Kali Linux

### Системные требования ВМ
- CPU: 2 ядра
- RAM: 2–4 ГБ
- Диск: 20 ГБ
- Сетевые адаптеры: DMZ + Management

### Начальная настройка

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install metasploit-framework nmap nikto dirb -y
```

### Настройка сетевого интерфейса

Отредактируйте `/etc/network/interfaces`:

```
auto eth0
iface eth0 inet static
  address 192.168.10.10
  netmask 255.255.255.0
  gateway 192.168.10.1
```

## Шаг 3. Установка Windows

### Системные требования ВМ
- CPU: 2 ядра
- RAM: 4 ГБ
- Диск: 40 ГБ
- Сетевой адаптер: LAN (VMnet2)

### Настройка IP-адреса (PowerShell)

```powershell
New-NetIPAddress -InterfaceAlias "Ethernet" -IPAddress 192.168.20.10 -PrefixLength 24 -DefaultGateway 192.168.20.1
Set-DnsClientServerAddress -InterfaceAlias "Ethernet" -ServerAddresses 192.168.20.1
```

### Уязвимые сервисы для тестирования
- **SMB** (порты 445, 139) — EternalBlue
- **RDP** (порт 3389)
- **IIS** (порты 80, 443)

## Шаг 4. Развёртывание ViPNet IDS NS VA

### Импорт OVA-файла

1. Скачайте OVA-файл ViPNet IDS NS VA с официального сайта [InfoTeCS](https://infotecs.ru/)
2. Откройте VMware Workstation: **File → Open** → выберите OVA-файл
3. Задайте параметры ВМ:
   - CPU: 4 ядра
   - RAM: 8 ГБ
   - Диск: 100 ГБ
   - Адаптеры: Management + Monitoring (SPAN)

### Настройка SPAN-порта

ViPNet IDS анализирует трафик через SPAN (Switched Port Analyzer):

1. Откройте параметры ВМ ViPNet IDS
2. Задайте сетевой адаптер `eth1` как Custom → VMnet2 (LAN)
3. Включите Promiscuous Mode для VMnet

### Первичная настройка веб-интерфейса

1. Откройте браузер: `https://192.168.30.10`
2. Войдите с логином `admin`
3. Укажите IP-адреса мониторируемых сетей
4. Настройте интерфейс мониторинга: `eth1` → LAN

## Проверка готовности

После настройки убедитесь:

- [ ] Kali Linux пингует Windows (через DMZ → LAN маршрут)
- [ ] ViPNet IDS видит трафик в интерфейсе мониторинга
- [ ] Веб-интерфейс IDS доступен по адресу `https://192.168.30.10`
- [ ] Все три сети изолированы друг от друга
