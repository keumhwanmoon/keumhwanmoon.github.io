---
title: Spring Boot Repository 계층의 메서드 네이밍 - find, get, fetch 차이점
date: 2025-02-28 21:00:00 +0900
categories: [Spring Boot, Repository, Naming]
tags: [Spring Boot, Repository, Method Naming]
description: Spring Boot의 Repository 계층에서 조회 메서드 네이밍을 고민해 봅니다. find, get, fetch의 차이를 정리하고, 언제 어떤 메서드를 사용하면 좋은지 설명합니다.
---

Spring Boot에서 Repository 계층을 설계할 때, 메서드 네이밍을 어떻게 할지 고민되는 경우가 많다. 특히, 데이터를 조회하는 메서드의 이름을 `find`, `get`, `fetch` 중 어떤 걸로 해야 할지 헷갈리는 경우가 많다.

이 글에서는 `find`, `get`, `fetch`의 의미 차이를 정리하고, 언제 어떤 걸 쓰면 좋은지에 대해 다뤄보려고 한다.

---

## 1. `find`: 데이터를 찾아보지만, 없을 수도 있음 🔎

### 특징
- 데이터가 존재할 수도 있고, 없을 수도 있음.
- 조회했을 때 없으면 `null` 또는 `Optional.empty()`를 반환하는 경우가 많음.
- 보통 JPA나 쿼리 메서드에서 많이 사용됨.

### 예제
```java
public Optional<User> findUserById(Long id);
```
- id로 사용자를 찾아보지만, 존재하지 않을 수도 있다.
- 반환 타입을 Optional<User>로 감싸서 없을 수도 있다는 걸 명확히 표현하는 게 좋다.

### 언제 사용하면 좋을까?
- "찾아볼게, 근데 없을 수도 있어."라는 의미가 필요한 경우
- 반환 값이 Optional<T>일 때
- 존재하지 않는 데이터를 조회해도 예외를 던지지 않는 게 자연스러운 경우

## 2. `get`: 데이터가 반드시 존재해야 함 🚀

### 특징
- 데이터를 무조건 가져와야 함. 없으면 예외를 던지는 경우가 많음.
- "이 값이 없는 게 말이 안 됨!"이라는 경우에 적합.
- find보다 더 강제적인 느낌.

### 예제
```java
public User getUserById(Long id);
```

### 언제 사용하면 좋을까?
- "이 데이터는 반드시 존재해야 해!"라는 경우
- 예외를 던져야 하는 상황 (NoSuchElementException, EntityNotFoundException 등)
- 예: 현재 로그인한 사용자 정보 (getCurrentUser())

## 3. `fetch`: 어떤 방식으로든 데이터를 가져와야 함 🚛

### 특징
- "데이터를 반드시 가져와야 하는데, 방법은 여러 개일 수도 있음"이라는 의미.
- 단순 DB 조회가 아니라, 캐시, API, 외부 시스템을 이용해서라도 가져와야 할 때 사용됨.
- 보통 지연 로딩(Lazy Loading)과 연관되기도 함.

### 예제
```java
public User fetchUserById(Long id);
```

- findUserById()와 달리, DB에서 못 찾으면 캐시나 외부 API에서라도 데이터를 가져올 수도 있음.
### 언제 사용하면 좋을까?
- 데이터를 DB에서 찾든, 캐시에서 찾든, API에서 가져오든 반드시 가져와야 하는 경우
- 예: "이 데이터가 DB에 없으면 API에서 가져와서라도 반환해야 함"

## 4. 비교 정리 🔥  

| 메서드  | 의미 | 데이터 없을 때 |
|---------|--------------------------------|----------------------|
| `find`  | 찾아볼게, 없을 수도 있어 | `null` 또는 `Optional.empty()` |
| `get`   | 무조건 있어야 해, 없으면 문제 | 예외 던짐 (`NoSuchElementException`) |
| `fetch` | 어떤 방식으로든 가져와야 해 | 캐시, DB, API 등에서 적극적으로 찾아서 반환 |

---

## 5. 네이밍 패턴 추천 💡  

### ✔ 단일 객체 조회  

```java
findUserById(Long id);  // Optional<User> 반환, 없을 수도 있음.
getUserById(Long id);   // 반드시 존재해야 함, 없으면 예외
fetchUserById(Long id); // 캐시, DB, API 등에서 가져와야 함.
```

### ✔ 목록 조회

```java
findAllUsers(); // 전체 사용자 조회 (없으면 빈 리스트)
fetchUsers();   // 캐시나 API를 통해서라도 가져와야 하는 경우
```

### ✔ 특정 조건으로 조회

```java
findUsersByRole(String role);   // 역할(role) 기준으로 사용자 찾기
getUsersByDepartment(String department); // 부서 기준, 반드시 존재해야 함
fetchUsersByRegion(String region); // 지역(region) 기준으로, 여러 방법으로 조회
```

## 6. 결론 🏁
- find → 데이터가 없을 수도 있음
- get → 데이터가 반드시 존재해야 함
- fetch → 데이터를 어떻게든 가져와야 함
