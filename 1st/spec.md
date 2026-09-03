# Spec for data model: SkateShop

# 1. Intention (Намір)
Розробити логічну модель даних для інтернет-магазину спорядження для скейтбордингу (деки, доріжки, колеса, компоненти), яка підтримує відстеження залишків, управління замовленнями та зберігання історії покупок.

# 2. Entities and attributes

1. **Customer (Клієнт)**
   - `id` *[PK, string]*: Ідентифікатор
   - `email` *[string]*: Електронна пошта
   - `fullName` *[string]*: ПІБ клієнта
   - `phoneNumber` *[string]*: Номер телефону
   - `createdAt` *[date]*: Дата реєстрації

2. **Category (Категорія)**
   - `id` *[PK, string]*: Ідентифікатор
   - `name` *[string]*: Назва категорії (Decks, wheels, trucks)
   - `description` *[string]*: Опис категорії

3. **Product (Продукт)**
   - `id` *[PK, string]*: Ідентифікатор
   - `categoryId` *[FK -> Category.id, string]*: Зв'язок з категорією
   - `title` *[string]*: Назва (наприклад, "Baker Logo Deck 8.25")
   - `description` *[string]*: Опис товару
   - `brand` *[string]*: Бренд виробника
   - `price` *[number]*: Актуальна ціна
   - `stockQuantity` *[number]*: Кількість на складі

4. **Order (Замовлення)**
   - `id` *[PK, string]*: Ідентифікатор
   - `customerId` *[FK -> Customer.id, string]*: Покупець
   - `totalAmount` *[number]*: Підсумкова сума (Кількість * ціна)
   - `status` *[string]*: Статус товару ('PENDING', 'DELIVERED', 'CANCELLED')
   - `createdAt` *[date]*: Дата оформлення

5. **OrderItem (Позиція замовленя)**
   - `id` *[PK, string]*: Ідентифікатор
   - `orderId` *[FK -> Order.id, string]*: Замовлення
   - `productId` *[FK -> Product.id, string]*: Продукт
   - `quantity` *[number]*: Кількість одиниць товару 
   - `unitPrice` *[number]*: ціна

## 3. Звязки

- `Category` 1 to N `Product` В категорії може міститися багато товарів, але товар може бути лише в одній категорії
- `Customer` 1 to N `Order` Один покупець може зробити багато замовлень
- `Order` N to M `Product` Order 1 до N OrderItem; Product 1 до N OrderItem.

## 4. Критерії прийняття
- Товари прив'язуються до чека через окрему сутність `OrderItem`, без прямого зв'язку "many-to-many".
- Ціна товару фіксується в `OrderItem.unitPrice` на момент оплати, щоб стара вартість замовлень не змінювалася після оновлення каталогу.
- Усі зв'язки між сутностями підтримують цілісність через зовнішні ключі (FK), тому якшо даних німає то запісі ні будіт.