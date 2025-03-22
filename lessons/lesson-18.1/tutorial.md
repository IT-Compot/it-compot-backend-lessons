# Начало работы с пользователями в Django

Сегодня мы изучим, как работает регистрация и авторизация пользователей в Django. Эти процессы являются основными при создании веб-приложений, требующих учетные записи.

---
## 1. Основные понятия: Аутентификация и Авторизация

Перед тем как приступить к кодингу, давайте разберемся с терминами:

- **Аутентификация** (Authentication) — это процесс проверки личности пользователя. Обычно осуществляется через ввод имени пользователя и пароля.
- **Авторизация** (Authorization) — процесс определения прав доступа пользователя к различным частям системы после успешной аутентификации.

Например, когда пользователь вводит логин и пароль, система проверяет их (аутентификация), а затем решает, какие действия он может выполнять (авторизация).

💡 **Важно!** Django использует встроенную систему пользователей, предоставляемую приложением `django.contrib.auth`.


---
## 2. Подготовка: создание приложения `acounts`

Для работы с пользователями создадим отдельное приложение:

```bash
python manage.py startapp acounts
```

Добавляем `acounts` в `INSTALLED_APPS` в `settings.py`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'acounts',  # Добавляем наше новое приложение
]
```

💡 **Почему отдельное приложение?**
Создание отдельного приложения `acounts` позволяет логически отделить функциональность, связанную с пользователями, от других частей проекта.


---
## 3. Настройка маршрутов

Создадим файл `acounts/urls.py`:

```python
from django.urls import path
from acounts.views import *

urlpatterns = [
    path('', signup, name='signup'),
    path('signin/', signin, name='signin'),
    path('signout/', signout, name='signout'),
]
```

Подключим маршруты `acounts` в `config/urls.py`:

```python
from django.urls import path, include
from django.contrib import admin

urlpatterns = [
    path('admin/', admin.site.urls),
    path('shop/', include('shop.urls')),
    path('', include('acounts.urls')),
]
```

📌 **Объяснение кода:**
- `path('')` — главная страница регистрации.
- `path('signin/')` — страница входа.
- `path('signout/')` — страница выхода.
- `include('acounts.urls')` — подключает маршруты из `acounts` к основному приложению.


---
## 4. Создание представлений (views)

Теперь напишем контроллеры для работы с пользователями в `acounts/views.py`:

```python
from django.shortcuts import render

def signup(request):
    return render(request, 'acounts/auth/signup.html')

def signin(request):
    return render(request, 'acounts/auth/signin.html')

def signout(request):
    pass  # Здесь будет реализация выхода
```

📌 **Объяснение:**
- `render(request, 'acounts/auth/signup.html')` — рендерит страницу регистрации.
- Аналогично для входа и выхода.

⚡ **Важно!** Пока что функция `signout` пустая, позже добавим логику выхода.


---
## 5. Создание шаблонов

Создадим папку `acounts/templates/acounts/auth/` и файлы:
- `signup.html`
- `signin.html`
- `profile.html`

> **Почему папка auth?** Так удобнее организовывать файлы.

Пример `signup.html`:
```html
<form method="post">
    {% csrf_token %}
    <input type="text" name="username" placeholder="Имя пользователя" required>
    <input type="password" name="password" placeholder="Пароль" required>
    <button type="submit">Зарегистрироваться</button>
</form>
```

📌 **Объяснение:**
- `{% csrf_token %}` — защита от атак CSRF.
- `<input>` — поля для ввода данных.
- `<button>` — кнопка отправки формы.


---
## 6. Итоги

✅ Мы разобрали:
- Разницу между аутентификацией и авторизацией.
- Подключение приложения `acounts`.
- Настройку маршрутов.
- Создание представлений и шаблонов.

⏭ **Что дальше?** В следующем уроке добавим обработку данных формы, хеширование паролей и систему входа/выхода.

🚀 **Не забудьте закоммитить изменения!**

