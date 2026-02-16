## Вариант 17: Система оценки фильмов (рейтинг)

### Описание функции
Расчет итогового рейтинга фильма на основе оценок пользователей с учетом веса голосов и фильтрации накруток.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `ratings` | list of dict | список оценок: {"user_id": int, "score": int (1-10), "user_age": int, "is_critic": bool} |
| `movie_age_days` | int | 0-10000 (сколько дней фильм в прокате) |
| `genre` | string | "comedy", "drama", "action", "horror", "sci-fi" |

### Ожидаемое поведение (бизнес-правила)
- Базовый рейтинг = среднее арифметическое всех оценок score
- Критики (is_critic=True) имеют вес 2.0 (их оценка учитывается дважды)
- Если пользователь поставил оценку 10 и это единственная его оценка (проверить нельзя, но есть флаг), считается подозрительным
- Если фильм новый (movie_age_days < 7), применяется поправка на "эффект новизны": +0.5 к рейтингу
- Для жанра "horror" оценки ниже 4 не учитываются (считается, что хоррор должен пугать, низкие оценки - не показатель)
- Если количество оценок < 5, рейтинг считается "недостаточно данных"
- Итоговый рейтинг округляется до 1 знака

### Исходный код
```python
def calculate_movie_rating(ratings, movie_age_days, genre):
    if len(ratings) < 5:
        return "INSUFFICIENT_DATA"
    
    total_score = 0
    total_weight = 0
    
    for rating in ratings:
        score = rating["score"]
        weight = 1.0
        
        if rating.get("is_critic", False):
            weight = 2.0
        
        if genre == "horror" and score < 4:
            continue
        
        if score == 10 and rating.get("is_only_rating", False):
            weight *= 0.5
        
        total_score += score * weight
        total_weight += weight
    
    if total_weight == 0:
        return "INSUFFICIENT_DATA"
    
    avg_score = total_score / total_weight
    
    if movie_age_days < 7:
        avg_score += 0.5
    
    return round(avg_score, 1)
```