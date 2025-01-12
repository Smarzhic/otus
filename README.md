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
