---
title: "M1, React Native iOS 시뮬레이터 실행"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - study
tags:
    - 리액트네이티브
    - 기초
    - 공부
last_modified_at: 2021-08-04
---
## 개발 환경 세팅

앞서 리액트 네이티브 설치를 위해 환경 세팅을 해 줬다면, 이번엔 iOS 시뮬레이터를 돌려볼 수 있는 환경을 만들 차례다.

엄청난 에러의 향연이었지만, 일단 기본적으로 해야 할 것들을 적어 놓았다. 에러는 정신 없이 구글링하고 처리하느라 링크로 대체한다.

### Xcode 설정

설치 후 실행한 뒤

  Preference > Location > Command Line Tools

경로에 CLT 가 설정되어있는지 확인한다.


### Simulator 설치

위에서 봤던 Locations 바로 옆 Components에 시뮬레이터 목록이 있다.

원하는 버전으로 다운로드하면 된다.

![](https://images.velog.io/images/seri_ous/post/27069124-7122-48dc-b42c-4b97f2720fe7/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%EC%A0%84%201.55.35.png)


## 나의 첫,, React Native Project...

### React Native Project

iTerm을 열어 내가 프로젝트를 저장하고자 하는 경로로 이동한다.

```
// 원하는 디렉토리로 이동
cd ~/Dev/React-Native
```

프로젝트를 실행한다.

```
npx react-native init MyFirstRN
```

npx를 빼도 되긴 하는 것 같은데, 무슨 차이인지는 나중에 알아봐야겠다.

```
npx react-native start
```

Metro 를 시작하는 명령어인데, start 를 하지 않고 바로 run-ios 로 시뮬레이터를 실행해도 자동으로 시작된다.


프로젝트가 정상적으로 잘 만들어졌으면, 어플리케이션을 실행하기 전 iOS 앱 실행에 필요한 라이브러리들을 설치해준다.
앞에서 열심히 깔아제꼈던 것들 중 **cocoapods**를 이용하면 된다.
iOS 앱 실행을 위한 과정이기 때문에 프로젝트 내의 ios 디렉토리로 들어가서 설치한다.

```
pod install
```

cf) 뭔가 변경이 있을 땐 `pod update` 혹은 `pod deintegrate` 후 `pod install` 을 하면 적용된다.

다 완료되면 다시 프로젝트의 루트 경로로 돌아와서 실행 해 준다.

```
npx react-native run-ios
```

![](https://images.velog.io/images/seri_ous/post/024fddde-a273-43c8-a106-042285c55711/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%EC%A0%84%201.13.42.png)

흑흑...

왜 그랬는 지 모르겠지만, 이렇게 되기까지 수많은 에러와 워닝을 맛보았다.

관련해서 어떤 워닝과 어떤 해결책이 있었는지는 링크로 대체한다...

<br/>

망할 에러놈..

[[Error 해결법 포함] The iOS Simulator deployment target 'IPHONES_DEVELOPMENT_TARGET' is set to 8.0,but the range of suppoted deployment target vesions is 9.0 to 14.4.99.](https://fomaios.tistory.com/entry/해결법-포함-The-iOS-Simulator-deployment-target-IPHONESDEVELOPMENTTARGET-is-set-to-80but-the-range-of-suppoted-deployment-target-vesions-is-90-to-14499)

[[React Native] iOS 실행시 에러(Flipper 관련)](https://success206.tistory.com/150)

[[!] Invalid `Podfile` file: [!] Specifying multiple `post_install` hooks is unsupported · Issue #6172 · CocoaPods/CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/6172)

[(Mac) React Native 개발환경 구축하기 & 에러해결](https://velog.io/@yeseul/Mac-React-Native-개발환경-구축하기-에러해결)

[[Xcode] The linked library '파일명' is missing one more architectures required by this target: arm64.](https://m.blog.naver.com/bb_/221920061509)

[[tip]xcode 5.1.1 missing required architecture arm64 error](https://yenos.tistory.com/entry/tipxcode-511-missing-required-architecture-arm64-error)

[React Native on Apple Silicon M1 - The linked library 'libPods-ProjectName.a' is missing one or more architectures required by this target: x86_64](https://stackoverflow.com/questions/65364886/react-native-on-apple-silicon-m1-the-linked-library-libpods-projectname-a-is)

[Xcode 12 Compilation Errors (While running with iOS 14 Simulators)](https://khushwanttanwar.medium.com/xcode-12-compilation-errors-while-running-with-ios-14-simulators-5731c91326e9)