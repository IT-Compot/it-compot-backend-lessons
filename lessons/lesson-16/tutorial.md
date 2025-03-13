# 🚀 Шестой урок

Сегодня мы **вспомним и научимся** рендерить внутренние URL в шаблонах Django, а также создавать **динамические URL** 📌 на примере страницы с отображением заказов и конкретного товара. 

---

## 🔹 **Можете показать раздел в** [📖 шпаргалке (Динамические маршруты)](https://github.com/xlartas/it-compot-backend-methods/blob/main/django-base.md#%D0%B4%D0%B8%D0%BD%D0%B0%D0%BC%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%B8%D0%B5-%D0%BC%D0%B0%D1%80%D1%88%D1%80%D1%83%D1%82%D1%8B).

---

## 🛠 Создание URL для отображения конкретного товара

Мы хотим, чтобы при **GET-запросе**
```
http://127.0.0.1:8000/shop/product/1/
```
отображалась информация о товаре с `id = 1`. То есть последняя цифра в URL обозначает **id товара**, который мы запрашиваем.

### 📌 **1. Определяем URL-маршрут**
📂 *shop/urls.py*
```python
urlpatterns = [
    ...
    path('shop/product/<int:product_id>/', views.product_detail, name='product_detail'),
]
```
👉 Здесь `<int:product_id>` — динамическая часть URL, передающая `id` товара в представление `product_detail()`.

### 📌 **2. Создаём функцию представления**
📂 *shop/views.py*
```python
def product_detail(request, product_id):
    product = Product.objects.get(id=product_id)
    return render(request, 'shop/product_detail.html', {'product': product})
```
🛑 **Важно**: если товара с таким `id` нет, произойдёт ошибка `DoesNotExist`! 


### 📌 **3. Шаблон product_detail.html**
📂 *shop/templates/shop/product_detail.html*
```html
{% extends 'shop/base.html' %}
{% load static %}

{% block title %}Shop | {{ product.name }} {% endblock %}

{% block content %}
    <div class="d-flex gap-3 flex-wrap justify-content-center mx-auto" style="max-width: 300px;">
        <div class="d-flex flex-column align-items-start text-center border-0 rounded-4 text-nowrap px-4 py-4"
             style="width: min-content; box-shadow: 0 0 5px #00000022;">
            <h1 class="text-wrap">{{ product.name }}</h1>
            <img width="340" src="{{ product.image.url }}" alt="">
            <span class="fs-2 fw-bold">{{ product.price }} ₽</span>
            <a class="btn fs-4 btn-outline-secondary w-100 mt-3">Оформить заказ</a>
        </div>
    </div>
{% endblock %}
```
✅ **Проверьте, что всё работает!**

---

## 🔗 Рендер динамических ссылок в шаблоне

Теперь сделаем так, чтобы на страницу товара можно было **перейти из каталога**. 
Используем `{% url %}`: 

📂 *shop/templates/shop/catalog.html*
```html
{% for product in products %}
    <a href="{% url 'product_detail' product_id=product.id %}"
       class="card border-0 rounded-4 text-decoration-none"
       style="width: 250px; box-shadow: 0 0 5px #00000022;">
        <h2>{{ product.name }}</h2>
    </a>
{% endfor %}
```
💡 Теперь клик на товар в каталоге откроет **страницу его описания**!

---

## 📦 Добавляем ссылку на товар в заказах
Сейчас в заказе отображается **только имя товара**. Сделаем так, чтобы оно стало **ссылкой на страницу товара**.

📂 *shop/templates/shop/orders.html*
```html
{% for order in orders %}
    <div class="d-flex flex-column text-center border-0 rounded-4 text-nowrap px-4 py-2"
         style="width: min-content; box-shadow: 0 0 5px #00000022;">
        <span class="align-self-start fw-bold fs-5">{{ order.id }}</span>
        <a href="{% url 'product_detail' product_id=order.product.id %}">
            {{ order.product.name }}
        </a>
        <span>{{ order.delivery_address|truncatewords:6 }}</span>
        <span>{{ order.created_at }}</span>
    </div>
{% endfor %}
```
💡 Теперь **клик по названию товара в заказе** откроет его страницу!

---

## 🏗 **Дополнительное задание**
💡 **Если осталось время**, обновите **шапку сайта**, добавив в навигацию ссылки на **каталог** и **заказы**.

---

## 🎯 **Итоги**

✅ Мы научились:
- Создавать **динамические маршруты** в `urls.py`.
- Использовать `{% url %}` для **генерации ссылок** внутри шаблонов.
- Делать так, чтобы **страницы были связаны между собой** 🔄.

📌 **Не забудьте сделать git push!**

