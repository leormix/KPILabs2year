# Spec for data model: SkateShop

# 1. Intention (Намір)
Design a logical data model for an online skate equipment store (decks, tracks, wheels, components) that supports inventory tracking, order management, and purchase history storage.

# 2. Entities and attributes
1. **Customer (Клієнт)**
    - `id` (PK, string) Ідентифікатор
    - `email` (string) Електронна пошта
    - `fullName` (string) ПІБ клієнта
    - `phoneNumber` (string) Номер телефону
    - `createdAt` (string) Дата реєстрації
2. **Category (Категорія)**
    - `id` (PK, string) Ідентифікатор
    - `name` (string) Назва категорії (Decks, wheels, trucks)
    - `description` (string) Опис категорії
