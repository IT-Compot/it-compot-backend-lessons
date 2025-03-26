# Кинопоиск. Шапка и страничка с фильмами

> 🧠 **Комментарий для преподавателя:** Здесь важно акцентировать внимание на повторном использовании кода и правильной структуре проекта. Покажите, как шаблоны можно «разбивать» на блоки (`header.html`, `footer.html`, `movie_card.html`) — это упрощает жизнь при масштабировании. Объясните назначение каждого класса и его эффект на внешний вид.

---

Не используем готовую карточку `bootstrap`, напишите её самостоятельно.

С Frontend курса, у довольно _**сильных**_ учеников, на мой взгляд,  _**очень слабые**_ знания об элементарном размещении элементов.

Достаточно объяснить 4 свойства:
* `display: flex` — включает использование `flex-direction`, `justify-content`, `align-items`, `gap` и делает блок гибким.
* `justify-content: center` — выравнивание по горизонтали
* `align-items: center` — по вертикали
* `gap` — отступы между элементами внутри flex-контейнера

---

## 1. Немного переделаем шапку

> 🧩 **Объяснение:** Вынесем шапку в отдельный шаблон. Это делается для повторного использования на всех страницах без дублирования кода.

> 📁 **Где создать `header.html` и `footer.html`?**
Создайте папку `includes` внутри `Core/templates/Core/`. Именно туда кладём:
- `header.html`
- `footer.html`

После чего в `base.html` подключаем их так:
```django
{% include 'Core/includes/header.html' %}
...
{% include 'Core/includes/footer.html' %}
```

Теперь пример шапки:

```html
<!-- Core/includes/header.html -->
<header>
    ..........
    <div class="collapse navbar-collapse flex-grow-0" 
         id="navbarSupportedContent">
        <ul class="navbar-nav mb-2 mb-lg-0 gap-3 gap-md-1 align-items-center">
            <li class="nav-item mt-3 mt-md-0">
                <a class="nav-link py-0" href="{% url 'movie_list' %}">Фильмы</a>
            </li>
            <li class="nav-item">
                <a class="nav-link py-0" href="{% url 'genre_list' %}">Жанры</a>
            </li>
            <li class="nav-item">
                <a class="nav-link py-0" href="{% url 'actor_list' %}">Актёры</a>
            </li>
            <li class="nav-item">
                <a class="nav-link py-0" href="{% url 'director_list' %}">Режиссёры</a>
            </li>
            <li class="d-flex justify-content-center gap-2">
                {% if request.user.is_authenticated %}
                    <div class="nav-item">
                        <a class="py-0" href="{% url 'profile' %}">
                            <img width="25" height="25" style="filter: invert(0.5)" src="{% static 'Core/img/user.png' %}" alt="profile">
                        </a>
                    </div>
                    <div class="nav-item my-auto">
                        <a class="py-0" href="{% url 'signout' %}">
                            <img width="25" height="25" style="filter: invert(0.5)" src="{% static 'Core/img/signout.png' %}" alt="signout">
                        </a>
                    </div>
                {% else %}
                    <div class="nav-item my-auto">
                        <a class="btn btn-secondary py-0" href="{% url 'signin' %}">Sign In</a>
                    </div>
                    <div class="nav-item my-auto">
                        <a class="btn btn-secondary py-0" href="{% url 'signup' %}">Sing Up</a>
                    </div>
                {% endif %}
                <div class="nav-item">
                    <img width="25" height="25" id="btn-change-theme" src="{% static 'Core/img/moon.png' %}" alt="theme">
                </div>
            </li>
        </ul>
    </div>
    ...
</header>
```

* ### Сейчас страница с фильмами должна выглядеть как-то так:
    ![](imgs/1.png)

---

## 2. Пишем карточку

> 🎯 **Зачем выносить карточку в отдельный файл:**
Если вы повторяете один и тот же HTML несколько раз — значит, пора выносить это в `include`. Так проще редактировать в будущем и код становится чище.

Скачайте `addon` к `bootstrap` — **[wide-classes](https://artasov.github.io/wide-classes/)**.  
Сохраните файл `wide-classes.css` в `Core/static/Core/css/`

Подключите его в `base.html`:
```html
<link type="text/css" rel="stylesheet" href="{% static 'Core/css/wide-classes.css' %}"/>
```

> 📘 **Поясните ученикам:**
`fccc` = `display: flex`, `flex-direction: column`, `justify-content: center`, `align-items: center`. Такие короткие записи делают верстку удобной, но важно понимать, что за ними стоит.

Карточка фильма:
```html
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | Фильмы{% endblock %}
{% block content %}
    <h1 class="text-center mb-4">Фильмы</h1>
    <div class="frc flex-wrap gap-4 mw-1000px mx-auto">
        {% for movie in movies %}
            <a href="{% url 'movie_detail' movie_id=movie.id %}" 
               class="fc mw-300px w-100 text-light text-decoration-none hover-scale-2">
                <img src="{{ movie.poster.url }}" alt="">
                <h3 class="mt-2">{{ movie.title }}</h3>
                <span class="frsc gap-2">
                    <span>Рейтинг:</span>
                    <span class="fs-5" style="color: #ffe655; padding-bottom: 1px;">
                        {{ movie.rating }}
                    </span>
                </span>
                <p>
                    {% for genre in movie.genres.all %}
                        {{ genre.name }}{% if not forloop.last %}, {% endif %}
                    {% endfor %}
                </p>
                <span class="text-secondary mt-auto">{{ movie.release_date }}</span>
            </a>
        {% endfor %}
    </div>
{% endblock %}
```

Установите русский язык для правильного отображения дат:
```python
# settings.py
LANGUAGE_CODE = 'ru-RU'
```

---

> 📁 **Создаём шаблон карточки:**
Файл `movie_card.html` создаём здесь:
`kinopoisk/templates/kinopoisk/includes/movie_card.html`

```html
<!-- kinopoisk/includes/movie_card.html -->
<a href="{% url 'movie_detail' movie_id=movie.id %}" 
   class="fc mw-300px w-100 text-light text-decoration-none hover-scale-2">
    <img src="{{ movie.poster.url }}" alt="">
    <h3 class="mt-2">{{ movie.title }}</h3>
    <span class="frsc gap-2">
        <span>Рейтинг:</span>
        <span class="fs-5" style="color: #ffe655; padding-bottom: 1px;">
            {{ movie.rating }}
        </span>
    </span>
    <p>
        {% for genre in movie.genres.all %}
            {{ genre.name }}{% if not forloop.last %}, {% endif %}
        {% endfor %}
    </p>
    <span class="text-secondary mt-auto">{{ movie.release_date }}</span>
</a>
```

И используем:

```html
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | Фильмы{% endblock %}
{% block content %}
    <h1 class="text-center mb-4">Фильмы</h1>
    <div class="frc flex-wrap gap-4 mw-1000px mx-auto">
        {% for movie in movies %}
            {% include 'kinopoisk/includes/movie_card.html' with movie=movie %}
        {% endfor %}
    </div>
{% endblock %}
```

---

## Должно получиться примерно так:
![](imgs/img.png)

---

## Загрузите проект на гит если еще не загружали.

---

## Подведите итоги.

✅ Сегодня мы:
- Разделили шаблоны на переиспользуемые части
- Написали свою карточку, а не использовали bootstrap-карточку
- Повторили основы flex-контейнеров
- Подключили кастомный CSS и научились включать его в шаблоны
- Научились использовать include и with для шаблонов

> # git push...

