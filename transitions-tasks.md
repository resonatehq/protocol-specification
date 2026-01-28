# Task State Transitions

## States

| Symbol | Description |
|--------|-------------|
| ⊥ | Task does not exist |
| ⟨p, e, l, v, c, R⟩ | Pending |
| ⟨a, e, l, v, c, R⟩ | Acquired |
| ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | Suspended |
| ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | Fulfilled |

## Symbols

| Symbol | Description |
|--------|-------------|
| t | Current time |
| e | Expiry time |
| l | TTL (time to live) |
| v | Version |
| c | Current (Invoke or Resume) |
| R | Set of Resumes |
| P | Set of Promises |

## Operations

```
TaskGet()
TaskCreate(t, l)
TaskAcquire(t, l, v)
TaskRelease(t, l, v)
TaskSuspend(v, P)
TaskFence(v)
TaskHeartbeat(t, v)
TaskFulfill(v)
Enqueue(Invoke, t, l)
Enqueue(Resume, t, l)
Tick(t)
```

## Side Effects

| Side Effect | Description |
|-------------|-------------|
| Send(Invoke) | Send invoke message |
| Send(Resume) | Send resume message |

## Transitions

| Operation | Current State | Next State | Result | Side Effect(s) |
|-----------|---------------|------------|--------|----------------|
| TaskGet() | ⊥ | ⊥ | 404 | |
| TaskGet() | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 200 | |
| TaskGet() | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 200 | |
| TaskGet() | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 200 | |
| TaskGet() | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 200 | |
| TaskCreate(t, l) | ⊥ | ⟨a, t+l, l, 0, Invoke, ∅⟩ | 200 | |
| TaskCreate(t, l) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 200 | |
| TaskCreate(t, l) | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 200 | |
| TaskCreate(t, l) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 200 | |
| TaskCreate(t, l) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 200 | |
| TaskAcquire(t, l, v) | ⊥ | ⊥ | 404 | |
| TaskAcquire(t, l, v) | ⟨p, e, l, v, c, R⟩ | ⟨a, t+l, l, v, c, R⟩ | 200 | |
| TaskAcquire(t, l, v') | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskAcquire(t, l, v) | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 409 | |
| TaskAcquire(t, l, v') | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 409 | |
| TaskAcquire(t, l, v) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskAcquire(t, l, v') | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskAcquire(t, l, v) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 409 | |
| TaskRelease(t, l, v) | ⊥ | ⊥ | 404 | |
| TaskRelease(t, l, v) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskRelease(t, l, v') | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskRelease(t, l, v) | ⟨a, e, l, v, Invoke, R⟩ | ⟨p, t+l, l, v+1, Invoke, R⟩ | 200 | Send(Invoke) |
| TaskRelease(t, l, v) | ⟨a, e, l, v, Resume, R⟩ | ⟨p, t+l, l, v+1, Resume, R⟩ | 200 | Send(Resume) |
| TaskRelease(t, l, v') | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 409 | |
| TaskRelease(t, l, v) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskRelease(t, l, v') | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskRelease(t, l, v) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v, P) | ⊥ | ⊥ | 404 | |
| TaskSuspend(v, P) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskSuspend(v', P) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskSuspend(v, P) | ⟨a, e, l, v, c, ∅⟩ : Pending(p) ∀p∈P | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 200 | |
| TaskSuspend(v, P) | ⟨a, e, l, v, c, ∅⟩ : Settled(p) ∃p∈P | ⟨a, e, l, v, Resume, ∅⟩ | 300 | |
| TaskSuspend(v, P) | ⟨a, e, l, v, c, c'::R'⟩ | ⟨a, e, l, v, c', R'⟩ | 300 | |
| TaskSuspend(v', P) | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 409 | |
| TaskSuspend(v, P) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v', P) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v, P) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 409 | |
| TaskFence(v) | ⊥ | ⊥ | 404 | |
| TaskFence(v) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskFence(v') | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskFence(v) | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 200 | |
| TaskFence(v') | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 409 | |
| TaskFence(v) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskFence(v') | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskFence(v) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 409 | |
| TaskHeartbeat(t, v) | ⊥ | ⊥ | 404 | |
| TaskHeartbeat(t, v) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 200 | |
| TaskHeartbeat(t, v') | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 200 | |
| TaskHeartbeat(t, v) | ⟨a, e, l, v, c, R⟩ | ⟨a, t+l, l, v, c, R⟩ | 200 | |
| TaskHeartbeat(t, v') | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 200 | |
| TaskHeartbeat(t, v) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 200 | |
| TaskHeartbeat(t, v') | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 200 | |
| TaskHeartbeat(t, v) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 200 | |
| TaskFulfill(v) | ⊥ | ⊥ | 404 | |
| TaskFulfill(v) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskFulfill(v') | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | 409 | |
| TaskFulfill(v) | ⟨a, e, l, v, c, R⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 200 | |
| TaskFulfill(v') | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | 409 | |
| TaskFulfill(v) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskFulfill(v') | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | 409 | |
| TaskFulfill(v) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | 409 | |
| Enqueue(Invoke, t, l) | ⊥ | ⟨p, t+l, l, 0, Invoke, ∅⟩ | | Send(Invoke) |
| Enqueue(Invoke, t, l) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R⟩ | | |
| Enqueue(Invoke, t, l) | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R⟩ | | |
| Enqueue(Invoke, t, l) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | | |
| Enqueue(Invoke, t, l) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | | |
| ~~Enqueue(Resume, t, l)~~ | ~~⊥~~ | ~~⊥~~ | | |
| Enqueue(Resume, t, l) | ⟨p, e, l, v, c, R⟩ | ⟨p, e, l, v, c, R::Resume⟩ | | |
| Enqueue(Resume, t, l) | ⟨a, e, l, v, c, R⟩ | ⟨a, e, l, v, c, R::Resume⟩ | | |
| Enqueue(Resume, t, l) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨p, t+l, l, v+1, Resume, ∅⟩ | | Send(Resume) |
| Enqueue(Resume, t, l) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | | |
| Tick(t) | ⊥ | ⊥ | | |
| Tick(t) | ⟨p, e, l, v, c, R⟩ : t < e | ⟨p, e, l, v, c, R⟩ | | |
| Tick(t) | ⟨p, e, l, v, Invoke, R⟩ : t ≥ e | ⟨p, t+l, l, v, Invoke, R⟩ | | Send(Invoke) |
| Tick(t) | ⟨p, e, l, v, Resume, R⟩ : t ≥ e | ⟨p, t+l, l, v, Resume, R⟩ | | Send(Resume) |
| Tick(t) | ⟨a, e, l, v, c, R⟩ : t < e | ⟨a, e, l, v, c, R⟩ | | |
| Tick(t) | ⟨a, e, l, v, Invoke, R⟩ : t ≥ e | ⟨p, t+l, l, v+1, Invoke, R⟩ | | Send(Invoke) |
| Tick(t) | ⟨a, e, l, v, Resume, R⟩ : t ≥ e | ⟨p, t+l, l, v+1, Resume, R⟩ | | Send(Resume) |
| Tick(t) | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | ⟨s, ⊥, ⊥, v, ⊥, ∅⟩ | | |
| Tick(t) | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ⊥, ⊥, ∅⟩ | | |
