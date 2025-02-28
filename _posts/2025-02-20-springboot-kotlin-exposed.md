---
layout: post
title: "스프링 부트와 코틀린, Exposed로 REST API 구현하기"
date: 2025-02-20 12:00:00 +0900
categories: [spring-boot, kotlin]
tags: [Spring Boot, Kotlin, Exposed, REST API, ORM]
description: "스프링 부트와 코틀린, Exposed를 사용해 REST API를 구현하는 방법을 소개하며, 프로젝트 설정부터 코드 작성 및 실행까지의 과정을 정리하고 GitHub 레포지토리를 공유한다."
---

---
Java와 Mybatis를 사용하는 방식 외에도 다양한 개발 방법을 경험해 보고 싶었다.  
그래서 여러 가지 기술을 간단히 설정해 보고 실험하는 예제를 작성하려고 한다.  
그 첫 번째로 스프링 부트와 코틀린을 사용하고, 코틀린 ORM 중 두번째로 Ktorm을 활용한 예제를 공유한다.  

개발을 하면서 항상 "더 좋은 방식이 있을까?"라는 질문을 던지며, 코드를 깔끔하게 유지하는 방법과 편리한 개발 환경을 구축하는 데 관심이 많다.  
최근 스프링 부트에서 코틀린(Kotlin)을 사용해 REST API를 구현하면서, ORM으로 Ktorm을 활용하는 방법이 궁금해졌다.  
그래서 직접 프로젝트를 만들어 보고, 그 결과를 <a href="https://github.com/keumhwanmoon/springboot-kotlin-exposed-demo" target="_blank" rel="noopener noreferrer">GitHub 레포지토리</a>에 올렸다.  

이 글에서는 스프링 부트와 코틀린, Exposed을 사용해 REST API를 만드는 과정을 소개하고, 환경 설정부터 코드 작성까지의 주요 단계를 정리해 본다. 또한, 완성된 데모 프로젝트를 확인할 수 있는 GitHub 링크도 함께 공유한다.
---

🤔 코틀린과 Exposed

🛠️ 코틀린과 스프링 부트

코틀린은 현대적이고 간결한 문법을 제공하는 언어로, 자바와 완벽하게 상호 운용이 가능해 스프링 부트 프로젝트에 적합하다. 특히 널 안전성(null safety)과 코루틴(coroutines) 같은 기능은 개발을 더 안전하고 편리하게 만들어 준다. 스프링 부트는 코틀린을 공식 지원하므로, 이런 장점을 최대한 활용할 수 있다.

🗄️ Exposed로 ORM

Exposed는 JetBrains에서 만든 경량 ORM 프레임워크로, DSL 기반의 타입 안전한 SQL 작성이 가능하다. JPA와 같은 무거운 프레임워크보다 가볍고 유연하게 사용할 수 있어 간단한 프로젝트에 적합하다.

🛠️ 개발 환경 설정

📋 준비물

JDK 11 이상

IntelliJ IDEA

Gradle 또는 Maven

🏗️ 프로젝트 생성

Spring Initializr에서 새 프로젝트 생성

언어로 Kotlin을 선택하고, 원하는 스프링 부트 버전을 지정

REST API를 위해 Spring Web 의존성을 추가

데이터베이스를 위해 H2 Database 추가

ORM으로 사용할 Exposed의 의존성 추가

📦 Exposed 의존성 추가

Gradle을 사용한다면 build.gradle.kts에 다음을 추가:

```kotlin
dependencies {
    implementation("org.jetbrains.exposed:exposed-core:0.41.1")
    implementation("org.jetbrains.exposed:exposed-dao:0.41.1")
    implementation("org.jetbrains.exposed:exposed-jdbc:0.41.1")
    implementation("com.h2database:h2:2.2.224")
}
```

📝 REST API 코드 작성

📂 프로젝트 구조

Entity: 테이블을 정의하는 객체

Repository: 데이터베이스 액세스 로직을 처리

Service: 비즈니스 로직 담당

Controller: API 요청을 처리

🧑‍💼 엔티티 및 테이블 정의

```kotlin
object Users : IntIdTable() {
    val name = varchar("name", 50)
    val email = varchar("email", 100)
}

class User(id: EntityID<Int>) : IntEntity(id) {
    companion object : IntEntityClass<User>(Users)

    var name by Users.name
    var email by Users.email
}
```

📥 리포지토리 및 서비스 구현

```kotlin
@Component
class UserRepository {
    fun findAll(): List<User> = transaction { User.all().toList() }
}

@Service
class UserService(private val userRepository: UserRepository) {
    fun getAllUsers(): List<User> = userRepository.findAll()
}
```

🌍 컨트롤러 구현

```java
@RestController
@RequestMapping("/users")
class UserController(private val userService: UserService) {
    @GetMapping
    fun getUsers(): List<User> = userService.getAllUsers()
}
```

🛢️ 데이터베이스 설정

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driverClassName: org.h2.Driver
    username: sa
    password:
  h2:
    console:
      enabled: true
```

🚀 애플리케이션 실행

IDE에서 실행하거나 터미널에서 실행:

```bash
./gradlew bootRun
```

http://localhost:8080/h2-console에서 데이터베이스 확인 가능

REST API 테스트:

```bash
curl http://localhost:8080/users
```

🏁 마무리

이 글에서는 스프링 부트와 코틀린, Exposed를 활용해 REST API를 구현하는 기본적인 설정과 코딩 과정을 정리했다. Exposed의 간결한 DSL 덕분에 SQL을 타입 안전하게 작성할 수 있고, 유지보수가 쉬운 코드를 작성할 수 있었다.

더 자세한 내용과 전체 코드는 GitHub 레포지토리에서 확인할 수 있다.