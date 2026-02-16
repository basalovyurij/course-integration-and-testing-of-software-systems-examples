## Вариант 18: Система бронирования отелей

### Описание функции
Расчет стоимости проживания в отеле с учетом сезона, количества гостей, дополнительных услуг и длительности.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `room_type` | string | "standard", "superior", "deluxe", "suite" |
| `guests` | int | 1-4 |
| `nights` | int | 1-30 |
| `season` | string | "low", "medium", "high" |
| `with_breakfast` | bool | true/false |
| `with_parking` | bool | true/false |
| `loyalty_level` | string | "none", "silver", "gold", "platinum" |

### Ожидаемое поведение (бизнес-правила)
- Базовые цены за ночь:
  - standard: 3000 руб.
  - superior: 5000 руб.
  - deluxe: 8000 руб.
  - suite: 15000 руб.
- Сезонные коэффициенты:
  - low: 0.8
  - medium: 1.0
  - high: 1.5
- Доплата за дополнительного гостя (свыше 2): +20% за каждого
- Завтрак: +500 руб./ночь за каждого гостя
- Парковка: +300 руб./ночь
- Скидки по программе лояльности:
  - silver: 5%
  - gold: 10%
  - platinum: 15%
- Если nights >= 7, дополнительная скидка 5% (суммируется со скидкой лояльности)
- Максимальная общая скидка не может превышать 25%

### Исходный код
```python
def calculate_hotel_stay(room_type, guests, nights, season, with_breakfast, with_parking, loyalty_level):
    base_prices = {
        "standard": 3000,
        "superior": 5000,
        "deluxe": 8000,
        "suite": 15000
    }
    
    if room_type not in base_prices:
        return "ERROR: Invalid room type"
    
    room_price = base_prices[room_type]
    
    season_coef = {
        "low": 0.8,
        "medium": 1.0,
        "high": 1.5
    }.get(season, 1.0)
    
    guest_surcharge = 1.0
    if guests > 2:
        guest_surcharge += (guests - 2) * 0.2
    
    night_cost = room_price * season_coef * guest_surcharge
    total_room = night_cost * nights
    
    extras = 0
    if with_breakfast:
        extras += 500 * guests * nights
    
    if with_parking:
        extras += 300 * nights
    
    discount = 0
    
    loyalty_discount = {
        "none": 0,
        "silver": 5,
        "gold": 10,
        "platinum": 15
    }.get(loyalty_level, 0)
    discount += loyalty_discount
    
    if nights >= 7:
        discount += 5
    
    if discount > 25:
        discount = 25
    
    total = (total_room + extras) * (1 - discount / 100)
    
    return round(total, 2)
```