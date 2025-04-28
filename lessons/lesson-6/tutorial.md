# ORM, for, if в шаблонах

---

## Введение

Сегодня мы продолжим строить наш **Блог**.

Представьте, что мы как архитекторы: у нас есть уже здания (наши посты), а теперь нам нужно красиво их разместить на одной улице (странице).

🎨 Чтобы было проще понимать, что мы делаем, давайте иногда заглядывать на **готовую картинку** результата:
> ![result.png](imgs/result.png)

_Ссылку на готовую страницу можно посмотреть здесь:_  
[Полный пример страницы](index.html)

---

## Header, Footer и карточка поста

---

### 1. Создаём страницу для всех постов

Представьте, что вы создаете **площадь города**, где будут стоять все дома (посты). Для этого сначала создаем специальное место в коде:

```python
# blog/views.py
def posts_list(request):
    return render(request, 'blog/posts_list.html')
```
Теперь подключаем эту площадь (страницу) к маршрутам города (сайта):
```
python
# project_name/urls.py
from blog.views import posts_list  # импортируем функцию

urlpatterns = [
    path('blog/posts_list/', posts_list),  # связываем маршрут и функцию
]
```
✅ Теперь по адресу /blog/posts_list/ можно будет открыть страницу!

2. Подключаем красивости с помощью Bootstrap
Чтобы наши дома выглядели красиво, мы воспользуемся уже готовыми шаблонами от "строителей" — Bootstrap.

Показываем ребятам:

Header — шапка сайта

Карточка для поста

Объясняем:

Header — это как вывеска города: название, меню, кнопочки.

Card — это как аккуратная коробочка для каждого поста.

Не забываем подключить Bootstrap:

```html
<!-- blog/posts_list.html -->
{% load static %}
<head>
    ...
    <link rel="stylesheet" href="{% static 'core/css/bootstrap.min.css' %}">
</head>
```
👉 Важно: шапку (header) делаем общую, чтобы потом использовать её на всех страницах.

Пример базовой структуры:

```html
<header>
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
        ...
    </nav>
</header>
<main>
    <h1 class="text-light text-center fw-bold">Посты</h1>
    <div class="card" style="width: 250px;">
        <img src="..." class="card-img-top" alt="...">
        <div class="card-body">
            <h5 class="card-title">Заголовок</h5>
            <p class="card-text">Текст текст текст текст</p>
        </div>
    </div>
</main>
<footer>
    ...
</footer>
```
3. Как достать пост из базы данных?
Объясняем детям:

Наша база данных — это большой склад с коробками (постами). Чтобы достать коробку номер 1, мы должны попросить Django: "Принеси мне пост с номером 1!"

```python
# blog/views.py
from .models import Post

def posts_list(request):
    post = Post.objects.get(id=1)
    return render(request, 'blog/posts_list.html', {'post': post})
```
✅ Объясняем, что через фигурные скобки мы передаем данные в HTML как подарок на праздник 🎁.

4. Как показать пост на странице?
Теперь открываем коробку и показываем её содержимое:

```html
<!-- blog/posts_list.html -->
<div class="card" style="width: 250px;">
    <img src="..." class="card-img-top" alt="...">
    <div class="card-body">
        <h5 class="card-title">{{ post.title }}</h5>
        <p class="card-text">{{ post.text }}</p>
    </div>
</div>
```
✅ {{ post.title }} — вытаскиваем название поста,
✅ {{ post.text }} — вытаскиваем текст поста.

5. Как отобразить сразу все посты?
Объясняем через аналогию:

Представьте, что у нас много коробок на складе. Мы говорим Django: "Принеси ВСЕ коробки!"

```python
# blog/views.py
from .models import Post

def posts_list(request):
    posts = Post.objects.all()
    return render(request, 'blog/posts_list.html', {'posts': posts})
```
А в HTML мы создаем для каждой коробки (поста) свою карточку, используя for:
# Как работает `for` в шаблонах?

---

Когда мы пишем цикл `for` в шаблоне Django, мы говорим:

> "Пройдись по каждому элементу в списке и для каждого сделай одно и то же."

## Представим:

У нас есть коробки с постами:

- 📦 Пост 1
- 📦 Пост 2
- 📦 Пост 3

И мы хотим для каждой коробки нарисовать красивую карточку на странице.
##Важно!
>posts — это список постов, который мы передали из views.py.

>post — это отдельный элемент из списка. На каждом шаге цикла for он меняется.

```html
<!-- blog/posts_list.html -->
<main>
    <h1 class="text-light text-center fw-bold">Посты</h1>
    <div class="posts_container d-flex gap-3 flex-wrap justify-content-center mx-auto" 
         style="max-width: 800px;">
         
        {% for post in posts %}
            <div class="card" style="width: 250px;">
                <img src="{{ post.image.url }}" class="card-img-top" alt="...">
                <div class="card-body">
                    <h5 class="card-title">{{ post.title }}</h5>
                    <p class="card-text">{{ post.text }}</p>
                </div>
            </div>
        {% endfor %}
    </div>
</main>
```
6. Как показать только опубликованные посты?
Логика простая:

Некоторые коробки у нас ещё не готовы. Мы хотим показать только те, которые уже проверены.
```html
Добавляем if:
<!-- blog/posts_list.html -->
<main>
    <h1 class="text-light text-center fw-bold">Посты</h1>
    <div class="posts_container d-flex gap-3 flex-wrap justify-content-center mx-auto" 
         style="max-width: 800px;">

        {% for post in posts %}
            {% if post.is_published == True %}
                <div class="card" style="width: 250px;">
                    <img src="{{ post.image.url }}" class="card-img-top" alt="...">
                    <div class="card-body">
                        <h5 class="card-title">{{ post.title }}</h5>
                        <p class="card-text">{{ post.text }}</p>
                    </div>
                </div>
            {% endif %}
        {% endfor %}
    </div>
</main>
```
Итоги
Мы научились создавать страницу для отображения постов.

Научились доставать данные из базы данных.

Отобразили эти данные с помощью HTML + Bootstrap.

Использовали for для перебора всех постов.

Использовали if для проверки условий.
