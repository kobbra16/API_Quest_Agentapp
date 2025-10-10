# API_Quest_Agentapp
API_Quest_Agentapp
Привет. 
Потребуется пройти первый этап квеста. 
Задание составить и выслать Postman коллекцию (файл JSON) на основе описанной документации. Часть примеров запросов была умышленно скрыта, чтобы ты мог составить запрос самостоятельно по аналогии с прошлыми примерами. 

Цель: 
С помощью полученной коллекции  дойти до этапа “Обновления полиса” в страховой компании и получить ответ. 

Примечание
Данные можно использовать свои или тестовые. 
    
Host: https://partner.agentapp.ru/
Login: qa@qa.qa
Password: 111
api_version: v1
AgentApp b2b
МЕТОДЫ ЗАПРОСОВ.
    • GET - получить объект/объекты. Если не указывать ID объекта, то получаем все доступные объекты. Пример, /dicts/credential_types
    • POST - создать объект.
    • PUT - обновить полностью объект. Требуется ID объекта
    • PATCH - обновить частично объект. Требуется ID объекта
    • DELETE - удалить объект. Требуется ID объекта
АУТЕНТИФИКАЦИЯ
    • Все запросы, кроме получения токена требуют передачу вместе с запросом заголовка:
Authorization: Token {{token}}',
где {{token}} - это токен
    • Получение токена осуществляется через метод /users/obtain-token
ТИП ПЕРЕДАВАЕМОЙ И ПОЛУЧАЕМОЙ ИНФОРМАЦИИ (ACCEPT, CONTENT-TYPE)
    • Каждый запрос, как ожидает, так и отвечает application/json


POST
{{http_schema}}://{{host}}/{{api_version}}/users/obtain-token
Сервис работает только с аутентифицированным пользователем.
Аутентификация работает через заголовок
"Authorization: Token {{token}}",
где {{token}} - токен, полученный от системы в ответ на данный /account/obtain-token рест
Запрос - POST-запрос с инфой:
    • "username" -- логин пользователя
    • "password" -- пароль
В ответе получаем "token"
Создание водителя
POST 
{{http_schema}}://{{host}}/{{api_version}}/insured_objects/drivers
В ответе находятся параметры по КБМ: "kbm_value" -- коэф. КБМ "is_rsa_checked" -- true/false (ответ из РСА о наличии водителя в базе РСА)

На каждого водителя требуется отдельный запрос.
Из ответа берём "id" -- потребуется при создании сущности "объект страхования"
Body
{
  "first_name": "Имя",
  "last_name": "Фамилия",
  "patronymic": "Отчество",
  "birth_date": "1990-01-01",
  "driving_experience_started": "2010-10-10",
  "driver_licenses": [
    {
      "credential_type": "DRIVER_LICENSE",
      "number": "012345",
      "series": "1234",
      "issue_date": "2010-10-10"
    }
  ]
}

Создание собственника 
POST 
{{http_schema}}://{{host}}/v1/insured_objects/owners/natural_persons

Создание собственника ФЛ требует следующих полей:
    • "last_name" -- Фамилия
    • "first_name" -- Имя
    • "patronymic" -- Отчётство
    • "gender" -- Пол (M,F)
    • "birth_date" -- дата рождения в формате "1987-01-13"
    • "credential" -- документы. Массив. Первым элементом необходимо добавить российский паспорт
    • "address" -- адреса. Массив. Два типа адресов - фактический и юридический. Различаются по параметру "address_type": "ACTUAL_ADDRESS" и "LEGAL_ADDRESS" соответственно.
Указание LEGAL_ADDRESS необходимо для получения расчетов страховых компаний.
Для создания страхового объекта брать ID из поля Person

Body

{
  "last_name": "Фамилия",
  "first_name": "Имя",
  "patronymic": "Отчество",
  "birth_date": "1990-01-01",
  "credential": [
    {
      "credential_type": "RUSSIAN_INTERNAL_PASSPORT",
      "issue_date": "2017-03-08",
      "issue_point": "УФМС",
      "issue_point_code": "123-456",
      "number": "123456",
      "series": "1234"
    }
  ],
  "address": [
    {
      "address_query": "г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 1 к 1, кв 1",
      "address_type": "LEGAL_ADDRESS",
      "region_kladr_id": "7800000000000",
      "city_kladr_id": "7800000600000"
    },
    {
      "address_query": "г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 1 к 1, кв 1",
      "address_type": "ACTUAL_ADDRESS",
      "region_kladr_id": "7800000000000",
      "city_kladr_id": "7800000600000"
    }
  ]
}

Создание страхователя 
Аналогично полному обновлению Собственника (физлицо)
POST 
{{http_schema}}://{{host}}/{{api_version}}/insured_objects/insurants/natural_persons
{
  "last_name": "Фамилия",
  "first_name": "Имя",
  "patronymic": "Отчество",
  "birth_date": "1990-01-01",
  "credential": [
    {
      "credential_type": "RUSSIAN_INTERNAL_PASSPORT",
      "issue_date": "2010-10-10",
      "issue_point": "УФМС",
      "issue_point_code": "123-456",
      "number": "123456",
      "series": "1234"
    }
  ],
  "address": [
    {
      "address_query": "г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 1 к 1, кв 1",
      "address_type": "LEGAL_ADDRESS",
      "region_kladr_id": "7800000000000",
      "city_kladr_id": "7800000600000"
    },
    {
      "address_query": "г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 1 к 1, кв 1",
      "address_type": "ACTUAL_ADDRESS",
      "region_kladr_id": "7800000000000",
      "city_kladr_id": "7800000600000"
    }
  ]
}
Для создания страхового объекта брать ID из поля Person
Создание автомобиля
POST
{{http_schema}}://{{host}}/v3/insured_objects/cars
Создание автомобиля требует следующих полей:
    • "car_model" -- id Модели
    • "engine_power" -- мощность двигателя в л.с. Уже присутствует, при PATCH'е не требуется указывать.
    • "chassis_number" -- Номер шасси (не обязательно)
    • "car_body_number" -- Номер кузов (прицеп) (не обязательно)
    • "vin_number" -- ВИН (не обязательно)
    • "number_plate" -- Номерной знак (не обязательно)
    • "manufacturing_year" -- год выпуска в формате "2010"
    • "max_mass" -- максимальная масса (для категории Д)
    • "credential" -- документ.
Необходимо указать минимум один из четырёх параметров:
    • "vin_number" -- ВИН (не обязательно)
    • "number_plate" -- Номерной знак (не обязательно)
В ответе придут:
    • "id"
{
  "car_model_id": 864026180,
  "engine_power": 211,
  "chassis_number": null,
  "car_body_number": null,
  "vin_number": "WAUZZZ8T4BA037241",
  "number_plate": "Р904МХ178",
  "manufacturing_year": 2010,
  "max_mass": null,
  "credential": [
    {
      "credential_type": "VEHICLE_REGISTRATION",
      "issue_date": "2010-11-01",
      "number": "267461",
      "series": "78УН"
    }
  ]
}
Объединение субъектов и объектов страхования в одну сущность “объект страхования”
{{http_schema}}://{{host}}/{{api_version}}/insured_objects/

Создаём техническую сущность - "объект страхования".
    • "drivers" -- массив водителей
    • "owner" -- собственник
    • "car" -- автомобиль
    • "insurant" -- Страхователь
    • 
Из ответа берём "id" -- потребуется при создании договора

Создание договора
POST
{{http_schema}}://{{host}}/v3/agreements/calculations

Параметры:
    • valid_from -- начало действия полиса. В системе срок страхования = 1 год, поэтому разница между датой valid from и valid to должна составлять 1 год. Например, если valid from "2019-06-30", дата valid to должна быть "2020-06-29"
    • valid_to -- окончание действия полиса
    • insurance_period -- id периода (длительность периода страховки) = 11
    • target_of_using -- цель использования
    • drivers_ids -- массив водителей
    • is_car_without_registration -- ТС без регистрации
    • engine_power -- мощность ТС
    • has_car_trailer -- ТС с прицепом
    • car_type -- тип ТС
    • owner_registration / address_query:--полный адрес регистрации собственника. Формируется аналогично адресу у Собственника. (тип строка см запрос собственник) 
Если был выбран период страхования "1 год", то передается пустой массив periods. А если был выбран другой период, то в массиве periods передаются параметры:
    • start_date -- начало периода
    • end_date -- окончание периода
Период не может выходить за даты начала и окончания договора. Возможные периоды: 3-9 месяцев, 1 год.
В ответе получаем:
    • id -- идентификатор договора. Этот id понадобится для дальнейших запросов.
    • calculation -- информация о расчёте
    • calculation.premium -- страховая премия
Обновление Договора в системе AgentApp

PATCH

{{http_schema}}://{{host}}/{{api_version}}/agreements/{{agreement_id}}

Необходимые параметры:
    • "insured_object" -- id сущности InsuredObject
В ответе получаем:
    • id -- сохраняем как agreement_id, дальше будем использовать.



Postman collection

	"info": {
		"_postman_id": "063371e3-791a-4a81-a939-12387a436e4a",
		"name": "Quest_ AgentApp",
		"schema": "https://schema.getpostman.com/json/collection/v2.1.0/collection.json",
		"_exporter_id": "37226616"
	},
	"item": [
		{
			"name": "Authentication",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Android\""
					},
					{
						"key": "Referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?1"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.10.01,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=14efae20707240a8bcf2c71ba2f22a05,sentry-sample_rate=0.5,sentry-sampled=true"
					},
					{
						"key": "sentry-trace",
						"value": "14efae20707240a8bcf2c71ba2f22a05-b18452295f9ebc85-1"
					},
					{
						"key": "User-Agent",
						"value": "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Mobile Safari/537.36"
					},
					{
						"key": "Accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "Content-Type",
						"value": "application/json"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{ \"username\":\"qa@qa.qa\",\r\n  \"password\":\"111\"\r\n}",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/{{api_version}}/users/obtain-token",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"{{api_version}}",
						"users",
						"obtain-token"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create_drivers",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "accept-language",
						"value": "ru,uk;q=0.9,en-US;q=0.8,en;q=0.7,th;q=0.6"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.09.4,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=8b620ff7b8cd4110b13cbfff511fc7fb,sentry-sample_rate=0.5,sentry-sampled=false"
					},
					{
						"key": "content-type",
						"value": "application/json"
					},
					{
						"key": "origin",
						"value": "https://partner.agentapp.ru"
					},
					{
						"key": "priority",
						"value": "u=1, i"
					},
					{
						"key": "referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?0"
					},
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Windows\""
					},
					{
						"key": "sec-fetch-dest",
						"value": "empty"
					},
					{
						"key": "sec-fetch-mode",
						"value": "cors"
					},
					{
						"key": "sec-fetch-site",
						"value": "same-origin"
					},
					{
						"key": "sentry-trace",
						"value": "8b620ff7b8cd4110b13cbfff511fc7fb-82e8aa69f77c956d-0"
					},
					{
						"key": "user-agent",
						"value": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36"
					},
					{
						"key": "Cookie",
						"value": "AMP_MKTG_10c012db0a=JTdCJTdE; __ddg1_=okq99EYhD2SdYNs85evj; AMP_10c012db0a=JTdCJTIyZGV2aWNlSWQlMjIlM0ElMjJiNWUzMTUxNi00MTBlLTQ4NDgtYmJjYy1kMWNiMzNiN2I0YTglMjIlMkMlMjJzZXNzaW9uSWQlMjIlM0ExNzU5MzQyNzkzMjgzJTJDJTIyb3B0T3V0JTIyJTNBZmFsc2UlMkMlMjJsYXN0RXZlbnRUaW1lJTIyJTNBMTc1OTM0MjgzNDIzMSUyQyUyMmxhc3RFdmVudElkJTIyJTNBNCU3RA==; _ym_uid=1759342839470538354; _ym_d=1759342839; _ym_visorc=w; _ym_isad=2"
					},
					{
						"key": "Authorization",
						"value": "Token {{token}}",
						"type": "text"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\r\n  \"last_name\": \"Листьев\",\r\n  \"first_name\": \"Владислав\",\r\n  \"patronymic\": \"Николаевич\",\r\n  \"birth_date\": \"1990-01-01\",\r\n  \"driving_experience_started\": \"2010-10-10\",\r\n  \"driver_licenses\": [\r\n    {\r\n      \"credential_type\": \"DRIVER_LICENSE\",\r\n      \"number\": \"012345\",\r\n      \"series\": \"1234\",\r\n      \"issue_date\": \"2010-10-10\"\r\n    }\r\n  ]\r\n}\r\n\r\n",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/{{api_version}}/insured_objects/drivers",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"{{api_version}}",
						"insured_objects",
						"drivers"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create_owner",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "accept-language",
						"value": "ru,uk;q=0.9,en-US;q=0.8,en;q=0.7,th;q=0.6"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.09.4,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=8b620ff7b8cd4110b13cbfff511fc7fb,sentry-sample_rate=0.5,sentry-sampled=false"
					},
					{
						"key": "content-type",
						"value": "application/json"
					},
					{
						"key": "origin",
						"value": "https://partner.agentapp.ru"
					},
					{
						"key": "priority",
						"value": "u=1, i"
					},
					{
						"key": "referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?0"
					},
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Windows\""
					},
					{
						"key": "sec-fetch-dest",
						"value": "empty"
					},
					{
						"key": "sec-fetch-mode",
						"value": "cors"
					},
					{
						"key": "sec-fetch-site",
						"value": "same-origin"
					},
					{
						"key": "sentry-trace",
						"value": "8b620ff7b8cd4110b13cbfff511fc7fb-82e8aa69f77c956d-0"
					},
					{
						"key": "user-agent",
						"value": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36"
					},
					{
						"key": "Cookie",
						"value": "AMP_MKTG_10c012db0a=JTdCJTdE; __ddg1_=okq99EYhD2SdYNs85evj; AMP_10c012db0a=JTdCJTIyZGV2aWNlSWQlMjIlM0ElMjJiNWUzMTUxNi00MTBlLTQ4NDgtYmJjYy1kMWNiMzNiN2I0YTglMjIlMkMlMjJzZXNzaW9uSWQlMjIlM0ExNzU5MzQyNzkzMjgzJTJDJTIyb3B0T3V0JTIyJTNBZmFsc2UlMkMlMjJsYXN0RXZlbnRUaW1lJTIyJTNBMTc1OTM0MjgzNDIzMSUyQyUyMmxhc3RFdmVudElkJTIyJTNBNCU3RA==; _ym_uid=1759342839470538354; _ym_d=1759342839; _ym_visorc=w; _ym_isad=2"
					},
					{
						"key": "Authorization",
						"value": "Token {{token}}",
						"type": "text"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\r\n  \"last_name\": \"Листьев\",\r\n  \"first_name\": \"Владислав\",\r\n  \"patronymic\": \"Николаевич\",\r\n  \"birth_date\": \"1990-01-01\",\r\n  \"credential\": [\r\n    {\r\n      \"credential_type\": \"RUSSIAN_INTERNAL_PASSPORT\",\r\n      \"issue_date\": \"2017-03-08\",\r\n      \"issue_point\": \"УФМС\",\r\n      \"issue_point_code\": \"123-456\",\r\n      \"number\": \"123456\",\r\n      \"series\": \"1234\"\r\n    }\r\n  ],\r\n  \"address\": [\r\n    {\r\n      \"address_query\": \"г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 2 к 1, кв 1\",\r\n      \"address_type\": \"LEGAL_ADDRESS\",\r\n      \"region_kladr_id\": \"7800000000000\",\r\n      \"city_kladr_id\": \"7800000600000\"\r\n    },\r\n    {\r\n      \"address_query\": \"г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 3 к 1, кв 1\",\r\n      \"address_type\": \"ACTUAL_ADDRESS\",\r\n      \"region_kladr_id\": \"7800000000000\",\r\n      \"city_kladr_id\": \"7800000600000\"\r\n    }\r\n  ]\r\n}\r\n",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/{{api_version}}/insured_objects/owners/natural_persons",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"{{api_version}}",
						"insured_objects",
						"owners",
						"natural_persons"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create_insured",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "accept-language",
						"value": "ru,uk;q=0.9,en-US;q=0.8,en;q=0.7,th;q=0.6"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.09.4,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=8b620ff7b8cd4110b13cbfff511fc7fb,sentry-sample_rate=0.5,sentry-sampled=false"
					},
					{
						"key": "content-type",
						"value": "application/json"
					},
					{
						"key": "origin",
						"value": "https://partner.agentapp.ru"
					},
					{
						"key": "priority",
						"value": "u=1, i"
					},
					{
						"key": "referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?0"
					},
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Windows\""
					},
					{
						"key": "sec-fetch-dest",
						"value": "empty"
					},
					{
						"key": "sec-fetch-mode",
						"value": "cors"
					},
					{
						"key": "sec-fetch-site",
						"value": "same-origin"
					},
					{
						"key": "sentry-trace",
						"value": "8b620ff7b8cd4110b13cbfff511fc7fb-82e8aa69f77c956d-0"
					},
					{
						"key": "user-agent",
						"value": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36"
					},
					{
						"key": "Cookie",
						"value": "AMP_MKTG_10c012db0a=JTdCJTdE; __ddg1_=okq99EYhD2SdYNs85evj; AMP_10c012db0a=JTdCJTIyZGV2aWNlSWQlMjIlM0ElMjJiNWUzMTUxNi00MTBlLTQ4NDgtYmJjYy1kMWNiMzNiN2I0YTglMjIlMkMlMjJzZXNzaW9uSWQlMjIlM0ExNzU5MzQyNzkzMjgzJTJDJTIyb3B0T3V0JTIyJTNBZmFsc2UlMkMlMjJsYXN0RXZlbnRUaW1lJTIyJTNBMTc1OTM0MjgzNDIzMSUyQyUyMmxhc3RFdmVudElkJTIyJTNBNCU3RA==; _ym_uid=1759342839470538354; _ym_d=1759342839; _ym_visorc=w; _ym_isad=2"
					},
					{
						"key": "Authorization",
						"value": "Token {{token}}",
						"type": "text"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\r\n  \"last_name\": \"Листьев\",\r\n  \"first_name\": \"Владислав\",\r\n  \"patronymic\": \"Николаевич\",\r\n  \"birth_date\": \"1990-01-01\",\r\n  \"credential\": [\r\n    {\r\n      \"credential_type\": \"RUSSIAN_INTERNAL_PASSPORT\",\r\n      \"issue_date\": \"2017-03-08\",\r\n      \"issue_point\": \"УФМС\",\r\n      \"issue_point_code\": \"123-456\",\r\n      \"number\": \"123456\",\r\n      \"series\": \"1234\"\r\n    }\r\n  ],\r\n  \"address\": [\r\n    {\r\n      \"address_query\": \"г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 2 к 1, кв 1\",\r\n      \"address_type\": \"LEGAL_ADDRESS\",\r\n      \"region_kladr_id\": \"7800000000000\",\r\n      \"city_kladr_id\": \"7800000600000\"\r\n    },\r\n    {\r\n      \"address_query\": \"г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 3 к 1, кв 1\",\r\n      \"address_type\": \"ACTUAL_ADDRESS\",\r\n      \"region_kladr_id\": \"7800000000000\",\r\n      \"city_kladr_id\": \"7800000600000\"\r\n    }\r\n  ]\r\n}\r\n",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/{{api_version}}/insured_objects/owners/natural_persons",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"{{api_version}}",
						"insured_objects",
						"owners",
						"natural_persons"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create_car",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "accept-language",
						"value": "ru,uk;q=0.9,en-US;q=0.8,en;q=0.7,th;q=0.6"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.09.4,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=8b620ff7b8cd4110b13cbfff511fc7fb,sentry-sample_rate=0.5,sentry-sampled=false"
					},
					{
						"key": "content-type",
						"value": "application/json"
					},
					{
						"key": "origin",
						"value": "https://partner.agentapp.ru"
					},
					{
						"key": "priority",
						"value": "u=1, i"
					},
					{
						"key": "referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?0"
					},
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Windows\""
					},
					{
						"key": "sec-fetch-dest",
						"value": "empty"
					},
					{
						"key": "sec-fetch-mode",
						"value": "cors"
					},
					{
						"key": "sec-fetch-site",
						"value": "same-origin"
					},
					{
						"key": "sentry-trace",
						"value": "8b620ff7b8cd4110b13cbfff511fc7fb-82e8aa69f77c956d-0"
					},
					{
						"key": "user-agent",
						"value": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Safari/537.36"
					},
					{
						"key": "Cookie",
						"value": "AMP_MKTG_10c012db0a=JTdCJTdE; __ddg1_=okq99EYhD2SdYNs85evj; AMP_10c012db0a=JTdCJTIyZGV2aWNlSWQlMjIlM0ElMjJiNWUzMTUxNi00MTBlLTQ4NDgtYmJjYy1kMWNiMzNiN2I0YTglMjIlMkMlMjJzZXNzaW9uSWQlMjIlM0ExNzU5MzQyNzkzMjgzJTJDJTIyb3B0T3V0JTIyJTNBZmFsc2UlMkMlMjJsYXN0RXZlbnRUaW1lJTIyJTNBMTc1OTM0MjgzNDIzMSUyQyUyMmxhc3RFdmVudElkJTIyJTNBNCU3RA==; _ym_uid=1759342839470538354; _ym_d=1759342839; _ym_visorc=w; _ym_isad=2"
					},
					{
						"key": "Authorization",
						"value": "Token {{token}}",
						"type": "text"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\r\n  \"car_model_id\": 864026180,\r\n  \"engine_power\": 211,\r\n  \"chassis_number\": 267461,\r\n  \"car_body_number\": 267461,\r\n  \"vin_number\": \"WAUZZZ8T4BA037241\",\r\n  \"number_plate\": \"Р904МХ178\",\r\n  \"manufacturing_year\": 2010,\r\n  \"max_mass\": null,\r\n  \"credential\": [\r\n    {\r\n      \"credential_type\": \"VEHICLE_REGISTRATION\",\r\n      \"issue_date\": \"2010-11-01\",\r\n      \"number\": \"267461\",\r\n      \"series\": \"78УН\"\r\n    }\r\n  ]\r\n}",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/v3/insured_objects/cars",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"v3",
						"insured_objects",
						"cars"
					]
				}
			},
			"response": []
		},
		{
			"name": "Create_object",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "accept-language",
						"value": "ru,uk;q=0.9,en-US;q=0.8,en;q=0.7,th;q=0.6"
					},
					{
						"key": "authorization",
						"value": "Token 306a6d42b6392f9ca1641f05b274ae9d61b42aec"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.10.01,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=14efae20707240a8bcf2c71ba2f22a05,sentry-sample_rate=0.5,sentry-sampled=true"
					},
					{
						"key": "cache-control",
						"value": "no-cache"
					},
					{
						"key": "content-type",
						"value": "application/json"
					},
					{
						"key": "origin",
						"value": "https://partner.agentapp.ru"
					},
					{
						"key": "priority",
						"value": "u=1, i"
					},
					{
						"key": "referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?1"
					},
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Android\""
					},
					{
						"key": "sec-fetch-dest",
						"value": "empty"
					},
					{
						"key": "sec-fetch-mode",
						"value": "cors"
					},
					{
						"key": "sec-fetch-site",
						"value": "same-origin"
					},
					{
						"key": "sentry-trace",
						"value": "14efae20707240a8bcf2c71ba2f22a05-b18452295f9ebc85-1"
					},
					{
						"key": "user-agent",
						"value": "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Mobile Safari/537.36"
					},
					{
						"key": "Cookie",
						"value": "AMP_MKTG_10c012db0a=JTdCJTdE; __ddg1_=okq99EYhD2SdYNs85evj; _ym_uid=1759342839470538354; _ym_d=1759342839; _ym_isad=2; _ym_visorc=w; AMP_10c012db0a=JTdCJTIyZGV2aWNlSWQlMjIlM0ElMjJiNWUzMTUxNi00MTBlLTQ4NDgtYmJjYy1kMWNiMzNiN2I0YTglMjIlMkMlMjJzZXNzaW9uSWQlMjIlM0ExNzYwMTIwMzEwMTEzJTJDJTIyb3B0T3V0JTIyJTNBZmFsc2UlMkMlMjJsYXN0RXZlbnRUaW1lJTIyJTNBMTc2MDEyMTcwMzI1NyUyQyUyMmxhc3RFdmVudElkJTIyJTNBNDQlN0Q="
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\"car\":\"3fb8dcad-9b62-404d-9e26-8bfbdf16c937\",\r\n\"insurant\":\"8d3539b1-8554-4bbc-951d-ee4121e006d1\",\r\n\"owner\":\"8d3539b1-8554-4bbc-951d-ee4121e006d1\",\r\n\"drivers\":[\"fee3618a-9200-44a7-b965-6344b896aace\"]}",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/{{api_version}}/insured_objects/",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"{{api_version}}",
						"insured_objects",
						""
					]
				}
			},
			"response": []
		},
		{
			"name": "Create_contract",
			"request": {
				"method": "POST",
				"header": [
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Android\""
					},
					{
						"key": "Authorization",
						"value": "Token 306a6d42b6392f9ca1641f05b274ae9d61b42aec"
					},
					{
						"key": "Cache-Control",
						"value": "no-cache"
					},
					{
						"key": "Referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?1"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.10.01,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=14efae20707240a8bcf2c71ba2f22a05,sentry-sample_rate=0.5,sentry-sampled=true"
					},
					{
						"key": "sentry-trace",
						"value": "14efae20707240a8bcf2c71ba2f22a05-b18452295f9ebc85-1"
					},
					{
						"key": "User-Agent",
						"value": "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Mobile Safari/537.36"
					},
					{
						"key": "Accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "Content-Type",
						"value": "application/json"
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\"has_car_trailer\":false,\r\n\"car_type\":\"B\",\r\n\"drivers_ids\":[\"fee3618a-9200-44a7-b965-6344b896aace\"],\r\n\"engine_power\":211,\r\n\"insurance_period\":8,\r\n\"insured_object\":\"c87cfa4e-4b41-488e-80f9-4cdc36688124\",\r\n\"owner_registration\":\r\n{\"address_query\":\"198412, г Санкт-Петербург, г Ломоносов, ул Швейцарская, д 1 к 1, кв. 1\",\r\n\"area\":null,\r\n\"area_fias_id\":null,\r\n\"area_kladr_id\":null,\r\n\"area_with_type\":null,\r\n\"block\":\"1\",\r\n\"block_type\":\"к\",\r\n\"city\":\"Ломоносов\",\r\n\"city_district\":null,\r\n\"city_district_fias_id\":null,\r\n\"city_district_kladr_id\":null,\r\n\"city_district_type\":null,\r\n\"city_district_type_full\":null,\r\n\"city_district_with_type\":null,\r\n\"city_fias_id\":\"3c2827f3-63f3-4692-8c74-c1318ee524fd\",\r\n\"city_kladr_id\":\"7800000600000\",\r\n\"city_with_type\":\"г Ломоносов\",\r\n\"country\":\"Россия\",\r\n\"fias_code\":null,\r\n\"fias_id\":\"2e2b9755-8b98-4446-9b14-f88227866768\",\r\n\"fias_level\":\"7\",\r\n\"flat\":\"1\",\r\n\"flat_type\":\"кв\",\r\n\"house\":\"1\",\r\n\"house_fias_id\":null,\r\n\"house_kladr_id\":null,\r\n\"house_type\":\"д\",\r\n\"kladr_id\":\"78000006000011700\",\r\n\"okato\":\"40290502000\",\r\n\"postal_code\":\"198412\",\r\n\"region\":\"Санкт-Петербург\",\r\n\"region_fias_id\":\"c2deb16a-0330-4f05-821f-1d09c93331e6\",\r\n\"region_kladr_id\":\"7800000000000\",\r\n\"region_with_type\":\"г Санкт-Петербург\",\r\n\"settlement\":null,\r\n\"settlement_fias_id\":null,\r\n\"settlement_kladr_id\":null,\r\n\"settlement_with_type\":null,\r\n\"street\":\"Швейцарская\",\r\n\"street_fias_id\":\"2e2b9755-8b98-4446-9b14-f88227866768\",\r\n\"street_kladr_id\":\"78000006000011700\",\r\n\"street_with_type\":\"ул Швейцарская\",\r\n\"address_type\":\"LEGAL_ADDRESS\",\r\n\"is_without_flat\":false},\r\n\"target_of_using\":11,\r\n\"valid_from\":\"2025-10-14\",\r\n\"valid_to\":\"2026-10-13\",\r\n\"is_car_without_registration\":false,\r\n\"with_ic_calculation\":false}",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/v3/agreements/calculations",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"v3",
						"agreements",
						"calculations"
					]
				}
			},
			"response": []
		},
		{
			"name": "Update_contract",
			"request": {
				"method": "PATCH",
				"header": [
					{
						"key": "accept",
						"value": "application/json, text/plain, */*"
					},
					{
						"key": "accept-language",
						"value": "ru,uk;q=0.9,en-US;q=0.8,en;q=0.7,th;q=0.6"
					},
					{
						"key": "authorization",
						"value": "Token 306a6d42b6392f9ca1641f05b274ae9d61b42aec"
					},
					{
						"key": "baggage",
						"value": "sentry-environment=production,sentry-release=master.ps.25.10.01,sentry-public_key=e29238c65deb4fa5aa004555aaeadda6,sentry-trace_id=14efae20707240a8bcf2c71ba2f22a05,sentry-sample_rate=0.5,sentry-sampled=true"
					},
					{
						"key": "cache-control",
						"value": "no-cache"
					},
					{
						"key": "content-type",
						"value": "application/json"
					},
					{
						"key": "priority",
						"value": "u=1, i"
					},
					{
						"key": "referer",
						"value": "https://partner.agentapp.ru/"
					},
					{
						"key": "sec-ch-ua",
						"value": "\"Google Chrome\";v=\"141\", \"Not?A_Brand\";v=\"8\", \"Chromium\";v=\"141\""
					},
					{
						"key": "sec-ch-ua-mobile",
						"value": "?1"
					},
					{
						"key": "sec-ch-ua-platform",
						"value": "\"Android\""
					},
					{
						"key": "sec-fetch-dest",
						"value": "empty"
					},
					{
						"key": "sec-fetch-mode",
						"value": "cors"
					},
					{
						"key": "sec-fetch-site",
						"value": "same-origin"
					},
					{
						"key": "sentry-trace",
						"value": "14efae20707240a8bcf2c71ba2f22a05-b18452295f9ebc85-1"
					},
					{
						"key": "user-agent",
						"value": "Mozilla/5.0 (Linux; Android 6.0; Nexus 5 Build/MRA58N) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/141.0.0.0 Mobile Safari/537.36"
					},
					{
						"key": "Cookie",
						"value": "AMP_MKTG_10c012db0a=JTdCJTdE; __ddg1_=okq99EYhD2SdYNs85evj; _ym_uid=1759342839470538354; _ym_d=1759342839; _ym_isad=2; _ym_visorc=w; AMP_10c012db0a=JTdCJTIyZGV2aWNlSWQlMjIlM0ElMjJiNWUzMTUxNi00MTBlLTQ4NDgtYmJjYy1kMWNiMzNiN2I0YTglMjIlMkMlMjJzZXNzaW9uSWQlMjIlM0ExNzYwMTIwMzEwMTEzJTJDJTIyb3B0T3V0JTIyJTNBZmFsc2UlMkMlMjJsYXN0RXZlbnRUaW1lJTIyJTNBMTc2MDEyMTcwMzI1NyUyQyUyMmxhc3RFdmVudElkJTIyJTNBNDQlN0Q="
					}
				],
				"body": {
					"mode": "raw",
					"raw": "{\r\n    \"insured_object\":\"64a3e37f-0464-4c2b-98d4-8a15bf32e8e9\"\r\n}",
					"options": {
						"raw": {
							"language": "json"
						}
					}
				},
				"url": {
					"raw": "{{http_schema}}://{{host}}/{{api_version}}/agreements/{{agreement_id}}",
					"protocol": "{{http_schema}}",
					"host": [
						"{{host}}"
					],
					"path": [
						"{{api_version}}",
						"agreements",
						"{{agreement_id}}"
					]
				}
			},
			"response": []
		}
	]
}
