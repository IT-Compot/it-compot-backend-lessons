# Расширение модели заказов (Order) с привязкой к пользователю

В этом разделе мы разберем, зачем нам нужно привязывать заказы к пользователям, как это реализуется и какие изменения вносит в наш код. 📌

## 1. Почему мы добавляем пользователя в модель заказов?

Ранее наша модель `Order` содержала только информацию о товаре и адресе доставки, но при этом не учитывала, **какой пользователь сделал заказ**. Это приводило к следующим проблемам:

- Все заказы хранились в базе данных **без указания владельца**.
- Любой пользователь мог увидеть все заказы, так как фильтрации по пользователям не было.
- Мы не могли отобразить **историю заказов** конкретного пользователя.

✅ Решение — добавить связь между заказами и пользователями через `ForeignKey`.

## 2. Как мы реализуем привязку заказа к пользователю?

Дополняем модель `Order`, добавляя поле `user`:

```python
from django.db import models
from django.contrib.auth.models import User

class Product(models.Model):
    name = models.CharField(max_length=100)
    image = models.ImageField(upload_to='images/Product/')
    desc = models.TextField()
    price = models.FloatField()
    rating = models.PositiveIntegerField()

class Order(models.Model):
    user = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)  # Привязка к пользователю
    product = models.ForeignKey(Product, on_delete=models.SET_NULL, null=True)
    deliverry_address = models.CharField(max_length=255)
    created_at = models.DateTimeField(auto_now_add=True)
```

### Разбор кода
- `user = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)` — создаем связь «многие-к-одному», указывая, что **каждый заказ принадлежит одному пользователю**.
- `on_delete=models.SET_NULL, null=True` — если пользователь будет удален, заказ останется в базе данных, но поле `user` станет `NULL`.

## 3. Как изменился код представлений (views)?

Ранее мы не учитывали пользователя при создании заказа. Теперь мы передаем `request.user` в `Order.objects.create()`:

```python
def order_create(request, product_id):
    if not request.user.is_authenticated:
        return redirect('signin')  # Запрещаем неавторизованным пользователям создавать заказы
    
    product = Product.objects.get(id=product_id)  # Получаем товар
    
    if request.method == 'POST':
        Order.objects.create(
            user=request.user,  # Связываем заказ с текущим пользователем
            product=product,
            deliverry_address=request.POST.get('delivery_address')
        )
        return redirect('orders')  # Перенаправляем на список заказов
    
    return render(request, 'shop/order_create.html', {'product': product})
```

### Что изменилось?
1. Проверяем, **вошел ли пользователь в систему** (`request.user.is_authenticated`).
2. При создании заказа **передаем текущего пользователя** (`user=request.user`).
3. Если заказ успешно создан, **перенаправляем пользователя на страницу заказов**.

## 4. Как получить заказы конкретного пользователя?

Раньше мы использовали `Order.objects.all()`, что выводило **все заказы** в системе, включая чужие. Теперь мы применяем `filter(user=request.user)`, чтобы отображать **только заказы текущего пользователя**:

```python
def orders(request):
    if not request.user.is_authenticated:
        return redirect('signin')
    
    orders = Order.objects.filter(user=request.user)  # Выбираем только заказы текущего пользователя
    return render(request, 'shop/orders.html', {'orders': orders})
```

### Почему используем `filter()`, а не `all()`?
- `Order.objects.all()` — получает **все заказы** в базе данных.
- `Order.objects.filter(user=request.user)` — **выбирает только заказы текущего пользователя**.

📌 Это важно для безопасности! Теперь пользователь **не может видеть чужие заказы**.

## 5. Итог
- Мы **расширили модель `Order`**, добавив связь с пользователем.
- В представлениях теперь **учитывается аутентификация**.
- Заказы привязываются к пользователю при создании.
- Для отображения заказов используется **`filter(user=request.user)`**.

**Теперь каждый пользователь может видеть только свои заказы!** 🚀

## 6. Дополнительные возможности 🔥

Вы можете улучшить систему, добавив:
- **Статусы заказов** (например, «В обработке», «Доставлен», «Отменен»).
- **Историю заказов** с возможностью фильтрации по дате.
- **Админ-панель** для управления заказами.

📢 Теперь преподаватель сможет четко объяснить ученикам, зачем нужны эти изменения и как они работают! 😊

