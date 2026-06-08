# MEPHI Session Project 2026 — Fedora 43

> Журнал выполнения учебного задания по администрированию Linux.  
> Обновляется по мере прохождения разделов.

---

## Данные студента

| Параметр | Значение |
|----------|----------|
| Номер зачётной книжки | **371205** |
| Текст web-страницы | `Hello from Student: 371205` |
| GitHub логин | **Axer-me** |
| Репозиторий (целевой) | https://github.com/Axer-me/mephi-session-project-2026 |
| VM в VirtualBox | `fedora-mephi` |
| Hostname (целевой) | `mephi-2026.domain.local` |
| Статический IP (целевой) | `192.168.1.100/24` |
| Шлюз / DNS | `192.168.1.1` / `8.8.8.8` |
| Пользователь (целевой) | `mephi-admin` / `P@ssw0rd2026` |
| Группа (целевая) | `mephi-devs` |
| Второй диск | **Да** (`/dev/sdb` — для раздела 3.1) |

### Уточнить позже

- [x] Имя сетевого интерфейса: **enp0s3** (Host-Only), **enp0s8** (NAT)
- [x] Сеть VirtualBox: Адаптер 1 — Виртуальный адаптер, Адаптер 2 — NAT
- [x] SSH: `ssh mephi-admin@192.168.1.100`

---

## Текущий статус

**Этап:** Раздел 1 выполнен (сеть, ping).  
**Следующий шаг:** GitHub — scp файлов, скриншот, `git push`.

---

## Прогресс по разделам

| Раздел | Баллы | Статус | Примечание |
|--------|-------|--------|------------|
| 0. Подготовка (VM, Fedora, 2-й диск) | — | ✅ Готово | Вход в систему выполнен |
| 1. Сеть (IP, hostname, ping) | 10 | ✅ Готово | enp0s3=192.168.1.100, ping OK, /tmp/network_check.txt |
| 2. Пакеты (dnf, RPM) | 10 | ✅ Готово | nginx, tcpdump, libcap-ng-utils; RPM в /tmp |
| 3. Диск и сервисы (sdb, fstab, nginx) | 10 | ✅ Готово | sdb1, fstab, nginx active |
| 4. Доступ (DAC, SELinux, capabilities) | 10 | ✅ Готово | DAC 2775, Enforcing, httpd_sys_content_t, cap_net_* |
| 5. PAM, web-страница, curl | 10 | ✅ Готово | root заблокирован, curl OK |
| 6. GitHub (артефакты, скриншот) | обяз. | 🟡 В процессе | Репо: `mephi-session-project-2026` |

**Легенда:** ⬜ не начато · 🟡 в процессе · ✅ готово

---

## Журнал выполнения

### 2026-06-07 — Старт проекта

- Создана VM `fedora-mephi` в VirtualBox (диск на D:)
- Установлена Fedora 43 Server
- Добавлен второй виртуальный диск
- **Проблема:** после установки VM снова показывает меню GRUB установщика (ISO не отключён)
- **Решено:** ISO отключён, пароль сброшен / вход выполнен
- SSH настроен для копирования команд из PowerShell

### 2026-06-07 — Раздел 1: Сеть

- Host-Only (enp0s3): 192.168.1.100/24, gateway 192.168.1.1, DNS 8.8.8.8
- NAT (enp0s8): 10.0.3.15 — интернет
- ping 192.168.1.1 и 8.8.8.8 — 0% packet loss
- `/tmp/network_check.txt` сохранён

### 2026-06-07 — Раздел 2: Пакеты

- `dnf install`: nginx, tcpdump, libcap-ng-utils — OK
- `dnf download` → `/tmp/tcpdump-4.99.6-2.fc43.x86_64.rpm`
- `getcap --version` — не ошибка установки (у getcap нет флага --version)

### 2026-06-07 — Раздел 3.1: Диск

- `/dev/sdb1` → ext4, метка `MEPHI_DATA`, ~15G
- `/data/mephi-web` создан, fstab настроен, `mount -a` OK
- UUID: `08f4e852-4b26-434c-b3b0-3a288db5afcf`

### 2026-06-08 — Раздел 4: Доступ

- DAC: `mephi-admin:mephi-devs`, права `2775`
- SELinux: `Enforcing`, контекст `httpd_sys_content_t`
- tcpdump: `cap_net_admin,cap_net_raw=ep`, mephi-admin может запускать
- nginx: HTTP 403 до создания index.html — ожидаемо

### 2026-06-08 — Раздел 5: PAM и web

- PAM: root в `/etc/ssh/denied_users`, `su - root` — отказ
- `index.html`: `Hello from Student: 371205`
- `curl localhost` и `curl 192.168.1.100` — OK

### 2026-06-08 — Сеть NAT + артефакты

- Исправлен default route: host-only `never-default`, NAT — интернет
- ping 8.8.8.8 и 192.168.1.1 — OK
- tcpdump RPM скачан в `~/`

---

## Артефакты для GitHub (чеклист)

- [ ] `project_history.txt`
- [ ] `network_check.txt`
- [ ] `nginx_recent_logs.txt`
- [ ] `fstab.txt`
- [ ] `selinux_status.txt`
- [ ] `file_contexts.txt`
- [ ] `tcpdump_capabilities.txt`
- [ ] `permissions.txt`
- [ ] `users_groups.txt`
- [ ] `index.html`
- [ ] `curl_output.txt`
- [ ] `mephi-nginx-screenshot.png`
- [ ] `tcpdump*.rpm`

---

## Справка — частые команды

### Как узнать имя сетевого интерфейса (внутри Fedora)

```bash
ip link show
# или
nmcli device status
```

Ищите интерфейс в состоянии UP (не `lo`). Часто: `enp0s3`, `eth0`, `ens33`.

### Как узнать тип сети в VirtualBox (на Windows)

1. VirtualBox → выбрать VM `fedora-mephi` → **Настроить**
2. **Сеть** → **Адаптер 1**
3. Смотреть поле **«Тип подключения»**:
   - **NAT** — IP гостя обычно `10.0.2.15`, снаружи не виден как `192.168.1.100`
   - **Сетевой мост (Bridged)** — VM получает IP из вашей домашней сети (удобно для `192.168.1.100`)

Для задания с IP `192.168.1.100/24` обычно нужен **Bridged**, если домашняя сеть `192.168.1.x`.

### Как зайти в установленную Fedora (если снова меню Install)

ISO всё ещё «вставлен» в виртуальный DVD. Отключить:

**Способ 1 (VM выключена):**  
Настроить → Носители → Optical Drive → **Убрать диск** / «Не выбран»

**Способ 2 (VM запущена):**  
Устройства → Оптические диски → **Убрать диск из привода**

Затем **перезагрузить** VM (Machine → Reset или Ctrl+Reset). Должна загрузиться установленная система с жёсткого диска.

---

## Ссылки

- [Задание (linuxоиды.docx)](./linuxоиды.docx)
- [Fedora 43 Server ISO](https://download.fedoraproject.org/pub/fedora/linux/releases/43/Server/x86_64/iso/)
- [GitHub репозиторий](https://github.com/Axer-me/mephi-session-project-2026) *(создать после выполнения)*
