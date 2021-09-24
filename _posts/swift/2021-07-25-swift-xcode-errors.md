---
title: "Xcode에서 내 기기로 앱 실행하려다 마주친 오류들"
toc: true
toc_sticky: true
categories:
    - swift
tags:
    - swift
    - 스위프트
    - Xcode
    - iOS
last_modified_at: 2021-07-25
---

## 내 기기로 실행하는 게 안 된다?!

스위프트 공부를 하면서 초간단 앱을 만들어보고 있었는데 어느 날 시뮬레이터가 아닌 내 기기에 깔아서 확인하고 싶은 마음이 들었다.

아주 옛날에 한 번 확인해본 적이 있는데, 그냥 USB로 기기를 연결하면 됐던 것 같아서 별 생각 없이 연결했는데 이게 웬 걸, 연결이 안 됐다.

<br/>

### 에러 1 : Xcode Please reconnect the device

이 에러 메시지가 계속 나와서 에러 그대로 다시 연결하기를 반복했다.

핸드폰과 맥북을 껐다 켜면 된다는 구글 선생님들의 말씀을 따라 각각 기기를 껐다 켜 봤지만 해결되지 않았다.

몰까... 하며 귀찮음에 포기하려던 차에, 나와 같은 상황의 블로그 글을 보게 되었다.

[Xcode Please reconnect the device 문제 해결하기](https://oneday0012.tistory.com/237)

<br/>

연결상태의 문제가 아니라, Xcode 버전과 iOS 버전의 간극때문이었다니...

Xcode의 **[Windows] → [Devices and Simulator]** 메뉴를 실행해보자. 제대로 된 에러 메시지를 볼 수 있다.

정신 놓고 캡쳐도 안 해서(ㅜㅜ) 에러 예시는 위 블로그에 들어가면 볼 수 있다.

<br/>

그렇다면 내 iOS 버전을 감당할 수 있는 Xcode 버전을 설치해야 하는데, 지금은 이미 너무 많이 `빅 서` 화 돼버렸다. 앱스토어에 나와 있는 최신 버전은 업데이트가 간편하지만, OS 최소 사양이 빅 서 였기 때문에 다른 방법을 찾을 수밖에 없었다.

(내 맥북은 2013 버전의 노인이었기 때문에 빅서게이트를 본 이상 차마 OS 업데이트를 하지 못했다...)

![version](/assets/images/swift-2/version.png)

다행히, 내 iOS 버전을 감당할 수 있는 Xcode 최소 버전과 내 맥 OS 버전과 맞아떨어졌다. 해당 버전의 다운로드는 아래 사이트에서 했다.

[Xcode Releases](https://xcodereleases.com/)

열심히 노인 학대를 한 끝에, 설치에 성공하고 '이젠 되겠지..' 라고 생각했지만 *응 안돼~..*

<br/>

### 에러 2 : Errors were encountered while preparing your device for development


> Errors were encountered while preparing your device for development. Please check the Devices and Simulators Window.

<br/>

다음으로 맞닥뜨린 에러. 보자마자 '시뮬레이터도 업뎃해야하나 설마...?' 라고 생각하고 구글링을 했다.

그냥 핸드폰을 껐다 켜란다.

이젠 되겠지...!!!!!!!!! 라고 생각했지만?

응 안돼~

<br />

### 에러 3 " Could not launch "ProjectName"

> The operation couldn’t be completed. Unable to launch stopwatch. NavigationBarCheck because it has an invalid code signature, inadequate entitlements or its profile has not been explicitly trusted by the user.

<br/>

이번엔 또 뭐냐 싶어서 다시 구글링을 해 봤고, 다행히 해결이 어렵지 않은 문제였다. 연결할 iOS 기기 설정에서 체크만 해 주면 됐다.


1. 연결할 기기의 **'설정(Setting)' → '일반(General)'**
2. **'기기 관리(Device Management)'** 선택

    -  앞선 두 차례의 에러 메시지 동안 여러 번 봤던 에러 해결 방법이어서 그 때마다 확인 해 봤는데, 이거로 해결 할 수 없는 문제가 아니면 '기기 관리'가 뜨지 않는다.

    -  즉, 내가 디바이스에 설정→ 일반에 들어갔는데 기기 관리가 없다면 이 문제가 아닌 것!

3. **Apple Development: 어쩌구**로 되어 있는 앱 선택
4. **파란 텍스트** 선택하고 팝업에서 **'신뢰'** 선택
5. 끝! 다시 Xcode 로 돌아가 **Run** 해보자.

<br/>

### 작고 소중한 나의 앱이 깔렸읍니다..

![done](/assets/images/swift-2/done.jpeg)

끝!
