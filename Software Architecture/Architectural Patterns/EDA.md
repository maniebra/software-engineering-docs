# Event-Driven Architecture (EDA)

System components communicate asynchronously via events/messages.

### Components:

- **Event Producer**: Generates and publishes events.
- **Event Consumer**: Listens and reacts to events.
- **Event Broker**: Manages event flow.

### Structure:

```
Producer → Event Broker → Consumers
```

### Pros:

- Loose coupling and flexibility.
- Highly scalable and adaptable.

### Cons:

- Complexity in debugging event flows.
- Challenges in event versioning and consistency.

### When to use:

- Real-time applications.
- Systems requiring asynchronous handling and scalability (IoT, real-time analytics).