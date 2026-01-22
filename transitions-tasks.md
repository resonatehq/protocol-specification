# Task State Transitions

## States

| Symbol | Description |
|--------|-------------|
| ⊥ | Task does not exist |
| ⟨p, v, c, C⟩ | Pending, version, current, pending |
| ⟨a, v, c, C⟩ | Acquired, version, current, pending |
| ⟨s, v, ⊥, ∅⟩ | Suspended, version |
| ⟨f, ⊥, ⊥, ∅⟩ | Fulfilled |

## Operations

```
TaskGet()
TaskCreate()
TaskAcquire(v)
TaskSuspend(v, P)
TaskFulfill(v)
TaskRelease(v)
Enqueue(Invoke)
Enqueue(Resume)
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
| TaskGet() | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 200 | |
| TaskGet() | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 200 | |
| TaskGet() | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 200 | |
| TaskGet() | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 200 | |
| TaskCreate() | ⊥ | ⟨a, 0, Invoke, ∅⟩ | 200 | |
| TaskCreate() | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 200 | |
| TaskCreate() | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 200 | |
| TaskCreate() | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 200 | |
| TaskCreate() | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 200 | |
| TaskAcquire(v) | ⊥ | ⊥ | 404 | |
| TaskAcquire(v) | ⟨p, v, c, C⟩ | ⟨a, v, c, C⟩ | 200 | |
| TaskAcquire(v') | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskAcquire(v) | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 409 | |
| TaskAcquire(v') | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 409 | |
| TaskAcquire(v) | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskAcquire(v') | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskAcquire(v) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 409 | |
| TaskAcquire(v') | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v, P) | ⊥ | ⊥ | 404 | |
| TaskSuspend(v, P) | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskSuspend(v', P) | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskSuspend(v, P) | ⟨a, v, c, ∅⟩ ∀p∈P : Pending(p) | ⟨s, v, ⊥, ∅⟩ | 200 | |
| TaskSuspend(v, P) | ⟨a, v, c, ∅⟩ ∃p∈P : Settled(p) | ⟨a, v, Resume, ∅⟩ | 300 | |
| TaskSuspend(v, P) | ⟨a, v, c, c'::C'⟩ | ⟨a, v, c', C'⟩ | 300 | |
| TaskSuspend(v', P) | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 409 | |
| TaskSuspend(v, P) | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v', P) | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v, P) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 409 | |
| TaskSuspend(v', P) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 409 | |
| TaskFulfill(v) | ⊥ | ⊥ | 404 | |
| TaskFulfill(v) | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskFulfill(v') | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskFulfill(v) | ⟨a, v, c, C⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 200 | |
| TaskFulfill(v') | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 409 | |
| TaskFulfill(v) | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskFulfill(v') | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskFulfill(v) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 409 | |
| TaskRelease(v) | ⊥ | ⊥ | 404 | |
| TaskRelease(v) | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskRelease(v') | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | 409 | |
| TaskRelease(v) | ⟨a, v, Invoke, C⟩ | ⟨p, v+1, Invoke, C⟩ | 200 | Send(Invoke) |
| TaskRelease(v) | ⟨a, v, Resume, C⟩ | ⟨p, v+1, Resume, C⟩ | 200 | Send(Resume) |
| TaskRelease(v') | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | 409 | |
| TaskRelease(v) | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskRelease(v') | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | 409 | |
| TaskRelease(v) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | 409 | |
| Enqueue(Invoke) | ⊥ | ⟨p, 0, Invoke, ∅⟩ | - | Send(Invoke) |
| Enqueue(Invoke) | ⟨p, v, c, C⟩ | ⟨p, v, c, C⟩ | - | |
| Enqueue(Invoke) | ⟨a, v, c, C⟩ | ⟨a, v, c, C⟩ | - | |
| Enqueue(Invoke) | ⟨s, v, ⊥, ∅⟩ | ⟨s, v, ⊥, ∅⟩ | - | |
| Enqueue(Invoke) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | - | |
| Enqueue(Resume) | ⊥ | ⊥ | - | |
| Enqueue(Resume) | ⟨p, v, c, C⟩ | ⟨p, v, c, C::{Resume}⟩ | - | |
| Enqueue(Resume) | ⟨a, v, c, C⟩ | ⟨a, v, c, C::{Resume}⟩ | - | |
| Enqueue(Resume) | ⟨s, v, ⊥, ∅⟩ | ⟨p, v+1, Resume, ∅⟩ | - | Send(Resume) |
| Enqueue(Resume) | ⟨f, ⊥, ⊥, ∅⟩ | ⟨f, ⊥, ⊥, ∅⟩ | - | |
