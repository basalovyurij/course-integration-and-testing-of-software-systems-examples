## Вариант 21: Калькулятор налогов для фрилансеров

### Описание функции
Расчет суммы налога для самозанятых и ИП с учетом системы налогообложения, дохода и расходов.

### Входные параметры
| Параметр | Тип | Диапазон/Допустимые значения |
|----------|-----|------------------------------|
| `income` | float | 0 - 10⁷ руб. (годовой доход) |
| `expenses` | float | 0 - 10⁷ руб. (расходы) |
| `tax_system` | string | "self_employed", "usn_6", "usn_15", "patent" |
| `region_coef` | float | 0.5 - 2.0 (региональный коэффициент) |
| `has_employees` | bool | true/false |
| `insurance_paid` | float | 0 - 500000 руб. (уплаченные страховые взносы) |

### Ожидаемое поведение (бизнес-правила)
- self_employed: налог 4% с дохода от физлиц, 6% с дохода от юрлиц (упрощенно: 6% со всего дохода)
- usn_6: налог 6% с дохода, можно уменьшить на сумму страховых взносов (но не более 50% налога)
- usn_15: налог 15% с разницы (доход - расход), минимальный налог = 1% от дохода
- patent: стоимость патента = базовая стоимость × region_coef (базовая стоимость = 100000 руб.)
- Если has_employees=True, для usn_6 максимальный вычет страховых взносов = 50% налога, иначе = 100%
- Итоговый налог округляется до целого рубля

### Исходный код
```python
def calculate_tax(income, expenses, tax_system, region_coef, has_employees, insurance_paid):
    if tax_system == "self_employed":
        tax = income * 0.06
        
    elif tax_system == "usn_6":
        tax = income * 0.06
        max_deduction = tax * 0.5 if has_employees else tax
        deduction = min(insurance_paid, max_deduction)
        tax -= deduction
        
    elif tax_system == "usn_15":
        if income - expenses < 0:
            tax = 0
        else:
            tax = (income - expenses) * 0.15
            min_tax = income * 0.01
            if tax < min_tax:
                tax = min_tax
                
    elif tax_system == "patent":
        tax = 100000 * region_coef
        
    else:
        return "ERROR: Invalid tax system"
    
    return round(max(tax, 0))
```