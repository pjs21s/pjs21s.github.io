---
layout: post
title: "JS ToDoList 연습 ES6 문법으로 바꿔보기"
tag: javascript
comments: true
---

참고: <https://haviyj.tistory.com/3>

원문: https://www.w3schools.com/HOWTO/howto_js_todolist.asp

```html
<div id="myDIV" class="header">
    <h2>My To DO List</h2>
    <input type="text" id="myInput" placeholder="title...">
    <span onclick="newElement()" class="addBtn">Add</span>
</div>
<ul id="myUL">
    <li>Hit the Gym</li>
    <li class="checked">Pay bills</li>
    <li>Meet George</li>
</ul>
<script>
    // close 버튼을 각 아이템에 만든다
    let myNodelist = document.getElementsByTagName("LI");
    for (let i = 0; i < myNodelist.length; i++) {
        let span = document.createElement("SPAN");
        let txt = document.createTextNode("\u00D7");
        span.className = "close";
        span.appendChild(txt);
        myNodelist[i].appendChild(span);
    }

    // close 버튼을 누르면 현재 아이템을 숨김
    let close = document.getElementsByClassName("close");
    for (let i = 0; i < close.length; i++) {
        close[i].onclick = function() {
        let div = this.parentElement;
        div.style.display = "none";
        }
    }

    // 아이템을 클릭하면 checked 심볼을 추가
    let list = document.querySelector('ul');
    list.addEventListener('click', (ev) => {
        if (ev.target.tagName === 'LI') {
        ev.target.classList.toggle('checked');
        }
    }, false);

    // Add 버튼을 누르면 새로운 아이템을 추가
    function newElement() {
    let li = document.createElement("li");
    let inputValue = document.getElementById("myInput").value;
    let t = document.createTextNode(inputValue);
    li.appendChild(t);
    if (inputValue === '') {
        alert("You must write something!");
    } else {
        document.getElementById("myUL").appendChild(li);
    }
    document.getElementById("myInput").value = "";

    let span = document.createElement("SPAN");
    let txt = document.createTextNode("\u00D7");
    span.className = "close";
    span.appendChild(txt);
    li.appendChild(span);

    for (i = 0; i < close.length; i++) {
        close[i].onclick = function() {
        let div = this.parentElement;
        div.style.display = "none";
        }
    }
    }
</script>
```

CSS, HTML은 스킵하고 Javascript 코드만 봐보자

```javascript
// close 버튼을 각 아이템에 만든다
let myNodelist = document.getElementsByTagName("LI");
for (let i = 0; i < myNodelist.length; i++) {
    let span = document.createElement("SPAN");
    let txt = document.createTextNode("\u00D7");
    span.className = "close";
    span.appendChild(txt);
    myNodelist[i].appendChild(span);
}
```

`li` 태그 이름을 가진 것들을 가져와서 `myNodelist`에 넣어준다.

`getElementsByTagName`는 리스트를 반환하니까 `for`문을 이용할 수 있다.

ES6 문법은 `var`를 사용하지 못하고 `let`을 사용해야 한다. 하지만 전역변수인 `var`와 다르게 `let`은 자신을 정의한 블록에서만 접근 가능하다. 그러므로 `let i = 0`을 `for` 밖에서 선언하면 동작을 안하길래 안에 넣어줬다.

`createElement`를 이용해 비어있는 `<span></span>`을 생성한다. `createTextNode`로 X 아이콘을 생성한다.

그리고 `appendChild`를 이용해서 `span` 끝에 `txt`의 값을 넣어준다.

`<span class="close">×</span>` 이렇게 값이 만들어진다.



```javascript
// close 버튼을 누르면 현재 아이템을 숨김
let close = document.getElementsByClassName("close");
for (let i = 0; i < close.length; i++) {
    close[i].onclick = function() {
    let div = this.parentElement;
    div.style.display = "none";
    }
}
```

`ClassName`이 'close'인 항목을 가져와 `close` 변수에 넣어준다.

`for`문으로 현재 있는 `close`항목을 돌면서 `onclick`시 `parentElement`의 스타일을 `none`으로 변경해 숨긴다.

`parentElement`는 아마도 위에서 선언한 `close`이겠지...?

ES6문법에 따라 function을 =>로 고쳤는데 this를 적절하게 안 바꾸니 기능이 제대로 작동을 안해서 그대로 놔뒀다. 아직 this를 어떻게 써야하는지는 정확히 모르겠다.



```javascript
// 아이템을 클릭하면 checked 심볼을 추가
let list = document.querySelector('ul');
list.addEventListener('click', (ev) => {
    if (ev.target.tagName === 'LI') {
    ev.target.classList.toggle('checked');
    }
}, false);
```

`ul`을 선택해서 그 안에 있는 `li`가 선택되면 `checked`를 선택된 항목에 추가한다.

```javascript
// Add 버튼을 누르면 새로운 아이템을 추가
function newElement() {
    let li = document.createElement("li");
    let inputValue = document.getElementById("myInput").value;
    let t = document.createTextNode(inputValue);
    li.appendChild(t);
    if (inputValue === '') {
        alert("You must write something!");
    } else {
        document.getElementById("myUL").appendChild(li);
    }
    document.getElementById("myInput").value = "";

    let span = document.createElement("SPAN");
    let txt = document.createTextNode("\u00D7");
    span.className = "close";
    span.appendChild(txt);
    li.appendChild(span);

    for (i = 0; i < close.length; i++) {
        close[i].onclick = function() {
        let div = this.parentElement;
        div.style.display = "none";
        }
    }
    }
```

`myInput` 즉 텍스트 박스에 있는 값을 가져와서 `li`에 추가하고 `myUL`에 새롭게 추가한다.

그리고 `myInput`은 값을 비워준다.



React 같은걸 배우는 것도 좋겠지만 이번에 바닐라 JS로만 예제를 해보면서 느끼는건 우선 기본 JS 연습부터 더 해야겠다는 생각뿐이다. 시간이 날때마다 해야겠다.