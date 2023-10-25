
컨테이너간 통신

컨테이너 안에서 외부 api 혹은 다른 app과 통신하려면 어떻게 해야할까. 
그리고 다른 컨테이너 간 통신은 어떻게 할까.

즉 다중 컨테이너로 작업하는 것은 빈번하다. 

![[network1.png]]

### 영화 api 예제

4개의 엔드 포인트고, post 요청에 mongo DB가 엮여있다. 돌려보면, 

```
$ docker logs favorites
(node:1) [MONGODB DRIVER] Warning: Current Server Discovery and Monitoring engine is deprecated, and will be removed in a future version. To use the new Server Discover and Monitoring engine, pass option { useUnifiedTopology: true } to the MongoClient constructor.
(Use `node --trace-warnings ...` to show where the warning was created)
MongoNetworkError: failed to connect to server [localhost:27017] on first connect [Error: connect ECONNREFUSED 127.0.0.1:27017
    at TCPConnectWrap.afterConnect [as oncomplete] (node:net:1595:16) {
  name: 'MongoNetworkError'
}]
    at Pool.<anonymous> (/app/node_modules/mongodb/lib/core/topologies/server.js:441:11)
    at Pool.emit (node:events:515:28)
    at /app/node_modules/mongodb/lib/core/connection/pool.js:564:14
    at /app/node_modules/mongodb/lib/core/connection/pool.js:1000:11
    at /app/node_modules/mongodb/lib/core/connection/connect.js:32:7
    at callback (/app/node_modules/mongodb/lib/core/connection/connect.js:300:5)
    at Socket.<anonymous> (/app/node_modules/mongodb/lib/core/connection/connect.js:330:7)
    at Object.onceWrapper (node:events:630:26)
    at Socket.emit (node:events:515:28)
    at emitErrorNT (node:internal/streams/destroy:151:8)
    at emitErrorCloseNT (node:internal/streams/destroy:116:3)
    at process.processTicksAndRejections (node:internal/process/task_queues:82:21)

```

