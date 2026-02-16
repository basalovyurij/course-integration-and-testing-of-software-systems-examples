## Вариант 19: Система мониторинга здоровья

### Описание функции
Оценка состояния здоровья на основе медицинских показателей с выдачей рекомендаций.

### Входные параметры
| Параметр | Тип | Диапазон |
|----------|-----|----------|
| `heart_rate` | int | 40-200 уд/мин |
| `blood_pressure_systolic` | int | 70-220 мм рт.ст. |
| `blood_pressure_diastolic` | int | 40-140 мм рт.ст. |
| `temperature` | float | 35.0-42.0 °C |
| `oxygen_saturation` | int | 70-100 % |
| `age` | int | 0-120 лет |
| `has_chronic_disease` | bool | true/false |

### Ожидаемое поведение (бизнес-правила)
- Нормальные показатели:
  - Heart rate: 60-100 (взрослые), 70-120 (дети до 12 лет), 50-90 (спортсмены, если age > 18 и has_chronic_disease=False)
  - Blood pressure: сист. 90-120, диаст. 60-80
  - Temperature: 36.1-37.2
  - Oxygen saturation: 95-100
- Если показатель вне нормы, добавляется предупреждение
- Критические значения:
  - Heart rate < 40 или > 140: критично
  - Систолическое > 180 или < 80: критично
  - Температура < 35.5 или > 39.5: критично
  - Сатурация < 90: критично
- Если есть хронические заболевания, границы нормы расширяются на 5% для давления
- Возвращает статус: "normal", "warning", "critical" и список рекомендаций

### Формат результата
```python
{
    "status": str, # "normal", "warning", "critical" 
    "recommendations": list[str],  # список текстовых рекомендаций
}
```

### Исходный код
```python
def assess_health(heart_rate, blood_pressure_systolic, blood_pressure_diastolic, temperature, oxygen_saturation, age, has_chronic_disease):
    status = "normal"
    recommendations = []
    
    if age < 12:
        hr_normal_min, hr_normal_max = 70, 120
    elif age > 18 and not has_chronic_disease:
        hr_normal_min, hr_normal_max = 50, 90
    else:
        hr_normal_min, hr_normal_max = 60, 100
    
    if heart_rate < hr_normal_min or heart_rate > hr_normal_max:
        if heart_rate < 40 or heart_rate > 140:
            status = "critical"
            recommendations.append("Critical heart rate - seek immediate medical attention")
        else:
            if status != "critical":
                status = "warning"
            recommendations.append("Heart rate is outside normal range")
    
    bp_normal_systolic_min, bp_normal_systolic_max = 90, 120
    
    if has_chronic_disease:
        bp_normal_systolic_max *= 1.05 
    
    if blood_pressure_systolic < bp_normal_systolic_min or blood_pressure_systolic > bp_normal_systolic_max:
        if blood_pressure_systolic > 180 or blood_pressure_systolic < 80:
            status = "critical"
            recommendations.append("Critical blood pressure - seek immediate medical attention")
        else:
            if status != "critical":
                status = "warning"
            recommendations.append("Blood pressure is outside normal range")
    
    if temperature < 36.1 or temperature > 37.2:
        if temperature < 35.5 or temperature > 39.5:
            status = "critical"
            recommendations.append("Critical temperature - seek immediate medical attention")
        else:
            if status != "critical":
                status = "warning"
            recommendations.append("Temperature is outside normal range")
    
    if oxygen_saturation < 95:
        if oxygen_saturation < 90:
            status = "critical"
            recommendations.append("Critical oxygen level - seek immediate medical attention")
        else:
            if status != "critical":
                status = "warning"
            recommendations.append("Oxygen saturation is below normal")
    
    return {"status": status, "recommendations": recommendations}
```