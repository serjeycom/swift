
# Структуры в языке `Swift`

## Вступление 🚪

Сегодня расскажу, но только по секрету, об одной из основополагающих тем в `Swift` — **структурах**. 

Без понятий о `структуре` вы не напишите ни одно серьёзное приложение. 

Представь, что структура — это как формы для выпечки: вы создаёте форму один раз, а потом можете печь сколько угодно печенюшек 🍪 с одинаковой формой, но разной начинкой.

При этом важно понимать, что саму форму для выпечки – есть нельзя!  

## Что такое структура? 🤔

`Структура` — это `пользовательский тип данных`, который **позволяет объединять разные значения и функции в одну логическую единицу**. 

По сути, это чертёж 🏗️, по которому создаются объекты.

Что значит `пользовательский тип данных` ? 

До этого мы работали с `примитивными типами данных`, которые уже создали за нас (`String`, `Bool`, `Int`, `Double` и так далее), но на то они и примитивные, что **полноценный объект мы можем описать только если сгруппируем их по смыслу в подходящую структуру данных**.

### Простейший пример
Представь, что мы пишем приложение для знакомств. 

Нам нужно описать человека:

```swift
struct User {
    var name: String
    var age: Int
    var city: String
}

// Создаём конкретного пользователя
let user = User(name: "Галина", age: 25, city: "Москва")
print("\(user.name), \(user.age) лет, \(user.city)") // Галина, 25 лет, Москва
```

Видишь? Мы создали структуру `User`, а потом сделали конкретную «Галина» 👩. Удобно, правда?


## Зачем нужны структуры? 🎯

### 1. Группировка данных 📦

Без структур нам пришлось бы хранить всё в разрозненных переменных:

```swift
// Как было бы без структур 😱
let userName1 = "Аня"
let userAge1 = 25
let userCity1 = "Москва"

let userName2 = "Петя"
let userAge2 = 30
let userCity2 = "Питер"

// Куча отдельных переменных... представляешь этот бардак? Ужас
```

Со структурами всё становится аккуратно:

```swift
struct User {
    let name: String
    let age: Int
    let city: String
}

let anna = User(name: "Анна", age: 25, city: "Москва")
let petya = User(name: "Петя", age: 30, city: "Питер")
```

Всё красиво и понятно ✨

### 2. Добавление поведения 🏃‍♂️

В структуры можно добавлять не только данные, но и функции:

```swift
struct User {
    var name: String
    var age: Int
    var city: String
    
    func sayHello() {
        print("Привет! Я \(name) из \(city) 👋")
    }
    
    mutating func haveBirthday() {
        age += 1
        print("🎉 У меня день рождения! Теперь мне \(age)!")
    }
}

var anna = User(name: "Аня", age: 25, city: "Москва")
anna.sayHello()     // Привет! Я Аня из Москва 👋
anna.haveBirthday() // 🎉 У меня день рождения! Теперь мне 26!
```

Что значит `mutating func` ? 

Эта та тема, к которой мы вернёмся в конце раздела, для её понимания, нам необходимо познакомиться с классами. 

### 3. Типобезопасность 🛡️

Компилятор следит, чтобы мы не перепутали типы:

```swift
struct User {
    var name: String
    var age: Int
}

// Компилятор не даст сделать так:
let user = User(name: 123, age: "двадцать") // ❌ Ошибка!
```


## Свойства в структурах 📋

### Хранимые свойства

Это самые обычные свойства, которые хранят значения:

```swift
struct Car {
    let brand: String       // константа (не изменить)
    var year: Int           // переменная (можно изменить)
    var isEngineOn = false  // значение по умолчанию
}

var myCar = Car(brand: "Tesla", year: 2023)
myCar.isEngineOn = true // ✅ можно поменять
// myCar.brand = "BMW"  // ❌ ошибка! brand - константа
```

### Вычисляемые свойства 🧮

Такие свойства не хранят значение, а вычисляют его на лету:

```swift
struct Circle {
    var radius: Double
    
    var diameter: Double {
        radius * 2
    }
    
    var area: Double {
        Double.pi * radius * radius
    }
    
    var description: String {
        "Круг радиусом \(radius) см, площадью \(String(format: "%.2f", area)) см² ⭕️"
    }
}

let circle = Circle(radius: 5)
print(circle.diameter)    // 10.0
print(circle.area)        // 78.53981633974483
print(circle.description) // Круг радиусом 5.0 см, площадью 78.54 см² ⭕️
```

Это своего рода функция, которая никогда не принимает входные параметры, а работает со значениями из полей. 

Это очень удобно, если нам нужна определённая логика, которую можно вычислить используя поля структуры. Например мы можем вычислить диаметр круга. (что мы и зделали чуть выше)


## Методы в структурах 🏊‍♂️

### Обычные методы

```swift
struct Temperature {
    var celsius: Double
    
    func toFahrenheit() -> Double {
        return celsius * 9 / 5 + 32
    }
    
    func toKelvin() -> Double {
        return celsius + 273.15
    }
}

let temp = Temperature(celsius: 25)
print("\(temp.celsius)°C = \(temp.toFahrenheit())°F") // 25°C = 77.0°F
print("\(temp.celsius)°C = \(temp.toKelvin())K")      // 25°C = 298.15K
```

### Методы, изменяющие структуру (mutating) 🔧

Вновь, вкратце, вернёмся к `mutating` — оно нужно, чтобы изменять свойства структуры внутри метода:

```swift
struct Counter {
    var value = 0
    
    mutating func increment() {
        value += 1
        print("⬆️ Счётчик увеличен: \(value)")
    }
    
    mutating func reset() {
        value = 0
        print("🔄 Счётчик сброшен")
    }
    
    mutating func add(_ amount: Int) {
        value += amount
        print("➕ Добавлено \(amount). Теперь: \(value)")
    }
}

var counter = Counter()
counter.increment() // ⬆️ Счётчик увеличен: 1
counter.add(5)      // ➕ Добавлено 5. Теперь: 6
counter.reset()     // 🔄 Счётчик сброшен
```

## Инициализаторы 🏗️

`Swift` сам создаёт инициализатор для структур, обычно отдельно прописывать их не нужно:

```swift
struct Book {
    var title: String
    var author: String
    var year: Int
    var pages: Int
}

// Инициализатор уже готов!
let book = Book(title: "Война и мир", 
                author: "Лев Толстой", 
                year: 1869, 
                pages: 1225)
                
print("📚 \(book.title) - \(book.author)") // "Лев Толстой – Война и мир"
```

`Инициализатор` это то, с помощью чего мы можем задать значение для всех требуемых полей. 

В примере выше, мы просто передаём реальные данные для каждого из обязательных полей, без какой-либо логики в `инициализаторе`.

### Кастомные инициализаторы

Можно создать свои `инициализаторы` для удобства:

```swift
struct Rectangle {
    var width: Double
    var height: Double
    
    // Кастомный инициализатор
    init(width: Double, height: Double) {
        self.width = width
        self.height = height
    }
    
    // Удобный инициализатор для квадрата
    init(side: Double) {
        self.width = side
        self.height = side
    }
    
    // Инициализатор с проверкой (? – говорит о том, что объект может и не получиться)
    init?(width: Double, height: Double) {
        guard width > 0 && height > 0 else {
            print("❌ Стороны должны быть положительными!")
            return nil
        }
        self.width = width
        self.height = height
    }
}

let rectangle = Rectangle(width: 10, height: 5)
let square = Rectangle(side: 7)
let valid = Rectangle(width: 5, height: 3)    // ✅ успешно
let invalid = Rectangle(width: -5, height: 3) // ❌ nil

print("📐 Прямоугольник: \(rectangle.area)") // 50
print("🔲 Квадрат: \(square.area)")          // 49
```

Как вы могли заметить, теперь `Rectangle` мы можем создать не только с помощью стандартного `инициализатора`, но и более удобными способами. 

Например, если мы хотим создать `квадрат` – нам нет необходимости указывать отдельно `высоту`, **достаточно лишь одной стороны.**
 
Поэтому мы переопределили наш инициализатор, что бы создать объект проще.

### Примеры из реальной жизни 🌍

```swift
// Структуры
struct User {
    var id: UUID
    var name: String
    var email: String
}

struct Product {
    var name: String
    var price: Double
    var quantity: Int
}

struct Point {
    var x: Double
    var y: Double
}
```

## Итоги 🎯

> "В Swift начинай со структуры, и только если нужна специфика класса — переходи на класс."

Это правило спасёт тебя от кучи проблем и сделает код производительнее. 

`Apple` сама следует ему: почти все типы в `Swif`t — структуры (`String`, `Array`, `Dictionary`, `Int`, `Bool` и т.д.)
