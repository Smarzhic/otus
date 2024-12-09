# GAP-1

## В данном репозитории находят роли для деплоя из Gitlab

### Для деплоя wordpress приложения необходимо выполнить job deploy-app

![alt text](img/image.png)

Результатом станет установка сервисов nginx, php, memcached, mysql и CMS wordpress

### Для деплоя системы мониторинга следует воспользоваться  job deploy-monitoring и deploy-exporters соответственно

![alt text](img/image2.png)

Результатом будет установленный Prometeus, Grafana, набор exporters и AlertManager

![alt text](img/image3.png)

![alt text](img/image4.png)

![alt text](img/image5.png)
