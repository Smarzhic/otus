# GAP-1

## В данном репозитории находят роли для деплоя из Gitlab

### Для деплоя wordpress приложения необходимо выполнить job deploy-app

![alt text](img/image.png)

![alt text](img/image8.png)
Результатом станет установка сервисов nginx, php, memcached, mysql и CMS wordpress

### Для деплоя системы мониторинга следует воспользоваться  job deploy-monitoring и deploy-exporters соответственно

![alt text](img/image2.png)

Результатом будет установленный Prometeus, Grafana, набор exporters и AlertManager

![alt text](img/image6.png)

![alt text](img/image4.png)

![alt text](img/image3.png)

![alt text](img/image5.png)

![alt text](img/image7.png)

# Подключение VictoriaMetric

Для установки VictoriaMetric следует выполнить роль  deploy-victoria. Конфигурирование Prometeus в данном примере осуществляется в ручную. Файл конфигурации находится в каталоге GAP1

![alt text](img/image9.png)

![alt text](img/image10.png)

![alt text](img/image11.png)

### Подключаем хранилище Victoria к Grafana

![alt text](img/image12.png)

### И подключим дашбор для мониторинга Victoria

![alt text](img/image13.png)

# Настройка алертинга

Для обновление правил отправки алертов а также изменения самих алертов добавлена роль alert-update. Ознакомиться с файлами конфигурации можно в каталоге Gap1

![alt text](img/image14.png)

Для приемки сообщений создан ТГ канал. с двуми трэдами Warning и Critical. Сообзения с различным severity поступают в соответствующий трэд

![alt text](img/image15.png)

![alt text](img/image16.png)

# Grafana - продвинутое использование

![alt text](img/image17.png)

![alt text](img/image18.png)

![alt text](img/image19.png)

![alt text](img/image20.png)

# Установка Zabbix

В ручную развернуты забикс-сервер изабикс-агент.

![alt text](img/image21.png)

Cформировать скрипт генерирующий метрики:

```
import random
import json

# Создаём список метрик
metrics = [
    {"{#METRIC}": "biba", "{#VALUE}": random.randint(0, 100)},
    {"{#METRIC}": "boba", "{#VALUE}": random.randint(0, 100)},
    {"{#METRIC}": "ziza", "{#VALUE}": random.randint(0, 100)},
]

# Преобразуем в JSON и выводим
json_result = json.dumps(metrics, indent=4)
print(json_result)
```
Добавлен UserParametr для получения метрик из скрипта

```
UserParameter=otus[*], python3 /etc/zabbix/zabbix_agent2.d/test.py
```
Добавил правило обнаружения

![alt text](img/image22.png)

Портотип итема

![alt text](img/image23.png)

Портотип тригера**

![alt text](img/image24.png)

** порог понижен до 80 так как не удавалось долго поймать срабатывание тригера

Убеждаемся что метрики обнаружилсь, тригеры добавились, данные поступают.

![alt text](img/image25.png)

![alt text](img/image26.png)

Настроена отправка оповещений в ТГ канал

![alt text](img/image27.png)

# Установка и настройка TICK стека

### Установка Telegraf на VM c wordpress

```
curl --silent --location -O \
https://repos.influxdata.com/influxdata-archive.key \
&& echo "943666881a1b8d9b849b74caebf02d3465d6beb716510d86a39f6c8e8dac7515  influxdata-archive.key" \
| sha256sum -c - && cat influxdata-archive.key \
| gpg --dearmor \
| sudo tee /etc/apt/trusted.gpg.d/influxdata-archive.gpg > /dev/null \
&& echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive.gpg] https://repos.influxdata.com/debian stable main' \
| sudo tee /etc/apt/sources.list.d/influxdata.list
sudo apt-get update && sudo apt-get install telegraf
```

### Генерация конфигурации

```
telegraf config > telegraf.conf
```

Настроил для Telegraf сбор и отправку метрик в [01-telegraf.conf](TICK/Telegraf/01-telegraf.conf) по пути /etc/telegraf/telegraf.d/

### Развёртывание Influxdb, Chronograf, Kapacitor

Influxdb, Chronograf, Kapacitor развернуn на отдельной машине с помощью docker-compose

```
networks:
  monitoring:
    driver: bridge

services:
  influxdb:
    image: influxdb:2.7
    restart: always
    container_name: influxdb
    hostname: influxdb
    ports:
      - 8086:8086
    environment:
      - DOCKER_INFLUXDB_INIT_MODE=setup
      - DOCKER_INFLUXDB_INIT_USERNAME=admin
      - DOCKER_INFLUXDB_INIT_PASSWORD=adminpassword
      - DOCKER_INFLUXDB_INIT_ORG=home
      - DOCKER_INFLUXDB_INIT_BUCKET=cms
      - DOCKER_INFLUXDB_INIT_ADMIN_TOKEN=my-super-secret-auth-token
    volumes:
      - ./volumes/influxdb/data:/var/lib/influxdb2:rw
      - ./volumes/influxdb/config:/etc/influxdb2
    networks:
      monitoring:
        aliases:
         - influxdb

  chronograf:
    image: chronograf:1.10.3
    restart: always
    container_name: chronograf
    hostname: chronograf
    ports:
      - 8888:8888
    environment:
      - INFLUXDB_URL=http://192.168.0.184:8086
      - INFLUXDB_ORG=home
      - INFLUXDB_TOKEN=my-super-secret-auth-token
      - KAPACITOR_URL=http://192.168.0.184:9092
#      - KAPACITOR_USERNAME=admin
#      - KAPACITOR_PASSWORD=admin
    volumes:
      - ./volumes/chronograf:/var/lib/chronograf:rw
    depends_on:
      - influxdb
    networks:
      monitoring:
        aliases:
         - chronograf

  kapacitor:
    image: kapacitor:1.7
    restart: always
    container_name: kapacitor
    hostname: kapacitor
    ports:
      - 9092:9092
    environment:
      KAPACITOR_HOSTNAME: kapacitor
      KAPACITOR_HTTP_AUTH_ENABLED: "false"
      KAPACITOR_INFLUXDB_0_URLS_0: "http://192.168.0.184:8086"
      KAPACITOR_INFLUXDB_0_ENABLED: "true"
      KAPACITOR_INFLUXDB_0_DEFAULT: "true"
      KAPACITOR_INFLUXDB_0_NAME: "cms"
      KAPACITOR_INFLUXDB_0_TOKEN: "my-super-secret-auth-token"
      KAPACITOR_INFLUXDB_0_DISABLE_SUBSCRIPTIONS: "true"
      KAPACITOR_INFLUXDB_0_KAPACITOR_HOSTNAME: kapacitor
      KAPACITOR_AS_ROOT: "true"
      KAPACITOR_LOAD_ENABLED: "true"
      KAPACITOR_LOAD_DIR: "/var/lib/kapacitor/load"
      KAPACITOR_LOGGING_FILE: "/var/lib/kapacitor/kapacitor.log"
      KAPACITOR_LOGGING_LEVEL: debug
      KAPACITOR_TELEGRAM_ENABLED: "true"
      KAPACITOR_TELEGRAM_URL: "https://api.telegram.org/bot"
      KAPACITOR_TELEGRAM_TOKEN: ${TG_TOKEN}
      KAPACITOR_TELEGRAM_CHAT_ID: ${TG_CHAT_ID}
      KAPACITOR_TELEGRAM_PARSE_MODE: "HTML"
#      KAPACITOR_INFLUXDB_0_USERNAME: admin
#      KAPACITOR_INFLUXDB_0_PASSWORD: secret-auth-token
    volumes:
#      - ./kapacitor/kapacitor.conf:/etc/kapacitor/kapacitor.conf:ro
      - ./volumes/kapacitor:/var/lib/kapacitor:rw
      - ./kapacitor/tasks:/var/lib/kapacitor/load/tasks:rw
    depends_on:
      - influxdb
    networks:
      monitoring:
        aliases:
         - kapacitor

```
### Сформирован дашборд в хронографе

![alt text](img/image28.png)

### Проверил что алерты в kapasitor работают и отпрвляются в ТГ

![alt text](img/image29.png)
