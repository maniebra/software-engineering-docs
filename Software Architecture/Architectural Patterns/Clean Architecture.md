
# Clean Architecture

Clean Architecture emphasizes decoupling, independence of frameworks, testability, and maintainability. Developed by Robert C. Martin (Uncle Bob).

### Layers:

- **Entities**: Core business objects.
- **Use Cases (Application)**: Contains application logic; interacts with Entities.
- **Interface Adapters**: Controllers, Presenters, Gateways. Converts data between Use Cases and external layers.
- **Frameworks & Drivers**: External layer (e.g., web, database, UI frameworks).

### Structure (Inner layers don't depend on outer layers):

```
Entities ← Use Cases ← Interface Adapters ← Frameworks & Drivers
```

### Pros:

- Excellent maintainability and testability.
- Highly decoupled design.

### Cons:

- May seem overly abstract and complicated for smaller projects.
- High initial setup complexity.

### When to use:

- Enterprise-scale applications.
- Projects needing long-term maintenance and evolution.