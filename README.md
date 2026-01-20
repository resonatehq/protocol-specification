# Protocol Specification

**request**
```ts
type Req =
  | PromiseGetReq
  | PromiseCreateReq
  | PromiseSettleReq
  | PromiseRegisterReq
  | PromiseSubscribeReq
  | TaskGetReq
  | TaskCreateReq
  | TaskAcquireReq
  | TaskSuspendReq
  | TaskFulfillReq
  | TaskReleaseReq
  | TaskFenceReq
  | TaskHeartbeatReq
  | ScheduleGetReq
  | ScheduleCreateReq
  | ScheduleDeleteReq
```

**response**
```ts
type Res =
  | PromiseGetRes
  | PromiseCreateRes
  | PromiseSettleRes
  | PromiseRegisterRes
  | PromiseSubscribeRes
  | TaskGetRes
  | TaskCreateRes
  | TaskAcquireRes
  | TaskSuspendRes
  | TaskFulfillRes
  | TaskReleaseRes
  | TaskFenceRes
  | TaskHeartbeatRes
  | ScheduleGetRes
  | ScheduleCreateRes
  | ScheduleDeleteRes
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
  param: { headers: { [key: string]: string }; data: string };
  value: { headers: { [key: string]: string }; data: string };
  tags: { [key: string]: string };
  timeoutAt: number;
  createdAt: number;
  settledAt?: number;
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
    corrId: string;
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
    param: { headers: { [key: string]: string }; data: string };
    tags: { [key: string]: string };
    timeoutAt: number;
  };
}
```

**response**
```ts
interface PromiseCreateRes {
  kind: "promise.create";
  head: {
    corrId: string;
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
    value: { headers: { [key: string]: string }; data: string };
  };
}
```

**response**
```ts
interface PromiseSettleRes {
  kind: "promise.settle";
  head: {
    corrId: string;
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
    corrId: string;
    status: 200;
  };
  data: {
    promise: Promise;
  };
}
```

### Subscribe

**request**
```ts
interface PromiseSubscribeReq {
  kind: "promise.subscribe";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    awaited: string;
    address: string;
  };
}
```

**response**
```ts
interface PromiseSubscribeRes {
  kind: "promise.subscribe";
  head: {
    corrId: string;
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
    corrId: string;
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
    corrId: string;
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
    corrId: string;
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
    corrId: string;
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
    corrId: string;
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
  data: {
    id: string;
    version: number;
  };
}
```

**response**
```ts
interface TaskReleaseRes {
  kind: "task.release";
  head: {
    corrId: string;
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
    corrId: string;
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
    corrId: string;
    status: 200;
  };
}
```

## Schedules

### Types

**Schedule**
```ts
interface Schedule {
  id: string;
  cron: string;
  promiseId: string;
  promiseTimeout: number;
  promiseParam: { headers: { [key: string]: string }; data: string };
  promiseTags: { [key: string]: string };
  createdAt: number;
  nextRunAt: number;
  lastRunAt?: number;
}
```

### Get

**request**
```ts
interface ScheduleGetReq {
  kind: "schedule.get";
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
interface ScheduleGetRes {
  kind: "schedule.get";
  head: {
    corrId: string;
    status: 200;
  };
  data: {
    schedule: Schedule;
  };
}
```

### Create

**request**
```ts
interface ScheduleCreateReq {
  kind: "schedule.create";
  head: {
    auth?: string;
    corrId: string;
  };
  data: {
    id: string;
    cron: string;
    promiseId: string;
    promiseTimeout: number;
    promiseParam: { headers: { [key: string]: string }; data: string };
    promiseTags: { [key: string]: string };
  };
}
```

**response**
```ts
interface ScheduleCreateRes {
  kind: "schedule.create";
  head: {
    corrId: string;
    status: 200;
  };
  data: {
    schedule: Schedule;
  };
}
```

### Delete

**request**
```ts
interface ScheduleDeleteReq {
  kind: "schedule.delete";
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
interface ScheduleDeleteRes {
  kind: "schedule.delete";
  head: {
    corrId: string;
    status: 200;
  };
}
```

## Messages

```ts
type Message = InvokeMessage | ResumeMessage | NotifyMessage;

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

interface NotifyMessage {
  kind: "notify";
  head: {};
  data: {
    promise: Promise;
  };
}
```
