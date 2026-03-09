# Документация

Этот раздел содержит техническую документацию по настройке и анализу системы.

## Содержимое

- [architecture.md](architecture.md) — архитектура системы
- [dpi-configuration.md](dpi-configuration.md) — настройка DPI в ViPNet IDS
- [detection-rules.md](detection-rules.md) — правила обнаружения
- [event-analysis.md](event-analysis.md) — анализ событий
- [siem-integration.md](siem-integration.md) — интеграция с SIEM

## Архитектура системы

### Схема сети

```
+------------------+     +------------------+     +-------------------+
|   Kali Linux     |     |  Windows 10/11   |     | ViPNet IDS NS VA  |
|  192.168.10.10   |     |  192.168.20.10   |     |  192.168.30.10    |
|  (DMZ/атака)   |     |  (LAN/цель)     |     | (Management/IDS)  |
+------------------+     +------------------+     +-------------------+
        |                        |                          |
        +----[VMnet1/DMZ]---+---[VMnet2/LAN]---+---[VMnet3/Mgmt]--+
                            |                  |
                       [SPAN порт]-------->[IDS eth1]
```

## DPI (глубокий анализ пакетов)

ViPNet IDS анализирует следующие протоколы:
- HTTP/HTTPS
- FTP
- SMTP, POP3, IMAP
- SMB
- DNS

### Пример DPI-правила для SMB

| Параметр | Значение |
|---------|----------|
| Имя правила | SMBScanDetection |
| Протокол | SMB/TCP |
| Порт | 445 |
| Порог | 10 соединений за 5 секунд |
| Уровень | WARNING |

## Интеграция с SIEM

ViPNet IDS поддерживает экспорт в SIEM через Syslog.

### Настройка Syslog

1. Откройте веб-интерфейс IDS
2. Перейдите в настройки Syslog
3. Укажите IP-адрес SIEM-сервера: `192.168.30.20`
4. Порт: `514/UDP`
5. Формат: `CEF (Common Event Format)`

## Ключевые понятия

| Термин | Описание |
|---------|----------|
| IDS | Intrusion Detection System — система обнаружения вторжений |
| IPS | Intrusion Prevention System — система предотвращения вторжений |
| DPI | Deep Packet Inspection — глубокий анализ пакетов |
| SPAN | Switched Port Analyzer — зеркалирование трафика |
| SIEM | Security Information and Event Management |
| DMZ | Demilitarized Zone — демилитаризованная зона |
| LAN | Local Area Network — локальная сеть |
| OVA | Open Virtualization Archive — формат виртуальной машины |
