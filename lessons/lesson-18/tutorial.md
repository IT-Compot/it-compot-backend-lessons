# Начало работы с пользователями в Django

Сегодня мы начнем разбирать, как работает аутентификация и система пользователей в Django.

---

## 🔑 1. Аутентификация и авторизация
### Что это и зачем?
**Аутентификация** — это процесс проверки личности пользователя (логин и пароль). 
**Авторизация** — определяет, какие права у пользователя после входа.

💡 *Пример:* 
> Когда вы входите в соцсеть, вводите логин и пароль — это аутентификация. 
> Когда вам доступны ваши посты, но не посты других пользователей в режиме редактирования — это авторизация.

В Django управление пользователями и аутентификацией реализуется через встроенное приложение `django.contrib.auth`.

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.auth',  # Модуль аутентификации пользователей 🔒
    'django.contrib.sessions',  # Модуль для хранения сессий 💾
    ...
]
```
💡 *Это приложение уже установлено по умолчанию!*

🛠 **Где посмотреть встроенную модель User?**
В файле `venv/Lib/site-packages/django/contrib/auth/models.py` содержится модель `User`.

---

## 🏗 2. Создаем приложение для работы с пользователями
### Почему отдельное приложение?
У нас уже есть `shop`, но логичнее вынести регистрацию в отдельное приложение для лучшей структуры кода.

📌 **Создаем приложение `acounts`**
```bash
python manage.py startapp acounts
```
Не забудьте добавить его в `INSTALLED_APPS`:
```python
INSTALLED_APPS = [
    'acounts',
    ...
]
```

---

## 🛤 3. Настраиваем маршруты
### `acounts/urls.py`
Здесь пропишем URL-адреса для регистрации, входа и выхода.

```python
from django.urls import path
from acounts.views import *

urlpatterns = [
    path('', signup, name='signup'),  # Регистрация ✍
    path('signin/', signin, name='signin'),  # Вход 🔑
    path('signout/', signout, name='signout'),  # Выход 🚪
]
```

📌 **Добавляем маршруты в `config/urls.py`**
```python
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('shop/', include('shop.urls')),
    path('', include('acounts.urls'))  # Подключаем маршруты аккаунтов 👤
]
```

---

## 📄 4. Создаем шаблоны страниц
📌 **Структура папок:**
```
acounts/
└── templates/
    └── acounts/
        ├── signup.html
        ├── signin.html
        ├── profile.html
```
Создадим базовые HTML-страницы для регистрации, входа и профиля пользователя.

---

## 🎯 5. Настраиваем контроллеры (views.py)
Контроллеры — это функции, которые обрабатывают запросы пользователей.

```python
from django.shortcuts import render

def signup(request):
    return render(request, 'acounts/signup.html')

def signin(request):
    return render(request, 'acounts/signin.html')

def signout(request):
    pass  # Реализуем позже 🏗
```

📌 **Проверьте, что страницы работают!**
Запустите сервер `python manage.py runserver` и откройте `/` в браузере.

---

## 🚀 Итог
✅ Разобрали аутентификацию и авторизацию.
✅ Создали приложение `acounts`.
✅ Настроили маршруты.
✅ Добавили базовые представления.

➡ **Далее:** Реализация логики регистрации и входа! 🔥

