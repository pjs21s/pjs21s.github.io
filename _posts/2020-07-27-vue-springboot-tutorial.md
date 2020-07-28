---
layout: post
title: "Vue.js와 Spring Boot 연동 및 CRUD JPA 기초"
tags: Vue.js Spring
comments: true
---

이 포스팅은 기존에 Spring MVC를 해봤고 Vue.js 기초를 찾아가면서 이해 할 수 있는 분들이 보기에 편한 포스팅입니다. 저 스스로 연동하고 세팅하는데 어려움을 겪었고 CRUD하면서 조금 시간이 걸렸기에 정리합니다. 한번에 CRUD까지 할 예정이라 글이 꽤 깁니다.

Vue init은 사용하지 않습니다.
처음에 연습할때는 좋지만 실제로 프로젝트를 만들때는 Vue init으로 하면 무겁고 디렉토리도 복잡해져서 관리가 안되어 사용하지 않습니다.

Vscode에서 진행했고 사용한 플러그인은 아래와 같습니다.

<details markdown="1">
<summary>펼치기</summary>

Debugger For Java, ESLint, Gradle Language Support, Java Extension Pack, Lombok Annotations Support fro VS Code, npm, npm Intellisense, Vetur, Spring Boot Extension Pack

</details>

  
**Vue.js + Spring Boot 연동 시작 및 확인**

<img src="/Users/vors/pjs21s/images/springboot1.png">

F1을 눌러서 프로젝트를 생성합니다.
기본 이름으로 쭉 생성하시다가 마지막에 Dependencies를 선택해야하는 곳에서 저는 아래와 같이 선택했습니다.
지금 선택하지 않아도 나중에 추가 가능합니다.

```
com.example -> demo -> 2.3.2 -> Spring Data JAP, Spring Boot DevTools, Lombok, Spring Web
```

정상적으로 완료되면 프로젝트가 DEMO라는 폴더 안에 생성이 됩니다.

그리고 새로운 터미널을 열어서 cd demo로 프로젝트 안에 들어옵니다.
그리고 `npm install –g @vue/cli` 명령어로 vue cli를 설치합니다.

설치가 완료되면 `vue create front` 명령어로 vue 프로젝트를 front라는 이름으로 생성합니다.

여기까지 정상적으로 설치가 되면 아래와 같은 디렉토리 구조를 가지게 됩니다.

<img src="C:\Users\user\Desktop\posting\springboot3.png">

그리고 JAVA 11을 설치하고 `File -> Preference -> Settings`에 들어가서 경로를 설정해줍니다.

<img src="C:\Users\user\Desktop\posting\springboot4.png">

```
"java.home": "C:\\Program Files\\Java\\jdk-11.0.8",
"spring-boot.ls.java.home": "C:\\Program Files\\Java\\jdk-11.0.8"
```

Windows 내 환경변수에서도 JAVA_HOME을 설정해주어야 합니다.
이런 설정이 잘 안되어 있으면 우측하단에 경고나 뜨거나 자동완성 및 JUnit 테스트가 잘 안될 수 있습니다.

그리고 front 프로젝트에 `vue.config.js` 파일을 생성하고 아래의 코드를 작성합니다.

`front\vue.config.js`

```javascript
module.exports = {
    outputDir: '../src/main/resources/static',
    devServer: {
        port: 3030,
        proxy: {
            '/api': {
                target: 'http://localhost:3000'
            }
        }
    }
}
```
화면 단의 포트는 3030이고 api 서버의 포트는 3000으로 정의합니다.

그리고 front 폴더에 들어가서 `npm run build` 명령어를 동작시킵니다.
이렇게 하면 `outputDir`에서 설정한 경로에 vue에서 사용할 기본 리소스가 만들어집니다.
즉 front 폴더안에 있는 public 폴더는 이제 필요가 없으니 삭제하셔도 됩니다.

`demo\src\main\resources\application.properties` 파일을 열어서 스프링부트 관련 설정을 적어줍니다.

```java
# 서버 설정
server.port=3000

# 수정하면 바로 새로고침 (배포할때 제거)
spring.devtools.livereload.enabled=true

# 데이터베이스 설정
spring.datasource.platform=postgres
spring.datasource.url=jdbc:postgresql://123.456.0.1:5432/test_db
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.jpa.database=POSTGRESQL
spring.jpa.show-sql=true
logging.level.org.hibernate.type.descriptor.sql=trace

# 데이터베이스 초기화 설정
spring.jpa.generate-ddl=false
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
```
DB 주소와 username, password는 각자에 맞게 수정해야 합니다.
build.gradle에 사용할 라이브러리를 더 추가해줍니다.

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter-security'
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation('org.springframework.boot:spring-boot-starter-test') {
		exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
	}

	//PostgreSQL 
	implementation('org.postgresql:postgresql')
}
```

이제 여기까지 설정한 결과를 가지고 실행시켜 보겠습니다.
`npm run serve`로 실행시켜서 `localhost:3030`에 Vue 화면이 뜨면 우선 Vue 설정은 잘 완료된 것입니다.

스프링도 실행시켜봅시다.
스프링부트는 톰캣이 내장되어 있기에 별다른 설정이 필요없습니다.
라이브러리 설치 완료만 기다렸다가 DemoApplication.java에서 Run을 눌러주거나
왼쪽 하단에 `Spring-Boot DashBoard`에서 실행을 시켜줘도 됩니다.

<img src="C:\Users\user\Desktop\posting\springboot5.png">

만약 이게 실행이 잘 안되면 아직 라이브러리가 설치되고 있거나 JAVA 관련 경로 설정을 잘 못했을 가능성이 있으니
다시 검토하셔야 합니다.

실행시키고 `localhost:3000`으로 들어가면 아래와 같은 화면이 나오면 정상 실행 된것입니다.
로그인 화면이 나오는 이유는 스프링부트의 기본적인 security때문에 그렇습니다.

<img src="C:\Users\user\Desktop\posting\springboot6.png">

**Postgres 연동 및 CRUD**

이제 Postgres를 연동하고 확인해보겠습니다. DB는 이미 설치되어 있고 PORT 관련 설정도 되어있다고 가정합니다.
`application.properties`에 본인 DB 주소와 ID, PW가 잘 적혀있는지 확인합니다.

DB 연동을 확인하려면 CRUD를 스프링 쪽에서 만들고 테스트 해봐야 하기에 일단 컨트롤러부터 쭉 만들겠습니다.

`src\main\java\com\example\demo\controller\rest\apiresult\BoardApiController.java`

```java
package com.example.demo.controller.rest.apiresult;

import java.util.List;

import com.example.demo.controller.rest.ApiResult;
import com.example.demo.service.board.Board;
import com.example.demo.service.board.BoardDTO;
import com.example.demo.service.board.BoardService;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

import javassist.NotFoundException;
import lombok.AllArgsConstructor;

@RestController
@AllArgsConstructor
public class BoardApiController {

    BoardService boardService;

    @GetMapping("/api/boards")
    public ApiResult<List<Board>> getAll() {
        List<Board> list = boardService.list();

        return ApiResult.<List<Board>>builder()
                .data(list)
                .build();
    }

    @GetMapping("/api/boards/{id}")
    public ResponseEntity<?> getBoardById(@PathVariable("id") Long id) {
        ResponseEntity<?> entity = null;

        try {
            entity = new ResponseEntity<BoardDTO>(boardService.view(id), HttpStatus.OK);
        } catch (NotFoundException e) {
            entity = new ResponseEntity<>(HttpStatus.NOT_FOUND);
        } catch (Exception e) {
            entity = new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
        }
        return entity;
    }

    @PostMapping("/api/boards/post")
    public ResponseEntity<?> create(@RequestBody final BoardDTO boarddto) {
        
        try {
            return new ResponseEntity<BoardDTO>(boardService.post(boarddto), HttpStatus.CREATED);
        } catch (final Exception e) {
            e.printStackTrace();
            return new ResponseEntity<>(HttpStatus.EXPECTATION_FAILED);
        }
    }

    @PutMapping("/api/boards/update/{id}")
    public ResponseEntity<?> update(@PathVariable("id") Long id, @RequestBody BoardDTO boarddto) {
        try {
            return new ResponseEntity<BoardDTO>(boardService.update(boarddto, id), HttpStatus.OK);

        } catch (Exception e) {
            return new ResponseEntity<>(HttpStatus.EXPECTATION_FAILED);
        }
    }

    @DeleteMapping("/api/boards/delete/{id}")
    public ResponseEntity<?> delete(@PathVariable("id") Long id) {
        
        ResponseEntity<?> entity = null;

        try {
            boardService.delete(id);
            entity = new ResponseEntity<>(HttpStatus.OK);
        } catch (NotFoundException e) {
            entity = new ResponseEntity<>(HttpStatus.NOT_FOUND);
        } catch (Exception e) {
            entity = new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
        }
        return entity;
    }

}
```

`@AllArgsConstructor`은 필드값을 모두 포함한 생성자를 자동 생성해줍니다.
원래는 `@Autowired`로 생성자 주입을 했는데 순환 참조라든지 여러 문제때문에 사용하지 않는게 좋다고 합니다. 아직 솔직히 이해 못해서 더 공부해야함...

`src\main\java\com\example\demo\controller\rest\ApiResult.java`

```java
package com.example.demo.controller.rest;

import lombok.Builder;
import lombok.Getter;
import lombok.NonNull;
import lombok.Setter;

@Getter @Setter
public class ApiResult<T> {
    @NonNull
    private T data;
    private String message;

    public ApiResult() { }
    public ApiResult(T data) {
        this.data = data;
    }

    @Builder
    public ApiResult(T data, String message) {
        this.data = data;
        this.message = message;
    }
}
```

`@Getter @Setter`를 통해 코드 가독성이 증가하고 코드 수정도 편해진다.

`src\main\java\com\example\demo\service\auditor\Auditor.java`

```java
package com.example.demo.service.auditor;

import java.time.LocalDateTime;

import javax.persistence.Column;
import javax.persistence.EntityListeners;
import javax.persistence.MappedSuperclass;

import org.springframework.data.annotation.CreatedBy;
import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedBy;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import lombok.Getter;
import lombok.Setter;

@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
@Getter @Setter
public abstract class Auditor<U> {
    
    @CreatedBy
    private String reguser;

    @CreatedDate
    private LocalDateTime regdate;

    @LastModifiedBy
    private String moduser;

    @LastModifiedDate
    private LocalDateTime moddate;
}
```

제발 Date 사용하지말고 LocalDateTime 사용하자
`@EntityListeners`를 선언해야 DB에 입력되기 전에 공통으로 사용하는 Auditor도 들어가게 된다.


`src\main\java\com\example\demo\service\board\Board.java`

```java
package com.example.demo.service.board;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

import com.example.demo.service.auditor.Auditor;

import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Getter @NoArgsConstructor
public class Board extends Auditor<String> {
    
    @Id
    @GeneratedValue
    private Long id;

    private String title;

    private String content;

    @Builder
    public Board(Long id, String title, String content) {
        this.id = id;
        this.title = title;
        this.content = content;
    }
}
```

`@NoArgsConstructor`를 사용해서 생성자만 의존성 주입한다.
`@AllArgsConstructor`은 필드에도 주입을 하는데 이미 `@Builder`로 필드는 만들어놔서 오류가 난다.

`src\main\java\com\example\demo\service\board\BoardDTO.java`

```java
package com.example.demo.service.board;

import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class BoardDTO {
    
    private Long id;
    private String title;
    private String content;

    public Board toEntity() {
        return Board.builder()
                    .id(id)
                    .title(title)
                    .content(content)
                    .build();
    }

    public BoardDTO(Board board) {
        this.id = board.getId();
        this.title = board.getTitle();
        this.content = board.getContent();
    }
}
```

`src\main\java\com\example\demo\service\board\BoardRepository.java`

```java
package com.example.demo.service.board;

import org.springframework.data.jpa.repository.JpaRepository;

public interface BoardRepository extends JpaRepository<Board, Long>{
    
}
```

`src\main\java\com\example\demo\service\board\BoardService.java`

```java
package com.example.demo.service.board;

import java.util.List;
import java.util.Optional;

import org.springframework.stereotype.Service;

import javassist.NotFoundException;
import lombok.AllArgsConstructor;

@Service
@AllArgsConstructor
public class BoardService {
    
    private BoardRepository boardRepository;

    public List<Board> list() {
        List<Board> list = boardRepository.findAll();
        
        return list;
    }

    public BoardDTO view(Long id) throws Exception {
        Optional<Board> board = boardRepository.findById(id);

        if(board.isPresent()) {
            return new BoardDTO(board.get());
        } else {
            throw new NotFoundException("리소스를 찾을 수 없습니다.");
        }
    }

    public BoardDTO post(BoardDTO model) {
        Board result = boardRepository.save(model.toEntity());
        return new BoardDTO(result);
    }

    public BoardDTO update(BoardDTO model, Long id) throws Exception {
        BoardDTO boarddto = this.view(id);
        
        boarddto.setTitle(model.getTitle());
        boarddto.setContent(model.getContent());
        boarddto.setId(id);
        Board result = boardRepository.save(boarddto.toEntity());

        return new BoardDTO(result);
    }

    public void delete(Long id) throws Exception {
        this.view(id);
        boardRepository.deleteById(id);
    }

}
```

update를 할 때는 insert와 같이 save를 사용하지만 JPA가 변경감지를 하여 같은 id인데 값이 변했다면 update를 해준다.
그래서 setId에 기존 id 값을 넣고 나머지는 사용자가 작성한 값을 넣으면 된다.


```java
@SpringBootApplication(exclude = { SecurityAutoConfiguration.class })
@EnableJpaAuditing 
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}
```
`@EnableJpaAuditing`을 해주어야 Auditor.java로 만들어둔 Audit를 사용할 수 있습니다.
이 어노테이션이 없으면 생성자, 생성날짜, 수정날짜 등 Auditor.java에서 정의해놓은 값들에 대해 정상적으로 DB 입력이 안됩니다.

`exclude = { SecurityAutoConfiguration.class }`를 하지 않으면 추후 UI로 테스트할때 보안 에러가 발생합니다.

```javascript
Access to XMLHttpRequest at 'http://localhost:3000/login' (redirected from 'http://localhost:3030/api/boards/post') from origin 'http://localhost:3030' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: It does not have HTTP ok status.
```

유저 기능을 만들기전까지는 이 기능을 사용해서 테스트 합니다.


`src\test\java\com\example\demo\rest\apiresult\BoardApiControllerTests.java`

```java
package com.example.demo.rest.apiresult;

import static org.junit.jupiter.api.Assertions.assertEquals;

import java.util.List;

import com.example.demo.service.board.Board;
import com.example.demo.service.board.BoardDTO;
import com.example.demo.service.board.BoardService;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class BoardApiControllerTests {

    @Autowired
    private BoardService boardService;
    
    /* @AfterEach
    public void cleanup() {
        boardRepository.deleteAll();
    } */
    
   @Test
    public void testRead() throws Exception {
        Long id = Long.valueOf(12);
        boardService.view(id);
    }

    @Test
    public void testList() {
        List<Board> list = boardService.list();

        assertEquals(7, list.size());
    }

    @Test
    public void testSave() {
        BoardDTO boarddto = new BoardDTO();
        boarddto.setTitle("테스트 제목1");
        boarddto.setContent("테스트 본문1");

        BoardDTO result = boardService.post(boarddto);

        assertEquals("테스트 제목 시간", result.getTitle());
        assertEquals("테스트 본문 시간", result.getContent());
    }

    @Test
    public void testUpdate() throws Exception {
        List<Board> list = boardService.list();
        Long id = list.get(0).getId();

        BoardDTO boarddto = new BoardDTO();

        boarddto.setTitle("테스트 제목 수정 중22");
        boarddto.setContent("테스트 본문 수정 중22");
        
        BoardDTO result = boardService.update(boarddto, id);

        assertEquals("테스트 제목 수정 중22", result.getTitle());
        assertEquals("테스트 본문 수정 중22", result.getContent());
    }

    @Test
    public void testDelete() throws Exception {
        List<Board> list = boardService.list();
        Long id = list.get(0).getId();

        boardService.delete(id);
    }

}
```

여기까지 전부 완료되면 이런 구조가 될것입니다.

<img src="C:\Users\user\Desktop\posting\springboot7.png">


그리고 이제 테스트를 할 수 있습니다.

<img src="C:\Users\user\Desktop\posting\springboot8.png">

이미지처럼 Run Test가 생기고 클릭하면 테스트가 실행됩니다.
성공하면 이미지처럼 체크표시가 뜨고 아니면 X 표시가 뜨게 됩니다.

junit5 테스트 할 때는 AllArgsConstructor가 아니라 Autowired로 생성자 주입을 해야 정상 작동 됩니다. 위 코드를 활용하여 CRUD 테스트가 전부 정상적으로 통과하면 이제 UI를 만들어 볼 차례입니다.

**Vue.js UI 만들고 기능 테스트**

우선은 글쓰기 기능만 만들어보겠습니다.
CSS가 괜히 들어가면 코드가 복잡해지니 기본 코드만 가지고 테스트 해봅니다.

`npm install --save axios vue-router`로 필요한 라이브러리를 설치합니다.

<img src="C:\Users\user\Desktop\posting\springboot9.png">

위와 같은 구조로 파일들을 만들겁니다.

`front\src\components\Layout.vue`

```javascript
<template>
    <div id="pagewrap">
        <header>
            <slot name="header"></slot>
        </header>
        <nav id="nav">
            <slot name="nav"></slot>
        </nav>
        <section id="content">
            <slot name="content"></slot>
        </section>
        <footer>
            <slot name="footer"></slot>
        </footer>
    </div>
</template>
```

https://kr.vuejs.org/v2/guide/components-slots.html


`front\src\components\board\Input.vue`

```javascript
<template>
    <div id="app">
        <h1>Board</h1>
        <div class="container">
            <input type="hidden" v-model="board.id" />
            <div class="form-group">
                <p>제목</p>
                <input type="text" class="form-control" v-model="board.title"/>
            </div>
            <div class="form-group">
                <p>내용</p>
                <textarea class="form-control" v-model="board.content" rows="10"></textarea>
            </div>
            <div class="form-group">
                <button @click="save">저장</button>
                <button @click="$router.back()">돌아가기</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "BoardInfo",
    data() {
        return {
            board: {
                message: '',
                id: '',
                title : '',
                content: ''
            }
        }
    },
    methods : {
        save : function() {
            this.$axios.post('/api/boards/post', this.board)
            .then(() => {
                this.$router.push('/board')
            })
            .catch((ex) => {
                console.error("failed write article", ex)
            })
        }
    }    
}
</script>
```

data()에 선언해놓은 변수를 v-model로 연결합니다.
vue.js에서는 ajax대신 axios를 사용합니다.
axios와 같은 라이브러리를 사용하기 위해서는 main.js에 선언해놓아야 합니다.

설정이 됐으면 사용 방법은 간단합니다.
this.$axios.post('api 주소', 넘길 값) 문법으로 작성하면 끝입니다.
개인적으로는 ajax보다 훨씬 편한것 같습니다.

`front\src\components\board\List.vue`

```javascript
<template>
    <div>
        <button @click="write">Write</button>
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>번호</th>
                    <th>제목</th>
                    <th>작성자</th>
                    <th>날짜</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, idx) in list" :key="item.id" @click="showInfo(idx)" style="cursor:pointer;">
                    <td>{{ list.length - idx }}</td>
                    <td>{{ item.title }}</td>
                    <td>{{ item.reguser }}</td>
                    <td>{{ item.regdate }}</td>
                </tr>
            </tbody>
        </table>
        <div class="board_info" v-if="selected">
            <div style="float:right"><button @click="closeInfo">X</button></div>
            <div><button @click="deleteArticle(item.id)">삭제</button></div>
            <div><button @click="moveUpdatePage(item.id)">수정</button></div>
            <table>
                <input type="hidden" v-text="item.id">
                <tr><th>제목</th><td>{{item.title}}</td></tr>
                <tr><th>본문</th><td><span v-text="item.content"></span></td></tr>
            </table>
        </div>
    </div>
</template>

<script>
export default {
    name: 'BoardList',
    created() {
        this.fetchData()
    },
    data() {
        return {
            selected: false,
            list: [],
            item: null
        }
    },
    methods: {
        fetchData() {
            this.$axios.get('/api/boards')
            .then(response => {
                this.list = response.data.data;
            })
            .catch(error => {
                console.log(error)
            })
        },
        write() {
            this.$router.push('/board/new')
        },
        showInfo(idx) {
            this.item = this.list[idx]
            this.selected = true
        },
        closeInfo() {
            this.item = null
            this.selected = false
        },
        deleteArticle(id) {
            let articleId = id;
            this.$axios.delete('/api/boards/delete/' + articleId)
            .then(() => {
                window.location.reload();
            })
        },
        moveUpdatePage(id) {
            this.$router.push({ path: '/board/update/', query: { id : id}})
        }
    }
}
</script>
```

`front\src\components\board\Update.vue`

```javascript
<template>
    <div id="app">
        <h1>Board Update</h1>
        <div class="container">
            <input type="hidden" v-model="board.id" />
            <div class="form-group">
                <p>제목</p>
                <input type="text" class="form-control" v-model="board.title"/>
            </div>
            <div class="form-group">
                <p>내용</p>
                <textarea class="form-control" v-model="board.content" rows="10"></textarea>
            </div>
            <div class="form-group">
                <button @click="update">수정</button>
                <button @click="$router.back()">돌아가기</button>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: "BoardInfo",
    created() {
        this.fetchBoard()
    },
    data() {
        return {
            board: {
                message: '',
                id: '',
                title : '',
                content: ''
            }
        }
    },
    methods : {
        fetchBoard : function() {
            let pid = this.$route.query.id;
            if (!pid) return;
            this.$axios.get('/api/boards/' + pid)
            .then((response) => {
                this.board = response.data;
            })
            .catch((ex) => {
                console.error("not found board: ", ex);
            })
        },
        update : function() {
            this.$axios.put('/api/boards/update/'+this.board.id, this.board)
            .then(() => {
                this.$router.push('/board')
            })
            .catch((ex) => {
                console.error("failed write article", ex)
            })
        }
    }    
}
</script>
```

수정 페이지는 입력 페이지와 다르게 수정하고 싶은 게시물의 데이터를 먼저 가져와야 합니다.
fetchBoard 함수를 만들어서 created() 단계에서 데이터를 가져옵니다.
LifeCycle 개념은 지금보고 이해안되면 나중에 계속 보시는게 좋습니다.
그리고 controller에 정의한대로 id 값과 board 값을 put으로 넘기면 수정이 됩니다.
pid 값은 List.vue에서 수정 버튼을 누르면 아래 코드처럼 주소에 같이 넘겨줍니다.

```javascript
moveUpdatePage(id) {
    this.$router.push({ path: '/board/update/', query: { id : id}})
}
```

그리고 Update.vue에서는 `let pid = this.$route.query.id;` 받아서 사용합니다.


`front\src\router\index.js`

```javascript
import Vue from 'vue'
import VueRouter from 'vue-router'

import Board from 'BOARD/List.vue'
import BoardDetail from 'BOARD/Input.vue'
import BoardUpdate from 'BOARD/Update.vue'

Vue.use(VueRouter)

const router = new VueRouter({
    mode: 'history',
    routes: [
        { path : '/board', component: Board },
        { path : '/board/new', component: BoardDetail },
        { path : '/board/update', component: BoardUpdate }
    ]
})

export default router
```

주소에 맞게 어떤 컴포넌트를 표시할지 정의합니다.

`front\src\App.vue`

```javascript
<template>
  <div id="app">
    <layout>
      <div slot="header">
        <h2>Sample</h2>
      </div>

      <div slot="nav">
        <ul>
          <li><router-link to="/board">BOARD</router-link></li>
        </ul>
      </div>

      <div slot="content">
        <router-view></router-view>
      </div>

      <p slot="footer">Footer ! </p>
    </layout>
  </div>
</template>
<script>
import Layout from './components/Layout.vue'

export default {
  components: { Layout }
}
</script>
```

여기 코드에서는 router-link에 이동할 페이지를 정의하고 router-view에 내용물이 표시되게 하였습니다.
하지만 이 방식이 마음에 들지 않으시면 다른 라우팅 방식도 있으니 찾아서 사용하시면 됩니다.

`front\src\main.js`

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'
import axios from 'axios'

Vue.config.productionTip = false
Vue.prototype.$axios = axios;

new Vue({
  router,
  render: h => h(App),
}).$mount('#app')
```

vue.config.js도 수정해줍니다.

`front\vue.config.js`

```javascript
const path = require('path');

module.exports = {
    outputDir: '../src/main/resources/static',
    devServer: {
        port: 3030,
        proxy: {
            '/api': {
                target: 'http://localhost:3000'
            }
        }
    },
    configureWebpack: {
        resolve: {
            alias: {
                'BOARD': path.join(__dirname, 'src/components/board')
                
            }
        }
    }
}
```

자주쓰는 경로인 `src/components/board`를 `BOARD`라는 키워드로 묶어서 사용하여 낭비를 줄입니다.

이렇게 코드를 작성한 후 `npm run serve`와 스프링 부트를 실행시킨 후 `localhost:3000`에 들어가서 글을 작성하면 DB에 들어갑니다.

이렇게 CRUD를 완료했습니다.
아직도 미비한 부분이 많지만 혼자서 기초적인 부분을 공부하시는데 도움이 되셨다면 좋겠습니다.