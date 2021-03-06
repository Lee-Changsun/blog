--- 
layout: single
classes: wide
title: "그래들(Gradle) 개요"
header:
  overlay_image: /img/java-bg.jpg
excerpt: 'Gradle 이란 무엇이고 어떠한 특징을 가지고 있는지'
author: "window_for_sun"
header-style: text
categories :
  - Java
tags:
    - Java
    - Gradle
---  

# Gradle 이란
- Maven 을 대체하는 빌드 도구(build tool) 이다.
	- Maven 과 Ant 의 장점을 모아 2012 년에 출시 되었다.
- Groovy 기반 DSL(Domain Specific Language) 를 사용한다.
- 스프링 오픈소스 프로젝트, 안드로이드 스튜디어에서 Gradle 이 사용되고 있다.

## Maven 에 비해 Gradle 의 장점
- Build 라는 동적인 요소를 XML(Maven) 로 정의하기에는 어려운 부분이 많다.
	- 설정 내용이 길어지고 가독성이 떨어짐
	- 의존관계가 복잡한 프로젝트 설정하기에 부적절
	- 상속구조를 이용한 멀티 모듈 구현
	- 특정 설정을 소수의 모듈에서 공유하기 위해서는 부모 프로젝트를 생성하여 상속하게 해야함 (상속 구조의 단점)
- Gradle 은 Groovy 를 사용하기 때문에, 동적인 빌드는 Groovy 스크립트 플러그인을 호출하거나 직접 코드를 짜면 된다.
	- Configuration Injection 방식을 사용해서 공통 모듈을 상속해서 사용하여 상속 구조의 단점을 극복 하였다.
	- 설정 주입 시 프로젝트의 조건을 체크할 수 있어서 프로젝트별로 주이되는 설정을 다르게 할 수 있다.
- Gradle 이 Maven 보다 최대 100배 빠르다.

## Gradle 특징
### 간결함
- XML 을 사용하지 않기 때문에 장황하지 않고 Groovy 언어 기반이기 때문에 변수, if, for 등 다양한 활용이 가능하다.
- Gradle 은 기본적으로 Groovy 라는 언어를 활용한 DSL 을 스크립트로 사용한다.
- 플러그인을 직접 작성하지 않다면 Groovy 언얼르 몰라도 Gradle 스크립트를 사용할 수 있다.

### 문서화
- 공식 홈페이지의 

추가 업데이트 필요

---
## Reference
[Gradle](http://www.gradle.org/)  
[Gradle[권남]](http://kwonnam.pe.kr/wiki/gradle#gradle)  
[운영 자동화#1 — 빌드 자동화 by Gradle](https://medium.com/@goinhacker/%EC%9A%B4%EC%98%81-%EC%9E%90%EB%8F%99%ED%99%94-1-%EB%B9%8C%EB%93%9C-%EC%9E%90%EB%8F%99%ED%99%94-by-gradle-7630c0993d09)  
[[Gradle] Gradle 준비](https://araikuma.tistory.com/460)  
[Gradle Java Plugin](http://kwonnam.pe.kr/wiki/gradle/java)  
[Maven vs Gradle](https://bkim.tistory.com/13)  

