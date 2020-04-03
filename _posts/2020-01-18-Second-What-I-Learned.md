---
layout: post
title: "20.01.13 - 01.17 WIL 정리"
tags: WIL
comments: true
---

20.01.13 - 01.17 한주동안 깨달은 점 정리
Week I Learned 형식으로 정리

# form:form path

jsp를 사용하다보면 form 형식의 데이터를 넘겨주기 위해 `<form:form>`형식을 자주 사용합니다.
근데 path에 정확히 무엇을 넣는지를 모르고 사용하다보니 오류가 발생해도 해결이 힘들었습니다.
대표적으로 `<form:textarea>`가 있는데요.

최신 버전의 스프링에서는 `<form:textarea>` 사용시 무조건 value가 비워져 있어야 합니다.
즉 수정 페이지에서는 `<form:textarea>`를 사용해서 유저에게 데이터를 보여줄 수 없다는 의미입니다.
그렇기에 `path`의 의미를 정확히 알아야 했습니다.

`<form:textarea path="testForm">`라고 하면 이 의미는 곧 `<textarea id="testForm" name="testForm"/>`
과 같은 의미입니다. path를 사용하면 id와 name을 자동으로 설정해주기 때문에 편해서 사용하는 것이지
필수가 아닙니다. 또한, id를 자동으로 설정해주기 때문에 jquery나 javascript를 사용할때도 path로 설정한
값을 가지고 사용할 수 있습니다.

# Img src 값이 없을시 처리

```javascript
$("img").error(function(){
        $(this).hide();
});
```

이미지가 있을때는 보여주고 없을때는 안보여주는 기능을 구현하고 있었는데 이미지가 없음에도 일단 src에 계속 값이 들어가는 것이었다. 찾다보니 그냥 에러가 발생시 아예 img 태그를 숨기는 방법이 있었다.
jquery를 사용하지 않는다면 `onerror` 이벤트로 처리하면 될 것 같다.

# localStorage, sessionStorage

이미지 미리보기 기능을 구현하는데 이걸 `window.open()`으로 보내야 했다.
같은 페이지에서 미리보기는 `FileReader()`를 사용해서 구현하면 되는데 문제는 `fakepath`로
이미지 주소가 나오다 보니 URL 전체를 새로운 창에 보내는건 불가능 했다.

그래서 찾아보니 localStorage나 sessionStorage를 사용하면 손쉽게 구현이 가능했다.

```javascript
// 첫번째 페이지
<script>
    function readURL(input) {
        if (input.files && input.files[0]) {
            var reader = new FileReader();

            reader.onload = function(e) {
                $('#blah').attr('src', e.target.result);
                window.open("second_page.html", "_blank");
                localStorage.setItem("image",e.target.result);
            }

            reader.readAsDataURL(input.files[0]);
        }
    }
    $("#imgInp").change(function() {
        readURL(this);
    });
</script>
```

파일을 읽어와서 src에 붙이고 읽어온 파일을 "image"라는 이름으로 localStorage에 세팅한다.

```javascript
// 두번째 페이지
<script>
$(document).ready(function() {
    $("#img2").attr('src', localStorage.getItem("image"));
});
</script>
```

그리고 `window.open()`으로 열린 새 페이지에서 `.getItem("image")` 앞에서 설정한 같은 이름으로
이미지를 얻어와서 붙여주면 끝이다.

# MyBatis 중복검사 코드 정리

```sql
<select id="selectDoubleCheckItem" parameterType="testVO" resultType="string">
    select count(testno)
    from testTable
    where testno = #{testno}
    group by testno
    having count(testno) > 0;
</select>
```

중복검사하는 코드 자체는 어렵지 않다. 애초에 자료도 많아서 구하기도 쉽다.
근데 개인적으로 간과한 점 중 하나는 `resultType="int"`를 했다는 점이다.

중복검사는 count를 통해 결과값이 null이나 1이 나오는 구조이다.
그런데 `resultType="int"`로 해놓으면 null이 int형이 아니므로 받지 못하고
에러가 발생한다.
하지만 `string`으로 해놓으면 null을 그냥 string 타입으로 받아오기 때문에
에러가 발생하지 않고 조건문에 넣어서 판단을 할 수 있는 것이다.

# Datatable get render index 자동으로 인덱스 만들기

테이블 앞에 게시물마다 번호를 달아야하는데 이걸 굳이 DB에서 가져와서 해야하냐고
차장님께서 말씀하셔서 방법을 찾아봤다.
물론 대부분은 DB에서 Auto_Increment로 생성해서 가져오기 때문에 별로 자료도 없었다.
다행히 자료가 있었고 아래처럼 선언해주면 알아서 값을 넣어준다.

```javascript
columns: [{
    data: null,
    className: "tab-1",
    "render": function ( data, type, full, meta ) {
        return  meta.row;
}];
```

# jquery를 사용한다면 인쇄기능은 printThis

프린트, 인쇄 기능을 굳이 구현할 필요 없다.
<https://github.com/jasonday/printThis>

이미 좋은 jquery 플러그인이 나와있기 때문이다.
만약 본인이 직접 구현해야 한다면 기본적인 원리는 간단하다.

`window.print()` 기능을 사용하기 전에 필요없는 `<div>`를 전부 숨기고
기능을 사용한 후에는 다시 보여주면 되는 것이다.

```javascript
$("#writeDiv").hide();
$("#titleDiv").hide();
$("#divPrint").show();
window.print();
$("#writeDiv").show();
$("#titleDiv").show();
```

# Ajax시에 새로고침이 되는 문제 해결하기

`<button type="button" id="btnTest">TEST</button>`

AJAX를 문법에 맞게 구현을 했음에도 새로고침이 되는 경우가 있다.
이거 때문에 몇 시간을 고생하는 신입 개발자들이 있을 것이다.
필자도 이거 때문에 너무 고생을 했는데 해결방안은 간단하다.

`type="button"`만 넣어주면 해결된다.
이게 없으면 `form`에서 지정해놓은 원래 url까지 가느라 새로고침이 발생한다.

혹은 AJAX 끝나기전에 `return false;`를 선언해준다.

# textarea 본문 엔터 유지하기

```java
vo.setContents(vo.getContents().replace("\r\n", "<br>"));
result.setContents(result.getContents().replace("<br>", "\r\n"));
````

저장 기능을 담당하는 컨트롤러에서 `\r\n`을 `<br>`로 바꿔서 저장하고
사용자에게 보여줄 때 `<br>`을 `\r\n`으로 대체하여 보여주면 된다.

문제는 이렇게 \r\n으로 엔터가 쳐진 본문을 자바스크립트 변수에 넣어
window.open()을 하면 오류가 나거나 한 줄로 나온다는 점이다.
왜냐하면 url로 보낼때 저런 태그들을 자동으로 삭제해버리기 때문이다.

```javascript
const uri = 'https://mozilla.org/?x=ше\r\nы';
const encoded = encodeURI(uri);
```

그래서 encodeURI라는 기능을 사용해 포함해서 새 창으로 보낸 후에
컨트롤러에서 `<br>`로 변경해주면 줄바꿈을 한 상태로 새 창에서도 출력이 가능하다.