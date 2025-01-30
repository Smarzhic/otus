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
