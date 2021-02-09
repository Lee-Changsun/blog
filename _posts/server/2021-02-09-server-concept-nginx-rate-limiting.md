--- 
layout: single
classes: wide
title: ""
header:
  overlay_image: /img/server-bg.jpg
excerpt: ''
author: "window_for_sun"
header-style: text
categories :
  - Server
tags:
  - Server
  - Rate Limiting
  - Nginx
---  

## Rate Limiting
`Rate Limiting` 이란 말 그대로 요청을 정해진 가용량 만큼만 받고, 
나머지는 에러 처리를 하는 것을 의미한다. 
무한한 자원을 가진 물리 머신은 존재하지 않고, 
그에 따라 항상 서버가 받을 수 있는 요청의 최대치는 존재하게 된다.  
그리고 최대치 이상으로 요청이 오게되면 서버가 죽어버리거나 가용할 수 없는 상태가 된다.  

이렇게 `DDos` 공격과 같이 많은 요청이 한번이 몰릴때 `Rate Limiting` 을 이용한다면, 이를 정해진 가용량 만큼만 받고 
나머지 요청에 대해서는 적절한 에러 응답을 내려 주는 방법으로 보다 안정적인 서비스를 구성할 수 있다.  

## Rate Limiting 의 원리
`Nginx` 의 `Rate Limting` 동작은 대역폭이 제한될 때, 
`Burst`(초과분)를 처리하기 위해 널리 사용되는 [Leaky Bucket Algorithm(https://en.wikipedia.org/wiki/Leaky_bucket) 
을 사용한다. 

![그림 1]({{site.baseurl}}/img/server/nginx-rate-limiting-1.jfif)


위 알고리즘은 주로 구멍이 뚫린 양동이에 물을 쏟아 붓는 비유를 많이 하는데, 
물을 붓는 속도와 양이 너무 크면 구멍으로 물이 빠져 나가기전에 양동이를 넘처 흐르게 될 것이다. 
이를 `Nginx` 에 비유하면 물은 클라이언트의 요청이되고, 
양동이는 `FIFO` 스케쥴링 알고리즘에 따라 요청을 처리를 위해 대기하는 버퍼 공간을 의미한다. 
그리고 구멍으로 빠져나가는 물은 서버에서 처리하기 위해 버퍼에서 나가는 요청이고, 
넘쳐 흐르는 물은 서비스되지 못하고 버려지는 요청을 의미한다.  

## Rate Limiting 설정
아래는 간단한 `Rate Limiting` 설정의 예시이다. 

```
limit_req_zone $binary_remote_addr zone=mylimit:10m rate=100r/s;

server {
    location /test {
        limit_req zone=mylimit;

        proxy_pass http://backend;
    }
}
```  

`Rate Limiting` 의 기본적인 설정은 크게 `limit_req_zone` 과 
`limit_req` 로 구성된 것을 확인할 수 있다. 
`limit_req_zone` 은 `Rate Limting` 에 대한 정의를 하는 부분이고, 
`limit_req` 는 특정 경로에 대해 `Rate Limiting` 을 적용하는 역할을 한다.  

`limit_req_zone <key> <zone> <rate>` 와 같이 크게 3가지로 구성되는 그에 대한 설명은 아래와 같다. 
- `<key>` : `Rate Limting` 이 적용되는 기준 값을 설정한다. 
위 예에서는 `Nginx` 변수인 `$binary_remote_addr`(이진 클라이언트 IP주소)를 사용했다. 
즉 클라이언트 IP 를 기준으로 요청 제한을 수행하게 된다. 
추가로 `$remote_addr` 도 설정 할 수 있다.
- `<zone>` : `Rate Limiting` 적용 기준이 되는 `IP`를 저장하는 공유 메모리에 대한 정의와 `URL` 에 따른 처리 빈도를 정의한다. 
공유 메모리라는 것은 `Nginx` 에 구동되는 `worker_processor` 간 공유 되는 메모리를 의미한다. 
그리고 `zone=<name>:<size>` 와 같이 다시 2개로 구성되는데, 
`<name>` 은 공유 메모리의 이름을 정의하고, `<size>` 해당 공유 메모리의 크기를 정의한다. 
여기서 `1m` 은 `1MB` 를 의미하고 1600개의 `IP` 정보를 저장 할 수 있다. 
그러므로 예시 설정에서는 160000개 `IP`를 저장할 수 있는 공간을 할당 한 것이다. 
>모든 공간이 차면 가장 오래된 `IP` 를 삭제하고, 더 이상 가용한 공간이 없는 경우 `503` 에러를 응답한다. 
- `<rate>` : 최대 요청 속도를 설정한다. 
예시 설정에서는 초당 100개의 요청을 초과할 수 없도록 설정된 상태이다. 
`Nginx` 는 요청을 미리초 단위로 추적하므로 현재 설정은 `10ms` 마다 1개의 요청을 받을 수 있다고 해석할 수 있다. 
이후 설정할 `Burst` 에 대한 설정이 없기 때문에 이전 요청보다 `10ms` 이내 도착한 요청은 거부 된다. 

`limit_req_zone` 은 `Rate Limiting` 관련 선언만 할 뿐, 
실제 `Rate Limiting` 동작을 수행하도록 하는 구문은 아니다. 
`Rate Limiting` 동작을 위해서는 `server`, `location` 블록에 `limit_req` 
구문을 통해 공유 메모리 이름과 함께 명시적으로 작성해 주어야 한다.  

`/test` 경로에 대해서 고유한 클라이언트 `IP` 에 대해 초당 100개의 요청만 가능하다. 
다시 말하면, `/test` 경로는 이전 `IP` 주소의 요청으로 부터 `10ms` 이내 요청은 할 수 없다.  


### 테스트
이후 진행하는 테스트를 포함해서 테스트는 `JMeter` 툴을 사용한다. 
그리고 테스트 환경은 `kubernetes` 르 사용해서 구성한다.  

- `basic-configmap.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: basic-configmap
  namespace: default
data:
  nginx.conf: |
    user  nginx;
    worker_processes  2;

    error_log  /var/log/nginx/error.log warn;
    pid        /var/run/nginx.pid;

    events {
        worker_connections  1024;
    }

    http {
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';

        access_log  /var/log/nginx/access.log  main;

        limit_req_zone $binary_remote_addr zone=mylimit:10m rate=100r/s;

        server {
            listen 80;
            server_name localhost;

            location /status {
                limit_req zone=mylimit;
                stub_status on;
                allow all;
                deny all;
            }
        }
    }
```  

- `basic-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: basic-rate-limiting
  labels:
    app: basic-rate-limiting
    type: rate-limiting
spec:
  containers:
    - name: basic-rate-limiting
      image: nginx:1.19
      ports:
        - containerPort: 80
      volumeMounts:
        - name: nginx-conf
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
          readOnly: true
  volumes:
    - name: nginx-conf
      configMap:
        name: basic-configmap
        items:
          - key: nginx.conf
            path: nginx.conf
```  

- `service.yaml`
    - 이후 테스트에서도 공통으로 사용한다. 
    
```yaml
apiVersion: v1
kind: Service
metadata:
  name: rate-limiting-service
spec:
  type: NodePort
  selector:
    type: rate-limiting
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 32222
```  

작성한 모든 템플릿을 `Kubernetes` 클러스터에 아래 명령으로 적용한다. 

```bash
$ kubectl apply -f basic-configmap.yaml
configmap/basic-configmap created
$ kubectl apply -f basic-pod.yaml
pod/basic-rate-limiting created
$ kubectl apply -f service.yaml
service/rate-limiting-service created
$ kubectl get configmap,pod,svc
NAME                             DATA   AGE
configmap/basic-configmap        1      20s

NAME                      READY   STATUS    RESTARTS   AGE
pod/basic-rate-limiting   1/1     Running   0          14s

NAME                            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/rate-limiting-service   NodePort    10.105.130.60   <none>        80:32222/TCP   8s
```  

`localhost:32222/status` 로 요청을 보내면 아래와 같이 `Nginx` 상태 정보가 출력된다. 

```bash
$ curl localhost:32222/status
Active connections: 1
server accepts handled requests
 1 1 1
Reading: 0 Writing: 1 Waiting: 0
```  

`JMeter` 툴의 설정은 아래와 같다. 
- `Number of Thread` : 100
- `Ramp-up period`: 10
- `Loop Count` : 100

테스트 결과에 따른 `TPS` 그래프는 아래와 같다. 

![그림 1]({{site.baseurl}}/img/server/nginx-rate-limiting-basic-tps.png)

응답시간에 따른 그래프는 아래와 같다. 

![그림 1]({{site.baseurl}}/img/server/nginx-rate-limiting-basic-rtt.png)

붉은 선이 `TPS` 100 을 넘지 않는 것을 확인 할 수 있고, 
초과하는 요청은 모두 실패 처리가 되는 것을 확인 할 수 있다. 
응답시간 같은 경우 `Nginx` 서버가 안정적으로 응답할 수 있는 `TPS` 로 `Rate Limiting` 을 설정 했다면, 
일정한 시간을 보이는 것을 확인 할 수 있다.  

`error.log` 를 확인하면 `Rate Limiting` 의 응답은 아래와 같이 에러 로그가 남게 된다. 

```
[error] 29#29: *100 limiting requests, excess: 1.000 by zone "mylimit", client: 192.168.65.3, server: localhost, request: "GET /status HTTP/1.1", host: "localhost:32222"
```  

그리고 응답 상태코드는 기본 503으로 내려 준다. 

```
192.168.65.3 - - "GET /status HTTP/1.1" 503 197 "-" "Apache-HttpClient/4.5.12 (Java/11.0.9)" "-"
```  

## Burst
만약 `100r/s` 로 `Rate Limiting` 이 설정된다면 1초에 100개의 요청이면서, 
`10ms` 당 1개의 요청 처리가 가능하다고 설명했었다. 
그런데 1초에는 100개의 요청이 들어왔지만, 
순간 `10ms` 에 2개의 요청이 들어온 상황이라면 하나의 요청은 `Rate Limting` 에 걸리게 된다. 
하지만 의도한 바는 최대 초당 100개의 요청을 처리하는 것이지만, 
위 상황에서는 초당 99개의 요청을 처리한 것이 된다.  

위와 같이 `10ms` 당 1개의 요청을 처리하는 설정에서 
버퍼를 둬서 `10ms` 당 1개 이상의 요청이 오더라도, 
정상적인 처리가 가능하도록 하는 것을 `Burst` 라고 한다.  

즉 `Burst` 설정을 통해 `Rate Limiting` 으로 설정한 최소 요청 간격보다 
빈번하게 요청이 오는 경우 초과 분에 대해서 `Burst` 에 넣어 두고 이후에 처리 할 수 있도록 하는 역할을 한다.  

```
limit_req_zone $binary_remote_addr zone=mylimit:10m rate=100r/s;

server {
    location /test {
        limit_req zone=mylimit burst=100;

        proxy_pass http://backend;
    }
}
```  

`Burst` 는 위와 같이 `limit_req` 부분에 `burst=<개수>` 와 같이 설정할 수 있다. 

1. `10ms` 에 100개 요청 도착
    - 1개는 요청처리
    - `burst queue = 99`
1. 다음 `10ms` 에 1개 요청 도착
    - `burst` 큐에 요청 하나 빼서 처리, `burst queue = 98`
    - 도착한 요청 큐에 추가, `burst queue = 99`
1. 다음 `10ms` 에 2개 요청 도착
    - `burst` 큐에 요청 하나 빼서 처리, `burst queue = 98`
    - 도착한 요청 큐에 추가, `burst queue = 100`
1. 다음 `10ms` 에 2개 요청 도착
    - `burst` 큐에 요청 하나 빼서 처리, `burst queue = 99`
    - 도착한 요청 하나만 큐에 추가, `burst queue = 100`
    - 도착한 나머지 요청은 `503` 에러
    
만약 `10ms` 에 101개의 요청이 온다고 하더라도, 
1개는 요청처리하고 나머지 100개는 `Burst` 에 넣어 두기 떄문에 에러는 발생하지 않는다. 
하지만 `10ms` 에 102` 개의 요청이 온다면, 
`Burst` 에 넣어야 하는 수가 101개로 설정된 100개를 초과하기 때문에 한개 요청에 대해서는 에러가 발생한다.  


### 테스트
테스트 전 아래 명령어로 이전에 실행한 `Pod` 를 꼭 삭제해 주어야 한다. 

```bash
$ kubectl delete pod <pod-name>
```  

사용하는 템플릿은 앞서 살펴본 `basic` 템플릿과 거의 비슷하고, 
`burst` 관련 설정과 템플릿 구분을 위해 이름 정도만 차이가 있다.  

- `burst-configmap.yaml`

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: basic-configmap
  namespace: default
data:
  nginx.conf: |
    user  nginx;
    worker_processes  2;

    error_log  /var/log/nginx/error.log warn;
    pid        /var/run/nginx.pid;

    events {
        worker_connections  1024;
    }

    http {
        include       /etc/nginx/mime.types;
        default_type  application/octet-stream;

        log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                          '$status $body_bytes_sent "$http_referer" '
                          '"$http_user_agent" "$http_x_forwarded_for"';

        access_log  /var/log/nginx/access.log  main;

        limit_req_zone $binary_remote_addr zone=mylimit:10m rate=100r/s;

        server {
            listen 80;
            server_name localhost;

            location /status {
                limit_req zone=mylimit;
                stub_status on;
                allow all;
                deny all;
            }
        }
    }
```  

- `burst-pod.yaml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: basic-rate-limiting
  labels:
    app: basic-rate-limiting
    type: rate-limiting
spec:
  containers:
    - name: basic-rate-limiting
      image: nginx:1.19
      ports:
        - containerPort: 80
      volumeMounts:
        - name: nginx-conf
          mountPath: /etc/nginx/nginx.conf
          subPath: nginx.conf
          readOnly: true
  volumes:
    - name: nginx-conf
      configMap:
        name: basic-configmap
        items:
          - key: nginx.conf
            path: nginx.conf
```  

```bash
$ kubectl apply -f burst-configmap.yaml
configmap/burst-configmap created
$ kubectl apply -f burst-pod.yaml
pod/burst-rate-limiting created
$ kubectl get configmap,pod,svc
NAME                             DATA   AGE
configmap/burst-configmap        1      17s

NAME                      READY   STATUS    RESTARTS   AGE
pod/burst-rate-limiting   1/1     Running   0          11s

NAME                            TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
service/rate-limiting-service   NodePort    10.105.130.60   <none>        80:32222/TCP   67m
```  

`JMeter` 툴의 설정은 아래와 같이 `basic` 테스트 때와 동일하다. 
- `Number of Thread` : 100
- `Ramp-up period`: 10
- `Loop Count` : 100

테스트 결과에 따른 `TPS` 그래프는 아래와 같다. 

![그림 1]({{site.baseurl}}/img/server/nginx-rate-limiting-burst-tps.png)

응답시간에 따른 그래프는 아래와 같다. 

![그림 1]({{site.baseurl}}/img/server/nginx-rate-limiting-burst-rtt.png)

먼저 `TPS` 그래프를 보면 `basic` 의 그래프와 달리 실패한 요청이 없는 것을 확인 할 수 있다. 
`Nginx` 로 들어오는 요청이 모두 `Burst` 에 추가되면서 처리가 되었기 때문이다. 
하지만 응답시간 그래프를 보면 `Burst` 가 만등 솔루션이 아닌 것을 확인 할 수 있다. 
`basic` 과 비교해서도 그렇고 일정 요청 개수까지 계속해서 응답시간이 증가하게 된다. 
이는 `Nginx` 로 들어온 요청이 `Burst` 큐에 들어가고 다시 큐에서 나와 요청이 처리 되기 까지 대기시간이 존재하기 때문이다.  

## Queueing with No Delay


---
## Reference
[Rate Limiting with NGINX and NGINX Plus](https://www.nginx.com/blog/rate-limiting-nginx/)  
[Limiting Access to Proxied HTTP Resources](https://docs.nginx.com/nginx/admin-guide/security-controls/controlling-access-proxied-http/)  
[Module ngx_http_limit_req_module](http://nginx.org/en/docs/http/ngx_http_limit_req_module.html)  
	