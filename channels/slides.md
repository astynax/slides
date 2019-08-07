# Django Channels: готовимся к асинхронному будущему!

.fx: centered

## Django Channels:<br/>готовимся к<br/>асинхронному будущему!

Алексей Пирогов ([@alex_pir]())

**"PiterPy-2016"**

---

# Что это?

**Channels** добавляет в Django новый слой, позволяющий

- использовать WebSockets (и довольно удобно!)
- запускать фоновые задачи в том же процессе, в котором запущен Django

---

# Как это работает?

---

# Процессы

Channels вводит два типа процессов:

1. обрабатывающие HTTP и WebSockets
1. запускающие views, WebSocket handlers, фоновые задачи

Общаются эти процессы посредством **ASGI** - Asynchronous Server Gateway Interface.

При этом не привносятся *asyncio*, *gevent* и прочая асинхронщина - *вся логика остается синхронной*!

---

# Из коробки

- Поддержка HTTP long-poll (и тысячи подключений)
- Полноценные сессии и аутентификация для WebSockets
- Автоматический login через WebSocket (cookies)
- Встроенные примитивы для трансляции событий (chats!)
- Перезапуск компонент без простоя!
- Расширяемость в сторону поддержки WebRTC и проч
- ...

---

# Что поменяется?

---

# Что поменяется?

- ASGI-сервер (напр. [Daphne](http://github.com/andrewgodwin/daphne)) заменит WSGI
- Добавятся workers (``python manage.py runworker``)
- Добавится некий транспорт для ASGI (напр. *Redis*)

---

# ASGI

---

# ASGI

**ASGI** (Asynchronous Server Gateway Interface) - интерфейс между серверами протоколов
(веб-серверами) и Python-приложениями.

Создан стать заменой WSGI.

Позволяет приложению работать *независимо от протоколов связи*, принимать и отправлять
сообщения в *любой момент времени*.

---

# ASGI

Компоненты ASGI

- protocol servers
- channel layer
- application code

---

# А это масштабируется?

---

# Масштабируется!

- Можно иметь несколько protocol-servers
- Можно иметь несколько applications
- И даже несколько channel layers

---

# Покажите уже код!

---

# Пример

``settings.py``:

    :::python
    INSTALLED_APPS = [
        ...
        'channels'
    ]

    CHANNEL_LAYERS = {
        "default": {
            "BACKEND": "asgiref.inmemory.ChannelLayer",
            "ROUTING": "chan.routing.CHANNEL_ROUTING",
        },
    }

---

# Пример

``routing.py``:

    :::python
    from channels.routing import route
    from chan.consumers import (
        ws_add, ws_message, ws_disconnect)

    CHANNEL_ROUTING = [
        route("websocket.connect", ws_add),
        route("websocket.receive", ws_message),
        route("websocket.disconnect", ws_disconnect),
    ]

---

# Пример

``consumers.py``:

    :::python
    from channels import Group

    def ws_add(message):
        Group("chat").add(message.reply_channel)

    def ws_message(message):
        Group("chat").send({
            "text": "[user] %s" % message.content['text'],
        })

    def ws_disconnect(message):
        Group("chat").discard(message.reply_channel)

---

# Вопросы?

---

# Спасибо за внимание!
