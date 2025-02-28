---
layout: post
title: "IntelliJ에서 JavaScript 객체 속성 자동 정렬 설정하기"
date: 2016-11-19 12:00:00 +0900
categories: [IntelliJ]
tags: [IntelliJ, 코드 스타일, 자동 정렬, JavaScript, 개발 환경]
description: "IntelliJ에서 Ctrl + Alt + L 단축키를 사용하여 JavaScript 객체의 속성을 콜론(:)에 맞춰 자동 정렬하는 방법을 소개합니다."
---

IntelliJ에서 `Ctrl + Alt + L` (macOS에서는 `Cmd + Alt + L`)을 누르면 코드 자동 정렬 기능을 사용할 수 있다. 이 기능을 통해 코드 스타일을 일관되게 유지할 수 있으며, 특히 JavaScript 객체의 속성을 정렬하는 데 유용하다.

### JavaScript 객체 속성 정렬 설정

JavaScript 객체의 속성을 콜론(:)에 맞춰 정렬하려면 다음과 같이 설정한다:

1. **설정 열기**  
   - Windows/Linux: `File` > `Settings` > `Editor` > `Code Style`
   - macOS: `IntelliJ IDEA` > `Preferences` > `Editor` > `Code Style`

2. **JavaScript 선택**  
   왼쪽 패널에서 `JavaScript`를 선택한다.

3. **객체 속성 정렬 설정**  
   `Tabs and Indents` 탭에서 `Align object properties` 옵션을 `On colon`으로 설정한다.

이렇게 설정하면, 객체의 속성이 콜론(:)을 기준으로 정렬된다.

### 예시

**정렬 전:**

```javascript
const obj = {
  name: 'Alice',
  age: 30,
  job: 'Engineer'
};
```

**정렬 후:**

```javascript
const obj = {
  name: 'Alice',
  age : 30,
  job : 'Engineer'
};
```

이제 `Ctrl + Alt + L` (또는 `Cmd + Alt + L`)을 눌러 자동 정렬을 실행하면, 객체의 속성이 콜론에 맞춰 정렬된다.

### 참고 사항

IntelliJ의 버전에 따라 설정 위치가 다를 수 있다. 최신 버전에서는 `Editor` > `Code Style` > `JavaScript` > `Tabs and Indents` 탭에서 `Align object properties` 옵션을 찾을 수 있다.

이러한 작은 설정을 통해 코드의 가독성과 일관성을 높일 수 있다. 자동 정렬 기능을 활용하여 효율적인 개발 환경을 구축해 보자.