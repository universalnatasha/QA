# Задание 3. API приложения Яндекс.Самокат

**Автор:** Наталья Галкина
**Инструмент:** Postman Version 12.23.8

> Ниже — избранные проверки и все баг-репорты. Полные версии — в [Google-таблице](https://docs.google.com/spreadsheets/d/1wzElPMYBIqpx98bidkGeo43Hz14wEUeH/edit?usp=sharing&ouid=100835019838458390635&rtpof=true&sd=true).

---

## 1. Чек-лист

Всего проверок: **21** (TestApi01–TestApi21).

### 1.1. Создание курьера: POST /api/v1/courier

| ID | Подтип проверки | Описание проверки | Статус | Баг |
|----|-----------------|-------------------|--------|-----|
| TestApi01 | Позитивные проверки (201 Created) | Создать курьера {"login": "ninja", "password": "1234", "firstName": "saske"}. | Passed | — |
| TestApi02 | Позитивные проверки (201 Created) | Создать курьера без поля firstName {"login": "ninja", "password": "1234"}. | Passed | — |
| TestApi03 | Позитивные проверки (201 Created) | Отправить запрос с пустым firstName: {"login": "ninja", "password": "1234", "firstName": ""}. ОР: 201 Created, заказ создан | Passed | — |
| TestApi04 | Проверки в БД и авторизации | Выполнить SELECT * FROM Couriers WHERE login='ninja'. Запись присутствует, passwordHash ≠ '1234', firstName совпадает | Passed | — |
| TestApi05 | Проверки в БД и авторизации | Проверить хэш через авторизацию: выполнить POST /api/v1/courier/login с login="ninja", password="1234". ОР: 200 OK, возвращается id курьера | Passed | — |
| TestApi06 | Негативные проверки (400 Bad Request) | Отправить запрос без поля login {"password": "1234", "firstName": "saske"} | Passed | — |
| TestApi07 | Негативные проверки (400 Bad Request) | Отправить запрос без поля password {"login": "ninja", "firstName": "saske"} | Passed | — |
| TestApi08 | Негативные проверки (409 Conflict) | Создать курьера с уже существующим логином (повторить создание "ninja") | Failed | BUG-API-01 |
| TestApi09 | Негативные проверки (400 Bad Request) | Отправить запрос с login как число: {"login": 123, "password": "1234", "firstName": "saske"}. ОР: 400 Bad Request, запрос отклонён | Failed | BUG-API-03 |
| TestApi10 | Негативные проверки (400 Bad Request) | Отправить запрос с пустым login: {"login": "", "password": "1234", "firstName": "saske"}. ОР: 400 Bad Request, запрос отклонён | Passed | — |
| TestApi11 | Негативные проверки (400 Bad Request) | Отправить запрос с password как число: {"login": "ninja", "password": 1234, "firstName": "saske"}. ОР: 400 Bad Request, запрос отклонён | Failed | BUG-API-04 |
| TestApi12 | Негативные проверки (400 Bad Request) | Отправить запрос с пустым password: {"login": "ninja", "password": "", "firstName": "saske"}. ОР: 400 Bad Request, запрос отклонён | Passed | — |
| TestApi13 | Негативные проверки (400 Bad Request) | Отправить запрос с firstName как число: {"login": "ninja", "password": "1234", "firstName": 123}. ОР: 400 Bad Request, запрос отклонён | Failed | BUG-API-05 |

### 1.2. Удаление курьера: DELETE /api/v1/courier/:id

| ID | Подтип проверки | Описание проверки | Статус | Баг |
|----|-----------------|-------------------|--------|-----|
| TestApi14 | Позитивные проверки (200 OK) после подготовки | Удалить курьера с login='ninja' (используя его id) | Passed | — |
| TestApi15 | Проверки в БД | Выполнить SELECT * FROM Couriers WHERE login='ninja'. Запись отсутствует | Passed | — |
| TestApi16 | Проверки в БД | Выполнить SELECT o.track FROM Orders o JOIN Couriers c ON o.courierId = c.id WHERE c.login='ninja'. Запрос возвращает 0 строк | Passed | — |
| TestApi17 | Негативные проверки (400 Bad Request) | Отправить запрос без id (например, /api/v1/courier/) | Failed | BUG-API-02 |
| TestApi18 | Негативные проверки (404 Not Found) | Удалить курьера с несуществующим id | Passed | — |

### 1.3. Получение заказа по треку: GET /api/v1/orders/track?t=:track

| ID | Подтип проверки | Описание проверки | Статус | Баг |
|----|-----------------|-------------------|--------|-----|
| TestApi19 | Позитивные проверки (200 OK) после подготовки | Запросить заказ с существующим треком | Passed | — |
| TestApi20 | Негативные проверки (400 Bad Request) | Запросить заказ без параметра t (/api/v1/orders/track) | Passed | — |
| TestApi21 | Негативные проверки (404 Not Found) | Запросить заказ с несуществующим треком | Passed | — |

---

## 2. Баг-репорты

Всего дефектов: **5** (BUG-API-01 — BUG-API-05).

| ID | Заголовок | Приоритет |
|----|-----------|-----------|
| BUG-API-01 | POST /api/v1/courier: при дублировании логина возвращается 409 с текстом, отличным от документации | Стандартный |
| BUG-API-02 | DELETE /api/v1/courier/: при запросе без id возвращается 404 вместо 400 | Стандартный |
| BUG-API-03 | POST /api/v1/courier: при передаче login как числа возвращается 500 вместо 400 | Критический |
| BUG-API-04 | POST /api/v1/courier: при передаче password как числа возвращается 201 Created вместо 400 | Критический |
| BUG-API-05 | POST /api/v1/courier: при передаче firstName как числа возвращается 201 Created вместо 400 | Критический |

### BUG-API-03 — POST /api/v1/courier: при передаче login как числа возвращается 500 вместо 400

| Поле | Значение |
|------|----------|
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/courier с телом {"login": 123, "password": "1234", "firstName": "saske"}. |
| **Ожидаемый результат** | HTTP/1.1 400 Bad Request, тело с сообщением об ошибке валидации |
| **Фактический результат** | HTTP/1.1 500 Internal Server Error, тело: {"code": 500, "message": "operator does not exist: character varying = integer"} |
| **Окружение** | Postman Version 12.23.8, стенд: https://05a2e610-fbd5-4491-8370-2c238c37e042.serverhub.praktikum-services.ru |
| **Статус** | Open |
| **Приоритет** | Критический |
| **Скриншот** | SAPI01 |

### BUG-API-04 — POST /api/v1/courier: при передаче password как числа возвращается 201 Created вместо 400

| Поле | Значение |
|------|----------|
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/courier с телом {"login": "ninja", "password": 1234, "firstName": "saske"}. |
| **Ожидаемый результат** | HTTP/1.1 400 Bad Request, тело с сообщением об ошибке валидации |
| **Фактический результат** | HTTP/1.1 201 Created, тело: {"ok": true}. Сервер принимает невалидный тип password (число вместо строки). |
| **Окружение** | Postman Version 12.23.8, стенд: https://05a2e610-fbd5-4491-8370-2c238c37e042.serverhub.praktikum-services.ru |
| **Статус** | Open |
| **Приоритет** | Критический |
| **Скриншот** | SAPI02 |

### BUG-API-05 — POST /api/v1/courier: при передаче firstName как числа возвращается 201 Created вместо 400

| Поле | Значение |
|------|----------|
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/courier с телом {"login": "ninja", "password": "1234", "firstName": 123}. |
| **Ожидаемый результат** | HTTP/1.1 400 Bad Request, тело с сообщением об ошибке валидации |
| **Фактический результат** | HTTP/1.1 201 Created, тело: {"ok": true}. Сервер принимает невалидный тип firstName (число вместо строки). |
| **Окружение** | Postman Version 12.23.8, стенд: https://05a2e610-fbd5-4491-8370-2c238c37e042.serverhub.praktikum-services.ru |
| **Статус** | Open |
| **Приоритет** | Критический |
| **Скриншот** | SAPI03 |

---

## 📊 Сводка

| Блок | Проверок | Passed | Failed |
|------|:--------:|:------:|:------:|
| Создание курьера | 13 | 10 | 3 |
| Удаление курьера | 5 | 4 | 1 |
| Получение заказа по треку | 3 | 3 | 0 |
| **Итого** | **21** | **17** | **4** |

**Найдено дефектов: 5** (BUG-API-01 — BUG-API-05).

---

## 🔗 Ссылки

- [Дипломная работа: все задания и баг-репорты (Google-таблица)](https://docs.google.com/spreadsheets/d/1wzElPMYBIqpx98bidkGeo43Hz14wEUeH/edit?usp=sharing&ouid=100835019838458390635&rtpof=true&sd=true)
- [Задание 1. Веб-приложение](./task-1-web.md)
- [Задание 2. Мобильное приложение](./task-2-mobile.md)
- [Назад к README](./README.md)
