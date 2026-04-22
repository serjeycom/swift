1. Базовая `Кнопка`

```swift
Button("Tap me") {
    print("Tapped")
}
```

2. С системной иконкой
```swift
Button {
    print("Deleted")
} label: {
    Label("Delete", systemImage: "trash")
}
```

3. Интересные кнопки, которые отлично подходят к определённым действиям.

Например для:

 - Удаления 
 - Отмены 
 - Закрытия 
 - Подтверждения действия

```swift
Button("Remove", role: .destructive) { }
Button("Cancel", role: .cancel) { }
```

4. Кнопка-переключатель (либо Да / либо Нет )
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

## Стили кнопок

1. Стили кнопок
```swift
Button("Borderless") { }
	// Автоматический (по умолчанию)
	.buttonStyle(.automatic)
	// Минимальный, без фона (для списков)
	.buttonStyle(.plain)
	// Как plain, но с поведением тулбара
	.buttonStyle(.borderless)
	// Тонкая рамка
	.buttonStyle(.bordered)
	// С заливкой (акцентное действие)
	.buttonStyle(.borderedProminent)
	/// Запрещаем нажатие на кнопку
	.disabled(isDisabled)
```

2. Размеры кнопок
```swift
Button("Кнопка") { }
    .controlSize(.mini)    // очень маленькая
    .controlSize(.small)   // маленькая
    .controlSize(.regular) // обычная (по умолчанию)
    .controlSize(.large)   // большая
    .controlSize(.extraLarge) // очень большая (iOS 17+)
```    

## Кнопки в NavigationBar
```swift
NavigationStack {
    Text("Главный экран")
        .toolbar {
            // Левая кнопка
            ToolbarItem(placement: .topBarLeading) {
                Button("Отмена") { }
            }
            
            // Правая кнопка
            ToolbarItem(placement: .topBarTrailing) {
                Button("Сохранить") { }
                    .bold()
            }
            
            // Принципиальная кнопка
            ToolbarItem(placement: .principal) {
                Button("Заголовок") { }
            }
        }
}
```

## Кнопки-Меню

 Кнопка, которая по нажатию раскрывается и предлагает один вариант из нескольких

```swift
Menu {
    Button("Копировать") { copy() }
    Button("Вставить") { paste() }
    Button("Удалить", role: .destructive) { delete() }
} label: {
    Label("Действия", systemImage: "ellipsis.circle")
}
```

 Кнопка, которая по долгому нажатию на себя раскрывается и предлагает один вариант из нескольких
```swift
// Или кнопка, открывающая меню
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

**Важно:** Кнопки в SwiftUI по умолчанию имеют область нажатия 44x44 pt (Human Interface Guidelines). Не нужно добавлять лишние паддинги для удобства — система уже позаботилась об этом.
