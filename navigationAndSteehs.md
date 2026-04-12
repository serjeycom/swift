
# Навигация в `SwiftUI`: от простых экранов до сложных сценариев

`SwiftUI` предлагает декларативный подход к навигации, который кардинально отличается от привычного `UIKit`. Вместо того чтобы вручную "пушить" и "попить" контроллеры, вы описываете, какой экран должен отображаться при определённом состоянии приложения. 

`SwiftUI` сам заботится об анимации и управлении стеком.

В этой статье мы разберём все актуальные инструменты навигации: от базового `NavigationLink` до программного управления стеком с использованием `NavigationPath`

## 1. Основы: `NavigationStack`

До выхода `iOS 16` основным инструментом был `NavigationView` 
Сейчас он официально объявлен вне закона (deprecated). 

`Apple` рекомендует использовать `NavigationStack`.  Он проще, производительнее и лучше работает с типами данных.

### 1.1 Простейший переход между экранами

Самый базовый способ — обернуть ваш контент в `NavigationStack` и использовать `NavigationLink`:

```swift
import SwiftUI

struct ContentView: View {

    var body: some View {
	    /// Оборачиваем главное View в NavigationStack
	    /// заметьте, что NavigationStack лежит в Самом верху
        NavigationStack {
            VStack(spacing: 20) {
                Text("Главный экран")
                
                /// Кнопка, по которой перейдём на экран DetailView
                NavigationLink("Перейти на детальный экран") {
                    DetailView()
                }
            }
            /// Заголовок экрана
            .navigationTitle("Главная")
        }
    }
}

struct DetailView: View {
    var body: some View {
        Text("Детальный экран")
            .navigationTitle("Детали")
            /// Режим отображения навБара – маленький (прям как я)
            .navigationBarTitleDisplayMode(.inline)
    }
}
```

Что здесь происходит:
- `NavigationStack` создает навигационный контейнер, позволяя осуществлять переходы с данного экрана. Как правило – достаточно обернуть основное `View`
- `NavigationLink` — это кнопка, при нажатии на которую в стек добавляется новый экран.
- Автоматически появляется кнопка `Назад` в левом верхнем углу.
- Заголовок на детальном экране отображается в компактном режиме `(.inline)` есть режим `.large` – можете поэкспериментировать.

### 1.2 Передача данных на следующий экран

Часто нужно передать данные на следующий экран.  Это делается не так уж и сложно:

```swift
struct Product: Identifiable {
    let id = UUID()
    let name: String
    let price: Double
}

struct ProductsView: View {
    let products = [
        Product(name: "iPhone 15", price: 999),
        Product(name: "iPad Air", price: 799),
        Product(name: "MacBook Pro", price: 1999)
    ]
    
    var body: some View {
        NavigationStack {
            List(products) { product in
                NavigationLink {
	                /// Передаём выбранный product внутрь нашей ProductDetailView
                    ProductDetailView(product: product)
                } label: {
	                /// Заметьте: мы сделали навигационную кнопку из всего HStack
	                /// Благодаря синтаксису с label
                    HStack {
                        Text(product.name)
                        Spacer()
                        Text("\(product.price)$")
                            .foregroundColor(.secondary)
                    }
                }
            }
            .navigationTitle("Товары")
        }
    }
}

/// Детальный экран, с выбранным product
struct ProductDetailView: View {
	/// Детальный экран ожидает некий Product
    let product: Product
    
    var body: some View {
        VStack(spacing: 20) {
            Text(product.name)
                .font(.largeTitle)
            Text("Цена: \(product.price)$")
                .font(.title2)
            Text("Подробное описание товара...")
                .padding()
        }
        .navigationTitle(product.name)
        .navigationBarTitleDisplayMode(.inline)
    }
}
```

Обратите внимание: мы передаем `product` напрямую в `ProductDetailView` через инициализатор внутри замыкания `NavigationLink`

### 1.3 Программная навигация с `NavigationPath`

Иногда нужно **перейти на другой экран не по нажатию кнопки, а программно** — например, после успешного входа в аккаунт или загрузки данных. 

Для этого используется `NavigationPath` — тип, который хранит в себе весь стек экранов.

```swift
struct ProgrammaticNavigationView: View {
    @State var navigationPath = NavigationPath()
    @State var isLoggedIn = false
    
    var body: some View {
	    /// Благодаря $ – мы можем изменить navigationPath где угодно, а наш NavigationStack 
	    /// непременно об этом узнает и осуществит переход 
        NavigationStack(path: $navigationPath) {
            VStack(spacing: 20) {
                Text("Экран входа")
                
                /// Когда пользователь нажмёт на кнопку – перейдём к экрану "main"
                Button("Войти в аккаунт") {
                    isLoggedIn = true
                    /// Добавляем путь в нашу навигацию
                    navigationPath.append("main")
                }
            }
            .navigationTitle("Авторизация")
            /// Как только navigationPath изменится – вызовется данный код
            /// Если значение равно "main" – откроем экран MainView()
            /// Значений для navigationPath может быть сколь угодно много 
            /// в зависимости от конкртеного – мы можем открывать разные экраны
            .navigationDestination(for: String.self) { value in
	            /// Если сделать navigationPath.append("main")
                if value == "main" {
                    MainView()
                    /// Если сделать navigationPath.append("logout")
                } else if value == "logout" {
	                /// LogoutView()
                }
            }
        }
    }
}

struct MainView: View {
    var body: some View {
        Text("Добро пожаловать!")
            .font(.largeTitle)
            .navigationTitle("Главный экран")
    }
}
```

`NavigationPath` может хранить разные типы данных. 

Вы можете пушить экраны с разными моделями:

```swift
navigationPath.append(Product(name: "iPhone", price: 999))
navigationPath.append("settings")
navigationPath.append(42)
```

А затем обрабатывать их через несколько модификаторов `navigationDestination`:

```swift
/// Если сделать вот так – navigationPath.append(Product(name: "iPhone", price: 999))
/// Можем обработать открытие экрана следующим образом:
.navigationDestination(for: Product.self) { product in
    ProductDetailView(product: product)
}
/// Если сделать вот так – navigationPath.append("settings")
/// Можем обработать открытие экрана следующим образом:
.navigationDestination(for: String.self) { value in
    if value == "settings" {
        SettingsView()
    }
}
/// Если сделать вот так – navigationPath.append(42)
/// Можем обработать открытие экрана следующим образом:
.navigationDestination(for: Int.self) { id in
    ItemDetailView(itemId: id)
}
```

Надеюсь, вы догадались, что лучше всего использовать `Enum`

```swift
navigationPath.append(Product(name: "iPhone", price: 999))
navigationPath.append("settings")
navigationPath.append(42)
```
**Всё это – полный бред, можно ошибиться в букве, нет автодополнения – Используйте Enum**

О навигации с помощью `Enum` – поговорим чуть ниже

### 1.4 Возврат на корневой экран

Чтобы программно вернуться на самый первый экран (pop to root), очистите `NavigationPath`:

```swift
Button("Выйти на главную") {
	/// Обнуляем navigationPath
    navigationPath = NavigationPath()
}
```

## 2. Настройка навигационной панели

### 2.1 Заголовки

```swift
/// Заголовок для экрана
.navigationTitle("Заголовок")
/// Компактный стиль, как в настройках iPhone
.navigationBarTitleDisplayMode(.inline)
/// Явно указываем большой стиль (используется по умолчанию)
.navigationBarTitleDisplayMode(.large)
```

### 2.2 Кнопки в навигационной панели

```swift
.toolbar {
	/// topBarLeading – Слева
    ToolbarItem(placement: .topBarLeading) {
        Button("Отмена") {
            // Действие
        }
    }
    /// topBarTrailing – Справа
    ToolbarItem(placement: .topBarTrailing) {
        Button("Сохранить") {
            // Действие
        }
    }
	/// principal – По центру (между левой и правой кнопкой)
	ToolbarItem(placement: .principal) {
        // Действие
    }
}
```


### 2.3 Скрытие панели навигации

Чтобы полностью скрыть навигационную панель на конкретном экране:

```swift
.navigationBarHidden(true)
```

Или с анимацией:

```swift
@State var hideNavBar = false

View...
	.navigationBarHidden(hideNavBar)
	.animation(.easeInOut, value: hideNavBar)
```

## 4. Модальные окна (`Sheets`)

Не вся навигация должна происходить через `push`

Модальные окна (`sheets`) используются для временных задач: редактирование профиля, добавление новой записи, настройки.

**`sheets` это хорошо знакомые вам Шторки** – *Поверх главного экрана открывается дополнительный экран, который перекрывает его сверху и его можно просто смахнуть вниз и он закроется.*

### 4.1 Простой `sheet`

```swift
struct SheetExampleView: View {
    @State var showingSheet = false
    @State var itemName: String = "Hello"
    
    var body: some View {
        Button("Показать модальное окно") {
            showingSheet.toggle()
        }
        /// Показ шторки
        .sheet(isPresented: $showingSheet) {
            SheetContentView(itemName: $itemName)
        }
    }
}

struct SheetContentView: View {
	/// Просто скопируйте эту строчку, чтобы иметь возможность закрыть шторку по кнопке
    @Environment(\.dismiss) var dismiss
    @Binding var itemName: String
    
    var body: some View {
        NavigationStack {
            Text("Содержимое модального окна")
                .toolbar {
                    ToolbarItem(placement: .cancellationAction) {
                    	/// Закрытие шторки, по нажатию на кнопку "Готово"
	                    Button("Отмена") { dismiss() }
	                }
	                ToolbarItem(placement: .confirmationAction) {
	                    Button("Сохранить") {
	                        itemName = "Текст, который мы можем ввести в шторке в TextField"
	                        dismiss()
	                    }
	                }
              }
        }
    }
}
```

### 4.3 `FullScreenCover` (полноэкранный режим)

В отличие от обычного `sheet`, `fullScreenCover` занимает весь экран и не позволяет видеть родительское окно:

```swift
/// Обратите внимание, что у вас должно быть булево поле showingFullScreen
/// за счёт которого мы и контролируем показ шторки
.fullScreenCover(isPresented: $showingFullScreen) {
    FullScreenView()
}
```

Это полезно для онбординга, входа в аккаунт или просмотра медиа, при этом единственное отличие – вы пишете не `.sheet` а `.fullScreenCover` – всё очень просто

## 5. Глубокая навигация (`Deep Linking`)

Данная тема несколько сложнее.

Иногда нужно открыть приложение сразу на определённом экране — например, по `push-уведомлению` или `URL-схеме`

Скажем – вы находитесь на главном экране, а вам сразу нужно отрыть некий дальний экран, до которого очень далеко идти пошагово. Можно открыть его напрямую, пропустив все предыдущие экраны.

```swift
struct DeepLinkingApp: View {
    @State private var navigationPath = NavigationPath()
    
    var body: some View {
        NavigationStack(path: $navigationPath) {
            HomeView()
	            /// Отслеживаем изменения в навигации
	            /// Ожидаем, что нам придёт Enum Route
                .navigationDestination(for: Route.self) { route in
                    switch route {
                    case .product(let id):
                        ProductDetailView(productId: id)
                    case .settings:
                        SettingsView()
                    case .profile:
                        ProfileView()
                    }
                }
        }
        /// Отслеживаем открытие приложения из Push-уведомления
        .onOpenURL { url in
            // К примеру: обработка URL – sergioApplication://product/123
            /// Если это наше приложение
            if url.scheme == "sergioApplication",
	           /// И нужно перйти к экрану "product"
               url.host == "product",
               /// Попытаемся получить АйДи
               let id = Int(url.path.replacingOccurrences(of: "/", with: "")) {
               /// Всё получилось –
               /// Скажем navigationPath, что ему нужно открыть экран Route с АйДи товара
                navigationPath.append(Route.product(id: id))
            }
        }
    }
}

/// Enum – лучше решение, в котором вы могли бы перечислить все требуеммые экраны
/// Которые вы собираетесь открывать в вашем приложении
enum Route: Hashable {
	/// Детальная карточка товара с определённым АйДи
    case product(id: Int)
    /// Настройки
    case settings
    /// Профиль пользователя
    case profile
}

/// Главный экран
struct HomeView: View {
    var body: some View {
        Text("Главный экран")
    }
}
```

**Такой подход позволяет управлять глубокими ссылками централизованно, через единый enum `Route`**

## 6. Советы и лучшие практики

### 6.1 Используйте `NavigationPath` для сложной навигации

Если у вас больше 2-3 переходов или экраны с разными типами данных — используйте `NavigationPath` вместо множества `@State` булевых флагов.

### 6.2 Не храните состояние навигации во `ViewModel`

`NavigationPath` должен жить во `View` (как `@State`), а не в отдельной `ViewModel`, потому что `SwiftUI` управляет жизненным циклом навигации.

### 6.3 Используйте `enum` для типизированной навигации

**Вместо строковых идентификаторов создавайте `enum Route`**, как показано в разделе про `deep linking`. 

Это даёт `type safety` и удобное автодополнение.

### 6.4 Избегайте вложенных `NavigationStack`

**Не кладите один `NavigationStack` внутрь другого!**

 Это вызывает проблемы с передачей пути и кнопкой "Назад". 
 Если вам нужен вложенный навигатор (например, табы с независимым стеком), используйте отдельные `NavigationStack` внутри каждого таба.

## Заключение

**Навигация в `SwiftUI` стала намного проще и декларативнее по сравнению с `UIKit`.**
 
 `NavigationStack` позволяет управлять стеком через простой тип `NavigationPath`, а `NavigationSplitView` даёт отличную поддержку `iPad` и `Mac` из коробки.

Ключевые моменты, которые стоит запомнить:

- Используйте `NavigationStack` вместо устаревшего `NavigationView`
- Для программной навигации применяйте `NavigationPath` 
- Для `iPad` и `Mac` используйте `NavigationSplitView`
- Модальные окна показывайте через `sheet` и `fullScreenCover`
- Для `deep linking` используйте `enum Route` и один `navigationDestination` внутри которого обрабатывайте, какой экран показать для каждого из варинта `enum Route`

Освоив эти инструменты, вы сможете строить навигацию любой сложности — от простого списка до многоуровневых приложений с глубокими ссылками и поддержкой всех платформ `Apple`
