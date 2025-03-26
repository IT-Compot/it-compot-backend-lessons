# Кинопоиск. Простые шаблоны

> 🧠 **Комментарий для преподавателя:** Этот этап — отличная возможность научить учеников работать с шаблонами Django. Объясняйте не столько синтаксис, сколько **логику передачи данных из views в шаблон**. Напоминайте, что в шаблонах мы *не пишем логику*, а просто **отображаем** то, что уже пришло.

---

## 1. Скачаем базу данных и медиа файлы

> 📦 **Важно:** Убедитесь, что структура моделей у всех совпадает. Если нет — будет конфликт при загрузке дампа. Дайте ученикам пару минут на самопроверку, прежде чем идти дальше.

- Удалите `db.sqlite3`
- Скачайте архив `kinopoisk_data.zip` из [репозитория со шпаргалками](https://github.com/xlartas/it-compot-backend-methods)
- Распакуйте в `MEDIA_ROOT`
- **Суперпользователь**: `123:123`

> 🧪 **Проверьте:** В админке должны появиться фильмы, актёры, режиссёры. А картинки должны отображаться. Если нет — проверьте пути, `MEDIA_ROOT` и соответствие моделей.

Если названия приложений и моделей отличаются от оригинала:
- Или переименуйте проект под автора 😅
- Или вручную отредактируйте дампы из папки `dump/`, подставив свои названия.

Команда загрузки:
```bash
python manage.py loaddata dump/kinopoisk_movieperson.json
```

Повторить для всех файлов.

> 🔧 **Если дамп не загружается:** Значит поля/названия моделей в вашем проекте не совпадают с теми, что в дампе. Проверьте и исправьте.

---

## 2. Допишем наши шаблоны

> 📌 **Комментарий:** Сейчас не важен внешний вид. Главное — научить учеников вытаскивать данные в шаблон. Сосредоточьтесь на `{{ переменные }}` и `{% for ... %}`. 

Каждый шаблон **наследуется** от базового (`Core/base.html`). Напомните, что наследование помогает **не копировать одинаковые части**, такие как шапка или подвал.

> 💡 Неважно какие теги используются. Главное — **передать и отобразить данные**.

---

## Пример базового шаблона `base.html`

> 🏗 **Пояснение:** Этот шаблон нужен для общей структуры сайта. Всё, что повторяется на каждой странице — меню, шапка, футер — можно положить сюда. Остальное будет вставляться через блоки `title` и `content`.

```html
<!-- Core/templates/Core/base.html -->
{% load static %}
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>{% block title %}Кинопоиск{% endblock %}</title>
    <link rel="stylesheet" href="{% static 'Core/style.css' %}">
</head>
<body>
    <header>
        <h1><a href="/">Кинопоиск</a></h1>
        <nav>
            <a href="/movies/">Фильмы</a>
            <a href="/actors/">Актёры</a>
            <a href="/directors/">Режиссёры</a>
            <a href="/genres/">Жанры</a>
        </nav>
    </header>

    <main>
        {% block content %}{% endblock %}
    </main>

    <footer>
        <p>© 2025 Кинопоиск</p>
    </footer>
</body>
</html>
```

---

## Как подключить Bootstrap (дополнительно)

> 🌐 **Инструкция:**
Чтобы сразу стилизовать элементы сайта, можно использовать Bootstrap. Это особенно удобно для преподавателей, чтобы не тратить время на CSS вручную.

Вставьте **в `<head>`** до стилей:
```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-9ndCyUaIbzAi2FUVXJi0CjmCapSmO7SnpJef0486qhLnuZ2cdeRhO02iuK6FUUvm" crossorigin="anonymous">
```

И **в конец `<body>`**:
```html
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js" integrity="sha384-ENjdO4Dr2bkBIFxQpeoE6ogZBwr5yZ6syk5q1jtVV+zr3eFu5xq3Gf1XKp5Lje7L" crossorigin="anonymous"></script>
```

Можно также сделать адаптированную версию `base.html` с Bootstrap (необязательная альтернатива для тех, кто хочет красивее сразу). Обе версии можно держать параллельно.

---

```html
<!-- kinopoisk/main.html -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | Главная{% endblock %}
{% block content %}
    {# Пока переменных нет. Позже можно добавить новинки, избранные и т.д. #}
{% endblock %}
```

```html
<!-- kinopoisk/movie_list.html -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | Фильмы{% endblock %}
{% block content %}
    <h2>Фильмы</h2>
    {% for movie in movies %}
        <p>{{ movie.title }}</p>
    {% endfor %}
{% endblock %}
```

```html
<!-- kinopoisk/person_list.html -->
<!-- Используется и для актёров, и для режиссёров -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | {{ title }}{% endblock %}
{% block content %}
    <h2>{{ title }}</h2>
    {% for person in persons %}
        <p>{{ person.name }}</p>
    {% endfor %}
{% endblock %}
```

```html
<!-- kinopoisk/genre_list.html -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | Жанры{% endblock %}
{% block content %}
    <h2>Жанры</h2>
    {% for genre in genres %}
        <p>{{ genre.name }}</p>
    {% endfor %}
{% endblock %}
```

```html
<!-- kinopoisk/movie_detail.html -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | {{ movie.title }}{% endblock %}
{% block content %}
    <h2>{{ movie.title }}</h2>
    <p>{{ movie.description }}</p>
{% endblock %}
```

```html
<!-- kinopoisk/person_detail.html -->
<!-- Используется и для актёров, и для режиссёров -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | {{ person.name }}{% endblock %}
{% block content %}
    <h2>{{ person.name }}</h2>
    <h3>Участвовал в:</h3>
    {% for movie in movies %}
        <p>{{ movie.title }}</p>
    {% endfor %}
{% endblock %}
```

```html
<!-- kinopoisk/genre_detail.html -->
{% extends 'Core/base.html' %}
{% block title %}Кинопоиск | {{ genre.name }}{% endblock %}
{% block content %}
    <h2>{{ genre.name }}</h2>
    {% for movie in movies %}
        <p>{{ movie.title }}</p>
    {% endfor %}
{% endblock %}
```

> ✅ **Проверьте:** Все страницы должны корректно отображаться. На них должен быть вывод — пусть пока простой, но рабочий. Убедитесь, что данные передаются из views в шаблон правильно.

---

## Загрузите проект на Git, если ещё не загружали

> 💬 Покажите коммит: `git add .`, `git commit -m "простые шаблоны"`, `git push` — и пусть делают это регулярно.

---

## Переходите к следующему занятию

---

## Подведите итоги

✅ Сегодня мы:
- Загрузили готовую БД и медиафайлы
- Сделали шаблоны на основе переменных из views
- Отработали базовое наследование и цикл for
- Добавили базовый шаблон `base.html`, `load static`
- Показали, как подключить Bootstrap через CDN для стилизации (опционально)

> 🚀 Визуально пока просто. Но с этого начинается любой интерфейс.

># git push...

