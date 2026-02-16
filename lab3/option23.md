## Вариант 23: Калькулятор чаевых

### Описание функции
Расчет суммы чаевых в зависимости от качества обслуживания и количества гостей.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `bill_amount` | float | 100 - 100000 руб. |
| `service_quality` | int | 1-5 (1 - ужасно, 5 - отлично) |
| `guests_count` | int | 1-20 |

### Ожидаемое поведение (бизнес-правила)
- quality = 1-2: чаевые 5% от счета
- quality = 3-4: чаевые 10% от счета
- quality = 5: чаевые 15% от счета
- Если guests_count >= 6, автоматически добавляется сервисный сбор 10% (включен в счет, не в чаевые)
- Чаевые округляются до целого рубля

### Формат результата
```python
{
    "tip_amount": int,  # сумма чаевых
    "total_with_tip": float  # итого с чаевыми
}
```

### Исходный код
```python
def calculate_tip(bill_amount, service_quality, guests_count):
    if service_quality <= 2:
        tip_percent = 5
    elif service_quality <= 4:
        tip_percent = 10
    elif service_quality == 5:
        tip_percent = 15
    
    if guests_count > 6:
        bill_amount *= 1.1
    
    tip_amount = round(bill_amount * tip_percent / 100)
    total = bill_amount + tip_amount
    
    return {
        "tip_amount": tip_amount,
        "total_with_tip": round(total, 2)
    }
```