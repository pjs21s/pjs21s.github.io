---
layout: post
title: "Component와 Util(static)은 어떤 차이를 가지고 사용할까?"
tags: Spring
comments: true
---

### @Component 사용하는 경우
- Spring 컨테이너에 의해 관리되고 다른 Bean과 상호작용
- 비즈니스 로직을 처리하거나 특정 책임 수행
- 의존성 주입

### Util 클래스
- 상태를 가지지 않는 정적 메소드
- 객체 생성 없이 바로 메서드 호출
- 코드 재사용성 높이거나 공통된 연산

### Util 클래스 PasswordEncoder 예시
- 단순히 입력된 값을 처리하는 형태라면 static 메소드로 사용해도 무방
```java
public class PasswordEncoderUtil {

    public static String encode(String rawPassword) {
        return BCrypt.withDefaults().hashToString(BCrypt.MIN_COST, rawPassword.toCharArray());
    }

    public static boolean matches(String rawPassword, String encodedPassword) {
        BCrypt.Result result = BCrypt.verifyer().verify(rawPassword.toCharArray(), encodedPassword);
        return result.verified;
    }
}

```

### @Component PasswordEncoder 예시
- Interface 구현
```java
public interface PasswordEncoder {
    String encode(String rawPassword);
    boolean matches(String rawPassword, String encodedPassword);
}

```
- BCryptPasswordEncoder 구현체
```java
import at.favre.lib.crypto.bcrypt.BCrypt;
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;

@Component
@Profile("prod") // 프로덕션 환경에서만 활성화됨
public class BCryptPasswordEncoder implements PasswordEncoder {

    @Override
    public String encode(String rawPassword) {
        return BCrypt.withDefaults().hashToString(BCrypt.MIN_COST, rawPassword.toCharArray());
    }

    @Override
    public boolean matches(String rawPassword, String encodedPassword) {
        BCrypt.Result result = BCrypt.verifyer().verify(rawPassword.toCharArray(), encodedPassword);
        return result.verified;
    }
}

```
- NoOpPasswordEncoder 구현체 (개발 환경용)
```java
import org.springframework.context.annotation.Profile;
import org.springframework.stereotype.Component;

@Component
@Profile("dev") // 개발 환경에서만 활성화됨
public class NoOpPasswordEncoder implements PasswordEncoder {

    @Override
    public String encode(String rawPassword) {
        // 암호화를 하지 않고 원래 문자열을 반환
        return rawPassword;
    }

    @Override
    public boolean matches(String rawPassword, String encodedPassword) {
        // 단순히 문자열이 같은지 비교
        return rawPassword.equals(encodedPassword);
    }
}

```
### Service 사용 예시
- PasswordEncoder 인터페이스에 의존하도로 설계한 서비스. 환경에 따라 적절한 구현체가 주입된다.
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    private final PasswordEncoder passwordEncoder;

    @Autowired
    public UserService(PasswordEncoder passwordEncoder) {
        this.passwordEncoder = passwordEncoder;
    }

    public void registerUser(String rawPassword) {
        String encodedPassword = passwordEncoder.encode(rawPassword);
        // 사용자 등록 로직 수행 (예: 데이터베이스에 저장)
        System.out.println("Encoded password: " + encodedPassword);
    }
}

```
### 프로파일 설정 (application.properties)
- 개발과 실제 배포 환경 중 선택하여 프로파일을 활성화
- application-dev.properties
```
application-dev.properties=dev
```
- application-prod.properties
```
spring.profiles.active=prod
```

### 정리

**프로파일 기반의 설정**: 개발 환경에서는 `NoOpPasswordEncoder`가 활성화되고 실제 배포 환경에서는 `BCryptPasswordEncoder`가 활성화된다. `@Profile` 어노테이션을 통해 제어되고`PasswordEncoder` 인터페이스를 통해 구체적인 구현체가 서비스에 주입된다.

**테스트 및 확장성**: 필요에 따라 다른 환경에서 구현체를 교체하거나 새로 추가할 수 있다. 스프링 컨테이너가 의존성을 관리하므로 이를 쉽게 변경할 수 있다.