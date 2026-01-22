# Promise State Transitions

## States

| Symbol | Description |
|--------|-------------|
| ⊥ | Promise does not exist |
| ⟨p, ⊥, C, S⟩ | Pending, no target, callbacks, subscriptions |
| ⟨p, t, C, S⟩ | Pending, target, callbacks, subscriptions |
| ⟨r, ⊥, ∅, ∅⟩ | Resolved |
| ⟨x, ⊥, ∅, ∅⟩ | Rejected |
| ⟨c, ⊥, ∅, ∅⟩ | Rejected (canceled) |
| ⟨t, ⊥, ∅, ∅⟩ | Rejected (timedout) |

## Operations

```
PromiseGet()
PromiseCreate()
PromiseCreate(t)
PromiseSettle(s)
PromiseSettle(x)
PromiseSettle(c)
PromiseRegister(c)
PromiseSubscribe(s)
```

## Side Effects

| Side Effect | Description |
|-------------|-------------|
| Enqueue(Invoke) | Enqueue invoke |
| Enqueue(Resume) | Enqueue resume |
| Send(Notify) | Send notify |

## Transitions

| Operation | Current State | Next State | Result | Side Effect(s) |
|-----------|---------------|------------|--------|----------------|
| PromiseGet() | ⊥ | ⊥ | 404 | |
| PromiseGet() | ⟨p, ⊥, C, S⟩ | ⟨p, ⊥, C, S⟩ | 200 | |
| PromiseGet() | ⟨p, t, C, S⟩ | ⟨p, t, C, S⟩ | 200 | |
| PromiseGet() | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseGet() | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseGet() | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseGet() | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate() | ⊥ | ⟨p, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate() | ⟨p, ⊥, C, S⟩ | ⟨p, ⊥, C, S⟩ | 200 | |
| PromiseCreate() | ⟨p, t, C, S⟩ | ⟨p, t, C, S⟩ | 200 | |
| PromiseCreate() | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate() | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate() | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate() | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate(t) | ⊥ | ⟨p, t, ∅, ∅⟩ | 200 | Enqueue(Invoke) |
| PromiseCreate(t) | ⟨p, ⊥, C, S⟩ | ⟨p, ⊥, C, S⟩ | 200 | |
| PromiseCreate(t) | ⟨p, t, C, S⟩ | ⟨p, t, C, S⟩ | 200 | |
| PromiseCreate(t) | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate(t) | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate(t) | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseCreate(t) | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(r) | ⊥ | ⊥ | 404 | |
| PromiseSettle(r) | ⟨p, ⊥, C, S⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | Enqueue(Resume) ∀c∈C, Send(Notify) ∀s∈S |
| PromiseSettle(r) | ⟨p, t, C, S⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | Enqueue(Resume) ∀c∈C, Send(Notify) ∀s∈S |
| PromiseSettle(r) | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(r) | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(r) | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(r) | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(x) | ⊥ | ⊥ | 404 | |
| PromiseSettle(x) | ⟨p, ⊥, C, S⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | Enqueue(Resume) ∀c∈C, Send(Notify) ∀s∈S |
| PromiseSettle(x) | ⟨p, t, C, S⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | Enqueue(Resume) ∀c∈C, Send(Notify) ∀s∈S |
| PromiseSettle(x) | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(x) | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(x) | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(x) | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(c) | ⊥ | ⊥ | 404 | |
| PromiseSettle(c) | ⟨p, ⊥, C, S⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | Enqueue(Resume) ∀c∈C, Send(Notify) ∀s∈S |
| PromiseSettle(c) | ⟨p, t, C, S⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | Enqueue(Resume) ∀c∈C, Send(Notify) ∀s∈S |
| PromiseSettle(c) | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(c) | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(c) | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSettle(c) | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseRegister(c) | ⊥ | ⊥ | 404 | |
| PromiseRegister(c) | ⟨p, ⊥, C, S⟩ | ⟨p, ⊥, C::c, S⟩ | 200 | |
| PromiseRegister(c) | ⟨p, t, C, S⟩ | ⟨p, t, C::c, S⟩ | 200 | |
| PromiseRegister(c) | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseRegister(c) | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseRegister(c) | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseRegister(c) | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSubscribe(s) | ⊥ | ⊥ | 404 | |
| PromiseSubscribe(s) | ⟨p, ⊥, C, S⟩ | ⟨p, ⊥, C, S::s⟩ | 200 | |
| PromiseSubscribe(s) | ⟨p, t, C, S⟩ | ⟨p, t, C, S::s⟩ | 200 | |
| PromiseSubscribe(s) | ⟨r, ⊥, ∅, ∅⟩ | ⟨r, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSubscribe(s) | ⟨x, ⊥, ∅, ∅⟩ | ⟨x, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSubscribe(s) | ⟨c, ⊥, ∅, ∅⟩ | ⟨c, ⊥, ∅, ∅⟩ | 200 | |
| PromiseSubscribe(s) | ⟨t, ⊥, ∅, ∅⟩ | ⟨t, ⊥, ∅, ∅⟩ | 200 | |
