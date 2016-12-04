
class: center, middle

FPConf 2016

# **T**he **E**lm **A**rchitecture:<br><small>model, view & update your UI</small>

Алексей Пирогов

(aka [@alex_pir](https://twitter.com/alex_pir)/[astynax](https://github.com/astynax))

[Typeable.IO](http://typeable.io)

---

# О чем доклад

- об **Elm** (в меньшей степени)
- о **The Elm Architecture**, т.е. о том, как
    - моделировать приложение
    - отображать его
    - организовывать взаимодействие приложения с внешним миром

---

class: center, middle

# Памятка по синтаксису Elm

---

```elm
-- это комментарий

foo : String  -- это аннотация типа
foo = "Foo"

bar : Int
bar = 42

add : Int -> Int -> Int
add x y = x + y

add5 : Int -> Int
add5 = add 5

(+*) : Int -> Int -> Int
(+*) x y = (x + y) * y
-- 5 +* 2 == 14
```

---

```elm
-- параметрический полиморфизм (generics!)
identity : a -> a
identity x = x

always : a -> b -> a
always x _ = x

twice : (a -> a) -> a -> a
twice f x = f    (f x)
-- or
twice f x = f <|  f x  -- x |> f |> f
-- or
twice f   = f << f    -- f >> f

(<<) : (b -> c) -> (a -> b) -> a -> c
(<<) f g x = f (g x)
```

---

```elm
-- типы-суммы
type Bool = True | False
type Color = Red | Green | Blue

-- типы-произведения
type alias Coords = (Int, Int)
type Stroke = Stroke Int Color

-- "Сумма произведений"
type Figure
  = Circle Coords Int Stroke
  | Line   Coords Coords Stroke
```

---

```elm
-- тип-запись (record)
type User
  = User
  { username : String
  , age : Int
  }

-- анонимная запись
foo : { bar : String, baz : Int }
foo = { bar = "qux", baz = 42}

-- обновление записей
incrementAge : User -> User
incrementAge u = { u | age = u.age + 1 }
```

---

```elm
-- специальные типы

type alias Unit = ()

f : a -> ()
f _ = ()

type Never -- нет конструктора!
```

---

```elm
-- параметризованный (полиморфный) тип
type Maybe a
  = Just a
  | Nothing

-- Pattern Matching
withDefault : a -> Maybe a -> a
withDefault default x =
  case x of
    Just value -> value
    Nothing    -> default

-- встроенный полиморфный тип списка
polygon : List (Int, Int)
polygon = [(0, 0), (10, 0), (10, 10), (0, 10)]
```

---

class: center, middle

# The Elm Architecture

---

### beginnerProgram

```elm
import Html exposing (Html, beginnerProgram)

-- скелет простейшего приложения

main : Program Never model msg
main =
  beginnerProgram
  { model = ...  -- : model
  , view = ...   -- : model -> Html msg
  , update = ... -- : msg -> model -> model
  }
```

---

### Hello World

```elm
-- TEA Hello World
import Html exposing (text)

main =
  Html.beginnerProgram
  { model = ()
  , view = always (text "Hello World!")
  , update = always identity  -- o_O
  }
-- или
main = text "Hello world!"
```

---

### It's alive!

```elm
import Html exposing (text, div)
import Html.Events exposing (onClick)

main = Html.beginnerProgram
  { model=model, view=view, update=update }

model = True

view model =
  div [ onClick () ]
    [ text <| toString model ]

update = always not
```

---

### Сообщения

```elm
type Msg = Inc | Dec

model = 0

view model = div []
  [ button [ onClick Dec ] [ text "-" ]
  , text <| toString model
  , button [ onClick Inc ] [ text "+" ] ]

update msg model =
  case msg of
    Inc -> model + 1
    Dec -> model - 1
```

---

### Side Effects!

`beginnerProgram` не имеет side effects, а значит не может

- посылать запросы к серверу
- получать случайные числа
- работать с текущим временем
- ...

---

### Side Effects!

`program` имеет и может:

```elm
import Html exposing (program)

program :
  { init : (model, Cmd msg)
  , view : model -> Html msg
  , update : msg -> model -> (model, Cmd msg)
  , subscriptions : model -> Sub msg
  }
  -> Program Never model msg
```

---

### `Cmd` и `Sub` - контролируемые эффекты

```elm
type Cmd msg
```

Cmd - "сделай то-то и сообщи результат"

Пример:

```elm
import Random exposing (Generator, generate)

generate
  : (a -> msg) -> Generator a -> Cmd msg
```

---

### `Cmd` и `Sub` - контролируемые эффекты

```elm
type Sub msg
```

Sub - "сообщи мне, если то-то произойдет"

Пример:

```elm
import Time exposing (Time, every, second)

every : Time -> (Time -> msg) -> Sub msg

every (10 * second) (always ()) : Sub ()
```

---

## Как же строить большие приложения?

- каждый элемент имеет свои `view`, `model` и `update`
- `model` контейнера *содержит* `model` дочернего элемента
- `view` контейнера *вызывает* `view` элемента
- `update` контейнера *делегирует* обновление подмодели в `update` элемента

---

### Вмещение под'model

```elm
import Counter

type alias Model =
  { ...
  , counter : Counter.Model
  , ...
  }

model : Model
model =
  { ...
  , counter = Counter.model
  , ...
  }
```

---

### Вызов под'view

```elm
import Counter

type Msg = FromCounter Counter.Msg | -- ...

view model =
  ...
  Html.map
  -- : (a -> b) -> Html a -> Html b
    FromCounter
    -- : Counter.Msg -> Msg
    (Counter.view model.counter)
    -- : Html Counter.Msg
  ...
```

---

### Вызов под'update

```elm
import Counter

type Msg = FromCounter Counter.Msg | -- ...

update msg model =
  case msg of
    FromCounter counterMsg ->
      { model
        | counter =
            Counter.update
              counterMsg model.counter
      }
    ...
```

---

### `Cmd`/`Sub`

```elm
...
Cmd.map FromCounter cmdFromCounter
...
Sub.map FromCounter subFromCounter
...
```

---

### Всё это можно "подсластить" :)

```elm
main =
  beginnerProgram
  { model  = Counter.model  <>  Counter.model
  , view   = Counter.view   <|> Counter.view
  , update = Counter.update <&> Counter.update
  }
```

См. [github.com/astynax/tea-combine](https://github.com/astynax/tea-compose)

---

class: center, middle

# Спасибо за внимание
