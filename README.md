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
