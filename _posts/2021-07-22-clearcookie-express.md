---
layout: post
title: "clearCookie를 하여도 쿠키가 사라지지 않는 이유"
tags: express
comments: true
---

express에서 로그인 할 때 쿠키를 설정해주고 로그아웃 할 때 쿠키를 제거해야 하는데
이때 `cleaerCookie`를 사용한다. 근데 `clearCookie`를 호출을 해도 쿠키가 사라지지 않아서
몇 시간을 낭비했었는데 답은 단순했다. `redirect`를 해주면 쿠키가 정상적으로 사라진다.
단지 이에 대한 이유를 찾아보려고 했는데 나오지가 않아서 그게 조금 찝찝하다.

```javascript
export const logout = async (req, res, next) => {
    res.clearCookie("jwt");
    res.redirect("/");
}
```