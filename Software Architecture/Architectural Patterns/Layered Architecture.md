
# Layered Architecture (N-tier)

Simplest form of software architecture, dividing software into horizontal layers.

### Common Layers:

- **Presentation Layer** (User interfaces)
- **Business Logic Layer** (Application logic)
- **Data Access Layer** (Database interactions)

### Structure:

```
Presentation → Business Logic → Data Access
```

### Pros:

- Easy to understand, widespread usage.
- Simple separation of concerns.

### Cons:

- Tends toward tight coupling.
- Harder to change foundational layers later.

### When to use:

- Simple CRUD applications.
- Small to medium-sized web apps.
