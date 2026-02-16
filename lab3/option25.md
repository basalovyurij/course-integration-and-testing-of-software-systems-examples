## Вариант 25: Оценка скорости интернета

### Описание функции
Оценка качества интернет-соединения на основе скорости загрузки и пинга.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `download_speed` | float | 0 - 1000 Мбит/с |
| `ping` | int | 0 - 500 мс |

### Ожидаемое поведение (бизнес-правила)
- Отлично: скорость > 100 Мбит/с и ping < 30 мс
- Хорошо: скорость 50-100 Мбит/с или ping 30-60 мс
- Удовлетворительно: скорость 10-50 Мбит/с или ping 60-100 мс
- Плохо: скорость < 10 Мбит/с или ping > 100 мс
- Если скорость = 0, соединение отсутствует

### Формат результата
```python
{
    "rating": str,  # "excellent", "good", "fair", "poor", "no_connection"
    "description": str  # текстовое описание
}
```

### Исходный код
```python
def rate_internet(download_speed, ping):
    if download_speed == 0:
        return {"rating": "no_connection", "description": "Нет соединения"}
    
    if download_speed > 100 and ping < 30:
        rating = "excellent"
        desc = "Отличное соединение"
    elif download_speed >= 50 or ping <= 60:
        rating = "good"
        desc = "Хорошее соединение"
    elif download_speed >= 10 or ping <= 100:
        rating = "fair"
        desc = "Удовлетворительное соединение"
    else:
        rating = "poor"
        desc = "Плохое соединение"
    
    return {"rating": rating, "description": desc}
```
