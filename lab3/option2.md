## Вариант 2: Система скидок интернет-магазина

### Описание функции
Расчет итоговой стоимости заказа с учетом суммы покупки, статуса клиента и наличия промокода.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `amount` | float | 0 - 1 000 000 руб. |
| `customer_status` | string | "new", "regular", "vip" |
| `promocode` | string | "SALE10", "SALE20", "WELCOME" или пустая строка |

### Ожидаемое поведение (бизнес-правила)
- Базовая скидка: new — 0%, regular — 3%, vip — 10%
- Если сумма > 5000, regular получает +2% (итого 5%), vip получает +5% (итого 15%)
- Промокоды: "SALE10" дает +10%, "SALE20" дает +20%, "WELCOME" дает 15% только для new (не суммируется с базовой скидкой, заменяет её)
- Промокоды не суммируются между собой (применяется только один, наибольший)
- Итоговая скидка не может превышать 30%

### Исходный код
```python
def calculate_discount(amount, customer_status, promocode):
    base_discount = 0
    
    if customer_status == "new":
        base_discount = 0
    elif customer_status == "regular":
        base_discount = 3
    elif customer_status == "vip":
        base_discount = 10
    
    if amount > 5000:
        if customer_status == "regular":
            base_discount += 2 
        elif customer_status == "vip":
            base_discount = 15 
    
    promo_discount = 0
    if promocode == "SALE10":
        promo_discount = 10
    elif promocode == "SALE20":
        promo_discount = 20
    elif promocode == "WELCOME":
        if customer_status == "new":
            promo_discount = 15
            base_discount = 0 
    
    final_discount = max(base_discount, promo_discount)
    
    if final_discount > 30:
        final_discount = 30
    
    return amount * (1 - final_discount / 100)
```