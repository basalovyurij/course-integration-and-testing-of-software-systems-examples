## Вариант 23: Система подбора персонала (HR-скрининг)

### Описание функции
Оценка соответствия кандидата вакансии на основе резюме и результатов тестирования.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `experience_years` | int | 0-50 |
| `education_level` | string | "high_school", "bachelor", "master", "phd" |
| `tech_stack` | list | список технологий (строки) |
| `required_tech` | list | список требуемых технологий |
| `test_score` | int | 0-100 |
| `interview_score` | int | 0-10 |
| `salary_expectation` | int | 0-1000000 руб. |
| `budget_max` | int | 0-1000000 руб. |
| `remote_possible` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Базовая оценка = test_score × 0.3 + interview_score × 7 (приведение к 100-балльной шкале)
- Бонус за образование:
  - phd: +10
  - master: +5
  - bachelor: +2
  - high_school: 0
- Бонус за опыт: +2 за каждый год опыта, но не более +30
- Покрытие технологий: процент совпадения required_tech с tech_stack (если < 50% - отказ)
- Если salary_expectation > budget_max × 1.1: отказ
- Если remote_possible=False и вакансия удаленная (передается отдельно) - отказ
- Итоговый вердикт: "hire" (>= 70), "consider" (50-69), "reject" (< 50)

### Формат результата
```python
{
    "verdict": str,  # "reject", "hire", "reject"
    "reason": str,  # причина
    "score": float  # оценка (0-100)
}
```

### Исходный код
```python
def screen_candidate(experience_years, education_level, tech_stack, required_tech, test_score, interview_score, salary_expectation, budget_max, remote_possible):
    if salary_expectation > budget_max * 1.1:
        return {"verdict": "reject", "reason": "Salary too high"}
    
    if not required_tech:
        tech_match_percent = 100
    else:
        matching_tech = set(tech_stack) & set(required_tech)
        tech_match_percent = len(matching_tech) / len(required_tech) * 100
    
    if tech_match_percent < 50:
        return {"verdict": "reject", "reason": "Insufficient tech stack"}
    
    score = test_score * 0.3 + interview_score * 7
    
    education_bonus = {
        "phd": 10,
        "master": 5,
        "bachelor": 2,
        "high_school": 0
    }.get(education_level, 0)
    score += education_bonus
    
    experience_bonus = min(experience_years * 2, 30)
    score += experience_bonus
    
    score = round(score)
    
    if score >= 70:
        verdict = "hire"
    elif score >= 50:
        verdict = "consider"
    else:
        verdict = "reject"
    
    return {"verdict": verdict, "score": score, "tech_match": round(tech_match_percent, 1)}
```