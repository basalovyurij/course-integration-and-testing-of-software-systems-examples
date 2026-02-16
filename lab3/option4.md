## Вариант 4: Система кредитного скоринга

### Описание функции
Оценка кредитоспособности клиента на основе нескольких параметров для принятия решения о выдаче кредита.

### Входные параметры
| Параметр | Тип | Диапазон |
|----------|-----|----------|
| `income` | float | 0 - 10^7 руб./мес |
| `credit_history` | int | 300-850 (кредитный рейтинг) |
| `employment_years` | float | 0-50 |
| `loan_amount` | float | 1000 - 10^7 руб. |
| `has_guarantors` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Если доход < 15000: отказ
- Рассчитать соотношение loan_amount / income. Если > 30 (месяцев) и нет гарантов: отказ
- Если кредитный рейтинг < 500: отказ
- Если рейтинг 500-650 и employment_years < 1: отказ
- Если рейтинг > 750: одобрение с пониженной ставкой
- Если рейтинг 650-750 и есть гаранты или employment_years > 3: одобрение
- Иначе: отказ

### Исходный код
```python
def assess_credit(income, credit_history, employment_years, loan_amount, has_guarantors):
    if income <= 15000: 
        return "REJECTED"
    
    months_to_pay = loan_amount / income
    if months_to_pay > 30 and not has_guarantors:
        return "REJECTED"
    
    if credit_history < 500:
        return "REJECTED"
    
    if credit_history > 750:
        return "APPROVED_LOW_RATE"
    
    if credit_history >= 650 and credit_history <= 750:
        if has_guarantors or employment_years > 3:
            return "APPROVED"
    
    if credit_history >= 500 and credit_history < 650:
        if employment_years < 1:
            return "REJECTED"
        else:
            return "APPROVED"
    
    return "REJECTED"
```