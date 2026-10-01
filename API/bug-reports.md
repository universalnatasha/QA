# Баг-репорты

**Автор:** Наталья Галкина
**Проект:** Яндекс.Прилавок — тестирование API
**Окружение:** сервер `https://{id}.serverhub.praktikum-services.ru`, Postman

Всего найдено дефектов: **42**.
Ниже — избранные: критический + те, что показывают разные техники тестирования. Полный список — в [Google-таблице](https://docs.google.com/spreadsheets/d/1h5WpUkkmivvwV8VFpI5aeLYNSERKwI1f9BcMmsnj0-4/edit?usp=sharing).

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
