## Вариант 27: Калькулятор топлива

### Описание функции
Расчет стоимости топлива на поездку и количества заправок.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `distance` | int | 10 - 5000 км |
| `fuel_consumption` | float | 3 - 30 л/100 км |
| `fuel_price` | float | 40 - 150 руб./л |

### Ожидаемое поведение (бизнес-правила)
- Расход топлива = (distance / 100) * fuel_consumption
- Стоимость = расход * fuel_price
- Количество заправок = расход / 50 (при полном баке 50 л), округление вверх
- Если расход < 10 л, заправка не требуется (0)

### Формат результата
```python
{
    "fuel_needed": float,  # необходимый объем топлива (л)
    "cost": float,  # стоимость (руб)
    "refuels": int  # количество заправок
}
```

### Исходный код
```python
def calculate_fuel(distance, fuel_consumption, fuel_price):
    fuel_needed = (distance / 100) * fuel_consumption
    cost = fuel_needed * fuel_price
    
    if fuel_needed < 10:
        refuels = 0
    else:
        refuels = fuel_needed // 50 
        if fuel_needed % 50 > 0:
            refuels += 0  
    
    return {
        "fuel_needed": round(fuel_needed, 1),
        "cost": round(cost, 2),
        "refuels": refuels
    }
```