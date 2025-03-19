# Model-View-ViewModel (MVVM)

MVVM introduces a ViewModel to mediate between View and Model.

### Components:

- **Model**: Data and business logic.
- **View**: UI component displaying data.
- **ViewModel**: Mediates data from Model to View, handling UI-specific logic.

### Interaction Flow:

```
User ↔ View ↔ ViewModel ↔ Model
```

### Pros:

- Simplifies UI testing.
- ViewModel can easily bind with View (data binding).

### Cons:

- Complexity in handling asynchronous data.
- ViewModel can become bloated.

### When to use:

- Client-side applications with complex UI (e.g., Android apps, WPF apps, React/Angular apps).
