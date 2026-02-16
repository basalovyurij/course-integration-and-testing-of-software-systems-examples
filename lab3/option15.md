## Вариант 15: Калькулятор коммунальных платежей

### Описание функции
Расчет суммы оплаты за ЖКУ с учетом тарифов, льгот и показаний счетчиков.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `electricity_reading` | float | 0-10000 кВт·ч |
| `water_reading` | float | 0-500 м³ |
| `gas_reading` | float | 0-1000 м³ |
| `has_benefits` | bool | true/false |
| `benefit_percentage` | int | 0-100 (только если has_benefits=True) |
| `residents_count` | int | 1-10 |
| `season` | string | "winter", "summer" |

### Ожидаемое поведение (бизнес-правила)
- Тарифы:
  - Электричество: 5 руб./кВт·ч (первые 100 кВт·ч), 7 руб./кВт·ч (свыше 100)
  - Вода: 40 руб./м³ (норма 5 м³ на человека, свыше - 60 руб./м³)
  - Газ: 8 руб./м³ (зимой), 6 руб./м³ (летом)
- Если есть льготы, итоговая сумма уменьшается на benefit_percentage%
- Если residents_count > 5, добавляется 10% за превышение нормы потребления
- Минимальная сумма платежа (если есть потребление хотя бы одного ресурса): 300 руб.
- Округление до целого рубля

### Исходный код
```python
def calculate_utilities(electricity_reading, water_reading, gas_reading, has_benefits, benefit_percentage, residents_count, season):
    total = 0
    
    if electricity_reading > 0:
        if electricity_reading <= 100:
            electricity_cost = electricity_reading * 5
        else:
            electricity_cost = 100 * 5 + (electricity_reading - 100) * 7
        total += electricity_cost
    
    if water_reading > 0:
        norm = residents_count * 5
        if water_reading <= norm:
            water_cost = water_reading * 40
        else:
            water_cost = norm * 40 + (water_reading - norm) * 60
        total += water_cost
    
    if gas_reading > 0:
        if season == "winter":
            gas_cost = gas_reading * 8
        else:
            gas_cost = gas_reading * 6
        total += gas_cost
    
    if residents_count > 5:
        total *= 1.1
    
    if has_benefits:
        if benefit_percentage > 0 and benefit_percentage <= 100:
            total *= (1 - benefit_percentage / 100)
    
    if total < 300 and (electricity_reading > 0 or water_reading > 0 or gas_reading > 0):
        total = 300
    
    return round(total)
```
