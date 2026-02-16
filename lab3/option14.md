## Вариант 14: Система рекомендации друзей в социальной сети

### Описание функции
Алгоритм рекомендации потенциальных друзей на основе общих интересов, друзей и геолокации.

### Входные параметры
| Параметр | Тип | Описание |
|----------|-----|----------|
| `user_interests` | list | список интересов пользователя |
| `user_friends` | list | список ID друзей |
| `user_location` | string | город проживания |
| `candidates` | list of dict | список кандидатов с полями: id, interests, friends, location, mutual_friends_count |

### Ожидаемое поведение (бизнес-правила)
- Для каждого кандидата рассчитывается рейтинг:
  - +10 баллов за каждого общего друга (mutual_friends_count)
  - +5 баллов за каждый общий интерес
  - +15 баллов, если одинаковый город
- Если у кандидата > 50 общих друзей, он получает бонус "супер-друг": +50 баллов
- Если кандидат уже в друзьях (ID в user_friends), он исключается из рекомендаций
- Возвращается список топ-10 кандидатов с наибольшим рейтингом (рейтинг >= 20)
- Если кандидат заблокировал пользователя (специальный флаг), он исключается

### Исходный код
```python
def recommend_friends(user_interests, user_friends, user_location, candidates):
    recommendations = []
    
    for candidate in candidates:
        if candidate["id"] in user_friends:
            continue
        
        if candidate.get("is_blocked", False):
            continue
        
        score = 0
        
        score += candidate.get("mutual_friends_count", 0) * 10
        
        common_interests = set(user_interests) & set(candidate.get("interests", []))
        score += len(common_interests) * 5
        
        if candidate.get("location") == user_location:
            score += 15
        
        if candidate.get("mutual_friends_count", 0) >= 50:
            score += 50
        
        if score > 20:
            recommendations.append({"id": candidate["id"], "score": score})
    
    recommendations.sort(key=lambda x: x["score"], reverse=True)
   
    return recommendations[:10]
```