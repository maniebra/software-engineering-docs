
#  Model-View-Controller (MVC)

MVC separates application logic into three components: **Model**, **View**, and **Controller**.

### Components:

- **Model**: Manages data, business logic, and state.
- **View**: Displays data to the user (UI components).
- **Controller**: Handles user input, interacts with model, updates view.

### Interaction Flow:

```
User → Controller → Model → View → User
```

### Pros:

- Clear separation of concerns.
- Easier testing and maintenance.

### Cons:

- Controller can become overly complex.
- Can lead to tight coupling between Controller and View.

### When to use:

- Web applications (e.g., Django, Ruby on Rails).
- Applications where UI and logic are clearly separated.

---


---



---


---

# 5. Onion Architecture

Similar to Clean Architecture; it's layered, focused on decoupling external dependencies from core logic.

### Layers:

- **Domain Layer**: Core domain logic and entities.
- **Application Services Layer**: Use cases and business logic orchestration.
- **Infrastructure Layer**: External resources (database, file system, etc.).
- **Presentation/UI Layer**: User interfaces and controllers.

### Structure:

```
Presentation/UI → Application Services → Domain ← Infrastructure
```

### Pros:

- Strong decoupling and testability.
- Easier to maintain domain purity.

### Cons:

- Requires clear discipline to avoid domain pollution.
- Can be overly structured for simpler apps.

### When to use:

- Complex applications with evolving business logic.
- Enterprise apps with strong domain models.
