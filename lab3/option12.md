## Вариант 12: Система оценки сложности пароля

### Описание функции
Модуль оценки сложности пароля и проверки соответствия требованиям безопасности.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `password` | string | любая строка |
| `username` | string | любая строка |
| `min_length` | int | 6-20 |
| `require_uppercase` | bool | true/false |
| `require_numbers` | bool | true/false |
| `require_special` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Проверка длины: пароль должен быть не меньше `min_length`
- Проверка на содержание имени пользователя (пароль не должен содержать username как подстроку)
- Проверка на заглавные буквы: если require_uppercase=True, должна быть хотя бы одна заглавная буква
- Проверка на цифры: если require_numbers=True, должна быть хотя бы одна цифра
- Проверка на спецсимволы: если require_special=True, должен быть хотя бы один символ !@#$%^&*
- Возвращает словарь с результатами каждой проверки и общий статус (True, если все проверки пройдены)
- Дополнительно: оценка сложности (слабый/средний/сильный) на основе количества выполненных критериев

### Формат результата
```python
{
    "length_ok": bool,
    "no_username_ok": bool,
    "uppercase_ok": bool,
    "numbers_ok": bool,
    "special_ok": bool,
    "overall_valid": bool,
    "strength": str, # "weak", "medium", "strong"
}
```

### Исходный код
```python
def check_password_strength(password, username, min_length, require_uppercase, require_numbers, require_special):
    result = {
        "length_ok": False,
        "no_username_ok": False,
        "uppercase_ok": False,
        "numbers_ok": False,
        "special_ok": False,
        "overall_valid": False,
        "strength": "weak"
    }
    
    if len(password) >= min_length:
        result["length_ok"] = True
    
    if username.lower() not in password.lower():
        result["no_username_ok"] = True
    
    if require_uppercase:
        if any(c.isupper() for c in password):
            result["uppercase_ok"] = True
    else:
        result["uppercase_ok"] = True
    
    if require_numbers:
        if any(c.isdigit() for c in password):
            result["numbers_ok"] = True
    else:
        result["numbers_ok"] = False
    
    special_chars = "!@#$%^&*"
    if require_special:
        if any(c in special_chars for c in password):
            result["special_ok"] = True
    else:
        result["special_ok"] = True
    
    if result["length_ok"] and result["no_username_ok"] and result["uppercase_ok"] and result["numbers_ok"] and result["special_ok"]:
        result["overall_valid"] = True
    
    criteria_met = sum([result["length_ok"], result["uppercase_ok"], result["numbers_ok"], result["special_ok"]])
    if criteria_met >= 4:
        result["strength"] = "strong"
    elif criteria_met >= 2:
        result["strength"] = "medium"
    
    return result
```