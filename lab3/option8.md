## Вариант 8: Калькулятор зарплаты с бонусами

### Описание функции
Расчет итоговой зарплаты сотрудника с учетом оклада, премий, штрафов и коэффициента эффективности.

### Входные параметры
| Параметр | Тип | Диапазон |
|----------|-----|----------|
| `base_salary` | float | 15000 - 500000 руб. |
| `performance_score` | int | 1-10 |
| `project_completion` | float | 0-1.5 (коэффициент выполнения плана) |
| `overtime_hours` | int | 0-100 |
| `sick_days` | int | 0-30 |

### Ожидаемое поведение (бизнес-правила)
- База: base_salary
- Бонус за эффективность: если score >= 8, бонус 15% от базы, если 5-7 — 5%, иначе 0
- Бонус за перевыполнение: если project_completion > 1.0, доп. бонус = (project_completion - 1) × base_salary × 0.5
- Штраф за больничные: если sick_days > 5, вычитается 2% за каждый день свыше 5
- Оплата сверхурочных: overtime_hours × (base_salary / 160) × 1.5 (если performance_score < 4, то коэф. 1.2)
- Итог не может быть меньше base_salary × 0.7

### Исходный код
```python
def calculate_salary(base_salary, performance_score, project_completion, overtime_hours, sick_days):
    result = base_salary
    
    if performance_score >= 8:
        result += base_salary * 0.15
    elif performance_score >= 5 and performance_score < 8:
        result += base_salary * 0.05
    
    if project_completion > 1:
        result += (project_completion - 1) * base_salary * 0.5
    elif project_completion < 0.8:
        result -= base_salary * 0.1
    
    if sick_days > 5:
        result -= (sick_days - 5) * base_salary * 0.02
    
    hourly_rate = base_salary / 160
    if performance_score < 4:
        overtime_rate = 1.2
    else:
        overtime_rate = 1.5
    
    result += overtime_hours * hourly_rate * overtime_rate
    
    if result < base_salary * 0.7:
        result = base_salary * 0.7
    
    return round(result, 2)
```