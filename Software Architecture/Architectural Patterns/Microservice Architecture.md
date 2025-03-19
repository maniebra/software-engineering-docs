
# Microservices Architecture

Builds applications as independent, loosely coupled services.

### Components:

- Independent services with specific functionality.
- Services communicate over network (usually via HTTP APIs or message queues).

### Structure:

```
API Gateway → [Microservice 1, Microservice 2, ... Microservice N]
```

### Pros:

- Independent scaling.
- Flexibility in choosing technology per service.

### Cons:

- Higher complexity in orchestration.
- Challenges with data consistency and latency.

### When to use:

- Large-scale distributed systems.
- Continuous deployment scenarios.