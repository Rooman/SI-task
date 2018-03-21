# SI-task

##Разрабатываем систему платежей. 

### Общие сведения
Система может принимать запросы или c помощью REST-endpoint или с помощью JMS-queue. 
REST: Формат запроса JSON или XML. Формат ответа JSON или XML (смотрим в Accepted-type header)
JMS: Формат запроса-ответа JSON. 

#### Внешние системы
* JMS Broker (ставим локально, например Apache MQ) - для request/response
* DB (подключаем in-memory, например H2) - для хранения информации о платежах
* Privat/NBU REST API - web-service для получения курсов валют
* https://mighty-chamber-51017.herokuapp.com/ - web-service для получения данных о пользователях

#### Сущности системы
##### Payment
```
long id;
String description;
PaymentStatus status;
User user;
LocalDateTime date;
double amount;
String currency;
```

##### PaymentStatus (enum)
```
REQUESTED
REJECTED
ACCEPTED
```

##### User
```
long id;
String firstName;
String lastName;
boolean isActive;
```


### Запросы обрабатываемые системой
* CreatePayment - создание заявки на проведения платежа
1. Система принимает запрос в формате JSON, например:
```
{
	"description": "Test payment",
	"user": {
		"id": 1
	},
	"amount": 270.25,
	"currency": "UAH"
}
```
Или XML, например:
```
<request>
	<description>Test payment</description>
	<user>
    <id>1</id>
  </user>
	<amount>270.25</amount>
	<currency>UAH</currency>
</request>
```

2. Сохраняет платеж во внутренней базе данных, генерирует ID для платежа.
3. Производит enrichment информацией о валютах и юзере. 
4. Отправляет ответ пользователю в нужно формате.
JSON:
```
{
	"id": 1,
	"description": "Test payment",
	"user": {
		"id": 1,
		"firstName": "Alex", // получаем во время енричмента через heroku 
		"lastName": "Nitavskiy" // получаем во время енричмента через heroku
	},
	"status": "REQUESTED",
	"date": "2018-03-03 18:00:03",
	"amount": 270.25,
	"currency": "UAH",
	"amountInUSD": 10.25, // получаем во время енричмента через NBU
	"amountInEUR": 8.75 // получаем во время енричмента через NBU
}
```
XML:
```
<response>
	<description>Test payment</description>
	<user>
    	<id>1</id>
    </user>
	<amount>270.25</amount>
	<currency>UAH</currency>
	<status>REQUESTED</status>
	<date>2018-03-03 18:00:03</date>
	<amountInUSD>10.25</amountInUSD>
	<amountInEUR>8.75</amountInEUR>
</response>
```
