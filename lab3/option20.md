## Вариант 20: Система управления задачами (Task Manager)

### Описание функции
Расчет приоритета задачи и рекомендуемого срока выполнения на основе различных факторов.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `task_type` | string | "bug", "feature", "improvement", "documentation" |
| `severity` | string | "trivial", "minor", "major", "critical", "blocker" |
| `deadline_days` | int | 0-365 (дней до дедлайна) |
| `assignee_load` | int | 0-100 (% загрузки исполнителя) |
| `dependencies_count` | int | 0-20 |
| `business_value` | int | 1-10 |
| `is_weekend` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Базовый приоритет (1-100):
  - task_type: bug = 80, feature = 60, improvement = 40, documentation = 20
  - severity: blocker = +30, critical = +20, major = +10, minor = 0, trivial = -10
  - deadline_days: если <= 1: +30, <= 3: +20, <= 7: +10, > 30: -10
  - business_value: умножается на 3 и добавляется
- Корректировки:
  - Если assignee_load > 80: приоритет снижается на 20% (чтобы не перегружать)
  - Если dependencies_count > 5: приоритет повышается на 10% (чтобы разблокировать другие задачи)
  - Если is_weekend и deadline_days <= 2: приоритет повышается на 50% (срочно в выходные)
- Итоговый приоритет округляется и ограничивается диапазоном 0-100
- На основе приоритета определяется срок выполнения:
  - priority >= 80: "ASAP"
  - priority >= 50: "this week"
  - priority >= 20: "this month"
  - priority < 20: "when possible"

### Формат результата
```python
{
    "priority": float, # приоритет (0-100)
    "timeline": str,  # "ASAP", "this week", "this month", "when possible"
}
```

### Исходный код
```python
def calculate_task_priority(task_type, severity, deadline_days, assignee_load, dependencies_count, business_value, is_weekend):
    type_base = {
        "bug": 80,
        "feature": 60,
        "improvement": 40,
        "documentation": 20
    }.get(task_type, 40)
    
    severity_bonus = {
        "blocker": 30,
        "critical": 20,
        "major": 10,
        "minor": 0,
        "trivial": -10
    }.get(severity, 0)
    
    deadline_bonus = 0
    if deadline_days <= 1:
        deadline_bonus = 30
    elif deadline_days <= 3:
        deadline_bonus = 20
    elif deadline_days <= 7:
        deadline_bonus = 10
    elif deadline_days > 30:
        deadline_bonus = -10
    
    value_bonus = business_value * 3
    
    priority = type_base + severity_bonus + deadline_bonus + value_bonus
    
    if assignee_load > 80:
        priority *= 0.8
    
    if dependencies_count > 5:
        priority *= 1.1
    
    if is_weekend and deadline_days <= 2:
        priority *= 1.5
    
    if priority < 0:
        priority = 0
    if priority > 100:
        priority = 100
    
    if priority >= 80:
        timeline = "ASAP"
    elif priority >= 50:
        timeline = "this week"
    elif priority >= 20:
        timeline = "this month"
    else:
        timeline = "when possible"
    
    return {
        "priority": round(priority),
        "timeline": timeline
    }
```