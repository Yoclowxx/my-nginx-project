# Docker practice — Уровень 1 (Базовый)

**Выполнил:** Камзинов Тамерлан Канатович  
**Группа:** ИБАС 24-11-2  
**Тема:** Контейнеризация веб-сервера Nginx с ограничениями безопасности

---

## 📁 Описание файлов проекта

| Файл / папка | Что делает |
|--------------|------------|
| `Dockerfile` | Инструкция для сборки Docker-образа на базе `nginx:alpine`. Копирует кастомную HTML-страницу в `/usr/share/nginx/html/`. |
| `index.html` | Кастомная HTML-страница с приветствием от стажёра. |
| `nginx_logs/` | Папка на хосте, куда через volume монтируются логи Nginx. Нужна, потому что контейнер запущен с `--read-only`. |
| `nginx_logs/access.log` | Лог всех HTTP-запросов к серверу. |
| `nginx_logs/error.log` | Лог ошибок и служебных сообщений Nginx. |
| `screenshots/` | Скриншоты проверки работоспособности (13 шт.). |
| `README.md` | Этот файл. |

---
##  Папка
![Папка](screenshots/15-ddd.png)

---

## 🛠️ Команды для запуска и проверки

### 1. Сборка образа

```bash
docker build -t my-nginx-practice:v1 .
docker images
```

![Docker images — образ собран](screenshots/13-create-files.png)

![Docker](screenshots/16-docker.png)

---
### 2. Запуск обычного контейнера

![Запуск контейнера](screenshots/03-run-container.png)

---
### 3. Результат

![Site](screenshots/14-site.png)

---

### 4. Запуск контейнера с ограничениями

```bash
docker run -d --name nginx-logs -p 8080:80 --memory="50m" --cpus="0.5" --read-only --tmpfs /var/cache/nginx --tmpfs /var/run --tmpfs /tmp -v %cd%\nginx_logs:/var/log/nginx my-nginx-practice:v1
```

**Что делают флаги:**
- `-d` — запуск в фоне
- `-p 8080:80` — порт 8080 на хосте → 80 в контейнере
- `--memory="50m"` — лимит RAM 50 МБ
- `--cpus="0.5"` — лимит CPU 0.5 ядра
- `--read-only` — корневая ФС только для чтения
- `--tmpfs ...` — временные папки Nginx в RAM
- `-v %cd%\nginx_logs:/var/log/nginx` — логи на хост

---

### 5. Проверка работоспособности

**Контейнер запущен:**
```bash
docker ps
```

![docker ps — контейнер Up](screenshots/04-safe-run.png)

**Сайт открывается:** http://localhost:8080

![Сайт в браузере](screenshots/07-site-browser.png)

**Запросы через curl:**
```bash
curl.exe http://localhost:8080
```

![Запросы curl](screenshots/11-curl-requests.png)

**Read-only включён:**
```bash
docker inspect nginx-logs | Select-String "ReadonlyRootfs"
```

![ReadonlyRootfs: true](screenshots/10-readonly.png)

**Логи на хосте:**
```bash
dir nginx_logs
type nginx_logs\access.log
type nginx_logs\error.log
```

![Папка nginx_logs](screenshots/12-create-logs-folder.png)
![Логи nginx](screenshots/09-logs-dir.png)
![access.log](screenshots/08-access-log.png)

---

## 📸 Все скриншоты по шагам

### 1. Проверка установки Docker
![Docker version](screenshots/02-docker-version.png)

### 2. Файлы проекта — `Dockerfile` и `index.html`
![Dockerfile](screenshots/01-index-html.png)

### 3. Остановка и удаление предыдущих контейнеров
![docker stop и rm](screenshots/06-stop-rm.png)

### 4. Запуск контейнера с volume
![Запуск с volume](screenshots/05-run-with-volume.png)

---

## 🎯 Что демонстрирует проект

1. **Докеризация Nginx** — образ на базе `nginx:alpine` (~26 МБ).
2. **Ограничение ресурсов** — 50 МБ RAM, 0.5 CPU.
3. **Безопасность** — корневая ФС read-only.
4. **Volume для логов** — логи Nginx сохраняются на хосте.
