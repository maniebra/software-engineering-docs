# Worked Example - Video Platform

This example designs a simplified video platform similar to YouTube. The goal is not to copy any real company's architecture, but to practice adding system design components incrementally.

## Requirements

Functional requirements:

- Users can upload videos.
- Users can watch videos.
- Users can search or browse videos.
- The system stores metadata such as title, description, owner, and upload time.
- The system tracks basic engagement such as views and likes.

Non-functional requirements:

- Video playback should be fast globally.
- Upload processing can happen asynchronously.
- The system should tolerate server failures.
- Popular videos should not overload the origin servers.
- Observability is required for playback errors, processing delays, and infrastructure health.

## Start Simple

The smallest design has a client, an application server, a database, and object storage.

```mermaid
flowchart LR
    C[Client] --> A[Application Server]
    A --> DB[(Metadata Database)]
    A --> O[(Object Storage)]
```

The database stores metadata. Object storage stores the video files, thumbnails, and generated versions.

This works for a tiny product, but video processing is slow. Upload requests should not wait for every encoding step.

## Add Upload Processing

Use a message queue to decouple upload completion from background processing.

```mermaid
sequenceDiagram
    participant C as Client
    participant A as App
    participant O as Object Storage
    participant Q as Queue
    participant W as Video Worker
    participant D as Database
    C->>A: Request upload URL
    A-->>C: Signed upload URL
    C->>O: Upload raw video
    C->>A: Confirm upload
    A->>D: Save video metadata as processing
    A->>Q: Publish VideoUploaded message
    Q->>W: Deliver job
    W->>O: Read raw video, write encoded versions
    W->>D: Mark video as ready
```

Workers can generate:

- Multiple resolutions.
- Thumbnails.
- Preview clips.
- Captions or transcripts.
- Safety or copyright review jobs.

If processing fails, the queue can retry the job. After repeated failures, the message can move to a dead-letter queue for inspection.

## Add Playback Scale

Playback traffic is usually much larger than upload traffic. Serve videos through a CDN instead of making every viewer hit object storage or application servers.

```mermaid
flowchart LR
    V[Viewer] --> CDN[CDN Edge]
    CDN -->|cache miss| O[(Object Storage)]
    V --> A[Application Server]
    A --> DB[(Metadata Database)]
```

The client asks the application for video metadata and playback URLs. The video bytes come from the CDN.

This improves:

- Latency.
- Origin bandwidth.
- Global availability for popular content.
- Traffic spike handling.

## Add Application Scaling

Application servers should be stateless so any server can answer metadata, search, profile, or engagement requests.

```mermaid
flowchart LR
    U[Users] --> DNS[DNS]
    DNS --> LB[Load Balancer]
    LB --> A1[App Server 1]
    LB --> A2[App Server 2]
    LB --> A3[App Server 3]
    A1 --> C[(Cache)]
    A2 --> C
    A3 --> C
    A1 --> DB[(Database)]
    A2 --> DB
    A3 --> DB
```

The load balancer spreads requests across healthy app servers. Shared state lives in the database, cache, or signed tokens rather than local server memory.

## Add Database Read Scale

Metadata reads can become heavy: video pages, channel pages, recommendations, and browse results all need data.

```mermaid
flowchart LR
    A[App Servers] -->|writes| P[(Primary Database)]
    A -->|reads| R1[(Read Replica 1)]
    A -->|reads| R2[(Read Replica 2)]
    P --> R1
    P --> R2
```

The primary handles writes such as uploads, title edits, likes, and comments. Replicas handle read-heavy pages.

Because replicas may lag, some reads should still go to the primary. For example, after a creator edits a title, the creator should see the new title immediately.

## Add Caching

Cache frequently read metadata and counters.

Good cache candidates:

- Video metadata for popular videos.
- Channel summaries.
- Public user profile snippets.
- Homepage or category fragments.
- Authorization/session lookups.

Be careful with counters such as views and likes. Exact real-time counts are expensive at high traffic. Many systems batch or approximate view updates, then periodically write aggregated values.

```mermaid
flowchart LR
    A[App Servers] --> C[(Cache)]
    C -->|miss| DB[(Database)]
    A --> Q[(Queue)]
    Q --> W[Counter Workers]
    W --> DB
```

## Add Global Routing

For global users, DNS and CDN handle much of the routing.

```mermaid
flowchart TB
    U1[User in Europe] --> D[GeoDNS]
    U2[User in Asia] --> D
    D --> CDN1[Nearby CDN Edge]
    D --> CDN2[Nearby CDN Edge]
    CDN1 --> O[(Origin/Object Storage)]
    CDN2 --> O
```

If the application itself runs in multiple regions, the design must also answer data questions:

- Which region accepts writes?
- How is metadata replicated?
- Can a user upload in one region and watch immediately in another?
- What happens if regions cannot talk to each other?

Do not add multi-region writes casually. CDN distribution is often enough for early video playback scale.

## Add API Gateway

An API gateway can centralize edge behavior.

```mermaid
flowchart LR
    C[Web and Mobile Clients] --> G[API Gateway]
    G --> A[App Services]
    G --> AUTH[Auth Service]
    G --> SEARCH[Search Service]
```

The gateway may handle authentication, rate limiting, request logging, and routing to internal services.

## Add Observability

A video platform needs visibility into both request/response traffic and background media pipelines.

Important signals:

- Playback start latency.
- CDN cache hit ratio.
- Video buffering rate.
- Upload success rate.
- Queue depth and message age.
- Encoding duration and failure rate.
- App error rate and p95/p99 latency.
- Database replication lag.
- Object storage errors.

```mermaid
flowchart LR
    A[App Servers] --> O[Observability Platform]
    W[Workers] --> O
    CDN[CDN] --> O
    DB[(Database)] --> O
    O --> Dash[Dashboards]
    O --> Alert[Alerts]
```

Alerts should point to user impact or urgent leading indicators. For example, "encoding queue message age is above 30 minutes" is more useful than "one worker restarted once."

## Final Shape

```mermaid
flowchart LR
    U[Users] --> DNS[DNS / GeoDNS]
    DNS --> CDN[CDN]
    U --> G[API Gateway]
    G --> LB[Load Balancer]
    LB --> A1[App Server]
    LB --> A2[App Server]
    A1 --> C[(Cache)]
    A2 --> C
    A1 --> P[(Primary DB)]
    A2 --> P
    P --> R[(Read Replicas)]
    A1 --> Q[(Message Queue)]
    A2 --> Q
    Q --> W[Video and Counter Workers]
    W --> O[(Object Storage)]
    CDN --> O
    A1 --> OBS[Logs, Metrics, Traces]
    A2 --> OBS
    W --> OBS
```

## Trade-offs to Discuss

- Strong consistency is expensive for global counters.
- CDN caching improves playback but complicates content removal and freshness.
- Object storage is excellent for video bytes, but metadata still needs a database.
- Queues improve upload responsiveness, but introduce retry, ordering, and dead-letter handling.
- Stateless app servers scale well, but shared state stores become critical dependencies.
- Multi-region applications improve latency and resilience, but make data consistency much harder.

## Check Your Understanding

<quiz>
Why should video encoding happen through workers and a queue instead of inside the upload request?

- [x] Encoding is slow and failure-prone, so asynchronous workers keep the user request fast and allow retries
> Correct. The app can acknowledge the upload and process video formats in the background.
- [ ] Queues make object storage unnecessary
- [ ] Encoding must happen inside DNS
- [ ] Workers prevent the need for monitoring
</quiz>

