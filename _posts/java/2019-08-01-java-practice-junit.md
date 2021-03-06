--- 
layout: single
classes: wide
title: "[Java 실습] Junit"
header:
  overlay_image: /img/java-bg.jpg
excerpt: 'Junit Java TDD 에 대해 알아보자'
author: "window_for_sun"
header-style: text
categories :
  - Java
tags:
    - Practice
    - Java
    - Junit
    - TDD
    - Test
---  

## Junit 이란
- 단위 테스트(Unit Test) 도구이다.
- TC(Test Case)를 작성해서 `System.out` 으로 디버깅 하지 않고, 더 효율적은 방법으로 테스트를 할 수 있도록 해준다.
- 단정(`asseert*`) 문을 통해 테스트 케이스의 수행 결과를 판별한다.
- 다양한 Annotation 을 지원한다.
- 각 테스트간 새로운 인스턴스를 생성하여 독립적인 테스트가 이루어지게 한다.
- Junit 의 검증은 아래와 같이 수행할 수 있다.

	```java
	assertEquals(expected, actual);
	```  
	
- Junit 를 사용하기 위해 아래와 같이 Maven 혹은 Gradle 에 의존성을 추가해 주면 된다.

	```xml
	<dependency>
		<groupId>junit</groupId>
		<artifactId>junit</artifactId>
		<version>4.12</version>
	</dependency>

	```  
	
	```groovy
	dependencies {
		testCompile "junit:junit:4.12"
  	}
	```  
	
- Junit 를 사용할 때는 아래 패키지를 static 으로 import 하고 사용한다.

	```java
	// Junit 의 assert 문
	import org.junit.Assert.*;
	```  

### Junit 의 Annotation

![그림 1]({{site.baseurl}}/img/java/practice-junithamcrest-1.png)

- `@Test`
	- 해당 Annotation 이 선언된 메서드는 테스트를 수행하는 메서드가 된다.
	- Junit 은 각 테스트가 서로 영향을 주지 않고 독립적으로 실행됨을 원칙으로 `@Test` 마다 새로운 객체를 생성한다.
	- `@Test(timeout=5000)` 을 통해 테스트의 시간에 대한 제한을 두고, 정해진 시간을 초과한다면 테스트는 실패이다.
	- `@Test(expected=RuntimeException.class)` 을 통해 테스트 시에 발생해야하는 예외를 정하고, 해당 예외가 발생해야 테스트가 성공한다.
- `@Ignore`
	- 해당 Annotation 이 선언된 메서드는 테스트를 실행하지 않는다.
- `@Before`
	- 해당 Annotation 이 선언된 메서드는 테스트(`@Test` 가 선언된 메서드) 실행 전에 한번씩 실행된다.
	- 테스트 전에 공통으로 사용하는 코드(초기화)를 `@Before` 메서드에 작성해주면 된다.
- `@After`
	- 해당 Annotation 이 선언된 메서드는 테스트(`@Test` 가 선언된 메서드) 실행 후에 한번씩 실행된다.
	- 테스트 후에 공통으로 사용하는 코드(해제)를 `@After` 메서드에 작성해주면 된다.
- `@BeforeClass`
	- 해당 Annotation 이 선언된 메서드는 테스트 실행 전에 가장 처음 한번 실행된다.
- `@AfterClass`
	- 해당 Annotation 이 선언된 메서드는 테스트 실행 후에 가장 처음 한번 실행된다.	

### 대표적인 Junit Assert 문
- `assertEqual(expected, actual)` : 객체 expected 와 actual 값이 일치한지 확인한다.
- `assertArrayEquals(expected, actual)` : 배열 expected 와 actual 이 일치한지 확인한다.
- `assertSame(expected, actual)` : 객체 a, b 가 같은 객체(레퍼런스)인지 확인한다.
- `assertTrue(condition)` : 조건 condition 이 참인지 확인한다.
- `assertNotNull(object)` : 객체 object 가 null 이 아닌지 확인한다.
- `assertThat(actual, Matcher)` : actual 의 값이 `Matcher`(Hamcrest) 의 조건에 만족한지 확인한다.

더 자세한 내용은 [Class Assert](http://junit.sourceforge.net/javadoc/org/junit/Assert.html) 에서 확인 가능하다.

### 예제 코드

```java
import static org.junit.Assert.*;
public class JunitTest {
    @BeforeClass
    public static void setUpBeforeClass() {
        // 테스트 전 한번 초기화하는 작업 ...
    }

    @AfterClass
    public static void tearDownAfterClass() {
        // 테스트 후 한번 해제하는 작업 ...
    }

    @Before
    public void setUp() {
        // 테스트 전마다 계속 초기화해야 하는 작업
    }

    @After
    public void tearDown() {
        // 테스트 후마다 계속 해제해야 하는 작업
    }

    @Test
    public void assertEquals_Expected_Equals() {
        int actualInt = 1;
        assertEquals(1, actualInt);

        String actualString = "str";
        assertEquals("str", actualString);

        String actualString2 = new String("str1");
        assertEquals(new String("str1"), actualString2);

        char actualChar = 'c';
        assertEquals('c', actualChar);

        Integer actualInteger = new Integer(100);
        assertEquals(new Integer(100), actualInteger);

        Double actualDouble = new Double(100.111d);
        assertEquals(new Double(100.111d), actualDouble);
    }

    @Test
    public void assertArrayEquals_Expected_ArrayEquals() {
        int[] actualInt = new int[]{1, 2};
        assertArrayEquals(new int[]{1, 2}, actualInt);

        String[] actualString = new String[]{"str1", "str2"};
        assertArrayEquals(new String[]{"str1", "str2"}, actualString);

        String[] actualString2 = new String[]{new String("str1"), new String("str2")};
        assertArrayEquals(new String[]{new String("str1"), new String("str2")}, actualString2);

        char[] actualChar = new char[]{'c', 'h'};
        assertArrayEquals(new char[]{'c', 'h'}, actualChar);
    }

    @Test
    public void assertSame_Expected_Same() {
        String actualString = "str";
        assertSame("str", actualString);

        Integer actualInteger = new Integer(1);
        Integer expectedInteger = actualInteger;
        assertSame(expectedInteger, actualInteger);
    }

    @Test(expected = AssertionError.class)
    public void assertSame_Expected_AssertionError() {
        String actualString2 = new String("str1");
        assertSame(new String("str1"), actualString2);
    }

    @Test
    public void assertTrue_Expected_True() {
        assertTrue(true);
        assertTrue(1 < 2);
        assertTrue(100 == 100);
        assertTrue("str".equals("str"));
        assertTrue((new String("str1")).equals(new String("str1")));
    }

    @Test
    public void assertNotNull_Expected_NotNull() {
        assertNotNull(new String("str"));
        assertNotNull(new Integer(1));
        assertNotNull(new String());
    }

    @Test(expected = NullPointerException.class)
    public void assertNotNull_Expected_AssertionError() {
        Integer integer = null;

        String str = integer.toString();
    }

    @Test(timeout = 10)
    public void for_Expected_PerformanceOk() {
        int sum = 0;
        for(int i = 1; i <= 10; i++) {
            sum += i;
        }

        assertEquals(55, sum);
    }
}
```  

---
## Reference
[Class Assert](http://junit.sourceforge.net/javadoc/org/junit/Assert.html)  
