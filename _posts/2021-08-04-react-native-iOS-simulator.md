---
title: "M1, React Native iOS 시뮬레이터 실행"
toc: true
toc_sticky: true
categories:
    - study
tags:
    - 리액트네이티브
    - 기초
    - 공부
last_modified_at: 2021-08-04
---

iOS 시뮬레이터까지는 [했다](https://2unbini.github.io/study/react-native-iOS-simulator).

하지만 iOS 앱만 확인할 순 없잖아..! 안드로이드도 잇따구..! 해서 설치 시작.

> [Developing a React Native app on an M1 Mac without Rosetta](https://medium.com/@davidjasonharding/developing-a-react-native-app-on-an-m1-mac-without-rosetta-29fcc7314d70)

사실 어제 실행해본 run-ios가 로제타 없이 돌아간 것 같아서 이게 어떻게 됐지..? 하는 마음에 서치하다가

안드로이드 스튜디오 깔고 하는 방법이 잘 정리돼있는 위 블로그를 참조했다.


## 안드로이드 스튜디오

### 안드로이드 스튜디오 설치

> [Android Studio install](https://developer.android.com/studio/preview)

안드로이드 스튜디오를 설치해준다.

블로그에서 'Arctic Fox'를 설치하라고 돼있었는데 사이트에는 범블비라고 적혀 있었다. 이전 버전인가 싶어서 찾아봤는데, 명칭이 다른 것 같다. 그냥 설치 해 줌.

설치 과정에선 딱히 할 게 없다.

### AVD 설치

안드로이드 스튜디오를 잘 깔았으면 들어가준다.

메인 화면이 이렇게 뜨는데, 여기서 하단의 `More Actions` 를 클릭해 `ADV Manager` 로 들어가준다.

![](https://images.velog.io/images/seri_ous/post/f8d8e04d-1d25-436e-934a-b69b997ff6f8/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%202.42.50.png)

그럼 이런 창이 나오는데, 밑에 `Create Virtual Device` 를 눌러 새 가상 기기를 만들어주면 된다.
![](https://images.velog.io/images/seri_ous/post/a45a2eff-1408-44c8-9f7d-a12dd6f64f2d/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%202.43.07.png)


기기 설정은 내가 원하는대로 맞춰주고, Finish를 눌러주면 아래와 같이 기기가 생성된다.

![](https://images.velog.io/images/seri_ous/post/d062e1f8-c2cc-47c3-a6ca-d049d3614b7b/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%201.06.56.png)

![](https://images.velog.io/images/seri_ous/post/dfed1104-1cc0-42b1-8b1c-41f9f6a0910d/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%201.07.02.png)

![](https://images.velog.io/images/seri_ous/post/3335e0ff-2d13-41ca-9a6c-5bf904783a65/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%201.07.08.png)

![](https://images.velog.io/images/seri_ous/post/d6a9ea28-1620-4623-a181-aa9c72c47ebb/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%201.07.19.png)



### Android Simulator 실행

시뮬레이터를 실행하기 전에, 프로젝트와 셸의 환경을 설정해줘야 한다.

프로젝트의 `android` 디렉토리로 간 뒤, `local.properties` 라는 파일을 하나 만들어준다.

eunbin 대신 본인 username을 쓰면 된다.

```
sdk.dir = /Users/eunbin/Library/Android/sdk
```

다음으로 `~/.zshrc` 를 열어 `ANDROID_SDK` 경로를 추가해준다.
역시 eunbin 자리에 username 쓰기!

```
export ANDROID_SDK=/Users/eunbin/Library/Android/sdk
```

그리고 android 디렉토리에 있는 `[gradle.properties](http://gradle.properties)` 파일을 열어 Flipper 버전을 `0.91.1` 로 수정해준다. 코드는 0.75.1 버전으로 쓰여져 있기 때문에 숫자만 살짝 바꿔주면 된다.

```
FLIPPER_VERSION=0.91.1
```

그리고 `./gradlew clean` 을 해 준다.


> 여기까지 하고 잘 되는 분들은 **다시 Android Simulator 실행** 으로 건너뛰세용.

<br/>

나는 여기서 문..제..?가 생겼는데 `./gradlew clean` 을 하라고 해서 했는데

> This operation couldnt be completed. Unable to locate a Java Runtime.

이딴 에러가 떴다.

역시 갓글링을 해보았더니 **java가 없어서** 그런거란다.

M1으로 바꾸면서 아무거나 막 다운받는다고 다 되는 게 아니란 걸 깨닫곤, java의 어떤 버전이 M1에서 유효한지를 검색해봤다.

Oracle에서 제공하는 버전은 아직 Apple Silicon을 지원하지 않는다고 한다. 그래서 찾아낸 다른 방법! **AZUL에서 다운**받으면 된다.

> [OpenJDK version 8 (다른 버전을 다운받으시려면 Azul로 고고)](https://www.azul.com/downloads/?version=java-8-lts&package=jdk)

<br/>

근데 또 난 아무 의식 없이 OpenJDK 16 버전을 다운받았었다.

다운 완료하고, 환경변수까지 잘 지정해줬다고 생각하고 다시 clean 을 해 보니 이번엔 다른 문제가 발생했다.

> Could not compile settings file '.../settings.gradle'.

다시 열심히 구글링을 해 보니 해당 에러에 포함된 내용 중에 `Unsupported class file major version 60` 라는 말이 **Java 16이 아직 제공하지 않는** 기능을 썼다는 거란다.

[관련 링크 : Android Studio: Could not compile settings file](https://stackoverflow.com/questions/67462224/android-studio-could-not-compile-settings-file)


그래서 **Java 8 버전**으로 다시 다운받았고, Java는 맥 상에서 자동으로 최상위 버전을 기본값으로 설정해주기 때문에 Java 16은 쓰레기통으로 갖다 버렸다.

⇒ 버린 뒤 **환경변수 재적용**을 안 했더니 에러 한 번 더 남... `source` 해주세요..

여차저차해서 다시 clean을 해 보니, 이번엔 잘 됐다.

자바 환경변수 세팅은 아래 글을 참고했다. 그냥 하던 대로 `~/.zshrc` 에 넣어주고 `source` 해 주면 된다 (MacOS 11 Big Sur 기준).

[관련 링크 : How to Set $JAVA_HOME environment variable on macOS - Mkyong.com](https://mkyong.com/java/how-to-set-java_home-environment-variable-on-mac-os-x/)


### 다시 Android Simulator 실행

./gradlew clean 까지 잘 마쳤으면, run을 할 차례다.

하지만 run을 하기 전에 먼저 아까 만들어놨던 가상 기기를 실행시킨다.

![](https://images.velog.io/images/seri_ous/post/762631ed-a09c-41d6-972b-6c2f4985dc51/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%201.07.19.png)

플레이 버튼처럼 생긴 저걸 누르면, 내가 세팅한 스펙의 안드로이드 가상 기기가 화면에 뜬다.

그럼 이제 진짜최종런을 하면 된다.

```
npx react-native run-android
```

하면..!!!


... 낄낄
![](https://images.velog.io/images/seri_ous/post/9fd5d6ff-d3fc-48aa-b14a-d24db24443be/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%201.35.34.png)


또 에러가 떴다.

> Failed to launch emulator. Reason: No emulators found as an output of `emulator -list-avds`.

> Failed to install the app. Please accept all necessary Android SDK licenses using Android SDK Manager: "$ANDROID_HOME/tools/bin/sdkmanager --licenses".

버전 문제라느니... 무슨 문제라느니... 라는 기타 등등 아주 많은 이야기들이 있었는데, 그 중 **SDK 라이선스를 체크**해줘야 한다는 것이 있었다.

다시 안드로이드 스튜디오를 들어가서,

> Preferences → Appearance&Behavior → System Settings → Android SDK

탭으로 들어가면, 뭐가 설치 돼있고, 안돼있는지 확인할 수 있다.

나는 저기에서 `Android SDK Command-line Tools(latest)` 항목이 Not Installed 로 돼있었고,

해당 항목을 체크한 뒤 하단의 Apply 를 해 Install 할 수 있었다.

![](https://images.velog.io/images/seri_ous/post/8ef4037c-0a3a-4974-b653-589ffb94bc1d/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%202.54.21.png)

이렇게 체크 해 주고, 스택오버플로우 형님들의 조언에 따라 `~/.zshrc` 안에 아래 내용을 넣어 줬다. <your_computer_name> 에는 본인 맥 username을 넣어주면 된다.

```
export ANDROID_SDK=/Users/<your_computer_name>/Library/Android/sdk
export PATH=/Users/<your_computer_name>/Library/Android/sdk/platform-tools:$PATH
```
[관련 링크 : React Native adb reverse ENOENT](https://stackoverflow.com/questions/38835931/react-native-adb-reverse-enoent)

`source` 를 잊지 않고 해 주면, 진짜 최종 끝이 난다.

다시 RUN 해 주면...!!

```
npx react-native run-android
```

흑흑... 드디어 실물(?) 영접..,,

![](https://images.velog.io/images/seri_ous/post/097ff32b-0401-4940-ae4b-d45f819bcd1e/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202021-08-04%20%EC%98%A4%ED%9B%84%2012.48.27.png)