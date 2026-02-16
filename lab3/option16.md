## Вариант 16: Система логистики и доставки

### Описание функции
Расчет стоимости и сроков доставки груза с учетом расстояния, веса, габаритов и срочности.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `distance` | int | 1-10000 км |
| `weight` | float | 0.1-2000 кг |
| `volume` | float | 0.01-50 м³ |
| `fragile` | bool | true/false |
| `delivery_type` | string | "standard", "express", "same_day" |
| `insurance_value` | float | 0-10^7 руб. |

### Ожидаемое поведение (бизнес-правила)
- Базовая стоимость = weight × 10 руб./кг + volume × 500 руб./м³
- Коэффициент расстояния:
  - до 100 км: 1.0
  - 100-500 км: 1.2
  - 500-1500 км: 1.5
  - свыше 1500 км: 2.0
- Если fragile=True, добавляется 20% к стоимости
- Тип доставки:
  - standard: срок = distance / 500 + 1 день
  - express: срок = distance / 800 + 1 день, стоимость × 1.5
  - same_day: срок = 1 день (только если distance < 300 км), стоимость × 2.5
- Страховка: 1% от insurance_value, но не менее 100 руб.
- Максимальный вес для same_day: 50 кг, максимальный объем: 1 м³

### Формат результата
```python
{
    "cost": float, # стоимость доставки
    "delivery_days": float,  # срок доставки
}
```

### Исходный код
```python
def calculate_delivery(distance, weight, volume, fragile, delivery_type, insurance_value):
    if delivery_type == "same_day":
        if distance > 300:
            return "ERROR: Same day delivery not available for this distance"
        if weight > 50 or volume > 1:
            return "ERROR: Same day delivery weight/volume limit exceeded"
    
    base_cost = weight * 10 + volume * 500
    
    if distance < 100:
        distance_coef = 1.0
    elif distance >= 100 and distance < 500:
        distance_coef = 1.2
    elif distance >= 500 and distance < 1500:
        distance_coef = 1.5
    elif distance >= 1500:
        distance_coef = 2.0
    
    cost = base_cost * distance_coef
    
    if fragile:
        cost *= 1.2
    
    if delivery_type == "express":
        cost *= 1.5
        delivery_days = distance / 800 + 1
    elif delivery_type == "same_day":
        cost *= 2.5
        delivery_days = 1
    else:
        delivery_days = distance / 500 + 1
    
    insurance_cost = max(insurance_value * 0.01, 100)
    cost += insurance_cost
    
    return {
        "cost": round(cost, 2),
        "delivery_days": round(delivery_days, 1)
    }
```