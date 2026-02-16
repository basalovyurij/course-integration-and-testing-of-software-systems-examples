## Вариант 5: Калькулятор налогового вычета

### Описание функции
Расчет суммы налогового вычета за покупку недвижимости с учетом различных условий.

### Входные параметры
| Параметр | Тип | Диапазон |
|----------|-----|----------|
| `purchase_price` | float | 0 - 20 млн руб. |
| `is_first_property` | bool | true/false |
| `is_mortgage` | bool | true/false |
| `mortgage_interest` | float | 0 - 10 млн руб. (только если is_mortgage=True) |
| `previous_deductions` | float | 0 - 2 млн руб. |

### Ожидаемое поведение (бизнес-правила)
- Максимальный вычет за покупку: 2 млн руб. (возврат 13% = 260 тыс.)
- Если это не первая недвижимость, вычет доступен только если ранее не использован лимит
- Вычет за проценты по ипотеке: максимум 3 млн руб. (возврат 390 тыс.)
- Общий вычет не может превышать (2 млн - использовано ранее) + проценты
- Вычет за проценты доступен только если is_mortgage=True

### Исходный код
```python
def calculate_tax_deduction(purchase_price, is_first_property, is_mortgage, mortgage_interest=0, previous_deductions=0):
    MAX_PURCHASE_DEDUCTION = 2_000_000
    MAX_INTEREST_DEDUCTION = 3_000_000
    
    purchase_deduction = 0
    interest_deduction = 0
    
    if is_first_property:
        purchase_deduction = min(purchase_price, MAX_PURCHASE_DEDUCTION)
    else:
        if previous_deductions < MAX_PURCHASE_DEDUCTION:
            remaining = MAX_PURCHASE_DEDUCTION - previous_deductions
            purchase_deduction = min(purchase_price, remaining)
    
    if is_mortgage:
        if is_first_property:
            interest_deduction = min(mortgage_interest, MAX_INTEREST_DEDUCTION)
    
    tax_refund = (purchase_deduction + interest_deduction) * 0.13
    
    return round(tax_refund, 2)
```
