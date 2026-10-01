# Баг-репорты

**Автор:** Наталья Галкина
**Проект:** Яндекс.Прилавок — тестирование API
**Окружение:** сервер `https://{id}.serverhub.praktikum-services.ru`, Postman

Всего найдено дефектов: **42**.
Ниже — избранные. Остальные — в [Google-таблице](https://docs.google.com/spreadsheets/d/1h5WpUkkmivvwV8VFpI5aeLYNSERKwI1f9BcMmsnj0-4/edit?usp=sharing).

---

## 🔴 Критический

### БР46 — Ошибка 404 Not Found при удалении существующей корзины в DELETE /api/v1/orders/{id}

| Поле | Значение |
|------|----------|
| **Приоритет** | Critical (Критический) |
| **Предусловие** | Авторизация выполнена. Корзина с id=2 только что создана (подтверждено ответом 201 Created). |
| **Шаги воспроизведения** | 1. Отправить DELETE-запрос на /api/v1/orders/2. |
| **ОР** | Код ответа 200 OK. Тело: {"ok": true}. |
| **ФР** | Код ответа 404 Not Found. Корзина не найдена, хотя была только что создана. |
| **Окружение** | Адрес сервера: https://8c7c2bdd-d3ed-480d-af04-be07965934ee.serverhub.praktikum-services.ru |
| **Примечание** | Баг системный, воспроизводится на разных стендах. |
| **Логи** | 2026-06-23T14:12:00.154Z DELETE:/api/v1/orders/2 - запрос, ответ 404 Not Found. Ошибка: NotFoundError: Корзина с id=2 не найдена. |

---

## 🟠 Высокий приоритет

### БР1 — Ошибка 500 Internal Server Error при невалидном id набора /api/v1/kits/abc/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан пользователь и получен токен авторизации. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/abc/products с телом {"productsList":[{"id":1,"quantity":1}]}. |
| **ОР** | Код ответа 404 Not Found в формате JSON. |
| **ФР** | Код ответа 500 Internal Server Error в формате HTML. |
| **Окружение** | Адрес сервера: https://bb731e73-da8a-4f37-855f-528dad9bb5f6.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T08:49:35.098Z [Main] Запрос POST /api/v1/kits/abc/products {"productsList":[{"id":1,"quantity":1}]}; Ответ 500 Internal Server Error |

---

### БР2 — Сервер не выполняет валидацию id продукта и возвращает 200 OK вместо 400 Bad Request для множества невалидных значений (несуществующий, отрицательное, ноль, null, отсутствие параметра) в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом, содержащим невалидный id продукта (например: 9999, -1, 0, null или без id). |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Продукт не добавлен или добавлен частично. Сервер игнорирует некорректный id. |
| **Окружение** | Адрес сервера: https://6b7ba9f0-dea0-4c1d-8443-0aa22e50d2b7.serverhub.praktikum-services.ru |
| **Примечание** | Проверены значения: 9999, -1, 0, null, отсутствие. Покрывает тесты Т4, Т8, Т9, Т10, Т11. |
| **Логи** | 2026-06-23T17:11:33.763Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":9999,"quantity":1}]}; Ответ 200 OK (пример для несуществующего id; аналогично для -1, 0, null и отсутствия) |

---

### БР3 — Ошибка 500 Internal Server Error при невалидном id продукта (буквы) в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":"abc","quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error в формате HTML. |
| **Окружение** | Адрес сервера: https://bb731e73-da8a-4f37-855f-528dad9bb5f6.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T09:22:32.338Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":"abc","quantity":1}]}; Ответ 500 Internal Server Error |

---

### БР4 — Ошибка 500 Internal Server Error при невалидном id продукта (спецсимволы) в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":"!@#","quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error в формате HTML. Тело: Internal Server Error. |
| **Окружение** | Адрес сервера: https://66c050f6-4d81-41ea-a356-9f17a329b386.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T17:22:14.499Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":"!@#","quantity":1}]}; Ответ 500 Internal Server Error |

---

### БР5 — Ошибка 500 Internal Server Error при невалидном id продукта (дробь) в POST /api/v1/kits/8/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор (на данном стенде id=8). Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/8/products с телом {"productsList":[{"id":1.5,"quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error в формате HTML. Тело: Internal Server Error. |
| **Окружение** | Адрес сервера: https://1c6d07fd-5102-46b4-a76e-04e518b06d93.serverhub.praktikum-services.ru |
| **Примечание** | Тест проводился на стенде, где набор получил id=8. Ошибка идентична. |
| **Логи** | 2026-06-23T17:34:51.601Z [Main] Запрос POST /api/v1/kits/8/products {"productsList":[{"id":1.5,"quantity":1}]}; Ответ 500 Internal Server Error |

---

### БР10 — Сервер принимает quantity=0 без ошибки вместо отклонения запроса с кодом 400 в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":1,"quantity":0}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 ОК. Продукт с quantity=0 проигнорирован, но запрос принят. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T09:47:11.572Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":1,"quantity":0}]}; Ответ 200 OK |

---

### БР11 — Сервер принимает quantity=-1 без ошибки вместо отклонения запроса с кодом 400 в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":1,"quantity":-1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 ОК. Продукт с quantity=-1 проигнорирован, но запрос принят. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T09:49:34.175Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":1,"quantity":-1}]}; Ответ 200 OK |

---

### БР12 — Ошибка 500 Internal Server Error при quantity="много" в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":1,"quantity":"много"}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error. Тело: {"code":500,"message":"invalid input syntax for integer: \"100много\""}. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T09:50:43.500Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":1,"quantity":"много"}]}; Ответ 500 Internal Server Error |

---

### БР13 — Сервер принимает quantity=null без ошибки вместо отклонения запроса с кодом 400 в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":1,"quantity":null}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 ОК. Продукт с quantity=null проигнорирован, но запрос принят. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР10 и БР11. Сервер не валидирует quantity. |
| **Логи** | 2026-06-23T09:53:01.894Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":1,"quantity":null}]}; Ответ 200 OK |

---

### БР14 — Ошибка 500 Internal Server Error при отсутствии параметра quantity в объекте продукта в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[{"id":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error. Тело: {"code":500,"message":"invalid input syntax for integer: \"NaN\""}. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T09:55:02.082Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[{"id":1}]}; Ответ 500 Internal Server Error |

---

### БР15 — Сервер принимает пустой массив productsList=[] без ошибки вместо отклонения запроса с кодом 400 в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":[]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 ОК. Пустой массив проигнорирован, набор не изменился. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР10, БР11, БР13. Сервер не валидирует структуру productsList. |
| **Логи** | 2026-06-23T09:58:54.745Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":[]}; Ответ 200 OK |

---

### БР16 — Сервер принимает productsList=null без ошибки вместо отклонения запроса с кодом 400 в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с телом {"productsList":null}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 ОК. null проигнорирован, набор не изменился. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР10, БР11, БР13, БР15. Сервер не валидирует структуру productsList. |
| **Логи** | 2026-06-23T09:59:43.710Z [Main] Запрос POST /api/v1/kits/7/products {"productsList":null}; Ответ 200 OK |

---

### БР17 — Сервер принимает пустое тело {} без ошибки вместо отклонения запроса с кодом 400 в POST /api/v1/kits/7/products

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Создан набор с id=7. Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос на /api/v1/kits/7/products с пустым телом {}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 ОК. Пустое тело проигнорировано, набор не изменился. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР10, БР11, БР13, БР15, БР16. Сервер не валидирует тело запроса. |
| **Логи** | 2026-06-23T10:00:40.046Z [Main] Запрос POST /api/v1/kits/7/products (пустое тело); Ответ 200 OK |

---

### БР18 — Сервер принимает превышение количества продуктов (15) без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=15, productsWeight=5, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. clientDeliveryCost=99, хотя ожидалась ошибка валидации. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T15:24:33.618Z [Courier][fast-delivery] Запрос productsCount=15, productsWeight=5, deliveryTime=12; Ответ 200 OK |

---

### БР19 — Сервер принимает productsCount=0 без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=0, productsWeight=1, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил нулевое количество. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T15:29:37.367Z [Courier][fast-delivery] Запрос productsCount=0, productsWeight=1, deliveryTime=12; Ответ 200 OK |

---

### БР20 — Сервер принимает productsCount=-5 без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=-5, productsWeight=1, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил отрицательное количество. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T15:31:25.613Z [Courier][fast-delivery] Запрос productsCount=-5, productsWeight=1, deliveryTime=12; Ответ 200 OK |

---

### БР21 — Сервер принимает productsCount="abc" без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount="abc", productsWeight=1, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил строковое значение. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T15:32:33.033Z [Courier][fast-delivery] Запрос productsCount="abc", productsWeight=1, deliveryTime=12; Ответ 200 OK |

---

### БР22 — Сервер принимает пустой тег <productsCount></productsCount> без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount пустым (тег без значения), productsWeight=1, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил отсутствие значения. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T15:35:02.942Z [Courier][fast-delivery] Запрос с пустым productsCount, productsWeight=1, deliveryTime=12; Ответ 200 OK |

---

### БР23 — Ошибка 500 Internal Server Error при отсутствии параметра productsCount в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос без тега <productsCount>, с productsWeight=1, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T15:36:34.601Z [Courier][fast-delivery] Запрос без productsCount, productsWeight=1, deliveryTime=12; Ответ 500 Internal Server Error (Cannot read property '0' of undefined) |

---

### БР24 — Сервер принимает превышение веса (7 кг) без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=10, productsWeight=7, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. clientDeliveryCost=99, хотя ожидалась ошибка валидации. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР18. |
| **Логи** | 2026-06-23T15:38:05.487Z [Courier][fast-delivery] Запрос productsCount=10, productsWeight=7, deliveryTime=12; Ответ 200 OK |

---

### БР25 — Сервер принимает productsWeight=0 без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=3, productsWeight=0, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил нулевой вес. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР19. |
| **Логи** | 2026-06-23T15:42:17.903Z [Courier][fast-delivery] Запрос productsCount=3, productsWeight=0, deliveryTime=12; Ответ 200 OK |

---

### БР26 — Сервер принимает productsWeight=-1 без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=3, productsWeight=-1, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил отрицательный вес. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР20, БР25. |
| **Логи** | 2026-06-23T15:44:05.877Z [Courier][fast-delivery] Запрос productsCount=3, productsWeight=-1, deliveryTime=12; Ответ 200 OK |

---

### БР27 — Сервер принимает productsWeight="abc" без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=3, productsWeight="abc", deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил строковое значение. |
| **Окружение** | Адрес сервера: https://d47ed412-c845-43ef-aa66-cab8902377a9.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР21. |
| **Логи** | 2026-06-23T15:45:17.339Z [Courier][fast-delivery] Запрос productsCount=3, productsWeight="abc", deliveryTime=12; Ответ 200 OK |

---

### БР28 — Сервер принимает пустой тег <productsWeight></productsWeight> без ошибки вместо кода 400 в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=3, пустым productsWeight, deliveryTime=12. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил пустой тег. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР22. |
| **Логи** | 2026-06-23T15:51:57.721Z [Courier][fast-delivery] Запрос productsCount=3, productsWeight=, deliveryTime=12; Ответ 200 OK |

---

### БР29 — Ошибка 500 Internal Server Error при отсутствии параметра productsWeight в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=3, deliveryTime=12, без тега productsWeight. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error. |
| **Окружение** | Адрес сервера: https://63749e5b-d2a0-4e16-b73c-adb071f53856.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР23. |
| **Логи** | 2026-06-23T15:56:23.583Z [Courier][fast-delivery] Запрос productsCount=3, productsWeight отсутствует, deliveryTime=12; Ответ 500 Internal Server Error |

---

### БР30 — Сервер не выполняет валидацию deliveryTime и возвращает 200 OK вместо 400 Bad Request для невалидных значений (включая нерабочие часы, отрицательные, буквы, пустой тег) в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с невалидным deliveryTime (например: 6, 5, 0, 22, 23, 24, -1, "abc", пустой тег) при валидных productsCount=1, productsWeight=1. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Тело ответа: <response name="Привезём быстро"/> (для пустого тега — расширенный ответ). Сервер не отклоняет запросы с некорректным временем доставки. |
| **Окружение** | Адрес сервера: https://10787ddc-5ca0-48ca-92c2-cee29088a8f2.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР27, БР28, БР29. |
| **Логи** | 2026-06-23T16:07:33.510Z [Courier][fast-delivery] Запрос productsCount=1, productsWeight=1, deliveryTime=6; Ответ 200 OK (пример для Т50, аналогично для Т51–Т58) |

---

### БР32 — Сервер возвращает 409 Conflict вместо 400 Bad Request при несуществующем id продукта в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":9999,"quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 409 Conflict. Тело: {"code":409,"message":"Нет склада, способного обработать Ваш заказ"}. |
| **Окружение** | Адрес сервера: https://10787ddc-5ca0-48ca-92c2-cee29088a8f2.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T12:50:02.470Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":9999,"quantity":1}]}, ответ 409 Conflict (Internal Server Error) |

---

### БР33 — Ошибка 500 Internal Server Error при невалидном формате id продукта (буквы) в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":"abc","quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request в формате JSON. |
| **ФР** | Код ответа 500 Internal Server Error. Тело: {"code":500,"message":"invalid input syntax for integer: \"abc\""}. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T13:15:36.379Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":"abc","quantity":1}]}, ответ 500 Internal Server Error. Ошибка: "invalid input syntax for integer: \"abc\"". |

---

### БР34 — Сервер возвращает 409 Conflict вместо 400 Bad Request при id продукта = 0 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":0,"quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 409 Conflict. Тело: {"code":409,"message":"Нет склада, способного обработать Ваш заказ"}. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР32. |
| **Логи** | 2026-06-23T13:20:28.266Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":0,"quantity":1}]}, ответ 409 Conflict (Internal Server Error). Ошибка: ConflictError: Нет склада, способного обработать Ваш заказ. |

---

### БР35 — Сервер возвращает 409 Conflict вместо 400 Bad Request при id продукта = -1 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":-1,"quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 409 Conflict. Тело: {"code":409,"message":"Нет склада, способного обработать Ваш заказ"}. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР32, БР34. |
| **Логи** | 2026-06-23T13:24:38.306Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":-1,"quantity":1}]}, ответ 409 Conflict (Internal Server Error). Ошибка: ConflictError: Нет склада, способного обработать Ваш заказ. |

---

### БР36 — Сервер возвращает 409 Conflict вместо 400 Bad Request при id продукта = null в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":null,"quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 409 Conflict. Тело: {"code":409,"message":"Нет склада, способного обработать Ваш заказ"}. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР32, БР34, БР35. |
| **Логи** | 2026-06-23T13:26:19.955Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":null,"quantity":1}]}, ответ 409 Conflict (Internal Server Error). Ошибка: ConflictError: Нет склада, способного обработать Ваш заказ. |

---

### БР37 — Сервер возвращает 409 Conflict вместо 400 Bad Request при отсутствии параметра id продукта в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"quantity":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 409 Conflict. Тело: {"code":409,"message":"Нет склада, способного обработать Ваш заказ"}. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР32, БР34-БР36. |
| **Логи** | 2026-06-23T13:28:28.021Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"quantity":1}]}, ответ 409 Conflict (Internal Server Error). Ошибка: ConflictError: Нет склада, способного обработать Ваш заказ. |

---

### БР38 — Сервер принимает quantity=0 без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":1,"quantity":0}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил нулевое количество. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР10. |
| **Логи** | 2026-06-23T13:29:52.131Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":1,"quantity":0}]}, ответ 200 OK. |

---

### БР39 — Сервер принимает quantity=-1 без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":1,"quantity":-1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил отрицательное количество. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР11, БР38. |
| **Логи** | 2026-06-23T13:32:19.854Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":1,"quantity":-1}]}, ответ 200 OK. |

---

### БР40 — Сервер принимает quantity="много" без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":1,"quantity":"много"}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил строковое значение. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР12. |
| **Логи** | 2026-06-23T13:36:50.352Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":1,"quantity":"много"}]}, ответ 200 OK. |

---

### БР41 — Сервер принимает quantity=null без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":1,"quantity":null}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил null. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР13, БР38-БР40. |
| **Логи** | 2026-06-23T13:38:26.726Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":1,"quantity":null}]}, ответ 200 OK. |

---

### БР42 — Сервер принимает запрос без quantity и записывает некорректное значение вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[{"id":1}]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. В ответе quantity="0многоnullnullundefined". Сервер не отклонил отсутствие параметра и записал мусор. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Логи** | 2026-06-23T13:41:32.857Z PUT:/api/v1/orders/2 - запрос {"productsList":[{"id":1}]}, ответ 200 OK. |

---

### БР43 — Сервер принимает пустой productsList=[] без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":[]}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил пустой массив. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР15. |
| **Логи** | 2026-06-23T13:43:39.389Z PUT:/api/v1/orders/2 - запрос {"productsList":[]}, ответ 200 OK. |

---

### БР44 — Сервер принимает productsList=null без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с телом {"productsList":null}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил null. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР16, БР43. |
| **Логи** | 2026-06-23T13:45:05.777Z PUT:/api/v1/orders/2 - запрос {"productsList":null}, ответ 200 OK. |

---

### БР45 — Сервер принимает пустое тело {} без ошибки вместо кода 400 в PUT /api/v1/orders/2

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. Корзина id=2 существует. |
| **Шаги воспроизведения** | 1. Отправить PUT-запрос на /api/v1/orders/2 с пустым телом {}. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 200 OK. Сервер не отклонил пустое тело. |
| **Окружение** | Адрес сервера: https://9704da74-a0a1-47c2-923c-9f45d5ef8309.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР17, БР43, БР44. Сервер накопил мусорное значение quantity из предыдущих запросов. |
| **Логи** | 2026-06-23T13:46:52.556Z PUT:/api/v1/orders/2 - запрос с пустым телом, ответ 200 OK. |

---

### БР50 — Сервер возвращает 500 Internal Server Error при полном отсутствии тега <deliveryTime> вместо 400 Bad Request в POST /fast-delivery/v3.1.1/calculate-delivery.xml

| Поле | Значение |
|------|----------|
| **Приоритет** | High (Высокий) |
| **Предусловие** | Авторизация выполнена. |
| **Шаги воспроизведения** | 1. Отправить POST-запрос с productsCount=1, productsWeight=1, без тега deliveryTime. |
| **ОР** | Код ответа 400 Bad Request. |
| **ФР** | Код ответа 500 Internal Server Error. Сообщение: "Cannot read property '0' of undefined". |
| **Окружение** | Адрес сервера: https://10787ddc-5ca0-48ca-92c2-cee29088a8f2.serverhub.praktikum-services.ru |
| **Примечание** | Аналогичен БР29. |
| **Логи** | 2026-06-23T16:24:07.578Z [Courier][fast-delivery] Запрос productsCount=1, productsWeight=1, deliveryTime отсутствует; Ответ 500 Internal Server Error |

---

## 📊 Сводка по всем 42 баг-репортам

| Приоритет | Кол-во | ID |
|-----------|:------:|-----|
| Critical (Критический) | 1 | БР46 |
| High (Высокий) | 41 | БР1–БР45, БР50 |
| **Итого** | **42** | |

Остальные — в [Google-таблице](https://docs.google.com/spreadsheets/d/1h5WpUkkmivvwV8VFpI5aeLYNSERKwI1f9BcMmsnj0-4/edit?usp=sharing).

---

## 🔗 Ссылки

- [Все 42 баг-репорта (Google-таблица)](https://docs.google.com/spreadsheets/d/1h5WpUkkmivvwV8VFpI5aeLYNSERKwI1f9BcMmsnj0-4/edit?usp=sharing)
- [Чек-лист](./checklist.md)
- [Назад к README](./README.md)
