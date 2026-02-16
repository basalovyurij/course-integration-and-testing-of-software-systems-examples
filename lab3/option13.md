## Вариант 13: Калькулятор командировочных расходов

### Описание функции
Расчет суммы выплат сотруднику за командировку с учетом страны, длительности и категории расходов.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `country` | string | "russia", "cis", "europe", "asia", "usa" |
| `days` | int | 1-365 |
| `has_visa` | bool | true/false |
| `expenses` | dict | {"hotel": float, "food": float, "transport": float} |
| `is_management` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Суточные нормы:
  - russia: 700 руб./день
  - cis: 1500 руб./день
  - europe: 3000 руб./день
  - asia: 2500 руб./день
  - usa: 4000 руб./день
- Если командировка > 30 дней, суточные снижаются на 20% после 30-го дня
- Если has_visa=True и страна не "russia", добавляется компенсация визы 5000 руб. (разово)
- Расходы компенсируются:
  - Отель: 100% фактических расходов, но не более 10000 руб./день для management, иначе 7000 руб./день
  - Питание: 100% фактических расходов, но не более 2000 руб./день
  - Транспорт: 100% фактических расходов, но не более 15000 руб. на всю поездку
- Если общая сумма компенсации превышает 300000 руб., требуется доп. согласование (функция возвращает предупреждение)

### Формат результата
```python
{
    "total": float, # Сумма выплаты сотруднику
    "warning": str,  # предупреждение
}
```

### Исходный код
```python
def calculate_travel_expenses(country, days, has_visa, expenses, is_management):
    daily_rates = {
        "russia": 700,
        "cis": 1500,
        "europe": 3000,
        "asia": 2500,
        "usa": 4000
    }
    
    if country not in daily_rates:
        return "ERROR: Invalid country"
    
    per_diem = daily_rates[country]
    if days > 30:
        per_diem_total = per_diem * 30 + per_diem * 0.8 * (days - 30)
    else:
        per_diem_total = per_diem * days
    
    visa_compensation = 0
    if has_visa and country != "russia":
        visa_compensation = 5000
    
    hotel_max = 7000
    if is_management:
        hotel_max = 10000
    
    hotel_compensation = min(expenses.get("hotel", 0), hotel_max * days)
    food_compensation = min(expenses.get("food", 0), 2000 * days)
    transport_compensation = min(expenses.get("transport", 0), 15000)
    
    total = per_diem_total + visa_compensation + hotel_compensation + food_compensation + transport_compensation
    
    # Проверка на лимит
    warning = None
    if total > 300000:
        warning = "WARNING: Requires additional approval"
    
    return {"total": total, "warning": warning}
```