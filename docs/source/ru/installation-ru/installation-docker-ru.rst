.. _installation-docker-ru:

# Установка Wallarm с использованием Docker

Для простоты установки фильтрующий узел Wallarm Node может быть установлен в виде Docker-контейнера. Фильтрующий узел, как и при обычной установке из пакета, будет работать в режиме обратного прокси-сервера.

Контейнер Wallarm Node является "толстым" и содержит в себе все подсистемы фильтрующего узла. С определенной точки зрения это противоречит идеологии Docker "один контейнер — одна сущность". Однако это позволяет упростить процесс развертывания и управления Wallarm Node, оставаясь в рамках идеологии "один контейнер — один логический сервис".

### 1. Быстрый запуск

Для быстрого запуска нового фильтрующего узла Wallarm Node вам потребуется:

- **/path/to/license.key**: путь к лицензионному ключу на хосте.
- **example.com**: защищаемый ресурс.
- **deploy@example.com**: логин для доступа к https://my.wallarm.com.
- **very_secret**: пароль для доступа к https://my.wallarm.com.

В общем случае развертывание узла сводится к одной команде запуска контейнера:

```
docker run -d -v /path/to/license.key:/etc/wallarm/license.key -e DEPLOY_USER="deploy@example.com" -e DEPLOY_ PASSWORD="very_secret" -e NGINX_BACKEND=example.com -p 80:80 wallarm/node
```

В результате на 80 порту сервера, где запущен контейнер, будет доступен защищаемый ресурс. При этом в облаке my.wallarm.com будет зарегистрирован новый фильтрующий узел; он будет отображаться в соответствующем разделе настроек веб-интерфейса.

Более тонкая настройка делается размещением дополнительных конфигурационных файлов внутри контейнера.

### 2. Подключение к облаку

Для постоянного обновления правил блокировки, фильтрующий узел (ноду) следует подключить к облачному сервису Wallarm Cloud.

Возможны следующие варианты подключения ноды к облачному сервису:

**Автоматическая регистрация**

Передайте переменные окружения `DEPLOY_USER`, `DEPLOY_PASSWORD` c реквизитами доступа к my.wallarm.com. В результате  при первом запуске контейнер автоматически зарегистрируется в облаке.

Если контейнер с таким именем уже существует, команда завершится с ошибкой. Чтобы избежать этого, используйте переменную окружения `DEPLOY_FORCE=true`.

```
docker run -d -v /etc/wallarm/license.key -e DEPLOY_USER="deploy@example.com" -e DEPLOY_PASSWORD="very_secret" -e NGINX_BACKEND=[ IP address ] wallarm/node
```

**Использование заранее известных реквизитов ноды**

Для доступа к облаку нода использует `uuid` и `secret`, которые можно передать в переменных окружения `NODE_UUID` и `NODE_SECRET`.

```
docker run -d -v /etc/wallarm/license.key -e "NODE_UUID=00000000-0000-0000-0000-000000000000" -e NODE_SECRET="0000000000000000000000000000000000000000000000000000000000000000" -e NGINX_BACKEND=93.184.216.34 wallarm/node
```

**Конфигурационный файл**

Если у Вас уже есть готовый конфигурационный файл node.yaml, передайте его внутрь контейнера при помощи "внешнего тома":

`docker run -d -v /etc/wallarm/license.key -v /etc/wallarm/node.yaml -e NGINX_BACKEND=93.184.216.34 wallarm/node`

### 3. Настройка NGINX-Wallarm

Конфигурации ноды во многих случаях осуществляется через конфигурационный файл NGINX. Контейнер позволяет использовать упрощенный режим настройки через переменные окружения, который включается при передаче переменой окружения `NGINX_BACKEND`.

**Упрощенный режим**

- `NGINX_BACKEND`: адрес бэкенда, на который необходимо передавать все входящие запросы. Если адрес не содержит префикса "http://" или "https://", то по-умолчанию будет использован "http://". Подробнее в документации на proxy_pass.
- `WALLARM_MODE`: режим работы NGINX-Wallarm. Подробнее в описании параметра [wallarm_mode](../configuration/configuration-options.html#wallarmmode).

**Конфигурационные файлы**

Директории, используемые NGINX:

- `/etc/nginx-wallarm/conf.d`: общие настройки.
- `/etc/nginx-wallarm/sites-enabled`: настройки виртуальных хостов.
- `/var/www/html`: статические файлы.

### 4. Настройка модуля постаналитики

Важной частью модуля постаналитики является находящаяся в памяти база данных Tarantool, в которую сохраняется последние N запросов для обнаружения поведенческих атак. Tarantool настраивается через переменные окружения:

- `SLAB_ALLOC_ARENA`: размер памяти в гигабайтах, выделенной под хранение сериализованных запросов. 
- `SLAB_ALLOC_MAXIMAL`: максимальный размер в байтах одного сериализованного запроса. 

Рекомендуется выделить для Tarantool 75% от общей памяти сервера. Например, если у сервера 32 Гб памяти, оптимально выделить под хранилище 24 Гб. Укажите размер выделяемой памяти в конфигурационном файле Tarantool, например:

`SLAB_ALLOC_ARENA=24`

Чтобы применить сделанные изменения, перезапустите Tarantool:

{% codetabs name="Debian 7.x (wheezy)", type="sh" -%}
service wallarm-tarantool restart
{%- language name="Debian 8.x (jessie)", type="sh" -%}
systemctl restart wallarm-tarantool
{%- language name="Ubuntu 14.04 LTS (trusty)", type="sh" -%}
service wallarm-tarantool restart
{%- language name="Ubuntu 16.04 LTS (xenial)", type="sh" -%}
service wallarm-tarantool restart
{%- language name="CentOS 6.x", type="sh" -%}
service wallarm-tarantool restart
{%- language name="CentOS 7.x", type="sh" -%}
systemctl restart wallarm-tarantool
{%- endcodetabs %}

### 5. Ротация логов

Журналы событий по умолчанию включены и пишутся в следующие директории:

- `/var/log/nginx-wallarm/`: логи NGINX.
- `/var/log/wallarm/`: логи различных wallarm-специфичных подсистем.

По-умолчанию логи ротируются раз в сутки. Изменение параметров ротации через переменные окружения не предусмотрено. Для настройки ротации логов необходимо изменять конфигурационные файлы в `/etc/logrotate.d/`.

### 6. Мониторинг

Внутри контейнера установлены nagios-совместимые скрипты для мониторинга ноды. Подробнее о них читайте в разделе [Мониторинг ноды](../configuration/node-monitoring.md).

Пример вызова скриптов:

```
docker exec -it wallarm-node /usr/lib/nagios-plugins/check_wallarm_tarantool_timeframe -w 1800 -c 900
docker exec -it wallarm-node /usr/lib/nagios-plugins/check_wallarm_export_delay -w 120 -c 300
```

На этом установка закончена.

### Что дальше 

!INCLUDE "../../includes/installation/installation-is-finished.md"

