# SI-task

##Разрабатываем систему платежей. 

### Сущности системы
* Payment
```
long id;
String description;
PaymentStatus status;
User user;
LocalDateTime date;
double amount;
String currency;
```

* PaymentStatus (enum)
```
REQUESTED
REJECTED
ACCEPTED
```

* User
```
long id;
String firstName;
String lastName;
boolean isActive;
```

### Общие сведения
Система может принимать запросы или c помощью REST-endpoint или с помощью JMS-queue. 
REST: Формат запроса JSON или XML. Формат ответа JSON или XML (смотрим в Accepted-type header)
JMS: Формат запроса-ответа JSON. 

#### Внешние системы
* JMS Broker (ставим локально, например Apache MQ) - для request/response
* DB (подключаем in-memory, например H2) - для хранения информации о платежах
* Privat/NBU REST API - web-service для получения курсов валют
* https://mighty-chamber-51017.herokuapp.com/ - web-service для получения данных о пользователях


### Запросы обрабатываемые системой
* CreatePayment - создание заявки на проведения платежа



