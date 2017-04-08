
class: center, middle

# react-flux: <br/>React-powered Web GUI<br/>на Haskell

Алексей Пирогов aka [astynax](https://github.com/astynax)

[Typeable.io](http://typeable.io)

---

# Мы поговорим о

- [React](https://facebook.github.io/react)
- [Flux](https://facebook.github.io/flux)
- [GHCJS](https://github.com/ghcjs/ghcjs)
- [react-flux](https://hackage.haskell.org/package/react-flux)

---

class: center, middle

# React

---

# React

- разработан Facebook
- популярен
- достаточно производителен
- близок по духу к чистому ФП

---

class: center, middle

# Flux

---

# Flux

- решает проблему **хранения состояния**
- предоставляет видение **архитектуры** всего приложения
- представляет собой не реализацию, но **описание идеи**

---

# <small>Официальная диаграмма</small>

<img src="media/flux-diagram.png" style="width: 100%;"/>

---

class: center, middle

# GHCJS

---

# GHCJS

- долго компилируется
- долго разворачивает TH
- генерирует очень большие "бинарники" <super>*</super>

---

# <small>...но при этом</small> GHCJS

- делает возможным переиспользование кода <small>(валидация, роутинг, транспортный слой)</small>
- умеет **concurreny в браузере**!
- в конце концов, это **Haskell**!

---

class: center, middle

# react-flux

---

# react-flux

- предоставляет "обёртку" для React
- реализует архитектуру Flux
- позволяет
  - создавать React-компоненты, пригодные для использования из JS
  - использовать сторонние компоненты, написанные на JS

---

# Store

```haskell
class Typeable storeData =>
      StoreData storeData where

    type StoreAction storeData

    transform
      :: StoreAction storeData
      -> storeData
      -> IO storeData
```

---

# Элемент

```haskell
newtype ReactElementM eventHandler a

page_ :: h -> s -> props -> ReactElementM h ()
page_ handler _ =
  div_ [ "className" $= "container" ] $ do
    span_ "Hello"
    "World!"
    div_ $
      button_ [ on "onClick" handler ] $
        elemText "Press Me"
```

---

```haskell
data X = X ...
data XAction = ...

instance StoreData X where
  type StoreAction X = XAction
  ...

main = reactRender "root" pageView ()

pageView :: ReactView ()
pageView =
  let store    = mkStore X
      dispatch =
        [SomeStoreAction store XAction]
  in defineControllerView "page" store $
    page_ dispatch
```

---

# Views

```haskell
type ViewEventHandler = [SomeStoreAction]

defineControllerView
  :: JSString
  -> ReactStore storeData
  -> (storeData -> props
      -> ReactElementM ViewEventHandler ())
  -> ReactView props
```

---

# Views

```haskell
type StatefulViewEventHandler state =
  state -> ([SomeStoreAction], Maybe state)

defineStatefulView
  :: JSString
  -> state
  -> (state -> props
      -> ReactElementM
         (StatefulViewEventHandler state)
         ())
  -> ReactView props
```

---

# Views

```haskell
defineView
  :: JSString
  -> (props
      -> ReactElementM ViewEventHandler ())
  -> ReactView props
```

---

class: center, middle

# Demo time!

([src](https://github.com/astynax/refluhs)/[result](https://astynax.github.io/refluhs/))

---

class: center, middle

# Fin<br /><small>Спасибо за внимание!</small>
