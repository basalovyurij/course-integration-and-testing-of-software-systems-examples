## Вариант 11: Калькулятор калорий для фитнес-приложения

### Описание функции
Модуль расчета дневной нормы калорий для пользователя на основе антропометрических данных и уровня активности.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `gender` | string | "male", "female" |
| `age` | int | 10-100 лет |
| `weight` | float | 30-250 кг |
| `height` | int | 100-250 см |
| `activity_level` | string | "sedentary", "light", "moderate", "active", "very_active" |
| `goal` | string | "lose", "maintain", "gain" |

### Ожидаемое поведение (бизнес-правила)
- Расчет базового метаболизма (BMR) по формуле Миффлина-Сан Жеора:
  - Для мужчин: BMR = 10 × weight + 6.25 × height - 5 × age + 5
  - Для женщин: BMR = 10 × weight + 6.25 × height - 5 × age - 161
- Коэффициенты активности:
  - sedentary (мало или нет упражнений): 1.2
  - light (1-3 дня в неделю): 1.375
  - moderate (3-5 дней в неделю): 1.55
  - active (6-7 дней в неделю): 1.725
  - very_active (тяжелые тренировки 2 раза в день): 1.9
- Корректировка по цели:
  - lose: итог = BMR × activity_coef - 500 (но не менее 1200 ккал)
  - maintain: итог = BMR × activity_coef
  - gain: итог = BMR × activity_coef + 500

### Исходный код
```python
def calculate_calories(gender, age, weight, height, activity_level, goal):
    if gender == "male":
        bmr = 10 * weight + 6.25 * height - 5 * age + 5
    elif gender == "female":
        bmr = 10 * weight + 6.25 * height - 5 * age - 161
    else:
        return "ERROR: Invalid gender"
    
    if activity_level == "sedentary":
        activity_coef = 1.2
    elif activity_level == "light":
        activity_coef = 1.375
    elif activity_level == "moderate":
        activity_coef = 1.55
    elif activity_level == "active":
        activity_coef = 1.725
    elif activity_level == "very_active":
        activity_coef = 1.9
    else:
        return "ERROR: Invalid activity level"
    
    result = bmr * activity_coef
    
    if goal == "lose":
        result -= 500
        if result < 1200:
            result = 1200
    elif goal == "maintain":
        pass
    elif goal == "gain":
        result += 500
    else:
        return "ERROR: Invalid goal"
    
    return round(result, 0)
```