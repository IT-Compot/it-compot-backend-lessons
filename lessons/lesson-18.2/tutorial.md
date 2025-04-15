# Продолжаем работать с пользователями. 😊

Вспомните начало предыдущего урока и чем вообще на нем занимались.

## Допишем контроллер для регистрации 🚀

На прошлом занятии мы разбирали, как устроена регистрация пользователей, но не писали контроллеры. Сегодня мы это исправим! 

Пишите код медленно с пониманием, используйте **print** если требуется. 🖥️
Обратите внимание, что мы импортируем стандартного пользователя из **auth** приложения.
Вспомните, что такое `objects.create`. 🤔

Так же мы используем `objects.create_user` вместо `objects.create`, и это не просто так.
`create_user` это обычный `create`, но дополнительно сделана шифровка пароля. 🔐

```python
# accounts/views.py
from django.contrib.auth.models import User
from django.shortcuts import render, redirect

def signup(request):
    if request.user.is_authenticated:
        return redirect('orders')
    if request.method == 'POST':
        username = request.POST['username']
        email = request.POST['email']
        password = request.POST['password']
        r_password = request.POST['r_password']
        if password != r_password:
            return render(request, 'accounts/signup.html', {'error':'Пароли не совпадают'})
        User.objects.create_user(username=username, email=email, password=password)
        return redirect('signin')
    return render(request, 'accounts/signup.html')
```

✨ **Сделайте отображение ошибки в случае неверно введенных паролей.**

```html
<!-- signup.html -->
...
<h1 class="text-body text-center fw-bold mb-4">Регистрация</h1>
{% if error %}
    <p class="text-center text-danger fw-bold">{{ error }}</p>
{% endif %}
...
```
Создайте пользователя через форму, перейдите в админку и откройте объект нового пользователя. 🔍
Вы увидите в поле password зашифрованный пароль и неполный хэш для расшифровки. 🔑

```
algorithm: pbkdf2_sha256
iterations: 600000 
salt: HcopBX**************** 
hash: ZMUUCk**************************************
```

Так как мы видим не полную соль и хэш, то даже администратор сайта не знает пароли своих пользователей.

---

## Вход в систему `Sign in` 🔑

После регистрации пользователь может войти в систему.
В этом процессе Django проверяет предоставленные учетные данные и, в случае успеха, создает сессию для пользователя. 🛠️ Напомните, что такое сессия.

```python
# accounts/views.py
...
from django.contrib.auth import authenticate, login

def signin(request):
    if request.user.is_authenticated:
        return redirect('orders')
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        if user:
            login(request,user)
            return redirect('orders')
        else:
            return render(request, 'accounts/signin.html', {'errors':'Неверные данные'})
    return render(request, 'accounts/signin.html')
```

Добавьте отображение ошибки в шаблоне как только что делали. ✅

---

## Logout 🚪

Функция `logout` из модуля `django.contrib.auth` используется для управления процессом выхода пользователя из системы. Когда эта функция вызывается, она выполняет следующие действия:

🔹 **Удаление сессии пользователя**
🔹 **Очистка данных сессии**
🔹 **Изменение cookie**

```python
# accounts/views.py
from django.contrib.auth import logout
...
def signout(request):
    logout(request)
    return redirect('signin')
```

🛠️ **Проверьте, что все работает**: при неверных данных выдает ошибку, иначе нас перенаправляет в `orders`. Если выйти из аккаунта через админку, а после зайти через нашу форму, то при повторном заходе в админку мы уже будем аутентифицированы.

---

## Добавим в шапку 4 ссылки: `Профиль` `Вход` `Регистрация` `Выход` 🖥️

```html
<!-- base.html -->
<nav class="nav justify-content-center"> 
    <a class="nav-link text-white" href="{% url 'signout' %}">Выход</a>
    <a class="nav-link text-white" href="{% url 'orders' %}">Ордерс</a> 
    <a class="nav-link text-white" href="{% url 'signup' %}">Регистрация</a>
    <a class="nav-link text-white" href="{% url 'signin' %}">Вход</a> 
</nav>
```

---
## !!!!!В конце подогреваем интерес ребёнка к другому курсу!!!!!
•	Фраза преподавателя:
«Сейчас мы пишем логику регистрации и входа вручную на сайте. Но представьте, насколько круче было бы, если можно было бы зарегистрироваться и авторизоваться через Telegram-бота! Просто написал боту, и всё – ты уже авторизован!»
•	Инструкция:
Показать пример работы Telegram-бота, кратко упомянуть, что именно так ученики научатся делать на следующем году Python

https://disk.yandex.ru/i/nJA3DHVXUmtaOw


## На следующем занятии мы сделаем корректное отображение этих кнопок и научимся немного работать с распределением доступа. 🚀

---

## 🔐 Дополнительная информация: Шифрование `pbkdf2_sha256`

`pbkdf2_sha256` относится к алгоритму хеширования паролей, который используется для обеспечения безопасности хранения паролей. 💾

🔹 `PBKDF2` - это функция, используемая для преобразования пароля в ключ. 🛠️ Она увеличивает сложность взлома паролей методом `brute-force`. 🤖

🔹 `SHA256` - алгоритм хеширования, который генерирует уникальный, фиксированный размер хеша (256 бит) из входных данных.

🔹 `Соль (Salt)` - случайная строка, добавляемая к паролю, чтобы даже одинаковые пароли выглядели по-разному в хранимом виде.

🔹 `Хеширование` - многократное шифрование пароля с солью для повышения безопасности.

🔹 `Хранение` - результат хеширования (с солью и количеством итераций) сохраняется в базе данных. **Это именно то, что мы видели в админке.** ✅

Когда пользователь входит в систему, введенный пароль обрабатывается тем же способом, и если хэши совпадают, доступ предоставляется. 🔓

---

### 📌 Подведите итоги. 
**Теперь у нас есть регистрация, вход и выход из системы. На следующем уроке разберемся с отображением кнопок и доступами!** 🔥

