

# `UserDefaults` и `AppStorage` : От основ до практики

## Введение


В разработке под устройства от `Apple` часто требуется хранить мелочи: тему приложения (тёмная или светлая), последний открытый `ID пользователя`, настройки уведомлений или флаг "первого запуска".

Флагом принято называть – булев тип

Булевым типом принято нызывать логический тип данных

Логический тип данных это `: Bool (true/false)`

Для этих задач традиционно использовался `UserDefaults`. С приходом `SwiftUI` появился новый механизм — `@AppStorage`, который делает работу с хранилищем ещё проще.

**`@AppStorage` это просто удобная обёртка над `UserDefaults`, по факту – это всё одно и тоже** – возможность сохранить данные в память телефона.

Вы будите таааак рады, но `UserDefaults` – это оооочень просто

## Важное ограничение: локальность

Стоит отдельно отметить, что `UserDefaults`  позволяет хранить данные **только на конкретном устройстве пользователя**, а не на всём его аккаунте, как вы могли бы ошибочно подумать.

**Пример:**
Если пользователь выберет в настройках тёмную тему и вы сохраните флаг `isDarkTheme` в `UserDefaults`, то **никакое другое устройство (`iPad` или не дай бог `Android`) этого пользователя не узнает об изменении.** Для них тема останется светлой, пока не изменятся настройки на каждом устройстве отдельно.

## Где не стоит использовать `UserDefaults`

Если перед вами стоит задача сохранить товары, которые пользователь добавил в корзину — `UserDefaults` худшее место для этого.

Пользователь ожидает, что на всех его устройствах, подключённых к единому аккаунту (`iPhone`, `iPad`, `iGay`), корзина будет идентична.

Для подобной задачи необходимо использовать **сервер**, в качестве единого источника информации.

**Хранилище на устройстве (`UserDefaults`) никак не синхронизируется между девайсами автоматически.**

## Идеальный кейс для `UserDefaults`

Идеальный случай для применения `UserDefaults` — показ приветственного экрана (онбординга) при первом входе в приложение.

**Как это работает:**
1. При первом запуске флага `hasSeenOnboarding` нет в хранилище.
2. Показываем онбординг.
3. Сохраняем `hasSeenOnboarding = true`.
4. При следующих запусках приложение видит флаг и сразу переходит к главному экрану, не показывая вступление.

Данные не должны синхронизироваться между устройствами: даже если пользователь установил приложение на `iPad` после `iPhone`, не страшно, а быть может даже и нужно показать ему онбординг заново.

## Что такое `UserDefaults` и `AppStorage`

### `UserDefaults` 
Система хранения пар "ключ-значение". 
Хранит простые типы: `Bool`, `Int`, `String`, `Array`, `Dictionary`.

`UserDefaults` очень похож на словарь. **В нём как и в словаре ключевую роль играет ключ, по которому мы сохраняем данные, а в последующем можем получить.**

**Сохранение:**
```swift
/// Стандартное хранилище
let defaults = UserDefaults.standard

/// По ключу username мы сохранили Джона
defaults.set("John", forKey: "username")

/// По ключу hasSeenOnboarding мы сохранили true
defaults.set(true, forKey: "hasSeenOnboarding")
```
**Чтение:**
```swift
/// Пытаемся получить значение по ключу username
let name = defaults.string(forKey: "username") ?? "Гость"

/// Пытаемся получить значение по ключу username
let hasSeenOnboarding = defaults.bool(forKey: "hasSeenOnboarding") ?? false
```

## `AppStorage` (`View`)

`AppStorage` это `Property Wrapper` от `SwiftUI`. 

Позволяет привязать переменную напрямую к `UserDefaults` и **автоматически обновляет интерфейс** при изменении значения.

**Синтаксис:**
```swift
@AppStorage("username") var username: String = "Гость"
@AppStorage("isDarkMode") var isDarkMode: Bool = false
@AppStorage("tapCount") var tapCount: Int = 0
```

`@AppStorage` это тот же `@State` но помимо прочего позволяет сохранить данные на устройстве пользователя. Это очень удобно, если на экране у нас есть:

 - Поле ввода 
 - Тогл (Переключатель `true`/`false`)
 - И так далее...


### Пример про `AppStorage`

`@AppStorage` используется во `View`, для удобства хранения примитивных типов данных, например `String` для Поля ввода.

```swift
struct ContentView: View {
    /// За счёт @AppStorage – поле userName будет сохранено в памяти телефона
    @AppStorage("userName") var userName: String = ""
    
    var body: some View {
        /// При запуске приложения в поле userName 
        /// будет подставлено последнее сохранённое значение по ключу "userName" 
        TextField("Введите имя", text: $userName)
            .textFieldStyle(.roundedBorder)
            .padding()
    }
}
```


## Ключевые преимущества `AppStorage`

1. **Реактивность без лишнего кода –** При изменении переменной, `SwiftUI` перерисовывает экран автоматически.
2. **Лаконичность –** Не нужно писать `UserDefaults.standard.set(...)` и заботиться о типах.
3. **Безопасность типов на уровне кода**

## `UserDefaults`

Как и было сказано в самом начале – `AppStorage` это удобная обёртка над `UserDefaults` для использования во `View`.

**`UserDefaults` это и есть сама основа.** 

Дело в том, что в `AppStorage` мы можем сохранить лишь примитивные значения, к тому же только те, которые показаны во `View` и с которыми взаимодействует пользователь.

Но есть много случаев, когда нам нужно хранить сложные данные (Созданные нами структуры, например) и которые вовсе не представлены во `View` – в таком случае нам на помощь приходит `UserDefaults`

Выше мы уже разбирали пример работы, но давайте повторим – 

```swift
/// По ключу username мы сохранили Джона
UserDefaults.standard.set("John", forKey: "username")

/// Пытаемся получить значение по ключу username
let name = UserDefaults.standard.string(forKey: "username") ?? "Гость"
```

**Всё.**

**Что значит `.standard` ?** 

Это стандартное хранилище, о котором за нас уже позаботились в `Apple`, просто используйте его, когда перед вами встанет задача создать своё собственное – к тому времени вы уже сделаете это самостоятельно.

Далее мы рассмотрим пример, в котором сохраним не примитивный тип данных, а кое что посложнее.

## Лучшие практики

**1. Не храните большие данные**
`UserDefaults` не предназначен для изображений, больших `JSON` или списков из тысяч элементов. 

Используйте `FileManager` или `SwiftData`

**2. Избегайте "магических строк"**

Плохо, так как если вдруг вам придётся изменять ключ (`usernmae`), а рано или поздно – придётся так как в нём есть ошибка – вам потребуется изменить его во всех места, где вы на него ориентируетесь и вы непременно ошибётесь.
```swift
@AppStorage("usernmae") var username: String = ""
```

Хорошо:

```swift
struct UserDefaultsKeys {
    static let username = "username"
}

/// Ориентируемся на static let username а не на "username"
@AppStorage(UserDefaultsKeys.username) var username: String = ""
```

Сейчас вы указали ключ – в одном месте и если вам потребуется его изменить – это не проблема, достаточно пометь значение внутри `static let username` , в одно месте, а не разрабросано по всему прилоежнию.

Помимо этого `UserDefaultKeys.username` привносит больше информации, чем просто "username" – мы сразу понимаем, что речь идёт о `UserDefaults`

**3. Всегда указывайте значение по умолчанию**
```swift
@AppStorage("isFirstLaunch") var isFirstLaunch: Bool = true
```

## Разбор на практических примерах

### Пример 1: Экран входа (`Bool`)

Допустим, мы хотим запомнить, что пользователь залогинен.

```swift
struct LoginView: View {
    /// Данное поле будет сохранено в память телефона
    @AppStorage("isLoggedIn") var isLoggedIn: Bool = false
    
    var body: some View {
        VStack {
            if isLoggedIn {
                Text("Добро пожаловать!")
                Button("Выйти") {
                    isLoggedIn = false
                }
            } else {
                Button("Войти") {
                    isLoggedIn = true
                }
            }
        }
    }
}
```

Здесь нет лишнего кода. 

При нажатии на кнопку "Войти" переменная становится `true`, и `SwiftUI` моментально переключает интерфейс на приветствие.

### Пример 2: Счётчик (`Integer`)

```swift
@AppStorage("tapCount") var tapCount: Int = 0

Button("Нажал: \(tapCount)") {
    tapCount += 1
}
```
**Даже если вы закроете и перезапустите приложение, счётчик сохранит последнее значение.** 

Это происходит потому, что `AppStorage` синхронизируется с дискетой при изменении данных.

### Пример 3: Работа с `UserDefault` а не с `AppStorage`

**Использование `AppStorage`  привязывает нас к `SwiftUI`, но что если вам нужно сохранить, а в последствии и получить определённые данные без взаимодействия со `View` ?** 

Это можно сделать следующим образом:

**Сохранение:**  
```swift
let defaults = UserDefaults.standard  

/// Сохраняем значения в память
defaults.set("Анна", forKey: "userName")  
defaults.set(25, forKey: "userAge")  
defaults.set(true, forKey: "isPremiumUser")  
defaults.set(3.14, forKey: "piValue")
```
**Чтение:**  
```swift
/// Получаем значения из памяти
let name = defaults.string(forKey: "userName") ?? "Гость"  
let age = defaults.integer(forKey: "userAge")  
let isPremium = defaults.bool(forKey: "isPremiumUser")  
let pi = defaults.double(forKey: "piValue")

print("Имя: \(name), Возраст: \(age), Премиум: \(isPremium)")
```

### Пример 4: Сохранение и загрузка массива

Сохранение массива строк:  
```swift
let favoriteColors = ["Красный", "Синий", "Зелёный"]  
UserDefaults.standard.set(favoriteColors, forKey: "favoriteColors")
```
Чтение массива:  
```swift
let savedColors = UserDefaults.standard.array(forKey: "favoriteColors") as? [String] ?? []  
print(savedColors) // ["Красный", "Синий", "Зелёный"]
```
Сохранение массива чисел:  
```swift
let scores = [100, 250, 75, 300]  
UserDefaults.standard.set(scores, forKey: "gameScores")
```
Чтение:  
```swift
let savedScores = UserDefaults.standard.array(forKey: "gameScores") as? [Int] ?? []  
print(savedScores) // [100, 250, 75, 300]
```

Как видите – мы можем совершенно элементарно сохранять в том числе и массивы данных. 

## Пример 4: Сохранение словаря (`Dictionary`)

**Сохранение:**  
```swift
let userSettings = [  
     "soundEnabled": true,  
     "musicVolume": 0.8,  
     "difficulty": "hard"  
] as [String : Any]

UserDefaults.standard.set(userSettings, forKey: "userSettings")
```
**Чтение:**  
```swift
let settings = UserDefaults.standard.dictionary(forKey: "userSettings") ?? [:]  
let soundOn = settings["soundEnabled"] as? Bool ?? false  
let volume = settings["musicVolume"] as? Double ?? 0.5  
let difficulty = settings["difficulty"] as? String ?? "medium"

print("Звук: \(soundOn), Громкость: \(volume), Сложность: \(difficulty)")
```

# Сохранение `Codable` структур в `UserDefaults`

## Пример 1: Базовая структура `Codable`

Представим, что у нас есть профиль пользователя и мы собираемся его прихранить.

**Во первых – нам необходимо подписать его под протокол `Codable`**

```swift
struct UserProfile: Codable {
    let id: Int
    let name: String
    let email: String
    let age: Int
    let isPremium: Bool
}
```
**Создать экземпляр:**

```swift
let user = UserProfile(
    id: 10,
    name: "Виктория Михайловна",
    email: "potatokartoha@iCLoud.com",
    age: 19,
    isPremium: true
)
```
**Сохранение в `UserDefaults`:**

```swift
/// Создадим Енкодер, задача которого будет енкодировать
let encoder = JSONEncoder()
/// У Енкодера, есть функция encode – которая енкодирует
if let encoded = try? encoder.encode(user) {
    UserDefaults.standard.set(encoded, forKey: "currentUser")
}
```
**Выгрузка из `UserDefaults`:**

```swift
/// Создадим Декодер, задача которого будет декодировать
let decoder = JSONDecoder()
/// У Декодера, есть функция decode – которая декодирует
if let savedData = UserDefaults.standard.data(forKey: "currentUser"),
   let loadedUser = try? decoder.decode(UserProfile.self, from: savedData) {
    print("Email: \(loadedUser.email)")
} else {
    print("Пользователь не найден")
}
```

Всё – ничего сложного, главнео, что бы структура была `Codable`

## Удобное расширение для `UserDefaults`

Создаём расширение, чтобы не писать кодирование/декодирование каждый раз:

```swift
extension UserDefaults {
    func saveCodable<T: Codable>(_ value: T, forKey key: String) {
        let encoder = JSONEncoder()
        if let encoded = try? encoder.encode(value) {
            set(encoded, forKey: key)
        }
    }
    
    func loadCodable<T: Codable>(forKey key: String) -> T? {
        guard let data = data(forKey: key) else { return nil }
        let decoder = JSONDecoder()
        return try? decoder.decode(T.self, from: data)
    }
}
```

Мы расширили функционал `UserDefaults`, теперь нам доступны функции `saveCodable...` и `loadCodable...`

**Использование расширения:**
```swift
struct Product: Codable {
    let name: String
    let price: Double
    let inStock: Bool
}

let product = Product(name: "iPhone", price: 999.99, inStock: true)

// Сохранение
UserDefaults.standard.saveCodable(product, forKey: "lastViewedProduct")

// Загрузка
let loadedProduct: Product? = UserDefaults.standard.loadCodable(forKey: "lastViewedProduct")
if let product = loadedProduct {
    print("Товар: \(product.name) за \(product.price)₽")
}
```

## Обновление существующей структуры

 1. Загружаем 
 2. Меняем поля 
 3. **Сохраняем обратно:**

Не стоит забывать, что после того как вы получили данные из `UserDefaults` и обновили значения – **нужно обязательно вновь сохранить эти данные в хранилище**, иначе в  `UserDefaults` так и останутся старые данные.

```swift
struct Counter: Codable {
    var value: Int
    let lastUpdated: Date
}

// Загружаем
var currentCounter: Counter? = UserDefaults.standard.loadCodable(forKey: "counter")

// Проверим, что по данному ключу вообще есть счётчик 
if var counter = currentCounter {
    // Изменяем значение
    counter.value += 1
    // Сохраняем обратно
    UserDefaults.standard.saveCodable(counter, forKey: "counter")
    print("Новое значение: \(counter.value)")
} else {
    // Создаём новый счётчик
    let newCounter = Counter(value: 1, lastUpdated: Date())
    UserDefaults.standard.saveCodable(newCounter, forKey: "counter")
    print("Создан новый счётчик")
}
```


## Важные замечания

Все поля структуры должны быть `Codable`. Для стандартных типов (`String`, `Int`, `Date`, `URL`) это работает из коробки.

Даты требуют особого подхода. Если у вас есть поле типа `Date`, `JSONEncoder` по умолчанию сохранит его как строку в формате `ISO 8601`. Это нормально работает.

Обработка ошибок. В примерах используется `try?`, который игнорирует ошибки. В реальном проекте лучше использовать `do-try-catch`:

```swift
do {
    let encoder = JSONEncoder()
    let encoded = try encoder.encode(user)
    UserDefaults.standard.set(encoded, forKey: "currentUser")
} catch {
    print("Ошибка кодирования: \(error.localizedDescription)")
}
```
`UserDefaults` не предназначен для хранения больших объёмов данных. Если структура содержит много полей или вы храните сотни объектов — рассмотрите `SwiftData` или `FileManager`.

`DateEncodingStrategy`. Если нужно изменить формат даты:

```swift
let encoder = JSONEncoder()
encoder.dateEncodingStrategy = .iso8601
encoder.dateDecodingStrategy = .iso8601
```

## Заключение

`UserDefaults` — это надёжная классика `iOS-разработки`. `AppStorage` — это элегантная обёртка от `SwiftUI`, делающая код чище и реактивнее. 

Теперь, когда вы знаете их сильные и слабые стороны, вы сможете выбрать правильный инструмент для хранения настроек в своём следующем приложении. Главное правило остаётся неизменным: не пытайтесь засунуть тяжёлые данные в легковесное хранилище.
