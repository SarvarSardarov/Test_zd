<<<<<<< HEAD
# Тестовое задание: WordPress + Nginx + мониторинг

## Установка и запуск

1. Склонируйте репозиторий:
   git clone https://github.com/SarvarSardarov/Test_zd
   cd Test_zd

2. Создайте файл `.env`:
   cp .env.example .env
   nano .env

   Обязательные переменные:
   ```
BD_root=
BD_bd=
BD_us=
BD_pass=
   ```
3. Самоподписанный SSL-сертификат

```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout ~/Test_zd/nginx/ssl/site.key \
  -out ~/Test_zd/nginx/ssl/site.crt \
  -subj "/CN=site.local"
```

4. В файле `nginx/conf.d/default.conf` замените IP-адрес в правиле `allow` на свой реальный IP (узнать: `curl ifconfig.me`):
   ```nginx
   allow ВАШ_IP; (19 строка)
   ```

5. Пропишите тестовые домены в `/etc/hosts` на своей локальной машине (Linux) или в `C:\Windows\System32\drivers\etc` на своей локальной машине (Винда) и файлы редактируются от имени админа:
   ```
   <IP_СЕРВЕРА>  site.local
   <IP_СЕРВЕРА>  metrics.local
   ```

6. Запустите проект:
   ```bash
   docker compose up -d
   ```

7. Проверьте, что все контейнеры запущены:
   ```bash
   docker compose ps
   ```

8. Откройте `https://site.local` - к WordPress.

9. Откройте `http://metrics.local` - к Grafana (по дефолту `admin` / `admin`).

## Настройка мониторинга

Data source в Grafana уже указывает на Prometheus (`http://prometheus:9090`) — доступен адрес внутри Docker-сети. Готовый дашборд "OS" (сетевой трафик, свободное место на диске, файловые дескрипторы) можно импортировать из файла `grafana/dashboard-os-general.json`:

Grafana → Dashboards → New → Import → загрузить JSON-файл.

## Защита SSH (fail2ban)

Настраивается на хосте, не в контейнере — следит за системными SSH-логами:
```bash
sudo apt install fail2ban
sudo cp fail2ban/jail.local /etc/fail2ban/jail.local
sudo systemctl restart fail2ban
sudo systemctl enable fail2ban
```

Проверить статус:
```bash
sudo fail2ban-client status sshd
```

Браузер при первом заходе покажет предупреждение о недоверенном сертификате - это ожидаемо, нужно подтвердить исключение вручную.

Защита `/wp-admin` и `/wp-login.php` по IP.

## Структура проекта

```
project/
├── docker-compose.yml
├── .env.example
├── nginx/
│   ├── conf.d/default.conf
│   └── ssl/ (site.crt, site.key — не в репозитории)
├── prometheus/prometheus.yml
├── grafana/
│   ├── provisioning/
│   └── dashboard-os-general.json
├── fail2ban/jail.local
└── README.md
```

А так же если хотите посмотреть то как у меня запущен весь сервис на моем сервере, то можете просто в свою .ssh директорию добавить закрытый ключ загруженный в гит 'Admin_2' (ну или указать в конфигурации явный путь к ключу) и в файле ~/.ssh/config добавить абзац:
```    
    Host Test_zd
        Hostname 135.106.146.66
        User Admin
        Port 22
        IdentityFile /home/chudo/.ssh/Admin_2
```
и после в самом терминале просто написать ssh Test_zd для прямого и безопасного подключения. А так же если желаете могу вести в список исключени (allow) ваш ip для теста сайтов.

## Время выполнения

   2 дня.
=======
