---
layout: post
title: "IntelliJ 단축키 충돌 해결 방법"
date: 2019-04-01 12:00:00 +0900
categories: [IntelliJ]
tags: [IntelliJ, 단축키, NVIDIA, 충돌 해결, 개발환경]
description: "IntelliJ에서 Ctrl + Alt + M 단축키가 작동하지 않는 문제의 원인과 해결 방법을 정리한다. 특히 NVIDIA Geforce Experience와의 충돌을 해결하는 방법을 포함한다."
---

IntelliJ에서 메서드 추출 (Ctrl + Alt + M) 단축키가 작동하지 않는다면, 다른 프로그램이 해당 단축키를 선점하고 있을 가능성이 크다. 이럴 때는 어떤 프로그램이 단축키를 차지하고 있는지 확인하고 해제해야 한다.

### 단축키 충돌 확인 방법
1. **다른 프로그램 실행 여부 확인**  
   먼저 백그라운드에서 실행 중인 프로그램을 살펴본다. `Ctrl + Alt + M` 같은 조합은 여러 프로그램에서 사용할 수 있기 때문에, 작업 관리자를 열어 불필요한 프로그램을 종료하는 것도 한 가지 방법이다.

2. **Windows 단축키 설정 확인**  
   Windows에서 특정 단축키가 OS 레벨에서 예약되어 있는 경우도 있다. '설정 > 키보드 > 단축키' 메뉴에서 시스템이 선점한 키 조합이 있는지 확인한다.

3. **IntelliJ 내부 단축키 설정 확인**  
   IntelliJ에서 `File > Settings > Keymap`으로 이동하여 해당 단축키가 올바르게 설정되어 있는지 확인한다. 만약 변경되었다면, 기본값으로 복원하거나 다른 키 조합으로 설정할 수도 있다.

### NVIDIA Geforce Experience 문제 해결
많은 경우, `Ctrl + Alt + M` 단축키를 방해하는 주범은 **NVIDIA Geforce Experience**다. 이 프로그램에서는 마이크를 끄거나 켜는 기능이 해당 단축키에 할당되어 있다.

이를 해결하려면 다음 단계를 따른다:

1. **NVIDIA Geforce Experience 실행**  
   시작 메뉴에서 `NVIDIA Geforce Experience`를 검색하여 실행한다.

    ![NVIDIA 단축키 설정](/assets/images/2019.04.01_nvidia-keyboard-shortcut.png)

2. **설정 메뉴 이동**  
   오른쪽 상단의 프로필 아이콘을 클릭한 후 '설정'으로 들어간다.

3. **단축키 변경 또는 해제**  
   `키보드 바로가기` 탭에서 `마이크 끄기/켜기 토글` 항목을 찾는다. 해당 항목을 선택한 후, 단축키를 제거하거나 다른 키 조합으로 변경한다.

4. **설정 저장 및 테스트**  
   변경 사항을 저장한 후, IntelliJ에서 `Ctrl + Alt + M`을 다시 시도해 본다.

### 추가 해결 방법
위 방법으로 해결되지 않는다면, 다른 프로그램에서도 단축키를 점유하고 있는지 확인해야 한다.

1. **다른 그래픽 드라이버 또는 오버레이 프로그램 확인**  
   - AMD Radeon Software, MSI Afterburner, OBS Studio 같은 프로그램도 특정 단축키를 선점할 수 있다.
   - 해당 프로그램의 설정에서 `Ctrl + Alt + M`이 사용되고 있는지 확인하고 해제한다.

2. **Windows 게임 바 (Game Bar) 설정 변경**  
   - `Windows + G`를 눌러 게임 바를 실행하고, 설정에서 `Ctrl + Alt + M` 관련 단축키가 할당되어 있는지 확인한다.

3. **IntelliJ 단축키 변경**  
   - 만약 단축키 충돌을 해결할 수 없다면, IntelliJ에서 `Ctrl + Alt + M` 대신 다른 단축키를 설정하는 것도 방법이다.
   - `File > Settings > Keymap`에서 `Extract Method`를 검색한 후, 원하는 키 조합으로 변경할 수 있다.

### 결론
IntelliJ의 `Ctrl + Alt + M` 단축키가 작동하지 않는 문제는 주로 다른 프로그램과의 충돌 때문인 경우가 많다. 특히 NVIDIA Geforce Experience가 가장 흔한 원인이며, 이 설정을 변경하면 쉽게 해결될 가능성이 높다. 만약 그래도 문제가 해결되지 않는다면, 다른 프로그램에서 동일한 단축키를 사용하는지 확인하고 조정하는 것이 필요하다.