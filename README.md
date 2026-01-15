# Protocol Specification

## Promises

### Get

**request**
```
{
  "kind": "promise.get",
  "head": {},
  "data": { "id": "p_123" }
}
```

**response**
```
{
  "kind": "promise.get",
  "head": { "status": 200 },
  "data": { "promise": {} }
}
```

### Create

**request**
```
{
  "kind": "promise.create",
  "head": {},
  "data": {
    "id": "p_123",
    "param": { "headers": {}, "data": "" },
    "timeout": 60000,
    "tags": {}
  }
}
```

**response**
```
{
  "kind": "promise.create",
  "head": { "status": 201 | 200 },
  "data": { "promise": {} }
}
```

### Settle

**request**
```
{
  "kind": "promise.settle",
  "head": {},
  "data": {
    "id": "p_123",
    "state": "RESOLVED" | "REJECTED" | "REJECTED_CANCELED",
    "value": { "headers": {}, "data": "" }
  }
}
```

**response**
```
{
  "kind": "promise.settle",
  "head": { "status": 201 },
  "data": { "promise": {} }
}
```

### Register

**request**
```
{
  "kind": "promise.register",
  "head": {},
  "data": {
    "awaiter": "p_123",
    "awaited": "p_456",
  }
}
```

**response**
```
{
  "kind": "promise.register",
  "head": { "status": 201 | 200 },
  "data": {
    "promise": {}
  }
}
```

## Tasks

### Get

**request**
```
{
  "kind": "task.get",
  "head": {},
  "data": {
    "id": "p_123"
  }
}
```

**response**
```
{
  "kind": "task.get",
  "head": { "status": 200 },
  "data": {
    "task": {}
  }
}
```

### Create

**request**
```
{
  "kind": "task.create",
  "head": {},
  "data": {
    "pid": "pid_123",
    "ttl": 30000
    "action": promise.create
  }
}
```

**response**
```
{
  "kind": "task.create",
  "head": { "status": 201 },
  "data": {
    "task": {},
    "promise": {}
  }
}
```

### Acquire

**request**
```
{
  "kind": "task.acquire",
  "head": {},
  "data": {
    "id": "t_123",
    "version": 1,
    "pid": "pid_123",
    "ttl": 30000
  }
}
```

**response**
```
{
  "kind": "task.acquire",
  "head": { "status": 201 },
  "data": {
    "kind": "invoke",
    "data": {
      "invoked": {}
    }
  }
}
```

```
{
  "kind": "task.acquire",
  "head": { "status": 201 },
  "data": {
    "kind": "resume",
    "data": {
      "invoked": {},
      "awaited": {}
    }
  }
}
```

### Suspend

**request**
```
{
  "kind": "task.suspend",
  "head": {},
  "data": {
    "id": "t_1",
    "version": 1,
    "actions": [ promise.register, ... ]
  }
}
```

**response**
```
{
  "kind": "task.suspend",
  "head": { "status": 201 | 300 },
  "data": {}
}
```

### Fulfill

**request**
```
{
  "kind": "task.complete",
  "head": {},
  "data": {
    "id": "t_123",
    "version": 1,
    "action": promise.complete
  }
}
```

**response**
```
{
  "kind": "task.complete",
  "head": { "status": 201 | 200 },
  "data": { 
    "promise": {}
  }
}
```

### Release

**request**
```
{
  "kind": "task.release",
  "head": {},
  "data": {
    "id": "t_123",
    "version": 0
  }
}
```

**response**
```
{
  "kind": "task.release",
  "head": { "status": 201 },
  "data": {}
}
```

### Fence

**request**
```
{
  "kind": "task.fence",
  "head": {},
  "data": {
    "id": "",
    "version": 1,
    "action": promise.create | promise.settle
  }
}
```

**response**
```
{
  "kind": "task.fence",
  "head": { "status": 200 },
  "data": {
    "action": {}
  }
}
```

### Heartbeat

**request**
```
{
  "kind": "task.heartbeat",
  "head": {},
  "data": {
    "pid": "pid_123"
  }
}
```

**response**
```
{
  "kind": "task.heartbeat",
  "head": { "status": 200 },
  "data": {}
}
```

## Messages

```
{
  "kind": "invoke" | "resume",
  "head": {},
  "data": {
    "task": {
      "id": "",
      "counter": 1
    }
  }
}
```
