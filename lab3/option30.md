## Вариант 30: Калькулятор калорий в кофе

### Описание функции
Расчет калорийности кофейного напитка.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `coffee_type` | string | "espresso", "americano", "latte", "cappuccino" |
| `milk_type` | string | "none", "regular", "oat", "soy" |
| `sugar_spoons` | int | 0-5 |

### Ожидаемое поведение (бизнес-правила)
- Базовая калорийность:
  - espresso: 5 ккал
  - americano: 10 ккал
  - latte: 180 ккал
  - cappuccino: 150 ккал
- Молоко:
  - regular: +50 ккал
  - oat: +40 ккал
  - soy: +30 ккал
  - none: 0
- Сахар: +20 ккал за ложку
- Если напиток с молоком (milk_type != "none") и coffee_type = "espresso" или "americano", считать как латте/капучино? (нет, просто добавить молоко)

### Формат результата
```python
{
    "calories": int,  # всего калорий
    "category": str  # "low", "medium", "high" (<50, 50-200, >200)
}
```

### Исходный код
```python
def calculate_coffee_calories(coffee_type, milk_type, sugar_spoons):
    base_cal = {
        "espresso": 5,
        "americano": 10,
        "latte": 180,
        "cappuccino": 150
    }.get(coffee_type, 0)
    
    milk_cal = {
        "none": 0,
        "regular": 50,
        "oat": 40,
        "soy": 30
    }.get(milk_type, 0)
    
    if milk_type != "none" and coffee_type in ["espresso", "americano"]:
        base_cal = 180
    
    sugar_cal = sugar_spoons * 20
    total = base_cal + milk_cal + sugar_cal
    
    if total < 50:
        category = "low"
    elif total <= 200:
        category = "medium"
    else:
        category = "high"
    
    return {"calories": total, "category": category}
```