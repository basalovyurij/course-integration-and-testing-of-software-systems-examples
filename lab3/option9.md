## Вариант 9: Система рекомендации фильмов

### Описание функции
Алгоритм подбора фильмов на основе предпочтений пользователя и истории просмотров.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `age` | int | 0-120 |
| `favorite_genres` | list | "comedy", "drama", "action", "horror", "sci-fi" |
| `watched_recently` | int | 0-50 (за месяц) |
| `rating_preference` | float | 0-10 (мин. рейтинг IMDb) |
| `is_premium` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Если возраст < 12: исключить жанр "horror"
- Если возраст > 60: добавить 20% к приоритету жанра "drama"
- Если watched_recently > 20: снизить рекомендации на 30% (чтобы не надоедать)
- Если rating_preference > 8.5: показывать только фильмы с рейтингом >= 8.5 (фильтровать строго)
- Если is_premium: добавить 2 дополнительных рекомендации из категории "premium"
- Базовая логика: подобрать 10 фильмов по жанрам с учетом весов

### Исходный код
```python
def get_recommendations(age, favorite_genres, watched_recently, rating_preference, is_premium):
    recommendations = []
    genre_weights = {genre: 1.0 for genre in favorite_genres}
    
    if age < 12:
        if "horror" in genre_weights:
            del genre_weights["horror"]
    
    if age > 60:
        if "drama" in genre_weights:
            genre_weights["drama"] *= 1.2
    
    view_factor = 1.0
    if watched_recently > 20:
        view_factor = 0.7
    
    for genre, weight in genre_weights.items():
        for i in range(int(3 * weight * view_factor)):
            movie = {
                "title": f"{genre} movie {i}",
                "rating": 7.5 + (i * 0.3),
                "genre": genre,
                "is_premium": i % 3 == 0
            }
            
            if movie["rating"] >= rating_preference:  
                recommendations.append(movie)
    
    if is_premium:
        for i in range(2):
            recommendations.append({
                "title": f"Premium exclusive {i}",
                "rating": 9.0,
                "genre": "premium",
                "is_premium": True
            })
    
    return recommendations[:10]
```