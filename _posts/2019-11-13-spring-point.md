---
layout: post
title: "Spring 글 작성시 포인트 지급하는 기능 만들기"
tags: Spring
comments: true
---

참고 URL : <https://doublesprogramming.tistory.com/122>

작성자에게 서비스를 사용할 동기부여를 하기위해 포인트를 지급하는 기능을 만들어보자 저번에 Django로 할 때는 어떻게 해야할지 도저히 이해가 안되서 결국 만들지 못했던 기능인데 오히려 Spring에서는 쉽게 가능했다.

포인트를 주는 기능은 그저 글을 작성했을때 특정 숫자값만 DB에 넣어 저장해주면 끝이다. 그렇기에 UI는 필요없고 Mapper부터 만든다.

```xml
<update id="updatePoint">
update USER set user_point = user_point + #{userpoint}
where user_name = #{name}
</update>
```

`UserVO`에 private Integer userpoint;
를 추가해준다. 물론 Getter, Setter도 추가한다.

# UserDAO

```java
public void updatePoint(String name, int userpoint) throws Exception;
```

UserDAOImpl.java에는 아래의 코드를 적어준다.
이게 핵심 로직이다.

```java
@Override
    public void updatePoint(String name, int userpoint) throws Exception {
    Map<String, Object> map = new HashMap<String, Object>();
    map.put("name", name);
    map.put("userpoint", userpoint);
    session.update(namespace + ".updatePoint", map);
    }
```

이름을 받아서 글쓴 유저가 맞다면 포인트를 지급해야 하기 때문에 인자를 두개 받아야한다.
근데 `session.update`를 써보면 동시에 인자를 두개 넣을 수가 없다.
그러니 결국 `map`같이 합쳐주는 기능을 사용해야 한다.

그리고 나서는 Controller, Service는 건드릴 필요 없이 바로 ServiceImpl 코드만 수정하면 된다.

```java
@Override
    public void regist(BoardVO boardvo) throws Exception {
    boarddao.create(boardvo);
    userdao.updatePoint(boardvo.getWriter(), 15);
    }
```

자료는 못찾고 혼자 개발할때는 Service에 논리를 넣은것이 아니라 Controller에서 포인트를 지급하도록 했었다.
지금 다시 생각해보니 비즈니스 로직은 Service 단에서 끝나야 하기 때문에 이와 같이 만드는게 알맞는것 같다.
이렇게 다시 한번 새로운 점을 깨달아 다행이다.

여담으로 Spring 개발을 하면서 인자를 정의하는 것이 정말 중요하고 어렵다는걸 느꼈다.
로직은 어느정도 만든다쳐도 작동을 제대로 안하는 이유는 대부분 인자를 무엇을 받을지 명확히 내 스스로가 모르기 때문이다.
그래서 정말 무엇이 필요한지 제대로 이해하는 것이 중요하다는걸 다시 한 번 느꼈다.
받을 인자는 없는데 인자를 받겠다고 넣어놓으면 동작을 안한다.