---
title: "SwiftUI Firebase 구글 로그인"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
    - Firebase
    - 구글로그인
last_modified_at: 2021-11-10
---

# SwiftUI에서 구글 로그인 기능 추가하기

### 준비할 것

구글 계정(Firebase 가입, 구글 로그인 실행)

### 과정과 참고한 자료

Firebase와 Google Login 패키지를 프로젝트에 추가하고, Firebase 홈페이지에서 앱을 등록하면 된다.

참고한 [유튜브 링크](https://www.youtube.com/watch?v=0ncKXZVaXOU)와, [Firebase](https://firebase.google.com/docs/auth/ios/google-signin?authuser=0), [Google SignIn](https://developers.google.com/identity/sign-in/ios/start-integrating) 웹 페이지이다.

## Firebase

클라이언트쪽 개발에 치중하고 있는 나는 서버니 로그인 인증이니 데이터베이스니 하는 것들에 익숙하지 않다. 그런 나에게 풀스택 개발을 할 수 있게 하는 멋진 친구가 있는데 그것이 바로 Firebase다. 서버, 데이터베이스, 인증 등의 기능을 내가 직접 구현하지 않아도 가져다 쓸 수 있다.

### 사용법

[Firebase 웹 사이트](https://firebase.google.com)에서 소개하는 대로 따라하면 된다.

1. Firebase 사이트 내에서 프로젝트를 추가하고,
2. 사용하려는 OS에 따라 내가 개발하고 있는 앱의 bundle id를 적고,
3. 구성 파일을 사이트에서 다운받아 프로젝트에 추가하고,
4. 패키지를 추가하고,
5. 초기화 코드를 추가하면 된다.

### 패키지 추가

Firebase를 사용하기 위해 여러 패키지들을 다운로드 받아 프로젝트 파일에 추가해야 한다. `Podfile`에 추가하는 방법이 있고, Xcode 13에선 GUI 방식으로 프로젝트 설정에서 프레임워크, 라이브러리, 임베디드 콘텐츠를 추가할 수 있는 방법이 있다. 웹 사이트에선 전자를 사용했지만, 나는 후자를 사용했다.

앱의 General - Frameworks, Libraries, and Embedded Content에서 패키지를 추가할 수 있다.

![package-1](/assets/images/swiftUI/package1.png)

'+' 버튼을 누르면 아래와 같은 창이 나오고, 나는 Firebase SDK를 추가하고 싶기 때문에 하단의 Add Other - Add Package Dependency 를 눌렀다.

스크린샷엔 패키지가 설치 돼 있지만, 처음엔 추가되어 있지 않다.

![package-2](/assets/images/swiftUI/package2.png)

검색 란에 내가 추가하고 싶은 SDK 깃허브 링크를 추가한 뒤, Add 를 누르면 된다.

![package-3](/assets/images/swiftUI/package3.png)

그럼 다음과 같이 Package Dependency가 추가된다.

![package-4](/assets/images/swiftUI/package4.png)

### 초기화 코드 추가

초기화 코드는 Firebase 사이트에 친절하게 나와 있다.

![init_code](/assets/images/swiftUI/firebase-init-code.png)

보면 알겠지만, 해당 코드는 `UIKit`에 사용할 수 있는 코드이다.

저는 SwiftUI로 개발하려는데요,,? 걱정할 것 없다,,

해당 초기화 코드는 `AppDelegate` 클래스에, `application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any?])` 메소드 안에 `FirebaseApp.configure()`를 추가하는 것이다.

SwiftUI에서 AppDelegate를, 그리고 그 안의 메서드를 재정의하고 싶을 때 App 코드 안에 다음과 같은 매애직 문장을 넣어 주면 된다.

```swift
@UIApplicationDelegateAdaptor(AppDelegate.self) var delegate
```

[도대체 이게 뭐냐..!!](https://www.hackingwithswift.com/quick-start/swiftui/what-is-the-uiapplicationdelegateadaptor-property-wrapper)

앞서 말했듯, SwiftUI에서 AppDelegate 클래스를 정의하고 싶을 떄 UIApplicationDelegateAdapter 라는 프로퍼티 래퍼를 사용해 주면 SwiftUI가 저 문장을 보고 '아 얘 App Delegate 쓰는구나'를 알게 된다고 한다.

결론적으로 그냥 `App` 파일의 `main`에 해당 코드를 추가 해 주고, Firebase에서 알려 주는 초기화 코드를 삽입하면 된다.

```swift
// App

import SwiftUI
import Firebase

@main
struct googleLogInApp: App {
    
    @UIApplicationDelegateAdaptor(AppDelegate.self) var delegate
    
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}

class AppDelegate: NSObject, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey : Any]? = nil) -> Bool {
        
        FirebaseApp.configure()
        
        return true
    }
}
```

## Google Sign In for iOS

[Google Sign In](https://developers.google.com/identity/sign-in/ios/start-integrating) <- 웹사이트에 들어가면 구글 로그인 기능을 사용하는 방법이 상세하게 나와 있다. 이를 따라 하면 된다.

### 패키지 추가

앞서 Firebase를 추가했던 것과 같이 동일한 방식으로 추가 해 주면 된다. 물론 Podfile을 수정해줘도 된다.

### URL 스키마 추가

구글 로그인을 사용하려면 프로젝트에 사용자 정의 URL 스키마를 추가해야 한다.

![url-scheme](/assets/images/swiftUI/url-scheme.png)

네모 박스 안에 부여받은 client id를 넣어 주면 된다. 그 id는 어디있을까..!! Firebase를 세팅할 때 프로젝트 파일에 추가했던 `GoogleService-Info` 라는 파일을 프로젝트 안에서 열어 보면 그 안에서 해당 id를 찾아볼 수 있다.

![reserved-client-id](/assets/images/swiftUI/reserved-client-id.png)

### 초기화 코드 추가

앞서 Firebase의 초기화 코드를 추가했던 것과 같이 동일한 방식으로 추가 해 주면 된다.

`GoogleSignIn`을 import 하고, AppDelegate의 `application` 메서드를 정의 해 주면 되는데, 이미 앞에서 초기 구성을 끝내놨기 때문에 [웹 사이트](https://developers.google.com/identity/sign-in/ios/sign-in#configure)에서 하라는 대로 초기화 코드를 써 주면 된다.

```swift
import GoogleSignIn
...

class AppDelegate: NSObject, UIApplicationDelegate {
    // firebase init code

    func application(_ app: UIApplication,
    open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    
	    var handled: Bool

        handled = GIDSignIn.sharedInstance.handle(url)
        if handled {
            return true
        }

        // Handle other custom URL types.

        // If not handled by this app, return false.
        return false
    }
}
```

## 로그인 기능 사용

Firebase에서 Google 로그인 기능을 사용하는 방법은 [웹 사이트](https://firebase.google.com/docs/auth/ios/google-signin?authuser=0)에 아주 잘 설명되어 있다. 코드를 추가 해 주기만 하면 된다!

```swift
import SwiftUI
import Firebase
import GoogleSignIn

struct GoogleLogInView: View {
    
    var body: some View {
        
        Button(action: {
            handleLogIn()
        }, label: {
            Text("Sign With Google")
        })
    }
    
    
    // Handling log in with... Google
    func handleLogIn() {
        
		// Set Client ID
        guard let clientID = FirebaseApp.app()?.options.clientID else { return }
        
        // Create Google Sign In configuration object.
        let config = GIDConfiguration(clientID: clientID)
        
        // Start the sign in flow!
        GIDSignIn.sharedInstance.signIn(with: config, presenting: getRootViewController()) { user, error in
            
            if let error = error {
                isLoading = false
                print(error.localizedDescription)
                return
            }
            
            guard
                let authentication = user?.authentication,
                let idToken = authentication.idToken
            else {
                isLoading = false
                return
            }
            
            let credential = GoogleAuthProvider.credential(withIDToken: idToken,
                                                           accessToken: authentication.accessToken)
            
            Auth.auth().signIn(with: credential) { result, error in
                
                if let error = error {
                    print(error.localizedDescription)
                    return
                }
                
                guard let user = result?.user else {
                    return
                }
                
                print(user.displayName ?? "Success")
            }
        }
    }
}

// To use Google login framework, you need UIViewController
extension View {
    func getRootViewController() -> UIViewController {
        guard let screen = UIApplication.shared.connectedScenes.first as? UIWindowScene else {
            return .init()
        }
        
        guard let root = screen.windows.first?.rootViewController else {
            return .init()
        }
        
        return root
    }
}
```

여기서 눈여겨볼만 한 부분은, `getRootViewController` 메서드인데, `UIViewController`를 반환하는 기능을 한다.

얘가 왜 있어야 하냐...! 했을 때, Google Sign In 메서드를 사용할 때 뷰 컨트롤러가 함수의 인자로 들어가기 때문에 뷰 컨트롤러가 따로 없는 SwiftUI에서 새로 생성해 사용하는 것이다.
