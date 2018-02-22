
class: center, middle

# Elm и<br/>The Elm Architecture<br/><small>что это и зачем это всё?</small>

---

# Elm - язык

- Статические типы, ADT, параметрический полиморфизм, анонимные records
```elm
type Pet = Dog | Cat
type alias Field a =
   { value : a, error : String }
```
- Компилируется в JS (своими силами)
- Дружественен новичку, нелюбим хаскелистами (как Go)
- Никак не зависит от JS-экосистемы (babel, webpack и т.п.)

---

# Elm - экосистема

- Встроенный фреймворк для GUI-строения
- Свой инструментарий для
  - сборки
  - пакетирования
  - генерирования документации
- Свой репозиторий пакетов, гарантирующий semver
- Time traveling отладчик
- Приличное кол-во пакетов (pure elm)

---

# The Elm Architecture

**Назначение**: упростить и систематизировать разработку **GUI** в функциональном стиле.

Особенности:

- Чётко выделенные слои: Model, View, Update
- Единый источник правды (состояния) - модель
- Эффекты изолированы в ядре фреймворка, сам код - чистый
- Асинхронность - тоже эффект

---

# TEA в [одном типе](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#program)

```elm
program :
  { init   : (model, Cmd msg)
  , view   : model -> Html msg
  , update : msg -> model -> (model, Cmd msg)
  , subscriptions
           : model -> Sub msg }
  -> Program Never model msg
```

- `Cmd` - заявки на произведение эффекта
- `Sub` - потоки внешних событий

---

# TEA == систематика

- Все возможные `msg` заранее известны и должны быть обработаны
- `model` всегда одна, но с подмоделями
- `update` вызывает sub-updates для подмоделей
- `view` вызывает sub-views для подмоделей

---

# Влияние

- [Miso](https://haskell-miso.org/) (Haskell/GHCJS)
- [Helm game engine](http://helm-engine.org/) (Haskell)
- [Pux](http://purescript-pux.org/) (PureScript)
- [Elmish](https://fable-elmish.github.io/) (F#/Fable)
- [Choo](https://choo.io/) (JavaScript)
- [Redux](https://redux.js.org/) (JavaScript)
- ...

---

class: center, middle

# Обсуждение
