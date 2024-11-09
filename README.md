- http://localhost - Сайт
- http://localhost:8080 - phpMyAdmin

# UmiDock
UmiDock позволяет легко и просто запускать **umi CMS** на **Docker**. Он запускает web окружение с готовыми сервисами PHP, Apache, MySQL.

## Требования
- Docker
- Docker Compose v2
- для Windows: WSL2 + Docker Desktop

## Выполните настройку окружения

Скопируйте файл .env

Для юникс систем ```cp -f .env_template .env```

Для windows ```copy .env_template .env```


⚠ Если у вас MacOS, удалите строчку `/etc/localtime:/etc/localtime/:ro` из docker-compose.yml

По умолчанию используется php 7.4. Настройки можно изменить в файле `.env`. Также можно задать путь к каталогу с сайтом и параметры базы данных MySQL.

```dotenv
COMPOSE_PROJECT_NAME=umidock  # Имя проекта. Используется для наименования контейнеров
PHP_VERSION=php74                # Версия php
MYSQL_DATABASE=bitrix            # Имя базы данных
MYSQL_USER=bitrix                # Пользователь базы данных
MYSQL_PASSWORD=123               # Пароль для доступа к базе данных
MYSQL_ROOT_PASSWORD=123          # Пароль для пользователя root от базы данных
INTERFACE=0.0.0.0                # На данный интерфейс будут проксироваться порты
SITE_PATH=./www                  # Путь к директории Вашего сайта (папка внутри репозиториия)
```

## Запуск и остановка umidock
### Запуск
```shell
docker compose -p umidock up -d
```
Чтобы проверить, что все сервисы запустились посмотрите список процессов `docker ps`.
Посмотрите все прослушиваемые порты, должны быть 80, 11211, 9000 `netstat -plnt`.
Откройте IP машины в браузере.

### Остановка
```shell
docker compose -p umidock stop
```

### Пересоздание контейнеров
```shell
docker-compose up -d --no-deps --build
```
## Как заполнять подключение к БД
- сервер - db
- имя пользователя - umi
- пароль - 123
- база - umi


## Примечание
- Для загрузки резервной копии в контейнер используйте команду (прежде поместите файл backup.sql в корень проекта): `cat ./backup.sql | docker exec -i db /usr/bin/mysql -u root -p123 umi`
- При использовании php74 в production удалите строку с `php7.4-xdebug` из файла `php74/Dockerfile`, сам факт его установки снижает производительность Битрикса и он должен использоваться только для разработки

## Использование xdebug.
- Настройки xdebug задаются в `phpXX/php.ini`.
- Для php73, php74 дефолтовые настройки xdebug - коннект на порт `9003` хоста, с которого пришел запрос. В случае невозможности коннекта - фаллбек на `host.docker.internal`.
- При изменении `php.ini` в проекте не забудьте пересоздать контейнеры