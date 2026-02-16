## Вариант 6: Система бронирования авиабилетов

### Описание функции
Модуль проверки доступности мест и расчета стоимости с учетом класса обслуживания, багажа и статуса пассажира.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `seat_class` | string | "economy", "business", "first" |
| `passenger_status` | string | "adult", "child", "infant" |
| `baggage_count` | int | 0-3 |
| `is_round_trip` | bool | true/false |
| `days_to_departure` | int | 0-365 |

### Ожидаемое поведение (бизнес-правила)
- Базовая цена: economy — 5000, business — 15000, first — 30000
- Дети (2-12 лет): скидка 25% от базовой цены
- Младенцы (до 2 лет): 10% от базовой цены, без места, без багажа (если baggage_count > 0, то ошибка)
- Туда-обратно: скидка 10% от общей суммы
- Если до вылета <= 3 дней: наценка 20%
- Бесплатный багаж: 1 место для economy, 2 для business, 3 для first. Каждое доп. место +1000 руб.

### Исходный код
```python
def book_flight(seat_class, passenger_status, baggage_count, is_round_trip, days_to_departure):
    if seat_class == "economy":
        base_price = 5000
        free_baggage = 1
    elif seat_class == "business":
        base_price = 15000
        free_baggage = 2
    elif seat_class == "first":
        base_price = 30000
        free_baggage = 3
    else:
        return "ERROR: Invalid class"
    
    if passenger_status == "infant" and baggage_count > 0:
        return "ERROR: Infants cannot have baggage"
    
    if passenger_status == "child":
        price = base_price * 0.75
    elif passenger_status == "infant":
        price = base_price * 0.1
    else:
        price = base_price
    
    if days_to_departure <= 3:
        price *= 1.2
    
    if is_round_trip:
        price *= 1.9
    
    baggage_cost = 0
    if baggage_count > free_baggage:
        baggage_cost = (baggage_count - free_baggage) * 1000
    
    return price + baggage_cost
```