
# Serjey Company Swift Style Guide

## Goals

Следование этому руководству по стилю должно:

-   Облегчить чтение и понимание незнакомого кода.
   
-   Сделать код более поддерживаемым.
    
-   Уменьшить количество ошибок у программиста.
    
-   Снизить когнитивную нагрузку во время написания кода.
    
-   Сфокусировать обсуждения на логике кода, а не на стиле.
    

**Обратите внимание, что краткость кода не является основной целью.** 

Код должен становиться более лаконичным, только если другие его качества (такие как читабельность, простота и ясность) остаются на том же уровне или улучшаются.

> ⚠️ **Важно!** 
> Иными словами – лучше более полно описанный код, через выковыристый, сложный, но короткий.


## Содержание

-   Именование
    
    -   Общие понятия
        
    -   Булевы переменные
        
    -   Аббревиатуры
        
    -   Функции
        
    -   Делегаты
        
    -   Дженерики
        
    -   Опционалы
        
-   Стиль
    
    -   Префиксы классов
        
    -   Использование выведенного контекста типа
        
    -   Использование `self`
        
    -   Запятая в конце
        
    -   Кортежи
        
    -   Избегайте сложной логики
        
    -   `Guard` в начале
        
    -   `TODO`, `NOTE` и `MARK`
        
    -   Тип `Void`
        
    -   Константы
        
    -   Использование опционалов
        
    -   Неизменяемые значения
        
    -   Пустые массивы и словари
        
    -   Синтаксический сахар
        
    -   Неожиданные ситуации
        
-   Контроль доступа
    
-   Организация файлов
    
    -   Импорты
        
    -   Пробелы
        
    -   Соответствие протоколам
        
    -   Неиспользуемый код
        
    -   Структура файла
    

## Именование

Осмысленные и последовательные имена облегчают чтение и понимание кода. Используйте соглашения об именах Swift, описанные в [API Design Guidelines](https://swift.org/documentation/api-design-guidelines/).

### Общее

-   **Язык**
    

Используйте американское написание английских слов, чтобы соответствовать API Apple.

```swift
 // ⛔️ WRONG
let colour = "red"
 // ✅ RIGHT
let color = "red"
```

-   **Используйте CamelCase** (Следующее слово должны быть с большой буквы)

```swift
// ⛔️ WRONG
protocol Worldchampion {
    // ...
}
// ✅ RIGHT
protocol WorldChampion {
    // ...
}

// ⛔️ WRONG
enum Categorykind { }
// ✅ RIGHT
enum CategoryKind { }

// ⛔️ WRONG
let iswoman: Bool
// ✅ RIGHT
let isWoman: Bool
```

-   **Давайте более осмысленные имена**
    
```swift
// ⛔️ WRONG
let title: String
let secondSubtitle: String

// ✅ RIGHT
let submitButtonTitle: String
let navigationBarSubtitle: UIButton
```
    
-   **Имена должны быть написаны так, чтобы их общая часть была первой, а специфичная — последней.** 
    
```swift
// ⛔️ WRONG
let rightTitlePading: CGFloat
let leftTitlePading: CGFloat
let bodyRightPading: CGFloat
let bodyLeftPading: CGFloat
    
// ✅ RIGHT
let titlePadingRight: CGFloat
let titlePadingLeft: CGFloat
let bodyPadingRight: CGFloat
let bodyPadingLeft: CGFloat
```

-   **Не используйте аббревиатуры, сокращенные имена или односимвольные имена.**
    
```swift
// ⛔️ WRONG
var arr = ["iOS", "Android"]
var nums = [1, 2, 3]

// ✅ RIGHT
var supportedPlatforms = ["iOS", "Android"]
var pageNumbers = [1, 2, 3]

// ⛔️ WRONG
func load() { ... }
func calc(a: Int, b: Int) { ... }

// ✅ RIGHT
func loadUserProfile() { ... }
func calculateSum(first: Int, second: Int) { ... }
```

### Булевы переменные

Называйте булевы переменные как `isSpaceship`, `hasSpacesuit` и т.д.
**Это сразу даёт понять, что это булевы значения, а не другой тип.**

```swift
// ⛔️ WRONG
var user = true
var status = false
var flag = true
var color = false
var animation = true

// ✅ RIGHT
var isAdmin = false
var hasUnreadMessages = true
var isVisible = false
var canEdit = true
var isEnabled = false
var isFinished = true
var isDarkMode = false
var hasInternetConnection = true
var isButtonEnabled = false
```

### Функции

**Функции обработки событий должны называться как предложения в прошедшем времени.**Субъект можно опустить, если он не нужен для ясности.

```swift
// ⛔️ WRONG
func do() { ... }
func process() { ... }
func handle() { ... }
func update() { ... }
func calculate() { ... }

// ✅ RIGHT
func fetchUserData() { ... }
func loadImage() { ... }
func getCurrentTime() { ... }
func readFile() { ... }

// ✅ RIGHT
func isValidEmail() -> Bool { ... }
func canSendRequest() -> Bool { ... }
func hasNetworkConnection() -> Bool { ... }
func shouldShowAlert() -> Bool { ... }

// ⛔️ WRONG
func add(a: Int, b: Int) { ... }
func set(n: String) { ... }

// ✅ RIGHT
func sum(firstNumber: Int, secondNumber: Int) { ... }
func updateUserName(to newName: String) { ... }
func findUser(by id: String) { ... }
```

> ⚠️ **Главное правило:** 
> 
> Функция должна отвечать на вопрос **"что делает?"** одним предложением. Например:
>   -  `loadUserProfile` — загружает профиль
>   -  `isEmailValid` — проверяет email
>   -  `saveToDatabase` — сохраняет в базу


### Дженерики

Параметры обобщенных типов должны быть описательными именами в PascalCase. Если имя типа не имеет значимой связи или роли, используйте традиционную заглавную букву, например `T`, `U` или `V`.

```swift
// ⛔️ WRONG
class List<T> {
    func add(_ item: T) { ... }
}

// ✅ RIGHT
class List<Element> {
    func add(_ item: Element) { ... }
}

// ⛔️ WRONG
func convert<A, B>(_ input: A) -> B { ... }

// ✅ RIGHT
func convert<Input, Output>(_ input: Input) -> Output { ... }

// ⛔️ WRONG
class Response<T> {
    var data: T
    var error: String?
}

// ✅ RIGHT
class Response<Data> {
    var data: Data
    var error: String?
}
```
### Опционалы

При именовании опциональных переменных и свойств избегайте названий вроде `optionalString` или `maybeView`, поскольку их опциональность уже указана в объявлении типа.

Для опциональной привязки (optional binding) используйте затенение (shadowing) оригинального имени, если это уместно, вместо имен вроде `unwrappedView` или `actualLabel`.


```swift
// ⛔️ WRONG
var optionalSubview: UIView?
var volume: Double?
if let unwrappedSubview = optionalSubview {
    if let realVolume = volume {
        // do something with unwrappedSubview and realVolume
    }
}

// ✅ RIGHT
var subview: UIView?
var volume: Double?

// later on...
if let subview, let volume {
    // do something with unwrapped subview and volume
}
```
## Стиль

### Префиксы классов

Типы Swift автоматически попадают в пространство имён модуля, который их содержит, поэтому не следует добавлять префикс класса, например SV (Serjey Company). Если два имени из разных модулей конфликтуют, их можно различить, указав имя модуля перед именем типа. Однако указывайте имя модуля только тогда, когда есть вероятность путаницы, что бывает редко.

text

import SomeModule
let myClass = MyModule.UsefulClass()

### Использование выведенного контекста типа

**Не указывайте типы там, где они легко выводятся.**

text

// ⛔️ WRONG
let host: Host = Host()
// ✅ RIGHT
let host = Host()

text

enum Direction {
 case left
 case right
}
func someDirection() -> Direction {
 // ⛔️ WRONG
 return Direction.left
 // ✅ RIGHT
 return .left
}

### Использование self

**Не используйте `self`, если это не необходимо для устранения неоднозначности или не требуется языком.**  [](https://github.com/nicklockwood/SwiftFormat/blob/master/Rules.md#redundantSelf)[https://img.shields.io/badge/SwiftFormat-redundantSelf-7B0051.svg](https://img.shields.io/badge/SwiftFormat-redundantSelf-7B0051.svg)

text

final class Listing {
 private let isFamilyFriendly: Bool
 private var capacity: Int
 init(capacity: Int, allowsPets: Bool) {
 // ⛔️ WRONG
 self.capacity = capacity
 self.isFamilyFriendly = !allowsPets // `self.` not required here
 // ✅ RIGHT
 self.capacity = capacity
 isFamilyFriendly = !allowsPets
 }
 private func increaseCapacity(by amount: Int) {
 // ⛔️ WRONG
 self.capacity += amount
 self.save()
 fetchData(completion: { [unowned self] in
 self.save()
 })
 // ✅ RIGHT
 capacity += amount
 save()
 fetchData(completion: { [unowned self] in
 save()
 })
 }
}

-   **Когда нужно усилить self, используйте конструкцию `guard let self = self`.**
    

text

// ⛔️ WRONG
func someFunc() {
 guard let sself = self else { return }
 ...
}
// ⛔️ WRONG
func someFunc() {
 guard let strongSelf = self else { return }
 ...
}
// ✅ RIGHT
func someFunc() {
 guard let self = self else { return }
 ...
}

### Запятая в конце

**Добавляйте запятую после последнего элемента многострочного массива.**[](https://github.com/nicklockwood/SwiftFormat/blob/master/Rules.md#trailingCommas)[https://img.shields.io/badge/SwiftFormat-trailingCommas-7B0051.svg](https://img.shields.io/badge/SwiftFormat-trailingCommas-7B0051.svg)

text

// ⛔️ WRONG
let rowContent = [
 listingUrgencyDatesRowContent(),
 listingUrgencyBookedRowContent(),
 listingUrgencyBookedShortRowContent()
]
// ✅ RIGHT
let rowContent = [
 listingUrgencyDatesRowContent(),
 listingUrgencyBookedRowContent(),
 listingUrgencyBookedShortRowContent(),
]

### Кортежи

-   **Именуйте элементы кортежей для большей ясности.** Если у вас больше трёх полей, вероятно, стоит использовать структуру.
    

text

// ⛔️ WRONG
func whatever() -> (Int, Int) {
 (4, 4)
}
let thing = whatever()
print(thing.0)
// ✅ RIGHT
func whatever() -> (x: Int, y: Int) {
 (x: 4, y: 4)
}
let thing = whatever()
print(thing.x)

### Избегайте сложной логики

-   **Избегайте выполнения какой-либо значительной или длительной работы в `init()`.** Не открывайте соединения с базой данных, не делайте сетевые запросы, не читайте большие объёмы данных с диска и т.п. Если объект должен выполнить такие действия перед тем, как стать готовым к использованию, создайте метод вроде `start()`.
    
-   **Выносите сложные наблюдатели свойств в отдельные методы.** Это уменьшает вложенность, отделяет побочные эффекты от объявления свойства и делает использование неявно передаваемых параметров, таких как `oldValue`, явным.
    
    text
    
    // ⛔️ WRONG
    class TextField {
     var text: String? {
     didSet {
     guard oldValue != text else {
     return
     }
     // Do a bunch of text-related side-effects.
     }
     }
    }
    // ✅ RIGHT
    class TextField {
     var text: String? {
     didSet { updateText(from: oldValue) }
     }
     private func updateText(from oldValue: String?) {
     guard oldValue != text else {
     return
     }
     // Do a bunch of text-related side-effects.
     }
    }
    
-   **Выносите сложные блоки обратного вызова в методы.** Это ограничивает сложность, связанную с weak-self в блоках, и уменьшает вложенность. Если вам нужно сослаться на self внутри метода, используйте `guard` для разворачивания self на время выполнения обратного вызова.
    
    text
    
    // ⛔️ WRONG
    class MyClass {
     func request(completion: () -> Void) {
     API.request() { [weak self] response in
     if let strongSelf = self {
     // Processing and side effects
     }
     completion()
     }
     }
    }
    // ✅ RIGHT
    class MyClass {
     func request(completion: () -> Void) {
     API.request() { [weak self] response in
     guard let self = self else { return }
     self.doSomething(self.property)
     completion()
     }
     }
     func doSomething(nonOptionalParameter: SomeClass) {
     // Processing and side effects
     }
    }
    
-   **Избегайте использования DispathQueue.main.async и DispathQueue.main.asyncAfter, если это действительно не необходимо.** Если используете, обязательно добавьте комментарий с объяснением причины.
    

text

// ⛔️ WRONG
func doSmth() {
 DispathQueue.main.asyncAfter(deadline: .now() + 1.0) {
 ...
 }
 DispathQueue.main.async {
 ...
 }
}
// ✅ RIGHT
func doSmth() {
 // NOTE: This is done because...
 DispathQueue.main.asyncAfter(deadline: .now() + 1.0) {
 ...
 }
 // NOTE: This is done because...
 DispathQueue.main.async {
 ...
 }
}

### Guard в начале

-   Инструкции Guard должны завершать выполнение каким-либо образом. **Как правило, это должна быть простая однострочная инструкция**, такая как `return`, `throw`, `break`, `continue` или `fatalError()`. Больших блоков кода следует избегать. Если для нескольких точек выхода требуется код очистки, рассмотрите возможность использования блока `defer`, чтобы избежать дублирования.
    
-   **Предпочитайте использовать `guard` в начале области видимости.**
    
    **Почему?**
    
    Код легче анализировать, когда все инструкции `guard` сгруппированы вместе вверху, а не перемешаны с бизнес-логикой.
    
-   **Всегда делайте новую строку после вызова super или инструкции guard.**
    

text

// ⛔️ WRONG
func doSmth() {
 guard let smth = smth else { return }
 doSmthMore()
}
func init(smth: Smth) {
 super.init()
 self.smth = smth
}
// ✅ RIGHT
func doSmth() {
 guard let smth = smth else { return }
 doSmthMore()
}
func init() {
 super.init()
 self.smth = smth
}

-   **При использовании `guard let` с несколькими проверками `let`, если они не помещаются в одну строку, делайте новую строку после первого `guard` и выравнивайте все остальные `let`.**
    

text

// ⛔️ WRONG
guard 
 let verySpecificNotEasyToPronounceCar = verySpecificNotEasyToPronounceCar, let verySpecificNotEasyToPronounceBike = verySpecificNotEasyToPronounceBike else { 
 return 
}
// ⛔️ WRONG
guard let verySpecificNotEasyToPronounceCar = verySpecificNotEasyToPronounceCar, 
 let verySpecificNotEasyToPronounceBike = verySpecificNotEasyToPronounceBike else { 
 return 
}
// ⛔️ WRONG
guard let verySpecificNotEasyToPronounceCar = verySpecificNotEasyToPronounceCar, 
 let verySpecificNotEasyToPronounceBike = verySpecificNotEasyToPronounceBike else { 
 return 
}
// ✅ RIGHT
guard let car = car, let bike = bike else { return }
// ✅ RIGHT
guard 
 let verySpecificNotEasyToPronounceCar = verySpecificNotEasyToPronounceCar, 
 let verySpecificNotEasyToPronounceBike = verySpecificNotEasyToPronounceBike else { 
 return 
}

### TODO, NOTE и MARK

-   **Добавляйте TODO следующим образом: `// TODO: ...`.** При создании TODO обязательно указывайте задачу.
    

text

// ⛔️ WRONG
func someFunction() {
 // TODO: find out why and fix
}
// ✅ RIGHT
func someFunction() {
 // TODO: find out why and fix in https://jira.serjey.company/browse/IOS-7777777
}

-   **Когда необходимо применить обходное решение (workaround), обязательно добавьте примечание `// NOTE: Workaround` с объяснением причины.** Если возможно, создайте TODO.
    

text

// ✅ RIGHT
func performWorkaround() {
 // NOTE: Workaround
 // Has to be done because ...
 // TODO: find out why and fix in https://jira.serjey.company/browse/IOS-7777777
}

-   **Для `MARK`, `TODO` и `NOTE` используйте два слеша.** Для файла, протокола/класса/перечисления/структуры и определения переменной/функции используйте три слеша.
    

text

// ⛔️ WRONG
/// TODO: ... 
...
// This class does this and that
class SomeClass: BaseClass {
}
// ✅ RIGHT
// TODO: ...
...
/// This class does this and that
class SomeClass: BaseClass {
}

### Тип Void

-   **При использовании дженериков с типом Void предпочитайте `Void` вместо `()`.** Также отдавайте предпочтение типу возврата `Void` вместо `()` в объявлениях замыканий. Если необходимо указать тип возврата `Void` в объявлении функции, используйте `Void` вместо `()`для улучшения читаемости.
    

text

// ⛔️ WRONG
typealias VoidResult = Result<(), SomeError>
// ✅ RIGHT
typealias VoidResult = Result<Void, SomeError>
// ⛔️ WRONG
func someFunc(completion: () -> ()) {
 ...
}
// ✅ RIGHT
func someFunc(completion: () -> Void) {
 ...
}

### Константы

**Используйте перечисления (enum) без кейсов для организации констант и функций в пространства имён.** Избегайте создания глобальных констант и функций вне пространства имён. Смело вкладывайте пространства имён друг в друга, если это добавляет ясности.

**Почему?**

Перечисления без кейсов отлично подходят в качестве пространств имён, потому что они не могут быть инстанциированы, что соответствует их назначению.

text

enum Environment {
 enum Earth {
 static let gravity = 9.8
 }
 enum Moon {
 static let gravity = 1.6
 }
}

### Использование опционалов

-   **Используйте опционалы только тогда, когда они имеют смысловое значение.**
    
-   **Проверяйте на nil вместо опциональной привязки, если вам не нужно использовать значение.**  [](https://github.com/realm/SwiftLint/blob/master/Rules.md#unused-optional-binding)[https://img.shields.io/badge/SwiftLint-unused_optional_binding-007A87.svg](https://img.shields.io/badge/SwiftLint-unused_optional_binding-007A87.svg)
    
    **Почему?**
    
    Проверка на nil сразу проясняет намерение инструкции. Опциональная привязка менее явна.
    
    text
    
    var thing: Thing?
    // ⛔️ WRONG
    if let _ = thing {
     doThing()
    }
    // ✅ RIGHT
    if thing != nil {
     doThing()
    }
    

### Неявный flatMap

**Не используйте flatMap для побочных эффектов. flatMap должен возвращать какие-либо объекты.**

text

 // WRONG
 struct ViewModel {
 let subtitle: String?
 }
 ...
 viewModel.subtitle.flatMap(subtitleAtom.configure)
 // RIGHT
 if let subtitle = viewModel.subtitle {
 subtitleAtom.configure(with: subtitle)
 }

### Предпочитайте неизменяемые значения

**Отдавайте предпочтение неизменяемым значениям везде, где это возможно.** Используйте `map` и `compactMap` вместо добавления элементов в новую коллекцию. Используйте `filter`вместо удаления элементов из изменяемой коллекции.

**Почему?**  
Изменяемые переменные увеличивают сложность, поэтому старайтесь ограничивать область их применения.

text

// ⛔️ WRONG
var results = [SomeType]()
for element in input {
 let result = transform(element)
 results.append(result)
}
// ✅ RIGHT
let results = input.map { transform($0) }

text

// ⛔️ WRONG
var results = [SomeType]()
for element in input {
 if let result = transformThatReturnsAnOptional(element) {
 results.append(result)
 }
}
// ✅ RIGHT
let results = input.compactMap { transformThatReturnsAnOptional($0) }

### Пустые массивы и словари

Для пустых массивов и словарей используйте аннотацию типа. (Для массива или словаря, присваиваемого большой многострочной константе, используйте аннотацию типа.)

**Предпочтительно:**

text

// ⛔️ WRONG
var names = [String]()
var lookup = [String: Int]()
// ✅ RIGHT
var names: [String] = []
var lookup: [String: Int] = [:]

### Синтаксический сахар

Предпочитайте сокращённые версии объявлений типов полному синтаксису дженериков.

**Предпочтительно:**

text

// ⛔️ WRONG
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
// ✅ RIGHT
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?

### Неожиданные ситуации

**Обрабатывайте неожиданное, но исправимое состояние с помощью метода `assert` в сочетании с соответствующим логированием в продакшене. Если неожиданное состояние неисправимо, предпочитайте метод `precondition` или `fatalError()`.** Это позволяет найти баланс между падением приложения и получением информации о неожиданных ситуациях в дикой природе. Отдавайте предпочтение `fatalError` перед `precondition` только тогда, когда сообщение об ошибке динамическое, поскольку метод `precondition` не передаст сообщение в отчёт о сбое. [](https://github.com/realm/SwiftLint/blob/master/Rules.md#fatal-error-message)[https://img.shields.io/badge/SwiftLint-fatal__error__message-007A87.svg](https://img.shields.io/badge/SwiftLint-fatal__error__message-007A87.svg)[](https://github.com/realm/SwiftLint/blob/master/Rules.md#force-cast)[https://img.shields.io/badge/SwiftLint-force__cast-007A87.svg](https://img.shields.io/badge/SwiftLint-force__cast-007A87.svg)[](https://github.com/realm/SwiftLint/blob/master/Rules.md#force-try)[https://img.shields.io/badge/SwiftLint-force__try-007A87.svg](https://img.shields.io/badge/SwiftLint-force__try-007A87.svg)  [](https://github.com/realm/SwiftLint/blob/master/Rules.md#force-unwrapping)[https://img.shields.io/badge/SwiftLint-force__unwrapping-007A87.svg](https://img.shields.io/badge/SwiftLint-force__unwrapping-007A87.svg)

text

func didSubmitText(_ text: String) {
 // It's unclear how this was called with an empty string; our custom text field shouldn't allow this.
 // This assert is useful for debugging but it's OK if we simply ignore this scenario in production.
 guard text.isEmpty else {
 assertionFailure("Unexpected empty string")
 return
 }
 // ...
}
func transformedItem(atIndex index: Int, from items: [Item]) -> Item {
 precondition(index >= 0 && index < items.count)
 // It's impossible to continue executing if the precondition has failed.
 // ...
}
func makeImage(name: String) -> UIImage {
 guard let image = UIImage(named: name, in: nil, compatibleWith: nil) else {
 fatalError("Image named \(name) couldn't be loaded.")
 // We want the error message so we know the name of the missing image.
 }
 return image
}

## Контроль доступа

-   **Уровень доступа должен быть максимально строгим.** Предпочитайте `public` вместо `open`и `private` вместо `fileprivate`, если вам не нужно именно такое поведение.
    
-   **Избегайте глобальных функций везде, где это возможно.** Предпочитайте методы внутри определений типов.
    
    text
    
    // ⛔️ WRONG
    func age(of person, bornAt timeInterval) -> Int {
     // ...
    }
    func jump(person: Person) {
     // ...
    }
    // ✅ RIGHT
    class Person {
     var bornAt: TimeInterval
     var age: Int {
     // ...
     }
     func jump() {
     // ...
     }
    }
    

## Организация файлов

### Импорты

**Минимизируйте импорты. Например, не импортируйте `UIKit`, если достаточно `Foundation`.**  
**Располагайте импорты модулей в алфавитном порядке вверху файла, через одну пустую строку после последней строки комментариев заголовка. Не добавляйте дополнительные пустые строки между операторами импорта.**  [](https://github.com/nicklockwood/SwiftFormat/blob/master/Rules.md#sortedImports)[https://img.shields.io/badge/SwiftFormat-sortedImports-7B0051.svg](https://img.shields.io/badge/SwiftFormat-sortedImports-7B0051.svg)

_**Почему?**_  
Стандартный метод организации помогает разработчикам быстрее определить, от каких модулей зависит файл.

text

// ⛔️ WRONG
//  Copyright © 2018 Airbnb. All rights reserved.
//
import DLSPrimitives
import Constellation
import Epoxy
import Foundation
// ✅ RIGHT
//  Copyright © 2018 Airbnb. All rights reserved.
//
import Constellation
import DLSPrimitives
import Epoxy
import Foundation

### Пробелы

**Ограничьте пустые вертикальные пробелы одной строкой.** Предпочитайте следующие правила форматирования вместо пробелов разной высоты для разделения файла на логические группы. [](https://github.com/realm/SwiftLint/blob/master/Rules.md#vertical-whitespace)[https://img.shields.io/badge/SwiftLint-vertical__whitespace-007A87.svg](https://img.shields.io/badge/SwiftLint-vertical__whitespace-007A87.svg)

**Файлы должны заканчиваться новой строкой.**  [](https://github.com/realm/SwiftLint/blob/master/Rules.md#trailing-newline)[https://img.shields.io/badge/SwiftLint-trailing__newline-007A87.svg](https://img.shields.io/badge/SwiftLint-trailing__newline-007A87.svg)

### Соответствие протоколам

В частности, при добавлении соответствия протоколу модели предпочтительно выносить методы протокола в отдельное расширение. Это сохраняет связанные методы сгруппированными вместе с протоколом и может упростить инструкции по добавлению протокола классу со связанными методами.

text

// ⛔️ WRONG
class MyViewController: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
 // all methods
}
// ✅ RIGHT
class MyViewController: UIViewController {
 // class stuff here
}
// MARK: - UITableViewDataSource
extension MyViewController: UITableViewDataSource {
 // table view data source methods
}
// MARK: - UIScrollViewDelegate
extension MyViewController: UIScrollViewDelegate {
 // scroll view delegate methods
}

### Неиспользуемый код

Неиспользуемый (мёртвый) код, включая шаблонный код Xcode и комментарии-заполнители, должен быть удалён. Исключение составляют случаи, когда ваше руководство или книга предлагают пользователю использовать закомментированный код.

Методы, к которым можно стремиться, но которые не имеют прямого отношения к руководству и чья реализация просто вызывает метод суперкласса, также должны быть удалены. Сюда входят любые пустые/неиспользуемые методы UIApplicationDelegate.

text

// ⛔️ WRONG
override func didReceiveMemoryWarning() {
 super.didReceiveMemoryWarning()
 // Dispose of any resources that can be recreated.
}
override func numberOfSections(in tableView: UITableView) -> Int {
 // #warning Incomplete implementation, return the number of sections
 return 1
}
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
 // #warning Incomplete implementation, return the number of rows
 return Database.contacts.count
}
// ✅ RIGHT
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
 return Database.contacts.count
}

### Структура файла

-   **Общий порядок свойств и методов в структуре данных должен быть примерно таким:**
    
    1.  public/internal свойства
        
    2.  private свойства
        
    3.  инициализаторы
        
    4.  deinit
        
    5.  public/internal методы
        
    6.  private методы
        
-   **При объявлении свойства или метода используйте следующий порядок:**
    
    1.  атрибут (@objc, @discardableResult и т.д.)
        
    2.  модификатор доступа (private, public и т.д.)
        
    3.  static (если не инстансный)
        
    4.  тип памяти (weak, unowned и т.д.)
        
    5.  имя
        
-   **Вложенные структуры данных должны объявляться в расширениях.**
    
