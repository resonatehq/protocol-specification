# Protocol Specification

**request**
```ts
type Req =
  | PromiseGetReq
  | PromiseCreateReq
  | PromiseSettleReq
  | PromiseRegisterReq
  | TaskGetReq
  | TaskCreateReq
  | TaskAcquireReq
  | TaskSuspendReq
  | TaskFulfillReq
  | TaskReleaseReq
  | TaskFenceReq
  | TaskHeartbeatReq
```

**response**
```ts
type Res =
  | PromiseGetRes
  | PromiseCreateRes
  | PromiseSettleRes
  | PromiseRegisterRes
  | TaskGetRes
  | TaskCreateRes
  | TaskAcquireRes
  | TaskSuspendRes
  | TaskFulfillRes
  | TaskReleaseRes
  | TaskFenceRes
  | TaskHeartbeatRes
  | Error

interface Error {
  kind: string;
  head: {
    corrId: string;
    status: 400 | 404 | 429 | 500;
  };
  data: string;
}
```

## Promises

### Types

**Promise**
```ts
interface Promise {
  id: string;
  state: "pending" | "resolved" | "rejected" | "rejected_canceled" | "rejected_timedout";
  param: { headers: [key: string]: string; data: string; }
  value: { headers: [key: string]: string; data: string; }
  tags: [key: string]: string;
  timeoutOn: number;
  createdOn: number;
  settledOn: number;
}
```

### Get

**request**
```ts
interface PromiseGetReq {
  kind: "promise.get";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
  };
}
```

**response**
```ts
interface PromiseGetRes {
  kind: "promise.get",
  head: {
    status: 200;
  };
  data: {
    promise: Promise;
  };
}
```

### Create

**request**
```ts
interface PromiseCreateReq {
  kind: "promise.create";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    param: { headers: [key: string]: string; data: string };
    tags: [key: string]: string;
    timeoutOn: number;
  };
}
```

**response**
```ts
interface PromiseCreateRes {
  kind: "promise.create";
  head: {
    status: 200;
  };
  data: {
    promise: Promise;
  };
}
```

### Settle

**request**
```ts
interface PromiseSettleReq {
  kind: "promise.settle";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    state: "resolved" | "rejected" | "rejected_canceled";
    value: { headers: [key: string]: string; data: string };
  };
}
```

**response**
```ts
interface PromiseSettleRes {
  kind: "promise.settle";
  head: {
    status: 200;
  };
  data: {
    promise: Promise;
  };
}
```

### Register

**request**
```ts
interface PromiseRegisterReq {
  kind: "promise.register";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    awaiter: string;
    awaited: string;
  };
}
```

**response**
```ts
interface PromiseRegisterRes {
  kind: "promise.register";
  head: {
    status: 200;
  };
  data: {
    promise: Promise;
  };
}
```

## Tasks

### Types

**Task**
```ts
interface Task {
  id: string;
  version: number;
}
```

### Get

**request**
```ts
interface TaskGetReq {
  kind: "task.get";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
  };
}
```

**response**
```ts
interface TaskGetRes {
  kind: "task.get";
  head: {
    status: 200;
  };
  data: {
    task: Task;
  };
}
```

### Create

**request**
```ts
interface TaskCreateReq {
  kind: "task.create";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    pid: string;
    ttl: number;
    action: PromiseCreateReq;
  };
}
```

**response**
```ts
interface TaskCreateRes {
  kind: "task.create";
  head: {
    status: 200;
  };
  data: {
    task: Task;
    promise: Promise;
  };
}
```

### Acquire

**request**
```ts
interface TaskAcquireReq {
  kind: "task.acquire";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    version: number;
    pid: string;
    ttl: number;
  };
}
```

**response**
```ts
interface TaskAcquireRes {
  kind: "task.acquire";
  head: {
    status: 200;
  };
  data:
    | { kind: "invoke"; data: { invoked: Promise } }
    | { kind: "resume"; data: { invoked: Promise; awaited: Promise } };
}
```

### Suspend

**request**
```ts
interface TaskSuspendReq {
  kind: "task.suspend";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    version: number;
    actions: PromiseRegisterReq[];
  };
}
```

**response**
```ts
interface TaskSuspendRes {
  kind: "task.suspend";
  head: {
    status: 200 | 300;
  };
}
```

### Fulfill

**request**
```ts
interface TaskFulfillReq {
  kind: "task.fulfill";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    version: number;
    action: PromiseSettleReq;
  };
}
```

**response**
```ts
interface TaskFulfillRes {
  kind: "task.fulfill";
  head: {
    status: 200;
  };
  data: {
    promise: Promise;
  };
}
```

### Release

**request**
```ts
interface TaskReleaseReq {
  kind: "task.release";
  head: {
    auth?: string;
    corrId: string;
  };
}
```

**response**
```ts
interface TaskReleaseRes {
  kind: "task.release";
  head: {
    status: 200;
  };
}
```

### Fence

**request**
```ts
interface TaskFenceReq {
  kind: "task.fence";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    version: number;
    action: PromiseCreateReq | PromiseSettleReq;
  };
}
```

**response**
```ts
interface TaskFenceRes {
  kind: "task.fence";
  head: {
    status: 200;
  };
  data: {
    action: PromiseCreateRes | PromiseSettleRes;
  };
}
```

### Heartbeat

**request**
```ts
interface TaskHeartbeatReq {
  kind: "task.heartbeat";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    pid: string;
    tasks: Task[];
  };
}
```

**response**
```ts
interface TaskHeartbeatRes {
  kind: "task.heartbeat";
  head: {
    status: 200;
  };
}
```

## Messages

```ts
type Message = InvokeMessage | ResumeMessage;

interface InvokeMessage {
  kind: "invoke";
  head: {};
  data: {
    task: Task;
  };
}

interface ResumeMessage {
  kind: "resume";
  head: {};
  data: {
    task: Task;
  };
}
```
