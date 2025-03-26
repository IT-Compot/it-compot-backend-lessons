# Кинопоиск. Проектирование сущностей.

> 💬 **Комментарий для преподавателя:** Этот проект помогает систематизировать знания по Django и работе с моделями. Необходимо уделить внимание структурированию материала и последовательной подаче, особенно если ученики давно не практиковались или пропустили что-то важное. 
> Не бойтесь быть капитаном Очевидность — лучше повторить, чем потом объяснять, почему сайт упал и не встаёт.

Теперь, когда мы знаем так много всего, нужно это закрепить на финальном проекте и узнать немного нового.

> 🧠 **Важно:** Ученики могут забыть детали — не стесняйтесь напоминать даже очевидные вещи: где писать команды, зачем нужна каждая часть структуры, что за что отвечает и т.д. Если кто-то растерян — помогите, не бросайте его в океане Django без спасательного круга.

В этом проекте я буду гораздо меньше акцентировать внимание на уже известных и сделанных не раз деталях. Но вы как преподаватель, не забывайте делать это за меня.

Разрабатывать мы будем слоями: сначала модели, потом маршруты, потом шаблоны, потом контроллеры. Поэтому важно, чтобы ученики понимали детали проекта изначально.

> 🎯 **Совет:** Начните с обсуждения структуры сайта: какие будут страницы, как они связаны между собой, откуда куда можно перейти. Используйте рисование на доске или блок-схемы — это работает лучше, чем просто объяснение. Ученикам проще представить сайт как карту метро, а не как абстрактную идею.

---

## 1. Создадим новый проект `kinopoisk`

Создайте новую папку, а в ней сделайте виртуальную среду, установите `django` и запустите новый проект.

> 🛠️ **Подсказка:** Проект мы называем всегда `config` — это просто хорошая практика. Она позволяет отделить настройки от других приложений. Название `config` = "папка с мозгами" проекта.

---

## 2. Продумаем наш проект

> 💡 **Обсудите с учениками:** Почему логика важнее дизайна? Потому что если у дома кривой фундамент — никакие обои не спасут. Сначала архитектура, потом внешний вид.

Пример: [Шрек на Кинопоиске](https://www.kinopoisk.ru/film/430/)

> 🔍 **Разбор ссылки:** В URL указан `id` фильма — это значит, что сайт работает с базой данных и использует идентификаторы для поиска информации. Мы будем делать так же. Это реальный пример RESTful URL.

#### В проекте будет:

##### 1. Core:
Страницы: `signin`, `signup`, `signout`, `profile`  
Модель: `User`

##### 2. kinopoisk:
Страницы: `signin`, `signup`, `signout`, `profile`  
Модели: `Movie`, `Actor`, `Review`, `Genre`

> ⚠️ **Вопрос:** Почему и там и там повторяются страницы? Потому что `Core` отвечает за пользователей, а `kinopoisk` — за контент. Это как рецепты и повара — и то, и другое нужно, но отвечает за них разное.

---

## 3. Продумаем модели

> 🧩 **Инструменты:** Предложите попробовать [sql.toad.cz](https://sql.toad.cz/) — визуальный редактор поможет лучше представить связи моделей. Даже если потом будете писать руками, сначала можно "поиграть в кубики".

> 🛑 **Пояснение:** Почему не стоит переопределять `User`, если можно обойтись `UserProfile`? Потому что потом миграции могут превратиться в адскую боль. Мы это уже проходили 🙃

Добавьте `Core` в `INSTALLED_APPS`, измените `AUTH_USER_MODEL = 'Core.User'`

> ❌ Удалите лишние ссылки в шаблоне шапки (на каталог), иначе будут ошибки. А ошибки на старте — это как разлитый кофе на клавиатуру.

Создайте приложение `kinopoisk` и подключите его к проекту.

> 🧠 **Миграции после всех моделей.** Почему? Потому что если вы мигрировали модель `Movie`, а потом поняли, что забыли поле `rating` — придётся либо страдать, либо делать новые миграции. Лучше один раз, но с чувством, толком, расстановкой.

```python
# Core/models.py
class User(AbstractUser):
    # 💬 Объясните, что такое ManyToManyField.
    # Это связь "многие ко многим" — пользователь может добавить много фильмов в избранное,
    # и один фильм может быть в избранном у многих пользователей.
    # По сути, это как корзина любимых фильмов, но у каждого своя.
    favorite_movies = models.ManyToManyField('Movie')
    avatar = models.ImageField(upload_to='avatars/', null=True, blank=True)
```

> 🔄 **Объяснение `related_name`:** Это имя, по которому можно будет обратиться к связи из обратной стороны. 
Пример:
```python
review = user.reviews.first()
movies = genre.movies.all()
```
Если `related_name` не указать — Django придумает его сам, и это может быть что-то вроде `user_set`, что не всегда читаемо.

📌 [Шпаргалка по related_name](https://github.com/xlartas/it-compot-backend-methods/blob/main/django-base.md#Related-Name)

```python
# kinopoisk/models.py
class MoviePerson(models.Model):
    class RoleType(models.TextChoices):
        ACTOR = 'actor', 'Actor'
        DIRECTOR = 'director', 'Director'

    name = models.CharField(max_length=255)
    birth_date = models.DateField(blank=True, null=True)
    photo = models.ImageField(upload_to="kinopoisk/images/person/photos/", blank=True, null=True)
    role = models.CharField(max_length=20, choices=RoleType.choices, blank=True, null=True)

class Genre(models.Model):
    name = models.CharField(max_length=255, unique=True)
    description = models.TextField(blank=True, null=True)

class Movie(models.Model):
    title = models.CharField(max_length=355)
    description = models.TextField()
    release_date = models.DateField(null=True, blank=True)
    rating = models.FloatField(null=True, blank=True)
    duration = models.PositiveSmallIntegerField()  # в минутах
    genres = models.ManyToManyField(Genre, related_name='movies')
    directors = models.ManyToManyField(MoviePerson, related_name='directed_movies')
    budget = models.PositiveIntegerField()
    actors = models.ManyToManyField(MoviePerson, related_name='acted_in_movies')
    poster = models.ImageField(upload_to="kinopoisk/images/movies/posters/", blank=True, null=True)

class MovieReview(models.Model):
    author = models.ForeignKey(User, on_delete=models.SET_NULL, null=True, related_name='reviews')
    movie = models.ForeignKey(Movie, on_delete=models.CASCADE, related_name='reviews')
    text = models.TextField()
    likes = models.PositiveIntegerField(default=0)
    created_at = models.DateTimeField(auto_now_add=True)
```

> 📚 **Что делает `TextChoices`:** 
Создает список фиксированных значений, которые удобно использовать в форме выбора (dropdown).

| В коде | В интерфейсе |
|-------|---------------|
| actor | Actor         |
| director | Director   |

> 🛠️ **Идея:** В `MoviePerson` — и актёры, и режиссёры. Если появятся каскадёры или продюсеры — просто добавим новые роли в `RoleType`. Удобно и масштабируемо.

---

## 4. Проведите миграции

> 🧼 **Не забудьте:** Удалите старые миграции приложения `Core`, если копировали их из проекта. Django не любит, когда у него в багаже старые хвосты — он начинает чудить.


---


## Подведите итоги

> 🗂 **Совет:** Хорошая привычка — каждый этап коммитить в Git. Если не успели — хотя бы к 3 занятию с Кинопоиском настройте GitHub.

📎 *Можно добавить обложку проекта — например, кадр из "Шрека", чтобы ученикам было приятнее визуально ассоциировать проект с чем-то знакомым.*

