## Вариант 7: Система контроля доступа

### Описание функции
Модуль проверки прав доступа пользователя к различным ресурсам на основе роли, времени суток и уровня секретности.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `role` | string | "admin", "manager", "employee", "contractor" |
| `resource_type` | string | "public", "internal", "confidential", "top_secret" |
| `access_time` | int | 0-23 (час) |
| `is_weekend` | bool | true/false |
| `clearance_level` | int | 1-5 (5 —最高) |

### Ожидаемое поведение (бизнес-правила)
- Admin имеет доступ ко всем ресурсам в любое время
- Manager: доступ к public и internal всегда, к confidential только в будни с 9 до 18
- Employee: доступ к public всегда, internal в будни 8-20, confidential только если clearance_level >= 4 и время 9-17
- Contractor: доступ только к public в рабочее время (9-18) в будни
- Top_secret доступен только admin и manager с clearance_level >= 5

### Исходный код
```python
def check_access(role, resource_type, access_time, is_weekend, clearance_level):
    if role == "admin":
        return True
    
    if is_weekend:
        if role == "contractor":
            return False
        if resource_type == "confidential" and role != "admin": 
            return False
    
    if resource_type == "public":
        return True
    
    if resource_type == "internal":
        if role == "manager" or role == "employee":
            if role == "employee" and (access_time < 8 or access_time > 20):  
                return False
            return True
        else:
            return False
    
    if resource_type == "confidential":
        if role == "manager":
            if access_time >= 9 and access_time <= 18 and not is_weekend: 
                return True
        elif role == "employee":
            if clearance_level >= 4 and access_time >= 9 and access_time < 17: 
                return True
        return False
    
    if resource_type == "top_secret":
        if role == "manager" and clearance_level >= 5:
            if access_time >= 9 and access_time <= 18: 
                return True
        return False
    
    return False
```