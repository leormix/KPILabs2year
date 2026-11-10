## 1. Сценарій: Успішне оформлення замовлення (US-1 / UC_Order)

### Учасники взаємодії (Об'єкти / Компоненти):
* **`Customer` (Актор):** Кінцевий користувач, який ініціює покупку.
* **`OrderController` (Контролер):** Обробляє бізнес-логіку формування замовлення.
* **`StockService` (Сервіс складу):** Перевіряє та резервує залишки товару.
* **`Database` (СУБД):** Репозиторій даних (`Product`, `Order`, `OrderItem`, `Customer`).
* **`NotificationService` (Зовнішній сервіс):** Надсилає підтвердження клієнту.

### Кроки виконання (Steps):
1. `Customer` надсилає запит на оформлення замовлення з товарів у кошику.
2. `OrderController` звертається до `StockService` для перевірки наявності та резервування товару (`stockQuantity`).
3. `StockService` оновлює залишки в `Database` та повертає статус успіху.
4. `OrderController` створює запис замовлення в `Database` із фіксацією поточних цін (`OrderItem.unitPrice` — Price Snapshot з ADR 0001).
5. `Database` підтверджує збереження замовлення.
6. `OrderController` ініціює асинхронну відправку листа через `NotificationService`.
7. `OrderController` повертає клієнту статус успішного створення замовлення (`201 Created`).

---

## 2. Сценарій: Помилка оформлення через нестачу товару (US-2 / UC_CheckStock)

### Учасники взаємодії:
* **`Customer`**
* **`OrderController`**
* **`StockService`**
* **`Database`**

### Кроки виконання:
1. `Customer` намагається оформити замовлення на кількість товару, що перевищує поточний залишок на складі.
2. `OrderController` перевіряє залишки через `StockService`.
3. `StockService` порівнює запитувану кількість із полем `stockQuantity` у `Database`.
4. `Database` повертає дані про недостатню кількість.
5. `StockService` формує помилку перевірки залишків.
6. `OrderController` відхиляє запит і повертає `Customer` сповіщення про помилку (недостатньо товару).

---

## 3. Сценарій: Обробка онлайн-оплати (US-3 / UC_Payment)

### Учасники взаємодії:
* **`Customer`**
* **`OrderController`**
* **`PaymentGateway`**
* **`Database`**

### Кроки виконання:
1. `Customer` вибирає опцію онлайн-оплати для створеного замовлення в статусі `PENDING`.
2. `OrderController` створює платіжну сесію та перенаправляє `Customer` до `PaymentGateway`.
3. `Customer` вводять дані картки та підтверджують платіж у `PaymentGateway`.
4. **[Варіанти розвитку подій]:**
   * *Успішна оплата:* `PaymentGateway` надсилає успішний Callback до `OrderController`. `OrderController` оновлює статус замовлення в `Database` на `CONFIRMED`.
   * *Невдала оплата:* `PaymentGateway` повертає помилку оплати. `OrderController` фіксує помилку, залишаючи замовлення в статусі `PENDING`.
5. `OrderController` повертає результат операції клієнту.