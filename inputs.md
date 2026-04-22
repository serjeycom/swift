
# TextField и способы ввода информации в SwiftUI

## 1. Базовое текстовое поле

```swift
TextField("Введите имя", text: $name)
```
## 2. Связывание с состоянием

```swift
@State var username = ""

var body: some View {
     /// Поле ввода, в которое пользователь введёт своё имя
     TextField("Имя пользователя", text: $username)
     
     /// Покажем на экране, что вводит пользователь
     Text("Вы ввели: \(username)")
}
```

## 3. Стили текстовых полей

### .roundedBorder — закруглённая рамка

```swift
TextField("Введите текст", text: $text)
    .textFieldStyle(.roundedBorder)
```
### .plain — минимальный стиль
```swift
TextField("Без рамки", text: $text)
    .textFieldStyle(.plain)
```
### .automatic — автоматический (по умолчанию)
```swift
TextField("Авто", text: $text)
    .textFieldStyle(.automatic)
```
### Кастомный стиль через overlay

```swift
TextField("Кастомный", text: $text)
    .padding()
    .background(Color.gray.opacity(0.1))
    .cornerRadius(8)
    .overlay(
        RoundedRectangle(cornerRadius: 8)
            .stroke(Color.blue, lineWidth: 1)
    )
```
## 4. Типы клавиатур (`keyboardType`)

| Тип | Применение |
|-----|------------|
| `.default` | Обычная клавиатура |
| `.asciiCapable` | Только ASCII символы |
| `.numbersAndPunctuation` | Цифры и пунктуация |
| `.numberPad` | Только цифры (без точек) |
| `.phonePad` | Телефонный набор |
| `.emailAddress` | Email (с @ и .) |
| `.decimalPad` | Десятичные числа (с точкой) |
| `.url` | URL клавиатура |
| `.twitter` | Twitter (@ и #) |
| `.webSearch` | Поиск в интернете |

```swift
TextField("Email", text: $email)
    .keyboardType(.emailAddress)

TextField("Телефон", text: $phone)
    .keyboardType(.phonePad)

TextField("Сумма", text: $amount)
    .keyboardType(.decimalPad)
```
## 5. Стиль возврата (`returnKeyType`)

| Тип | Внешний вид |
|-----|-------------|
| `.default` | Возврат |
| `.done` | Готово |
| `.go` | Перейти |
| `.search` | Поиск |
| `.send` | Отправить |
| `.continue` | Продолжить |
| `.join` | Присоединиться |
| `.next` | Далее |
| `.previous` | Назад |
| `.emergencyCall` | Экстренный вызов |

```swift
TextField("Поиск", text: $search)
    .textFieldStyle(.roundedBorder)
    .onSubmit {
        performSearch()
    }

TextField("Логин", text: $login)
    .submitLabel(.next)

TextField("Пароль", text: $password)
    .submitLabel(.done)
```
## 6. `SecureField` (поле для пароля)

```swift
SecureField("Пароль", text: $password)
```
С иконкой показа/скрытия:

```swift
@State private var password = ""
@State private var isPasswordVisible = false

var body: some View {
HStack {
    if isPasswordVisible {
        TextField("Пароль", text: $password)
    } else {
        SecureField("Пароль", text: $password)
    }
    
    Button {
        isPasswordVisible.toggle()
    } label: {
        Image(systemName: isPasswordVisible ? "eye.slash" : "eye")
    }
}
.textFieldStyle(.roundedBorder)
}
```
## 7. `TextEditor` (многострочный ввод)

```swift
@State private var bio = ""

var body: some View {
     TextEditor(text: $bio)
         .frame(height: 100)
         .overlay(
             RoundedRectangle(cornerRadius: 8)
                 .stroke(Color.gray.opacity(0.3), lineWidth: 1)
         )
}
```
С плейсхолдером:

```swift
TextEditor(text: $bio)
    .frame(height: 100)
    .overlay(alignment: .topLeading) {
        if bio.isEmpty {
            Text("О себе")
                .foregroundColor(.gray)
                .padding(.horizontal, 4)
        }
    }
```
## 8. Форматирование ввода

### Числовое форматирование

```swift
@State private var amount = 0.0

TextField("Сумма", value: $amount, format: .currency(code: "RUB"))
    .keyboardType(.decimalPad)

TextField("Возраст", value: $age, format: .number)
    .keyboardType(.numberPad)
```

### Процентное форматирование

```swift
TextField("Скидка", value: $discount, format: .percent)
    .keyboardType(.decimalPad)
```
## 9. Валидация ввода

```swift
@State private var email = ""
@State private var emailError = false

TextField("Email", text: $email)
    .textFieldStyle(.roundedBorder)
    .onChange(of: email) { newValue in
        emailError = !isValidEmail(newValue)
    }
    .overlay {
        if emailError {
            RoundedRectangle(cornerRadius: 8)
                .stroke(Color.red, lineWidth: 1)
        }
    }

func isValidEmail(_ email: String) -> Bool {
    email.contains("@") && email.contains(".")
}
```
## 10. Ограничение длины ввода

```swift
TextField("Имя", text: $name)
    .onChange(of: name) { newValue in
        if newValue.count > 20 {
            name = String(newValue.prefix(20))
        }
    }
```
## 11. Фокус на поле ввода

```swift
@FocusState private var isFocused: Bool

TextField("Имя", text: $name)
    .focused($isFocused)

Button("Редактировать") {
    isFocused = true
}
```
## 12. Несколько полей с фокусом

```swift
enum Field {
    case firstName, lastName, email
}

@FocusState private var focusedField: Field?

TextField("Имя", text: $firstName)
    .focused($focusedField, equals: .firstName)
    .submitLabel(.next)
    .onSubmit {
        focusedField = .lastName
    }

TextField("Фамилия", text: $lastName)
    .focused($focusedField, equals: .lastName)
    .submitLabel(.next)
    .onSubmit {
        focusedField = .email
    }

TextField("Email", text: $email)
    .focused($focusedField, equals: .email)
    .submitLabel(.done)
    .onSubmit {
        submitForm()
    }
```
## 13. Отключение автозаполнения

```swift
TextField("Секретный код", text: $code)
    .textContentType(.oneTimeCode)
    .autocorrectionDisabled()
    .textInputAutocapitalization(.never)
```
## 14. Автоматическая заглавная буква

| Режим | Описание |
|-------|----------|
| `.never` | Никогда |
| `.words` | Каждое слово |
| `.sentences` | Каждое предложение |
| `.characters` | Каждый символ |

```swift
TextField("Название", text: $title)
    .textInputAutocapitalization(.words)
```
## 15. `DatePicker` (выбор даты)

```swift
@State private var birthDate = Date()

DatePicker("Дата рождения", selection: $birthDate, displayedComponents: .date)

DatePicker("Время", selection: $birthDate, displayedComponents: .hourAndMinute)

DatePicker("Дата и время", selection: $birthDate)
```

## 16. Стили `DatePicker`

| Стиль | Внешний вид |
|-------|-------------|
| .automatic | Автоматический |
| .compact | Компактный |
| .graphical | Календарь |
| .wheel | Колесо прокрутки |

```swift
DatePicker("Дата", selection: $date)
    .datePickerStyle(.graphical)
```
## 17. `ColorPicker` (выбор цвета)

```swift
@State private var selectedColor = Color.blue

ColorPicker("Выберите цвет", selection: $selectedColor)

ColorPicker("", selection: $selectedColor)
    .labelsHidden()
```
С поддержкой непрозрачности:
```swift
ColorPicker("Цвет фона", selection: $bgColor, supportsOpacity: true)
```
## 18. `Toggle` (переключатель)

```swift
@State private var isEnabled = false

Toggle("Включить уведомления", isOn: $isEnabled)

Toggle(isOn: $isDarkMode) {
    Label("Тёмная тема", systemImage: "moon")
}
```
## 19. Picker (выбор из списка)
```swift
@State private var selectedOption = "Опция 1"
let options = ["Опция 1", "Опция 2", "Опция 3"]

Picker("Выберите", selection: $selectedOption) {
    ForEach(options, id: \.self) { option in
        Text(option)
    }
}
```
### Стили Picker

```swift
Picker("Выбор", selection: $selected) {
    Text("Да").tag(true)
    Text("Нет").tag(false)
}
.pickerStyle(.segmented)

Picker("Страна", selection: $country) {
    ForEach(countries, id: \.self) { country in
        Text(country)
    }
}
.pickerStyle(.menu)

Picker("Категория", selection: $category) {
    ForEach(categories, id: \.self) { category in
        Text(category)
    }
}
.pickerStyle(.wheel)
```
## 20. Stepper (шаговый ввод)

```swift
@State private var quantity = 1

Stepper("Количество: \(quantity)", value: $quantity, in: 1...10)

Stepper("Значение", value: $value, step: 5)

Stepper {
    Text("Настройка")
} onIncrement: {
    value += 1
} onDecrement: {
    value -= 1
}
```
## 21. Slider (ползунок)

```swift
@State private var progress = 0.5

Slider(value: $progress)

Slider(value: $progress, in: 0...100, step: 10)

Slider(value: $brightness, in: 0...1) {
    Text("Яркость")
} minimumValueLabel: {
    Image(systemName: "sun.min")
} maximumValueLabel: {
    Image(systemName: "sun.max")
}
```
## 22. Комбинированный пример — форма регистрации

```swift
struct RegistrationForm: View {
    @State private var name = ""
    @State private var email = ""
    @State private var password = ""
    @State private var age = 18
    @State private var isSubscribed = false
    @State private var selectedCountry = "Россия"
    @State private var birthDate = Date()
    
    let countries = ["Россия", "США", "Германия", "Франция"]
    
    var isFormValid: Bool {
        !name.isEmpty && !email.isEmpty && !password.isEmpty && password.count >= 6
    }
    
    var body: some View {
        Form {
            Section("Личная информация") {
                TextField("Полное имя", text: $name)
                    .textContentType(.name)
                    .textInputAutocapitalization(.words)
                
                TextField("Email", text: $email)
                    .keyboardType(.emailAddress)
                    .textContentType(.emailAddress)
                    .autocorrectionDisabled()
                    .textInputAutocapitalization(.never)
                
                SecureField("Пароль", text: $password)
                    .textContentType(.newPassword)
                
                DatePicker("Дата рождения", selection: $birthDate, in: ...Date(), displayedComponents: .date)
                
                Picker("Страна", selection: $selectedCountry) {
                    ForEach(countries, id: \.self) { country in
                        Text(country)
                    }
                }
                
                Stepper("Возраст: \(age)", value: $age, in: 18...100)
            }
            
            Section("Настройки") {
                Toggle("Подписаться на рассылку", isOn: $isSubscribed)
            }
            
            Section {
                Button("Зарегистрироваться") {
                    register()
                }
                .disabled(!isFormValid)
                .frame(maxWidth: .infinity)
                .buttonStyle(.borderedProminent)
            }
        }
        .navigationTitle("Регистрация")
    }
    
    func register() { }
}
```
## 23. Отображение ошибок под полем

```swift
@State private var email = ""
@State private var emailError = ""

TextField("Email", text: $email)
    .textFieldStyle(.roundedBorder)
    .onChange(of: email) { newValue in
        if !newValue.contains("@") {
            emailError = "Введите корректный email"
        } else {
            emailError = ""
        }
    }

if !emailError.isEmpty {
    Text(emailError)
        .font(.caption)
        .foregroundColor(.red)
}
```
## 24. Скрытие клавиатуры

```swift
extension View {
    func hideKeyboard() {
        UIApplication.shared.sendAction(#selector(UIResponder.resignFirstResponder), to: nil, from: nil, for: nil)
    }
}

TextField("Ввод", text: $text)
    .onSubmit {
        hideKeyboard()
    }

// Или по тапу на фон
VStack {
    TextField("Введите текст", text: $text)
}
.onTapGesture {
    hideKeyboard()
}
```
## 25. Полный пример — экран профиля

```swift
struct ProfileEditorView: View {
    @State private var fullName = ""
    @State private var username = ""
    @State private var bio = ""
    @State private var birthday = Date()
    @State private var notificationsEnabled = true
    @State private var theme = "Светлая"
    @State private var volume: Float = 0.7
    
    let themes = ["Светлая", "Тёмная", "Системная"]
    
    var body: some View {
        NavigationStack {
            Form {
                Section("Основная информация") {
                    TextField("Полное имя", text: $fullName)
                        .textContentType(.name)
                    
                    TextField("Имя пользователя", text: $username)
                        .textContentType(.username)
                        .textInputAutocapitalization(.never)
                        .autocorrectionDisabled()
                    
                    DatePicker("Дата рождения", selection: $birthday, displayedComponents: .date)
                }
                
                Section("О себе") {
                    TextEditor(text: $bio)
                        .frame(height: 100)
                        .overlay(alignment: .topLeading) {
                            if bio.isEmpty {
                                Text("Расскажите о себе...")
                                    .foregroundColor(.gray)
                                    .padding(.top, 8)
                                    .padding(.leading, 4)
                            }
                        }
                }
                
                Section("Настройки") {
                    Toggle("Push-уведомления", isOn: $notificationsEnabled)
                    
                    Picker("Тема", selection: $theme) {
                        ForEach(themes, id: \.self) { theme in
                            Text(theme)
                        }
                    }
                    .pickerStyle(.segmented)
                    
                    HStack {
                        Image(systemName: "speaker.wave.1")
                        Slider(value: $volume, in: 0...1)
                        Image(systemName: "speaker.wave.3")
                    }
                }
                
                Section {
                    Button("Сохранить изменения") {
                        saveProfile()
                    }
                    .frame(maxWidth: .infinity)
                    .buttonStyle(.borderedProminent)
                }
            }
            .navigationTitle("Редактировать профиль")
            .toolbar {
                ToolbarItem(placement: .topBarTrailing) {
                    Button("Готово") {
                        saveProfile()
                    }
                }
            }
        }
    }
    
    func saveProfile() { }
}
```
## Важно

- Всегда используйте `@State` или `@Binding` для хранения значений полей ввода
- Для паролей используйте `SecureField`, а не `TextField`
- Указывайте правильный `keyboardType` и `textContentType` для лучшего `UX`
- Добавляйте `submitLabel` для навигации между полями
- Используйте `.focused` для управления клавиатурой
- Для форм лучше использовать `Form`или `List` с `Section`
- Добавляйте валидацию и показывайте ошибки под полями
- Не забывайте про `accessibilityLabel` для полей без видимых меток
