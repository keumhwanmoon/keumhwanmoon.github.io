---
layout: post
title: "스프링 부트 REST API 개발 시 자주 하는 실수 TOP 10"
date: 2025-02-28 12:00:00 +0900
categories: [개발, 스프링부트, REST API]
tags: [스프링부트, REST API, 자바, 백엔드개발, 개발실수]
description: 스프링 부트로 REST API를 개발할 때 개발자들이 자주 저지르는 10가지 실수를 정리하고, 이를 해결하기 위한 모범 사례와 코드 예제를 제공한다.
---

---

REST API는 현대 웹 애플리케이션 개발에서 핵심적인 부분이다. 스프링 부트는 강력한 REST API 개발 도구를 제공하지만, 개발자들이 흔히 범하는 실수들이 있다. 아래에서 스프링 부트 REST API 개발 시 가장 흔히 발생하는 10가지 실수와 그 해결책을 알아보자.

---

### 1. 적절한 HTTP 상태 코드 사용하지 않기

**문제점**: 많은 개발자들이 모든 응답에 대해 200 OK 상태 코드만 사용하고, 응답 본문에 성공/실패 메시지를 포함시킨다.

**해결책**: HTTP 프로토콜은 다양한 상태 코드를 제공한다. 이를 적절히 활용하자:
- 200 OK: 요청 성공
- 201 Created: 리소스 생성 성공
- 204 No Content: 성공했지만 반환할 내용 없음
- 400 Bad Request: 잘못된 요청
- 401 Unauthorized: 인증 필요
- 403 Forbidden: 권한 없음
- 404 Not Found: 리소스 없음
- 500 Internal Server Error: 서버 오류

```java
@PostMapping
public ResponseEntity<User> createUser(@RequestBody User user) {
    User savedUser = userService.save(user);
    return new ResponseEntity<>(savedUser, HttpStatus.CREATED);
}

@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
    userService.delete(id);
    return new ResponseEntity<>(HttpStatus.NO_CONTENT);
}
```

---

### 2. URL에 동사 사용하기

**문제점**: REST API URL에 동사를 사용하는 것은 REST 원칙에 맞지 않는다.

**잘못된 예**:
```
/getAllUsers
/createUser
/deleteUser/{id}
```

**올바른 예**:
```
GET /users - 모든 사용자 조회
POST /users - 새 사용자 생성
DELETE /users/{id} - 특정 사용자 삭제
```

---

### 3. 예외 처리 미흡

**문제점**: 많은 API가 적절한 예외 처리 없이 기본 오류 응답을 반환한다.

**해결책**: 전역 예외 처리기를 사용하여 일관된 오류 응답 형식을 제공하자:

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleResourceNotFoundException(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse("NOT_FOUND", ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGlobalException(Exception ex) {
        ErrorResponse error = new ErrorResponse("INTERNAL_ERROR", "예상치 못한 오류가 발생했다");
        return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

---

### 4. 버전 관리 부재

**문제점**: API 버전 관리가 없으면 기존 클라이언트에 영향을 주지 않고 API 변경이 어렵다.

**해결책**: URL, 헤더, 매개변수 등을 통해 API 버전을 관리하자:

```java
// URL 기반 버전 관리
@RequestMapping("/api/v1/users")
public class UserControllerV1 { ... }

@RequestMapping("/api/v2/users")
public class UserControllerV2 { ... }

// 헤더 기반 버전 관리
@RequestMapping(value = "/api/users", headers = "X-API-VERSION=1")
public class UserControllerV1 { ... }
```

---

### 5. 페이지네이션 구현 미흡

**문제점**: 대량의 데이터를 반환할 때 페이지네이션이 없으면 성능 문제가 발생한다.

**해결책**: 스프링 데이터의 페이지네이션 지원을 활용하자:

```java
@GetMapping
public ResponseEntity<Page<User>> getAllUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size) {
    
    Pageable pageable = PageRequest.of(page, size);
    Page<User> users = userService.findAll(pageable);
    return ResponseEntity.ok(users);
}
```

---

### 6. 적절한 응답 형식 사용하지 않기

**문제점**: 일관되지 않은 응답 형식은 클라이언트 측 처리를 복잡하게 만든다.

**해결책**: 표준화된 응답 형식을 사용하자:

```java
public class ApiResponse<T> {
    private boolean success;
    private String message;
    private T data;
    
    // 생성자, getter, setter 생략
}

@GetMapping("/{id}")
public ResponseEntity<ApiResponse<User>> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    ApiResponse<User> response = new ApiResponse<>(true, "사용자를 성공적으로 조회했다", user);
    return ResponseEntity.ok(response);
}
```

---

### 7. API 문서화 부족

**문제점**: 문서화되지 않은 API는 사용하기 어렵고 유지보수가 힘들다.

**해결책**: Swagger/OpenAPI와 같은 도구를 사용하여 API를 문서화하자:

```java
@Configuration
public class SwaggerConfig {
    @Bean
    public OpenAPI customOpenAPI() {
        return new OpenAPI()
                .info(new Info()
                        .title("사용자 관리 API")
                        .version("1.0")
                        .description("사용자 관리를 위한 REST API 문서"));
    }
}
```

---

### 8. 입력 유효성 검사 부족

**문제점**: 유효성 검사가 없으면 잘못된 데이터가 시스템에 저장될 수 있다.

**해결책**: Bean Validation을 사용하여 입력 데이터를 검증하자:

```java
public class UserRequest {
    @NotBlank(message = "이름은 필수다")
    private String name;
    
    @Email(message = "유효한 이메일 주소를 입력해야 한다")
    private String email;
    
    @Size(min = 8, message = "비밀번호는 최소 8자 이상이어야 한다")
    private String password;
    
    // getter, setter 생략
}

@PostMapping
public ResponseEntity<User> createUser(@Valid @RequestBody UserRequest userRequest) {
    // 유효성 검사를 통과한 요청 처리
}
```

---

### 9. 하이퍼미디어 링크(HATEOAS) 부재

**문제점**: REST API의 중요한 원칙 중 하나는 하이퍼미디어를 통한 자기 설명적 특성이다.

**해결책**: 스프링 HATEOAS를 사용하여 관련 리소스에 대한 링크를 제공하자:

```java
@GetMapping("/{id}")
public ResponseEntity<EntityModel<User>> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    
    EntityModel<User> resource = EntityModel.of(user);
    resource.add(linkTo(methodOn(UserController.class).getUser(id)).withSelfRel());
    resource.add(linkTo(methodOn(UserController.class).getAllUsers(0, 10)).withRel("users"));
    resource.add(linkTo(methodOn(OrderController.class).getOrdersByUser(id)).withRel("orders"));
    
    return ResponseEntity.ok(resource);
}
```

---

### 10. 보안 고려 부족

**문제점**: REST API에서 보안을 간과하면 심각한 취약점이 발생할 수 있다.

**해결책**:
- HTTPS 사용
- JWT 또는 OAuth2와 같은 안전한 인증 방식 구현
- 입력 데이터 검증
- CORS 구성
- 속도 제한 구현

```java
@Configuration
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()  // REST API에서는 CSRF 보호가 필요 없을 수 있음
            .authorizeRequests()
                .antMatchers("/api/public/**").permitAll()
                .antMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            .and()
            .oauth2ResourceServer().jwt();  // JWT 토큰 사용
        
        return http.build();
    }
}
```

---

## 결론

스프링 부트로 REST API를 개발할 때 이러한 일반적인 실수를 피하면 더 나은 품질의 API를 제공할 수 있다. REST 원칙을 준수하고, 적절한 상태 코드를 사용하며, 예외를 적절히 처리하고, API를 문서화하는 것이 중요하다. 또한 페이지네이션, 버전 관리, 유효성 검사, 보안을 고려하면 더 강력하고 확장 가능한 API를 구축할 수 있다.

REST API 개발은 단순히 기능만 구현하는 것이 아니라, 좋은 설계 원칙과 실천 방법을 따르는 것이 중요하다. 이 글에서 제시한 실수와 해결책을 참고하여 더 나은 REST API를 개발하길 바란다.

## 출처
이 글은 Java Guides의 <a href="https://www.javaguides.net/2024/12/top-10-spring-boot-rest-api-mistakes.html" target="_blank" rel="noopener noreferrer">Top 10 Spring Boot REST API Mistakes</a> 포스팅을 번역 및 재구성한 것이다. 원문에서 제시된 REST API 개발 시 흔한 실수와 해결책을 바탕으로 스프링 부트 환경에 적합하게 정리했다.