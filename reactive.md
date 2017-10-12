
class: center, middle

# Reactive Programming

---

# План

- Reactive Programming, как **парадигма**
- **Reactive Extensions** (RX)
- **Functional Reactive Programming** (FRP)

---

# RP, как парадигма

- RP ∈ **Data Flow programming**
- В RP программа - направленный **граф** потоков данных
  - вычисление производится в узлах
  - результаты вычислений передаются по рёбрам
- Реализации RP бывают
  - **pull** (что-то вроде Iterator pattern)
  - **push** (что-то вроде Observer pattern)
  - **гибридные**

---

# RX

Концепция = **Observable** + **"операторы"**:

- создающие: `Empty`, `Range`, `Timer`...
- изменяющие: `Buffer`, `Map`, `Scan`...
- фильтрующие: `Distinct`, `Skip`, `Filter`...
- комбинирующие: `Merge`, `Switch`, `Zip`...
- а также `TakeWhile`, `SkipUntil` и проч.

"Запускаются" программы с помощью **Schedulers**.

---

# Пример программы:

```javascript
sourceObservable
  .map((e) => e * 2)
  .reduce((n,e) => n + e, 0)
  .subscribe((e) => {
    console.log(e); //e = 2 + 4 + 6 + 8 + 10
  }, (error) => {
    console.error(error); //handle errors
  }, () => {
    console.log('done');
  })
```

---

# FRP

В FRP

- есть `map`, `reduce`, `filter`
- нет **изменяемого состояния** (переменных)
- нет **побочных эффектов**

---

# Пример программы

```elm
clicks : Signal Int
clicks = Mouse.clicks
  |> Signal.map (\_ -> 1)
  |> Signal.foldp (+) 0

data : Signal (List Pet)
data = Http.get "/api/data.json"
  |> Signal.map
    (Result.map
      (\res -> decode res)
      (\_   -> [])
    )
```

---

class: center, middle

# Конец
