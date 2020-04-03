---
layout: post
title: "순수 자바 연습 깨달은 점"
tags: Java
comments: true
---

https://github.com/jojoldu/oop-java

원문: https://github.com/jojoldu/oop-java

자바를 배우긴 했지만 최근에는 순수하게 자바만 가지고 프로그램을 만들어 본적이 없기에 복습 차원에서 한번 만들면서 공부했습니다.

1. 클래스는 명사, 메소드는 동사

2. 각 메소드는 하나의 역할만 해야한다.

3. 처음부터 완벽하게 하려고 하지 말고 먼저 기본적인 코드를 구현 후 수정한다.

4. 중간중간 제거나 추가의 작업을 시행할 경우 `ArrayList`보다 `LinkedList`가 낫다.

5. 긴 문자열을 더하거나 빼는 연산작업이 필요할 경우 `String`보다 `StringBuilder`를 쓰는 것이 효율적이다.

6. 객체는 다른 객체에게 요청을 할때, 이렇게 저렇게와 같이 세세하게 요청해서는 안된다. 예를 들어, `Gamer.java`와 `CardDeck.java`가 있다고 가정한다. `Gamer`는 단지 `CardDeck`에게 카드 하나를 뽑아 달라는 요청만 하면 된다. `CardDeck` 내부에서 일어나는 과정을 `Gamer`는 알 필요가 전혀 없다.

7. `private static final int INIT_RECEIVE_CARD_COUNT = 2;` 간단하게 `i<2`해도 같은 기능이지만 상수로 선언하는 이유는 **매직넘버**를 피하기 위함이다. 매직넘버는 어떤 기능을 하는 지 알 수 없는 숫자를 이야기한다. 매직넘버를 사용하면 `2`라는 숫자가 어떤 의미인지 알 수 없게 되어 전체 코드를 읽어야만 한다. 그래서 전체 히스토리를 알 수 없는데 이는 변경시 문제가 발생할 수 있다.

8. 하나의 클래스에 private 메소드가 많아지면 객체 설계를 다시 고민해봐야 하는 신호

9. `if(getPointSum() <= CAN_RECEIVE_POINT)` 라고 기존의 메소드에 넣을 수 있지만 이렇게 될 경우 매직넘버와 같이 정확히 어떤 일을 하는지 알수가 없다. 그러므로 메소드를 분리하여 `isReceiveCard`로 선언해 만들면 메소드명을 통해 무슨일을 하는지 명확히 알 수 있다. 주석을 적지 않고 의도가 명확한 변수명, 메소드명을 적도록 한다.

```java
 private boolean isReceiveCard(){
        return getPointSum() <= CAN_RECEIVE_POINT;
 }
```

10. 인터페이스는 상수와 추상메소드만 가질 수 있다.

11. 좋은 메소드란 어떤 인자가 필요하고 그 인자를 통해 어떤 결과를 내는지 명확한 것