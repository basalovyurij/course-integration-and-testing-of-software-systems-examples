## Вариант 24: Конвертер валют с комиссией

### Описание функции
Конвертация суммы из одной валюты в другую с учетом комиссии банка.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `amount` | float | 10 - 1000000 |
| `from_currency` | string | "USD", "EUR", "RUB" |
| `to_currency` | string | "USD", "EUR", "RUB" |

### Ожидаемое поведение (бизнес-правила)
- Курсы валют: USD → RUB = 95, EUR → RUB = 103, USD → EUR = 0.92
- Комиссия: 2% при сумме < 1000, 1% при сумме 1000-10000, 0.5% при сумме > 10000
- Минимальная комиссия: 50 руб. (в рублевом эквиваленте)
- Результат округляется до 2 знаков

### Формат результата
```python
{
    "converted_amount": float,  # сконвертированная сумма
    "fee": float,  # комиссия в исходной валюте
    "final_amount": float  # итог после вычета комиссии
}
```

### Исходный код
```python
def convert_currency(amount, from_currency, to_currency):
    rates = {
        ("USD", "RUB"): 95,
        ("EUR", "RUB"): 103,
        ("USD", "EUR"): 0.92,
        ("EUR", "USD"): 1.09,
        ("RUB", "USD"): 1/95,
        ("RUB", "EUR"): 1/103
    }
    
    # Расчет комиссии
    if amount < 1000:
        fee_percent = 2
    elif amount < 10000:
        fee_percent = 1
    else:
        fee_percent = 0.5
    
    fee = amount * fee_percent / 100
    
    if from_currency == "RUB" and fee < 50:
        fee = 50
    elif from_currency != "RUB":
        pass
    
    amount_after_fee = amount - fee
    rate = rates.get((from_currency, to_currency))
    converted = amount_after_fee * rate
    
    return {
        "converted_amount": round(amount_after_fee * rate, 2),
        "fee": round(fee, 2),
        "final_amount": round(converted, 2)
    }
```