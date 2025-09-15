## Feature 09 — Offline Resilience & Sync

### Overview
Ensure the app continues functioning without connectivity; queue events and flush reliably.

### User Stories
- As a guardian, I still receive events after the child goes back online.
- As a child app, I never lose critical events.

### Frontend Mapping
- Service: `syncService` with local persistent queue
- State: `network: { isOnline, lastSyncAt }`, `queue: QueuedEvent[]`

### Data Models
- `QueuedEvent`: `{ id, type, payload, ts, attempts }`

### API Contracts
- Uses existing endpoints; adds retry and backoff policy

### Sequence Diagram
```mermaid
sequenceDiagram
  participant C as Child App
  participant B as Backend
  C->>C: Enqueue events when offline
  C->>B: Flush with exponential backoff when online
  alt 4xx validation
    C->>C: Drop and log
  else 5xx/network
    C->>C: Retry later
  end
```

### Acceptance Criteria
- Zero event loss in airplane-mode test (up to local storage limit)
- Backoff policy prevents server overload
