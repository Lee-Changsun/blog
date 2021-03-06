--- 
layout: single
classes: wide
title: "REST 개요"
header:
  overlay_image: /img/web-bg.jpg
excerpt: 'REST 란 무엇이고, 어떠한 특징을 가지고 있는지'
author: "window_for_sun"
header-style: text
categories :
  - Web
tags:
    - Web
    - REST
    - RESTful
    - ROA
    - Intro
---  

# REST 란
- Representational State Transfer
- HTTP URI를 통해 Resource를 명시하고, HTTP Method(Get, Post, Put, Delete)를 통해 해당 Resource에 대한 CRUD Operation을 적용한다. 즉, REST는 [ROA(Resource Oriented Architecture)]({{site.baseurl}}{% link _posts/web/2019-02-22-web-roa-intro.md %})설계의 중심에 Resource가 있고 HTTP Method를 통해 Resource를 처리하도록 설계된 아키텍쳐를 의미한다.



## HTTP Method 와 CRUD Operation

| HTTP Method | CRUD | Idempotent |
|---|---|---|
| POST | Create | No
| GET | Select | Yes
| PUT | Update | Yes
| DELETE | Delete | Yes

- Idempotent : 여러번 수행을 해도 결과가 같은 경우
	- REST 는 각 개별 API를 상태 없이 수행하게된다. 그래서 해당 REST API를 다른 API와 함께 호출하다가 실패했을 경우, 트랜잭션 복구를 위해 다시 실행해야 하는 경우가 있는데, Idempotent한 메서드의 경우에는 반복적으로 다시 메서드를 수행해주면 된다.

## REST 구성
- 자원(Resource) : URI
	- 모든 자원에는 고유한 ID가 존재하고, 이 자원은 Server에 존재한다.
	- 자원을 구별하는 ID는 '/groups/:group_id' 와 같은  HTTP URI 다.
	- Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청한다.
- 행위(Verb) : HTTP Method
	- HTTP 프로토콜의 Method를 사용한다.
	- HTTP 프로토콜은 GET, POST, PUT, DELETE와 같은 메서드를 제공한다.
- 표현(Representations of Resource)
	- Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답(Representation)을 보낸다.
	- REST에서 하나의 자원은 JSON, XML, TEXT, RSS등 여러 형태의 Representation으로 나타내어 질 수 있다.
	- JSON, XML을 사용하여 데이터를 주고 받는 것이 일반적이다.

## REST 장점
- OPEN API제공에 용이하다.
- 멀티 플랫폼 지원 및 연동이 용이하다.
- 원하는 타입으로 데이터를 주고 받을 수 있다. (Json, XML, RSS)
- 기존 웹 인프라(HTTP)를 그대로 사용가능하다. (방화벽, 장비 요건 불필요)
- 사용하기 쉽다.
- 세션을 사용하지 않는다. 각 요청에 독립적

## REST 단점
- 표준이 없어서 관리가 어렵다.
- 사용할 수 있는 메서드가 4가지 밖에 없다.
- 분산환경에는 부적합하다.
- HTTP 통신 모델에 대해서만 지원한다.

## REST의 특징
- Uniform Interface (인터페이스 일관성)
	- URI로 지정된 Resource에 대한 조작을 통일되고 한정적인 인터페이스로 수행한다.
	- HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다. (특정 언어난 기술에 종속되지 않는다.)
- Stateless (무상태성)
	- HTTP 또한  Stateless를 가지므로, HTTP 를 사용하는 REST 또한 Stateless를 가진다.
	- Client의 Context(Session, Cookie)를 별도로 저장하고 관리하지 않는다.
	- Server에서는 API 요청만 단순히 처리한다.
	- Server의 처리 방식에 일관성을 부여하게되고, 서비스의 자유도가 높아지고, 구현이 단순해 진다.
- Cacheable (캐시 가능)
	- REST는 HTTP(기존 웹표준)을 그대로 사용하기 때문에, 웹에서 사용하는 기존 인프라 활용이 가능하다.
		- HTTP가 가진 캐싱 기능을 사용할 수 있다.
	- HTTP 표준에서 사용하는 Last-Modified 태그나 E-Tag를 이용하면 캐싱 구현이 가능하다.
- Self-descriptiveness (자체 표현 구조)
	- REST의 큰 특징 중하나인 자체 표현 구조는 REST API 메시지만 보고 이를 쉽게 이해 할 수 다는 것이다.
	- OPEN API의 경우 API 문서를 별도로 제공하고 있지만, 디자인 사상은 최소한의 문서의 도움만으로도 API 자체를 이해할 수 있어야 한다.
- Client-Server 구조
	- REST 서버는 API 제공 및 비지니스 로직 처리/저장, 클라이언트는 사용자 인증, Context(Session, 로그인 정보)등을 직접 관리하는 구조이다.
	- 각각의 역할이 확실히 구분되기 때문에 클라이언트-서버 간의 개발 내용이 명확해지고 서로 의존성이 줄어든다.
- Layered System (계층형 구조)
	- REST 서버는 비지니스 로직을 수행하는 API 서버 외에, 다중 계층으로 구성될 수 있으며 보안, 로드 밸련싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있다.
	- 마이크로 서비스 아키텍쳐의 API Gateway, Reverse Proxy 등의 네트워크 기반의 중간 매체를 통해 구성할 수 있다.

## REST 안티 패턴
- GET/POST 를 이용한 터널링
	- ex) http://www.my.com?method=update&id=cs
		- 메서드의 실제 동작은 리소스를 업데이트 하는 내용이지만, PUT를 사용하지 않고, GET 쿼리 파라미터로 method=update라고 넘겨주며 이 메서드가 수정 동작임을 명시했다.
	- HTTP 메서드의 규약을 따르지 않았기 때문에 REST라고 할 수 없고, 웹 캐시 인프라도 사용 불가능하다.
	- 다른 안좋은 예는 POST를 이용한 터널링이다.
	```
	{
		"method" : "GetUserInfo",
		"id" : "cs"
	}
	``` 
	
	- Insert(Create)성 오퍼레이션이 아닌대도 불구하고, Json에 오퍼레이션 명을 넘기는 형태로 사용자 정보를 가지고 오고 있다.
- Self-Descriptiveness 속성을 사용하지 않음
	- REST의 특징 중하나인 Self-Descriptiveness는 자기 서술성으로 REST URI와 메서드 그리고 정의된 메시지 포맷에 의해 쉽게 API를 이해할 수 있는 기능이 되어야 한다.
	- 하지만 Self-Descriptiveness 을 깨먹는 가장 대표적인 사례가 위에서 앞서 언급한 GET이나 POST을 이용한 터널링 구조가 된다.
- HTTP Response Code를 사용하지 않음
	- HTTP Response Code를 출실하게 따르지 않고, 성공은 200, 실패는 500과 같이 1~2 개의 HTTP Response Code만 사용하는 경우이다.
	- 심한 경우에는 HTTP Response Code 200으로 정의한 후 별도의 에러 메시지를 200 Response Code와 함께 보내는 경우이다.
	- 이는 REST 디자인 사상에도 어긋남은 물론이고 자기 서술성에도 어긋난다.
	
# RESTful 이란
- RESTful은 일반적으로 REST라는 아키텍쳐를 구현하는 웹 서비스를 나타내기 위해 사용되는 용어이다.
	- `REST API`를 제공하는 웹서비스를 `RESTful` 하다고 할 수 있다.
- RESTful은 REST를 REST답게 쓰기 위한 방법으로, 누군가가 공식적으로 발표한 것이 아니다.
	- `REST 원리`를 따르는 시스템은 `RESTful`이란 용어로 지칭된다.

## RESTful의 목적
- 이해하기 쉽고 사용하기 쉬운 REST API를 만드는 것
- RESTful한 API를 구현하는 근본적인 목적이 성능 향상에 있는 것이 아니라 일관적인 컨벤션을 통한 API의 이해도 및 호환성을 높이는 것이 주 동기이다.
- 성능이 중요한 상황에서는 굳이 RESTful한 API를 구현할 필요는 없다.

## RESTful 하지 못한 경우
- CRUD 기능을 모두 POST로만 처리하는 API
- route에 resource, id외의 정보가 들어가는 경우(/students/updateName)

---
## Reference
[REST API의 이해와 설계-#1 개념 소개](https://bcho.tistory.com/953)  
[[웹기본개념] HTTP 그리고 REST API 다가가기](https://jinbroing.tistory.com/96)  
[RESTful API란 ?](https://brainbackdoor.tistory.com/53)  
[RestFul이란 무엇인가?](https://lalwr.blogspot.com/2016/01/restful.html)  
[[Network] REST란? REST API란? RESTful이란?](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)  
