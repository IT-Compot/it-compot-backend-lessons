# Кинопоиск. Доделываем контроллеры

> 🧠 **Комментарий для преподавателя:** Сейчас особенно важно объяснять **зачем** мы передаём те или иные переменные в шаблоны. Если ученик просто повторит за вами код — он забудет его через 10 минут. Если поймёт, почему и как он работает — останется надолго.

На этом уроке мы должны доделать контроллеры так, чтобы они передавали внутрь шаблонов нужные нам данные.

Похожих контроллеров много, а значит у учеников будет много личной практики.

---

## 1. Контроллеры

> 📚 **Методика:** Обсуждайте с учениками, что именно нужно передать в шаблон. Не давайте сразу код — задавайте вопросы:
- Что нужно вывести?
- Где эти данные лежат?
- Как их получить через ORM?

Параллельно можно поглядывать в раздел **[ORM](https://github.com/xlartas/it-compot-backend-methods/blob/main/django-base.md#orm)** в шпаргалке.

```python
from django.shortcuts import render, get_object_or_404
from .models import Movie, MoviePerson, Genre

def main(request):  # Сами
    # 💬 Пока ничего не передаём. Позже сюда можно вывести "Лучшие фильмы" или "Последние добавленные".
    return render(request, 'kinopoisk/main.html')

def movie_list(request):  # Сами
    # 🎬 Получаем все фильмы. Это список, который отобразится в шаблоне "все фильмы".
    movies = Movie.objects.all()
    return render(request, 'kinopoisk/movie_list.html', {
        'movies': movies
    })

def actor_list(request):  # Подсказываем
    # 👨‍🎤 Фильтруем всех людей по роли "актёр".
    actors = MoviePerson.objects.filter(role=MoviePerson.RoleType.ACTOR)
    return render(request, 'kinopoisk/person_list.html', {
        'persons': actors,  # 👈 в шаблоне будет список людей-актёров
        'title': 'Актёры'   # 👈 можно использовать в заголовке страницы
    })

def director_list(request):  # Сами
    # 🎬 Фильтруем по роли "режиссёр"
    directors = MoviePerson.objects.filter(role=MoviePerson.RoleType.DIRECTOR)
    return render(request, 'kinopoisk/person_list.html', {
        'persons': directors,
        'title': 'Режиссёры'
    })

def genre_list(request):  # Сами
    # 🎭 Получаем все жанры из базы
    genres = Genre.objects.all()
    return render(request, 'kinopoisk/genre_list.html', {
        'genres': genres
    })

def movie_detail(request, movie_id):  # Сами
    # 📽 Получаем один конкретный фильм по ID
    movie = Movie.objects.get(id=movie_id)
    return render(request, 'kinopoisk/movie_detail.html', {
        'movie': movie
    })

def actor_detail(request, actor_id):  # Напомнить про related_name
    # 👤 Получаем актёра по ID
    actor = MoviePerson.objects.get(id=actor_id)
    # 🎥 Используем related_name='acted_in_movies' — получаем все фильмы с этим актёром
    movies = actor.acted_in_movies.all()
    return render(request, 'kinopoisk/person_detail.html', {
        'person': actor,
        'movies': movies
    })

def director_detail(request, director_id):  # Сами
    director = MoviePerson.objects.get(id=director_id)
    movies = director.directed_movies.all()  # 🎯 related_name='directed_movies'
    return render(request, 'kinopoisk/person_detail.html', {
        'person': director,
        'movies': movies
    })

def genre_detail(request, genre_id):  # Сами
    genre = Genre.objects.get(id=genre_id)
    movies = genre.movies.all()  # 📚 related_name='movies' из поля в Movie: genres = ManyToManyField(..., related_name='movies')
    return render(request, 'kinopoisk/genre_detail.html', {
        'genre': genre,
        'movies': movies
    })
```

---

## Место для загрузки на гит или для доделать что-либо

> 💾 **GitHub:** Самое время закоммитить изменения. Покажите, как делать осмысленные коммиты — `git commit -m "добавил контроллеры"` лучше, чем просто `update`.

---

## Подведите итоги

✅ Сегодня мы:
- Написали контроллеры для всех маршрутов
- Объяснили логику передачи данных в шаблоны
- Вспомнили `related_name` и как через него обращаться к связанным объектам

> ⛵ Убедитесь, что ученики поняли: **контроллер** — это мост между моделью и шаблоном. Он вытаскивает нужные данные и отправляет их в HTML.

># git push...

