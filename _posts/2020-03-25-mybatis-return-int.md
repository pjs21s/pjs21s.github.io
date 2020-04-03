---
layout: post
title: "Mybatis insert, update, delete return을 int로 하는 이유"
tags: Mybatis
comments: true
---

`insert`를 하면 당연히 반환 값이 없기 때문에 return type을 void로 하게 된다.
당연히 문제없이 작동하지만 편한 기능을 사용하지 못하게 된다.
Mybatis에서는 기본적으로 쿼리가 돌고 나면 업데이트 한 행의 개수를 리턴해준다.

예를 들어, insert 구문을 성공시켜서 1 행의 데이터를 입력했다면 return 1을 반환해준다.
이걸 이용해서 쿼리가 성공적으로 돌았는지 그렇지 않았는지를 판단할 수 있다.
그렇게 되면 후속 작업을 할때 도움이 된다.
앞의 로직이 성공해야만 뒤의 로직을 실행시킬 때나 성공과 실패를 가려서 에러코드를
사용자에게 보내줄 때 등등

이건 쿼리 resultType에 int를 선언하는 것이 아니라 자바 코드에서 리턴 타입을
int로 선언하는 것이다.

Insert - 1 (여러개인 경우 1)
Update - 업데이트 된 행의 개수 (없으면 0)
Delete - 삭제 된 행의 개수 (없으면 0)