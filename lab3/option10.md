## Вариант 10: Система управления подписками

### Описание функции
Модуль обработки действий с подпиской пользователя (активация, продление, отмена) с учетом пробного периода и способа оплаты.

### Входные параметры
| Параметр | Тип | Допустимые значения |
|----------|-----|---------------------|
| `action` | string | "activate", "renew", "cancel", "upgrade" |
| `subscription_type` | string | "basic", "pro", "enterprise" |
| `trial_used` | bool | true/false |
| `payment_method` | string | "card", "paypal", "crypto" |
| `account_age_days` | int | 0-5000 |

### Ожидаемое поведение (бизнес-правила)
- Активация: если trial_used=False и subscription_type!="enterprise", то пробный период 14 дней, иначе сразу платный период
- Продление: возможно только если подписка активна и payment_method="card" или "paypal" (не crypto)
- Отмена: возможна в любое время, но если account_age_days < 7, взимается штраф 10% от стоимости
- Апгрейд: с basic на pro или pro на enterprise. Разница в стоимости оплачивается пропорционально оставшимся дням
- Crypto-платежи доступны только для enterprise и при account_age_days > 30

### Исходный код
```python
def process_subscription(action, subscription_type, trial_used, payment_method, account_age_days):
    prices = {"basic": 10, "pro": 25, "enterprise": 100}
    
    if payment_method == "crypto":
        if subscription_type != "enterprise" or account_age_days <= 30:
            return "ERROR: Crypto not allowed for this subscription"
    
    if action == "activate":
        if not trial_used and subscription_type != "enterprise":
            return "ACTIVATED: Trial period 14 days"
        else:
            return f"ACTIVATED: Paid {prices[subscription_type]}"
    
    elif action == "renew":
        if payment_method == "crypto":
            return "ERROR: Cannot renew with crypto"
        return f"RENEWED: {prices[subscription_type]} charged"
    
    elif action == "cancel":
        if account_age_days < 7:
            fee = prices[subscription_type] * 0.1
            return f"CANCELLED: Early cancellation fee {fee}"
        return "CANCELLED: No fee"
    
    elif action == "upgrade":
        if subscription_type == "basic":
            new_type = "pro"
        elif subscription_type == "pro":
            new_type = "enterprise"
        else:
            return "ERROR: Cannot upgrade enterprise"
        
        diff = prices[new_type] - prices[subscription_type]
        return f"UPGRADED to {new_type}: Additional cost {diff}"
    
    return "ERROR: Invalid action"
```