---
layout: post
title: "스프링 부트와 코틀린, Ktorm으로 REST API 구현하기"
date: 2025-02-21 12:00:00 +0900
categories: [spring-boot, kotlin]
tags: [Spring Boot, Kotlin, Ktorm, REST API, ORM]
description: "스프링 부트와 코틀린, Ktorm을 사용해 REST API를 구현하는 방법을 소개하며, 프로젝트 설정부터 코드 작성 및 실행까지의 과정을 정리하고 GitHub 레포지토리를 공유한다."
---

---

Java와 Mybatis를 사용하는 방식 외에도 다양한 개발 방법을 경험해 보고 싶었다.  
그래서 여러 가지 기술을 간단히 설정해 보고 실험하는 예제를 작성하려고 한다.  
그 첫 번째로 스프링 부트와 코틀린을 사용하고, 코틀린 ORM 중 두번째로 Ktorm을 활용한 예제를 공유한다.  

개발을 하면서 항상 "더 좋은 방식이 있을까?"라는 질문을 던지며, 코드를 깔끔하게 유지하는 방법과 편리한 개발 환경을 구축하는 데 관심이 많다.  
최근 스프링 부트에서 코틀린(Kotlin)을 사용해 REST API를 구현하면서, ORM으로 Ktorm을 활용하는 방법이 궁금해졌다.  
그래서 직접 프로젝트를 만들어 보고, 그 결과를 <a href="https://github.com/keumhwanmoon/springboot-kotlin-ktorm-demo" target="_blank" rel="noopener noreferrer">GitHub 레포지토리</a>에 올렸다.  

이 글에서는 스프링 부트와 코틀린, Ktorm을 사용해 REST API를 만드는 과정을 소개하고, 환경 설정부터 코드 작성까지의 주요 단계를 정리해 본다. 또한, 완성된 데모 프로젝트를 확인할 수 있는 GitHub 링크도 함께 공유한다.

---

## 🤔 코틀린과 Ktorm

### 🛠️ 코틀린과 스프링 부트
코틀린은 현대적이고 간결한 문법을 제공하는 언어로, 자바와 완벽하게 상호 운용이 가능해 스프링 부트 프로젝트에 적합하다. 특히 널 안전성(null safety)과 코루틴(coroutines) 같은 기능은 개발을 더 안전하고 편리하게 만들어 준다. 스프링 부트는 코틀린을 공식 지원하므로, 이런 장점을 최대한 활용할 수 있다.

### 🗄️ Ktorm으로 ORM
<a href="https://www.ktorm.org/" target="_blank" rel="noopener noreferrer">Ktorm</a>은 코틀린 전용으로 설계된 가벼운 ORM 프레임워크다. 무거운 ORM과 달리 간단하면서도 코틀린의 언어 특성을 잘 활용해 타입 안전성과 유창한(fluent) API를 제공한다. SQL 쿼리를 코틀린 스타일로 작성할 수 있어 코드가 깔끔하고 읽기 쉬운 점이 매력적이다.

## 🛠️ 개발 환경 설정

### 📋 준비물
- **JDK 11 이상**: 스프링 부트는 Java 기반이므로 필수.
- **IntelliJ IDEA**: 코틀린과 스프링 부트를 지원하는 IDE라면 어떤 것이든 상관없지만, 여기서는 IntelliJ를 사용.
- **Gradle** 또는 **Maven**: 의존성 관리를 위해 필요.

### 🏗️ 프로젝트 생성
1. <a href="https://www.ktorm.org/" target="_blank" rel="noopener noreferrer">Spring Initializr</a>에 접속해 새 프로젝트를 생성.
2. 언어로 **Kotlin**을 선택하고, 원하는 스프링 부트 버전을 지정.
3. REST API를 위해 **Spring Web** 의존성을 추가.
4. 데모를 위해 인메모리 데이터베이스인 **H2 Database**를 추가.

Ktorm은 스프링 데이터(Spring Data) 프로젝트가 아니므로, Spring Initializr에서 바로 추가할 수 없다. 따라서 수동으로 의존성을 추가해야 한다.

### 📦 Ktorm 의존성 추가
Gradle을 사용한다면 `build.gradle.kts`에 다음을 추가:

```kotlin
dependencies {
    implementation("org.ktorm:ktorm-core:3.4.1")
    // 기타 의존성...
}
```

Maven이라면 `pom.xml`에 아래를 추가:

```xml
<dependency>
    <groupId>org.ktorm</groupId>
    <artifactId>ktorm-core</artifactId>
    <version>3.4.1</version>
</dependency>
```

## 📝REST API 코드 작성

### 📂 프로젝트 구조
스프링 부트 프로젝트는 보통 컨트롤러, 서비스, 리포지토리, 엔티티로 구성된다. Ktorm을 사용할 때는 엔티티를 코틀린 데이터 클래스로 정의하고, 리포지토리에서 Ktorm의 DSL을 활용해 데이터베이스 작업을 처리한다.

### 🧑‍💼 엔티티 정의
간단한 `User` 엔티티 예제:

```kotlin
data class User(
    val id: Int,
    val name: String,
    val email: String
)
```

### 🗃️ 테이블 정의
Ktorm에서는 데이터베이스 테이블에 매핑되는 테이블 객체를 정의한다:

```kotlin
object Users : Table<Nothing>("users") {
    val id = int("id").primaryKey()
    val name = varchar("name")
    val email = varchar("email")
}
```

### 📥 리포지토리 구현
스프링 데이터의 리포지토리 인터페이스 대신, Ktorm의 DSL을 사용해 직접 리포지토리 클래스를 작성:

```kotlin
@Component
class UserRepository(private val database: Database) {
    fun findAll(): List<User> {
        return database.from(Users).select().map { row ->
            User(
                id = row[Users.id]!!,
                name = row[Users.name]!!,
                email = row[Users.email]!!
            )
        }
    }
}
```

### 🛢️ 데이터베이스 설정
H2를 사용하므로 `application.yml`에 다음과 같이 설정:

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

Ktorm에서 사용할 `Database` 인스턴스는 스프링이 제공하는 `DataSource`를 활용해 생성:

```kotlin
@Configuration
class KtormConfig {
    @Bean
    fun database(dataSource: DataSource): Database {
        return Database.connect(dataSource)
    }
}
```

## 🚀 애플리케이션 실행

1. IDE에서 실행 버튼을 누르거나, 터미널에서 다음 명령어를 사용해 애플리케이션을 빌드하고 실행:
   ```bash
   ./gradlew bootRun
   ```
2. H2 데이터베이스가 메모리에서 초기화되며, `http://localhost:8080/h2-console`에서 확인 가능.
3. Postman이나 curl로 REST API 테스트:
   ```bash
   curl http://localhost:8080/users
   ```

## 🏁 마무리

이 글에서는 스프링 부트와 코틀린, Ktorm을 활용해 REST API를 구현하는 기본적인 설정과 코딩 과정을 정리했다. 코틀린의 깔끔한 문법과 Ktorm의 간단한 ORM 방식 덕분에 유지보수가 쉬운 코드를 작성할 수 있었다.

더 자세한 내용과 전체 코드는 <a href="https://github.com/keumhwanmoon/springboot-kotlin-ktorm-demo" target="_blank" rel="noopener noreferrer">GitHub 레포지토리</a>에서 확인할 수 있다.

---