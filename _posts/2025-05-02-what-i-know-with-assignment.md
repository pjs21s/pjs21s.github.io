---
layout: post
title: "사전과제 전형을 수행하면서 생긴 질문들"
tags: Django
comments: true
---

최근에는 강의 자료를 만들고 가르치는 것만 하다보니 Django 기반 사전과제가 생각보다 어려웠다. 객관적으로 보았을 때 요구사항이 어려운 과제는 아니었지만 Spring, Flask를 주로 사용하다가 막상 하려니 프레임워크에서 자주 사용하는 문법들을 생각보다 많이 알아야 한다는 점을 깨달았다. 그래서 과제를 하면서 그리고 준비하면서 몰랐던 점들과 의문들을 기록했다. 예전에는 언어나 프레임워크에 갇혀있지 않고 개발을 해야한다고 생각했는데 또 이러한 점을 보니 생각이 조금은 바뀌기도 한다. 기업 입장에서는 빠르게 성과를 낼 수 있는 사람을 원할테니 말이다.

### Django ForeignKey on_delete
1. CASCADE
	1. 참조 대상이 삭제 되면 현재 모델 인스턴스도 함께 삭제된다.
	2. 부모가 사라지면 자식도 같이 삭제할 때 사용한다.
2. PROTECT
	1. 대상 객체를 삭제하려 하면 `ProtectedError` 예외를 발생시켜 삭제 자체를 막는다.
	2. RESTRICT와의 차이
		1. 관련 객체가 하나라도 있으면 무조건 삭제가 불가하다.
3. RESTRICT
	1. 같은 트랜잭션 안에서 모두 같이 지워져 고아 레코드가 남지 않으면 삭제를 허용한다.
4. SET_NULL
	1. 삭제되면 FK 값을 NULL로 설정한다. 필드 선언 시 `null=True`가 필수이다.
5. SET_DEFAULT
	1. 삭제되면 FK 값을 default 값으로 변경한다. FK 필드에 `default=_` 가 정의되어 있어야 한다.
6. SET
	1. 삭제 시 지정한 값이나 callable 반환값을 넣는다. callable를 사용하면 모델 import 시점에 DB 조회가 실행되는 것을 피할 수 있다.
	2. callable이란 무엇인가?
		1. 함수, lambda, 클래스 그리고 `__call__()` 메소드를 가진 인스턴스가 모두 callable(obj) 테스트 통과한다.
7. DO_NOTHING
	1. 아무 작업도 하지 않는다.

**그 외**
1. soft delete에는 어떤 전략이 적절할까?
	1. 실제 delete() 호출이 거의 없고 호출 시에 막고 싶다.
		1. PROTECT -> 실수로 hard delete 하면 ProtectedError로 방어한다.
	2. 부모를 영구 삭제하는 경우 자식도 영구 삭제하고 싶다.
		1. CASCADE
2. `pre_delete`와 `post_delete`의 차이는 무엇일까?
	1. `pre_delete`: delete() 후, DB 삭제 이전
		1. 레코드가 아직 DB에 있으므로 조회 가능하다.
		2. 잘못 지웠다면 raise 통해 삭제 취소 가능하다.
	2. `post_delete`: DB 삭제 완료 후
		1. 실제 삭제가 이루어졌기 때문에 로그나 알림 시 명확한 타이밍이다.
	3. `**pre_delete**`, `post_delete`는 항상 호출될까?
		1. ORM을 통해 삭제하면 호출되지만 직접 쿼리를 작성하는 것처럼 DB 레벨에서 일어나는 삭제에서는 호출되지 않는다.
3. `SET`에서 callable을 사용하는 구체적인 이유는 무엇일까?
	1. `on_delete=SET(get_sentinel_user())` 처럼 괄호를 붙여서 넘기면 함수가 즉시 실행된다.
		1. DB 접속, 조회, 생성 모듈이 import 될 때마다 반복된다. (test, migration, runserver 등)
	2. `on_delete=SET(get_sentinel_user)` 처럼 callable 객체를 전달하면 DB 접근이 없다.
		1. 모델을 가져오기만 하는 작업들에서는 DB를 치지 않아도 된다.
		2. 모델 import 단계에서 예기치 않은 DB 접근을 피한다.
		3. 실제 삭제 시점에 필요한 만큼 DB에 접근한다.


### DRF Viewset
`ModelViewSet`도 상위에 `GenericAPIView`가 있는데 왜 `ModelViewSet`을 더 사용하는 듯이 말할까?

1. `Mixin + GenericAPIView`
	1. 5-6줄씩 동일한 보일러플레이트를 반복
	2. `path()` / `re_path()`를 각각 작성
	3. 필요한 `mixin`을 스스로 선택 (`ListModelMixin`, `CreateModelMixin`)
	4. 어떤 기능이 섞였는지 클래스 헤더를 봐야 파악 가능
	5. 노출되는 HTTP 메서드를 명확히 선택 가능
	6. `Django CBV + DRF Mixin` 계층 구조 이해 필요
2. `viewsets.ModelViewSet`
	1. `class BookViewSet(ModelViewSet):` 한 줄로 끝
	2. router가 list / retrieve / create 등 패턴을 자동 생성
	3. ModelViewSet이 6가지 표준 액션을 이미 믹스인해 둠 (list, retrieve, create, update, partial_update, destroy)
	4. @action(detail=False) 같은 헬퍼 데코레이터로 비표준 엔트포인트 추가가 수월
	5. 기본 액션이 과도하면` http_method_names`나 `mixin` 제거로 비활성화해야 함
	6. 단일 추상화만 익히면 되기에 온보딩 속도 향상

**그 외**
1. 클래스 헤더를 봐야 파악 가능하다는 것이 무슨 의미일까?
	1. `Mixin + GenericAPIView` 패턴은 지원 메서드 목록이 클래스 선언부에 숨어 있다.
	2. `class BookDetail(GenericAPIView, mixins.RetrieveModelMixin, mixins.UpdateModelMixin):`
	3. 처음보는 사람은 클래스를 하나씩 보면서 추론해야 한다.
		1. `ReadOnlyModelViewSet, @action(methods=['post'])`는 더욱 직관적이다.
2. 믹스인 특징 중 HTTP 메서드를 명확히 선택 가능하다는 것이 좋다는 의미일까?
	1. ListModelMixin + CreateModelMixin -> GET / POST 만
	2. RetrieveUpdateDestroyMixin -> GET / PUT/PATCH /DELETE
	3. 보안, API면에서 과하게 열어두지 않는 점이 강점
3. HTTP 메서드 제어
	1. `http_method_names`는 ViewSet 전체에 적용
	2. `@action()`은 해당 액션에만 적용
4. 헬퍼 데코레이터를 사용할 수 있다는 것에 대해 장단점은 무엇인가?
	1. 장점
		1. 기존 ViewSet 안에 비표준 엔드포인트를 쉽게 추가
		2. Swagger가 메타데이터를 읽어 스키마에 포함
		3. 권한, 스로틀링, 시리얼라이저 선택을 옵션으로 선언
	2. 단점
		1. URL 패턴이 자동 생성되어 명시적 URLConf 없이 숨어있을 수 있다.
		2. CRUD와 성격이 전혀 다른 로직을 마구 넣어 클래스가 비대해질 가능성 있다.
		3. 너무 많은 커스텀 액션이 쌓여 라우터 경로, 네이밍 충돌 위험이 있다.
	3. 이러한 단점을 극복하기 위해 어떻게 해야할까?
		1. url_name을 사용하거나 url을 모아 문서화, path를 쓰면 중복이 된다.
		2. @action을 사용하지 않으면서 urls.py에 path를 사용하여 수동으로 정의
5. 기본 액션이 과도하다는 의미는 무엇일까?
	1. django에서 action이라는 것은 6가지를 의미한다.
		1. list, retrieve, create, update, partial_update, destroy
		2. 그런데 단순 조회를 하거나 삭제되면 안되는 데이터에 대한 API가 있을 때 GET만 필요하거나 DELETE가 정의되어 있으면 안된다. 그러므로 http_method_names = ['get'] 처럼 수동으로 지정해야 한다.
6. ViewSet 클래스를 선언 후 상단에는 초기 세팅 영역일까?
	1. 클래스 레벨의 구성 값 선언
		1. 가독성, 실행 시점, 공식 문서 예시 스타일
	2. 자주 사용하는 초기 세팅 속성들
		1. 쿼리, 시리얼라이저: queryset, serializer_class, lookup_field
		2. 인증, 권한, 스로틀: authentication_classes, permission_classes, throttle_classes
		3. HTTP 제한: http_method_names = ['get', 'patch']
		4. 속도, 캐시: renderer_classes, parser_classes
		5. 기타: schema, versioning_class

### @action 데코레이터
1. 추가 엔드포인트 정의
2. 라우팅 메타데이터 지정
	1. `detail=True/False`
		1. True -> 개별 객체 URL (`/users/{pk}/...`)
		2. False -> 컬렉션 URL (`/users/...`)
3. HTTP 메서드 선택
	1. 기본은 GET
4. URL, 이름 커스터마이즈
	1. `url_path`, `url_name` 통해 원하는 대로 변경 가능
5. 액션별 설정 오버라이드
	1. 권한, 스로틀, 시리얼라이저, 필터를 메서드 수준에서 재정의 가능
	2. `@action(detail=True, permission_classes=[IsAdminUser], serializer_class=PwdSerializer)`

### asgi, wsgi
1. WSGI (Web Server Gateway Interface)
	1. 파이썬 웹 프레임워크와 웹서버 간 동기 인터페이스 표준화
	2. 요청마다 단일 쓰레드 / 단일 코루틴 -> 블로킹 I/O 전체
		1. 멀티 쓰레드 worker로 동시 처리가 가능하지만 요청당 블로킹
	3. `Gunicorn, uWSGI, mod_wsgi` 등 대표 서버
2. ASGI (Asynchronous Server Gateway Interface)
	1. 비동기 하위호환 인터페이스
	2. 이벤트 루프 기반 코루틴 -> 비동기, 실시간 스트림, 웹소켓 지원
	3. `Uvicorn, Daphne, Hypercorn` 등 대표 서버

### SmallIntegerField, BigIntegerField에서 max_length를 무시하는 이유는 무엇일까?
1. SMALLINT, BIGINT 자료형의 저장 범위를 타입 자체로 고정한다. VARCHAR처럼 옵션이 존재하지 않는다.
2. Django는 필드 생성 시 자동으로 `MaxValueValidator / MinValueValidator`를 붙여 숫자 범위를 검사한다.
3. 과거에는 max_length를 넣는 예가 있었기 때문에 코드 호환을 위해 매개변수를 제거하지 않고 경고만 출력하도록 변경했다.

### default=datetime.date.today와 auto_now_add=True 차이는 무엇일까?
1. `default=datetime.date.today`
	1. 인스턴스 생성 시 파이썬이 한 번만 호출해서 필드 값에 넣는다.
	2. 값 덮어쓰기와 수정이 가능하다.
		1. admin에서 수정 가능
	3. 기본 날짜는 오늘이지만 필요하면 고칠 수 있는 필드인 경우 사용
		1. 기본 마감일
2. `auto_now_add=True`
	1. DB에 첫 save() 될 때 `pre_save()`가 자동으로 필드 값을 today/now로 채운다.
	2. 수동 변경이 불가하다.
		1. read-only로 설정되어 수정 불가
		2. `update()` 쿼리나 `obj.save(update_fields=[...])`로는 값 수정이 가능 (폼/어드민은 차단)
	3. 레코드 생성 시각을 변경 불가하게 기록하는 경우 사용
		1. created_at, 가입 일시

### AbstractUser와 AbstractBaseUser 차이는 무엇일까?
1. AbstractUser
	1. 기본 User 모델에서 id만 제외한 것
	2. 기본 User 구조에서 필드만 늘리고 싶을 때
2. AbstractBaseUser
	1. 비밀번호, 마지막 로그인 등 인증 최소 필드만 포함
	2. username 없이 이메일만 로그인
	3. 사번, 휴대폰 등 전혀 다른 식별자 사용
3. 둘 다 `settings.AUTH_USER_MODEL`을 프로젝트 시작 전에 지정 필요