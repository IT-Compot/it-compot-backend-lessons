# Понятие `Code refactoring`, страница персоны и жанра.

> 🧠 **Комментарий для преподавателя:** Этот урок — отличная возможность показать, как можно писать **меньше кода** без потери логики. Объясните идею DRY (Don't Repeat Yourself): если код повторяется, его надо объединять. Также важно проговорить, что **рефакторинг** — это не просто про «сделать красивее», а про поддержку читаемости, стабильности и расширяемости проекта в будущем.

---

Рефакторинг кода — это процесс улучшения существующего кода 
без изменения его внешнего поведения. Цель рефакторинга — сделать 
код более понятным, читаемым и эффективным, а также упростить 
дальнейшую разработку и обслуживание программы.
> Однако мы все же перепишем некоторую логику

---

## 1. Исправляем код

> 💡 **Комментарий:** Покажите на доске или в обсуждении: раньше было две почти одинаковых функции — `actor_detail` и `director_detail`. Мы можем оставить одну функцию `person_detail`, потому что по роли (actor/director) можно получить нужные фильмы.

Заметим, что зная `id` объекта `MoviePerson` мы можем его получить. 
Нам не нужно знать его роль (актер или режиссер).
Исходя из этого, можно сделать вывод, что нам не нужно 2 контроллера
для отображения `actor_detail` и `director_detail`.

### Urls

> 📁 **Файл:** `kinopoisk/urls.py`

#### Поменяем это:
```python
urlpatterns = [
    ...
    path('actor/<int:actor_id>/', actor_detail, name='actor_detail'),
    path('director/<int:director_id>/', director_detail, name='director_detail')
]
```

#### На это:
```python
urlpatterns = [
    ...
    path('person/<int:person_id>/', person_detail, name='person_detail')
]
```

---

### Controllers

> 📁 **Файл:** `kinopoisk/views.py`

#### Поменяем это:
```python
def actor_detail(request, actor_id):
    actor = MoviePerson.objects.get(id=actor_id)
    movies = actor.acted_in_movies.all()
    return render(request, 'kinopoisk/person_detail.html', {
        'person': actor, 'movies': movies,
    })

def director_detail(request, director_id):
    director = MoviePerson.objects.get(id=director_id)
    movies = director.directed_movies.all()
    return render(request, 'kinopoisk/person_detail.html', {
        'person': director, 'movies': movies,
    })
```

#### На это:
```python
def person_detail(request, person_id):
    person = MoviePerson.objects.get(id=person_id)
    if person.role == MoviePerson.RoleType.ACTOR:
        movies = person.acted_in_movies.all()
    else:
        movies = person.directed_movies.all()
    return render(request, 'kinopoisk/person_detail.html', {
        'person': person,
        'movies': movies
    })
```

> 🔁 **Обсудите:** «А почему мы не можем сделать так же со списками актёров и режиссёров?» — можем, и это будет хорошей задачей на дом или бонусом к занятию.

---

## 2. Напишем `person_detail.html`

> ⚠️ **Внимание:** Для слабых учеников это может быть слишком насыщенный шаблон. Упростите, если нужно. Главное — логика шаблона и подключение карточек через `include`.

```html
<!-- kinopoisk/person_detail.html -->
{% extends "Core/base.html" %}
{% load static %}
{% block title %}Кинопоиск | {{ person.name|title }}{% endblock %}

{% block content %}
    <div class="frc">
        <div class="person_container fccs justify-content-md-center flex-md-row m-sm-0 gap-5 w-90 mx-auto ">
            <img class="h-min mx-md-0 mx-auto" src="{{ person.photo.url }}" alt="">
            <div class="fc mx-auto mx-md-0">
                <h1 class="mb-4 text-center me-md-auto d-inline">{{ person.name|title }}</h1>
                <span class="text-center me-md-auto d-inline">
                    Дата рождения: {{ person.birth_date }}
                </span>
                <div class="mt-3 frc gap-3 mw-550px flex-wrap bg-black-30 p-4 rounded-4">
                    {% for movie in movies %}
                        {% include 'kinopoisk/includes/movie_card.html' with movie=movie %}
                    {% endfor %}
                </div>
            </div>
        </div>
    </div>
{% endblock %}
```

> 💬 **Поясните:** здесь `include` помогает не копипастить шаблон карточки, и в будущем можно будет менять отображение карточек централизованно.

---

Добавьте ссылку в список персон:
```html
<!-- kinopoisk/person_list.html -->
...
{% for person in persons %}
    <a href="{% url 'person_detail' person_id=person.id %}" 
       class="fc gap-2 mw-150px w-100 text-light text-decoration-none hover-scale-2">
        <img src="{{ person.photo.url }}" alt="" class="h-max">
        <h2 class="fs-6">{{ person.name }}</h2>
    </a>
{% endfor %}
...
```

---

## 3. Напишем `genre_detail.html`

```html
{% extends "Core/base.html" %}
{% load static %}
{% block title %}Кинопоиск | {{ genre.name|title }}{% endblock %}

{% block content %}
    <div class="fccc">
        <h1 class="mb-4">{{ genre.name|title }}</h1>
        <div class="fr gap-3 mw-1000px flex-wrap">
            {% for movie in movies %}
                {% include 'kinopoisk/includes/movie_card.html' with movie=movie %}
            {% endfor %}
        </div>
    </div>
{% endblock %}
```

> 🧩 Совсем простой шаблон: заголовок + карточки фильмов. Убедитесь, что `genre` и `movies` приходят в шаблон.

---

## 4. Если осталось время

Карточки фильмов на странице с актерами и режиссерами большеваты.

> 🧪 Это упражнение на **адаптивность и управление внешним видом через CSS или параметры include**. Обязательно объясните оба подхода, чтобы у учеников был выбор.

### Я вижу 2 решения:

#### 1. Использовать класс-контейнер (`person_container`) и кастомные стили

> 🛠️ Файл: `kinopoisk/static/kinopoisk/css/person_detail.css`
```css
.person_container .movie_card {
    max-width: 150px !important;
}
.person_container .movie_card h3 {
    font-size: 1.5rem !important;
}
```

Добавим блок `head` в `base.html`, если его ещё нет:
```html
<!-- Core/base.html -->
<head>
    ...
    {% block head %}{% endblock %}
    ...
</head>
```

А в `person_detail.html`:
```html
{% block head %}
    <link rel="stylesheet" href="{% static 'kinopoisk/css/person_detail.css' %}">
{% endblock %}
```

#### 2. Передавать классы в include:
```html
{% include 'kinopoisk/includes/movie_card.html' with movie=movie movie_card_classes='mw-150px' %}
```
И в шаблоне карточки:
```html
<a href="{% url 'movie_detail' movie_id=movie.id %}" 
   class="{{ movie_card_classes }} fc mw-300px w-100 text-light text-decoration-none hover-scale-2">
   ...
</a>
```

> ✅ Второй способ чуть сложнее, но он гибче: можно на лету менять отображение карточек прямо в шаблоне.

---

## Подведите итоги.

✅ Сегодня мы:
- Отрефакторили контроллеры и urls
- Использовали условную логику для показа разных фильмов (актёр/режиссёр)
- Освоили `include` и `with`
- Научились подключать отдельные стили только для нужных страниц
- Упростили шаблоны и сделали их читаемыми

> # git push...

