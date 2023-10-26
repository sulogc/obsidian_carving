
컨테이너간 통신

컨테이너 안에서 외부 api 혹은 다른 app과 통신하려면 어떻게 해야할까. 
그리고 다른 컨테이너 간 통신은 어떻게 할까.

즉 다중 컨테이너로 작업하는 것은 빈번하다. 

![[network1.png]]

## 영화 api 예제

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

몽고 db 부분을 지우고 하면 잘 돌아간다. 

### 로컬의 몽고 DB와  연결

호스트의 `localhost`은 컨테이너에서  `host.docker.internal` 로 접근 가능하다. 

### 컨테이너 몽고 DB와 연결

몽고DB의 경우 도커 허브에 이미 있기 때문에 
`docker run mongo` 를 하면 된다. 

1. IPAdress
그리고 나서 해당 컨테이너를 보기 위해 inspect를 하면, NetworkSettings에 IPAdress를 볼 수 있다.
```
$ docker container inspect mongodb
[
    ...
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "6097412bde10c4a91e27b105864959415b246f1c3a72adef5cdc506cce72c57b",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {
                "27017/tcp": null
            },
            "SandboxKey": "/var/run/docker/netns/6097412bde10",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "ea267ec2353225d4db27c50d7bc08e8a9b230fb80e054298bde6c1c33f4e79f8",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "7c31ab53329fcd6ea0d597abd3e33f7ca4de8d132682f76cd3b2439fd0aaff0b",
                    "EndpointID": "ea267ec2353225d4db27c50d7bc08e8a9b230fb80e054298bde6c1c33f4e79f8",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```

해당 값을 node App에서 몽고 커넥트 부분에 넣으면 통신이 된다. 

하지만, 주소가 변경 될 때마다 하드 코딩 해 놔야하는 단점이 있다. 

2. Container Network

컨테이너간 통신을 위해 같은 네트워크로 구성할 수 있다.
`--network`

단, 먼저 네트워크를 만들어 줘야한다.
`docker network create favorites-net`

다음과 같이 확인 가능.
`docker network ls` 

다음 구성한 네트워크에 도커런을 해주자
`docker run -d --name mongodb --network favorites-net mongo`

```
그리고 몽고DB는 다른 도커 컨테이너속 APP만 접근 하기 때문에 포트 연결을 안해줘도 된다.   
```

네트워크가 같은 경우 컨테이너 이름으로 연결할 수 있다.
`'mongodb://mongodb:27017/swfavorites',`



