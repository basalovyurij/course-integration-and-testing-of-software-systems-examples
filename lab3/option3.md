## Вариант 3: Валидатор банковской карты

### Описание функции
Проверка корректности данных банковской карты при онлайн-оплате.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `card_number` | string | 16 цифр, может содержать пробелы |
| `expiry_month` | int | 1-12 |
| `expiry_year` | int | 2023-2030 |
| `cvv` | string | 3 цифры |

### Ожидаемое поведение (бизнес-правила)
- Удалить все пробелы из номера карты, проверить что осталось 16 цифр
- Проверить номер карты алгоритмом Луна (Luhn algorithm)
- Проверить, что срок действия не истек (месяц/год >= текущей даты). Текущая дата: март 2024
- CVV должен содержать ровно 3 цифры
- Вернуть словарь с результатами проверки по каждому полю и общим статусом

### Исходный код
```python
def validate_card(card_number, expiry_month, expiry_year, cvv):
    result = {
        "card_number_valid": False,
        "expiry_valid": False,
        "cvv_valid": False,
        "overall_valid": False
    }
    
    clean_number = card_number.replace(" ", "")
    if clean_number.isdigit() and len(clean_number) == 16:
        # Алгоритм Луна 
        total = 0
        for i, digit in enumerate(clean_number):
            n = int(digit)
            if i % 2 == 0: 
                n *= 2
                if n > 9:
                    n -= 9
            total += n
        if total % 10 == 0:
            result["card_number_valid"] = True
    
    current_year = 2026
    current_month = 2
    
    if expiry_year > current_year:
        result["expiry_valid"] = True
    elif expiry_year == current_year:
        if expiry_month > current_month: 
            result["expiry_valid"] = True
    
    if len(cvv) == 3 and cvv.isdigit():
        result["cvv_valid"] = True
    
    if result["card_number_valid"] and result["expiry_valid"] and result["cvv_valid"]:
        result["overall_valid"] = True
    
    return result
```