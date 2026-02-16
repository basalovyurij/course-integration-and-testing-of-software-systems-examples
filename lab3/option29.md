## Вариант 29: Расчет скидки на книгу

### Описание функции
Расчет скидки на книгу в зависимости от количества и типа обложки.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `quantity` | int | 1 - 100 |
| `cover_type` | string | "soft", "hard" |

### Ожидаемое поведение (бизнес-правила)
- Цена: мягкая обложка = 500 руб., твердая = 800 руб.
- Скидка:
  - 5-9 книг: 5%
  - 10-19 книг: 10%
  - 20+ книг: 15%
- Если сумма покупки > 5000 руб., дополнительная скидка 5% (суммируется)
- Итоговая скидка не более 20%

### Формат результата
```python
{
    "base_price": float,  # базовая цена
    "discount_percent": int,  # итоговая скидка
    "final_price": float  # итоговая цена
}
```

### Исходный код (с ошибками)
```python
def calculate_book_discount(quantity, cover_type):
    price_per_book = 500 if cover_type == "soft" else 800
    base_price = quantity * price_per_book
    
    if quantity >= 20:
        discount = 15
    elif quantity >= 10:
        discount = 10
    elif quantity >= 5:
        discount = 5
    else:
        discount = 0
    
    if base_price > 5000:
        discount += 5
    
    if discount > 20:
        discount = 20
    
    final_price = base_price * (1 - discount / 100)
    
    return {
        "base_price": base_price,
        "discount_percent": discount,
        "final_price": round(final_price, 2)
    }
```