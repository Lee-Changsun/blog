--- 
layout: single
classes: wide
title: "미 [TDD 개념] TDD 란"
header:
  overlay_image: /img/tdd-bg.jpg
excerpt: 'TDD 가 무엇인지 알아보자'
author: "window_for_sun"
header-style: text
categories :
  - TDD
tags:
    - Concept
    - TDD
---  

## TDD 란
- Test Driven Development 의 약자로 테스트가 주도하는 개발을 뜻한다.
- 먼저 테스트 코드를 작성하고, 그 다음 테스트 코드를 통과할 수 있는 코드를 작성한다.

## TDD 의 장점
- 향상된 객체지향 코드 작성  
테스트 코드를 먼저 작성하기 때문에, 하나의 기능에 대해 구조화된 코드를 작성할 수 있다. 
TDD 는 코드의 재사용성을 보장해야만 가능하기 때문이다. 
메서드하나에 여러가지 동작이 들어가 있는 구조에서 하나의 메서드에서 하나의 동작만 수행하는 구조로 개선 되고, 자연스럽게 객체지향의 원칙중 하나인 느슨한 결합을 사용하게 된다.

- 명확한 구조에서 개발가능  
테스크 코드를 먼저 작성하는 또 하나의 이점으로, 어떠한 것을 구현해야 하는지 명확히 할 수 있다. 
개발기간이 길어지고, 다양한 업무를 동시에 처리하다보면, 자신이 무엇을 구현해야하고 어떠한 상황을 고려해야 하는지(예외 상황) 잊는 경우가 많다. 
하지만 TDD 는 먼저 목적과 고려해야 하는 상황에 대해서 모두 작성해 두었기 때문에 이런 상황을 극복할 수 있다. 

- 디버깅 시간 단축  
TDD 에서 단위테스트에 해당되는 장점이다. 
단위 구현체 별로 나눠 자동화 테스트를 진행하기 때문에 버그를 찾기 위해 구성되어 있는 전체 레벨에 대해 디버깅 할 필요가 없어진다.
즉, 모든 부분에 대해서 고려를 하지 않아도 되기 때문에 유닛 테스트를 통해 버그를 손쉽게 찾아 낼 수 있다.

- 테스트 문서의 대체  
테스트를 위해서 테스트 문서를 작성하는 경우가 많다. 
TDD 를 활용하게 되면 테스트 문서에서 나타나는 통합 테스트적인 테스트 부분 뿐만아니라, 보다 세부적인 단위 테스트와 테스트 자동화의 이점을 얻을 수 있다.

- 기획 변경의 유연함  
기획이 변경에 따른 기능 수정, 기능 추가를 할 때 가장 우려하는 부분은 기존에 구성되어 있는 코드들에 미치는 사이드 이펙트 일 것이다.
유닛 테스트의 자동화를 통해 자동으로 수정된 코드의 상태를 확인 가능하다.

## TDD 의 단점
- 추가적인 업무  
TDD 에서는 먼저 테스트를 작성하기 때문에, 테스트를 작성하는 업무가 불가피하게 추가 된다.
코드의 퀄리티 보다 빠른 결과물을 원하는 경우 적용이 어려울 수 있다.

- 추가적인 시간  
TDD 를 실무에 적용해 활용하기 위해서는 TDD 에 대해 공부하는 시간이 필요하다.
공부에서 끝나는 것이 아니라, 이를 실무에 적용하기 까지는 실무에서는 상당히 부담스러운 시간이 투자 될 수 있다.

- 실무에 따른 어려움  
TDD 를 적용해 프로젝트를 진행하다 보면, 테스트하기 어려운 예외가 생길 수 있다.
현재 구성된 구조에서 테스트하기 어려운 상황일때, 테스트를 위해 코드를 변경해야 하는지 등의 많은 고민에 빠질 수 있다.

## TDD 의 과정

![그림 1]({{site.baseurl}}/img/tdd/concept-tdd-1.gif)

1. RED : 실패하는 테스트 만들기
1. GREEN : 테스트에 통과할 만한 작은 코드를 작성하기
1. REFACTOR : 반복되는 코드, 긴 메서드, 큰 클래스, 긴 매개변수 목록 등등 다양한 관점에서 개선 된 방향으로 리펙토링 하기

## TDD 관련 팁
- 모든 구현사항에 대해서 테스트가 가능하고, 필요한가  
하나의 기능에 대해서 다양한 예외사항이 존재할 수 있다.




---
## Reference
[(1) TDD의 소개 - TDD의 장단점](http://www.hoons.net/Lecture/View/644)  
[Test-Driven Development(TDD)란 무엇인가?](https://medium.com/@dog156987/test-driven-development-tdd-%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80-59b7b4a65c4)  
[TDD란?](https://m.blog.naver.com/PostView.nhn?blogId=suresofttech&logNo=221039173819&proxyReferer=https%3A%2F%2Fwww.google.com%2F)  
[[Agile] TDD(테스트 주도 개발)란](https://gmlwjd9405.github.io/2018/06/03/agile-tdd.html)  
[테스트 주도 개발에 대하여 TTD와 BDD 그리고 DDD](https://asfirstalways.tistory.com/296)  
[TDD(Test-Driven Development) 알아보기](https://blog.leop0ld.org/posts/test-driven-development/)  
[TDD란?](https://velog.io/@essri/TDD%EB%9E%80)  
[테스트 주도 개발방법론 TDD(Test-driven Development)이란?](https://nesoy.github.io/articles/2017-01/TDD)  
