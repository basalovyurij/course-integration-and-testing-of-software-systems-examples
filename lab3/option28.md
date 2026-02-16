## Вариант 28: Калькулятор возраста собаки

### Описание функции
Перевод возраста собаки в "человеческий" возраст.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `dog_years` | int | 1 - 20 лет |
| `breed_size` | string | "small", "medium", "large" |

### Ожидаемое поведение (бизнес-правила)
- Первый год собаки = 15 человеческих лет
- Второй год = +9 лет (итого 24)
- Каждый последующий год:
  - small: +4 года
  - medium: +5 лет
  - large: +6 лет

### Формат результата
```python
{
    "human_years": int,  # человеческий возраст
    "life_stage": str  # "щенок", "взрослая", "пожилая"
}
```
- жизненный этап: human_years < 20 = "щенок", 20-50 = "взрослая", >50 = "пожилая"

### Исходный код
```python
def dog_to_human_years(dog_years, breed_size):
    if dog_years == 1:
        human = 15
    elif dog_years == 2:
        human = 24
    else:
        extra = dog_years - 2
        if breed_size == "small":
            human = 24 + extra * 4
        elif breed_size == "medium":
            human = 24 + extra * 5
        elif breed_size == "large":
            human = 24 + extra * 5
    
    if human < 20:
        stage = "щенок"
    elif human <= 50:
        stage = "взрослая"
    else:
        stage = "пожилая"
    
    return {"human_years": human, "life_stage": stage}
```