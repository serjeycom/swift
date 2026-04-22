
# Кнопки в SwiftUI

## 1. Базовая кнопка

```swift
Button("Tap me") {
    /// Вызовется в момент нажатия на кнопку
    print("Tapped")
}
```

## 2. С системной иконкой

```swift
Button {
    print("Deleted")
} label: {
    Label("Delete", systemImage: "trash")
}
```
## 3. Кнопки с ролями

 - удаление
 - отмена
 - закрытие
 - подтверждение

```swift
/// Доступно несолько стилей в Enum (.confirm, .close и т.д.)
Button("Remove", role: .destructive) { }
Button("Cancel", role: .cancel) { }
```
| Роль | Цвет | Применение |
|------|------|------------|
| .destructive | Красный | Удаление, опасные действия |
| .cancel | Полужирный | Отмена, закрытие |

## 4. Кнопка-переключатель (Да / Нет)

```swift
struct ToggleButton: View {
    @State private var isFavorite = false

    var body: some View {
        Button(isFavorite ? "★" : "☆") {
            isFavorite.toggle()
        }
        .buttonStyle(.borderedProminent)
    }
}
```
## Стили кнопок (подробно)

`SwiftUI` предоставляет 5 встроенных стилей кнопок. Каждый стиль меняет внешний вид и поведение кнопки.

### .automatic (стиль по умолчанию)

```swift
Button("Авто") { }
    .buttonStyle(.automatic)
```
Система сама выбирает подходящий стиль в зависимости от контекста:
- В списке может стать .plain
- На панели навигации может стать .borderless
- В основном контенте часто ведёт себя как .bordered

### .plain

```swift
Button("Обычный") { }
    .buttonStyle(.plain)
```
Особенности:
- Без фона и рамки
- Текст меняет цвет при нажатии
- Идеален для кнопок внутри списков (List)
- Не добавляет отступов вокруг содержимого

Пример использования в списке:

```swift
List {
    Button("Удалить из списка", role: .destructive) { }
        .buttonStyle(.plain)
    Button("Поделиться") { }
        .buttonStyle(.plain)
}
```
### .borderless


```swift
Button("Без границ") { }
    .buttonStyle(.borderless)
```
Особенности:
- Похож на .plain, но оптимизирован для тулбаров и меню
- Лучше работает в составе ToolbarItem
- Сохраняет отзывчивость при нажатии

Пример в тулбаре:

```swift
.toolbar {
    ToolbarItem(placement: .topBarTrailing) {
        Button("Готово") { }
            .buttonStyle(.borderless)
    }
}
```
### .bordered

```swift
Button("Рамка") { }
    .buttonStyle(.bordered)
```
Особенности:
- Тонкая рамка вокруг кнопки
- При нажатии рамка меняет цвет
- Фон остаётся прозрачным
- Хорошо работает в группах кнопок

### .borderedProminent

```swift
Button("Выделенная") { }
    .buttonStyle(.borderedProminent)
```
Особенности:
- Цветная заливка (обычно акцентный цвет приложения)
- Белый текст поверх заливки
- При нажатии цвет затемняется
- Лучший выбор для главного действия на экране

Пример для главной кнопки:

```swift
VStack {
    Text("Оплата: $99")
        .font(.title)
    
    Button("Оплатить") { }
        .buttonStyle(.borderedProminent)
        .controlSize(.large)
}
```
## Комбинирование стилей с другими модификаторами

### tint() — меняем цвет

```swift
Button("Красный") { }
    .buttonStyle(.borderedProminent)
    .tint(.red)

Button("Синий") { }
    .buttonStyle(.bordered)
    .tint(.blue)
```
### controlSize() — меняем размер

```swift
Button("Маленькая") { }
    .buttonStyle(.borderedProminent)
    .controlSize(.small)

Button("Большая") { }
    .buttonStyle(.borderedProminent)
    .controlSize(.large)
```

```swift
Button("Усиленная") { }
    .buttonStyle(.bordered)
    .controlProminence(.increased)
```

## Размеры кнопок

| Размер | Значение |
|--------|----------|
| .mini | Очень маленькая |
| .small | Маленькая |
| .regular | Обычная (по умолчанию) |
| .large | Большая |
| .extraLarge | Очень большая (iOS 17+) |

```swift
Button("Кнопка") { }
    .controlSize(.large)
```
## Кнопки в NavigationBar

```swift
NavigationStack {
    Text("Главный экран")
        .toolbar {
            ToolbarItem(placement: .topBarLeading) {
                Button("Отмена") { }
            }
            
            ToolbarItem(placement: .topBarTrailing) {
                Button("Сохранить") { }
                    .bold()
            }
            
            ToolbarItem(placement: .principal) {
                Button("Заголовок") { }
            }
        }
}
```
## Кнопки-Меню

### По нажатию

```swift
Menu {
    Button("Копировать") { copy() }
    Button("Вставить") { paste() }
    Button("Удалить", role: .destructive) { delete() }
} label: {
    Label("Действия", systemImage: "ellipsis.circle")
}
```
### По долгому нажатию

```swift
Button {
    // действие при тапе
} label: {
    Text("Нажми и держи")
}
.contextMenu {
    Button("Редактировать") { }
    Button("Удалить", role: .destructive) { }
}
```
## Состояния кнопки

```swift
@State private var isLoading = false

Button("Отправить") {
    isLoading = true
    Task {
        await sendData()
        isLoading = false
    }
}
.disabled(isLoading)

Button {
    // действие
} label: {
    if isLoading {
        ProgressView()
    } else {
        Text("Отправить")
    }
}
```
## Важно

Кнопки в `SwiftUI` по умолчанию имеют область нажатия 44x44 pt (Human Interface Guidelines). Не нужно добавлять лишние паддинги для удобства — система уже позаботилась об этом.
