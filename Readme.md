# «taskforce» в docker

Это конфигурации для докера и traefik, которые позволяют запускать несоклько проектов taskforce параллельно.

Делал я это для себя, документация может быть не полной.

## Как использовать

### Установите docker и docker-compose.

### Включите игнорирование локальных файлов

```sh
echo /.env >> .git/info/exclude
```

`vendor` и так игнорируется в `.gitignore`, так что в `.git/info/exclude` ее добавлять не требуется.

### Настройте переменные запуска в .env

Скопируйте `.etv.template` из данного репозитория в `.env` в директории вашего проекта taskforce.

Задайте значения переменным окружения в файле `.env` в соответствии с комментариями в нем.

### Запуск

Поддерживается два варианта запуска:

- с маппингом порта прямо на хост
- с использованием `traefik` (пример конфигурации в директории `traefik`)

Выбор варианта выполняется через указание соответствующего конфига в `.env`.

1. Выполните команду в директории проекта

```sh
docker-compose up -d --build
```

Если запускать с конфигом `docker-compose.direct` сайт будет доступн по любому имени внутри .localhost с указанным портом,
например: http://taskforce.localhost:8080.

Если запускать с конфигом `docker-compose.traefik` - сайт будет доступен по указанному SITE_URL по порту 80, например
http://taskforce.docker.localhost.

composer пакеты устанавливаются пользователем самостоятельно, когда это становится нужно. Для них будет использоваться
директория vendor в директории с исходным кодом. Поскольку докер работает под root (по умолчанию), владельцем файлов в
дирентории будет root. Это можео решить, запуская процессы внутри docker контейнера под пользовтелем с тем же id, что и
текущий пользователь, но я не стал с этим заморачиваться, тем более, что для Windows это, наверное, не актуально.

```
docker-compose exec application composer install
docker-compose exec application composer dump-autoload -p
```

## Xdebug

Настройки Xdebug находятся в docker_compose.yml, в XDEBUG_CONFIG. Он подключается к локалхосту на порт 9000.

### vscode

Чтобы работать с xDebug из vscode нужно установить расширение felixfbecker.php-debug.

Пример его настроек (launch.json):

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Listen for XDebug",
            "type": "php",
            "request": "launch",
            "port": 9000,
            // server -> local
            "pathMappings": {
              "/var/www/html": "${workspaceRoot}"
            }
        },
        {
            "name": "Launch currently open script",
            "type": "php",
            "request": "launch",
            "program": "${file}",
            "cwd": "${fileDirname}",
            "port": 9000
        }
    ]
}
```
