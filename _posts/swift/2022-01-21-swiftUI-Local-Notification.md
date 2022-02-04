---
title: "SwiftUI, Local Notification 구현하기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SwiftUI
last_modified_at: 2022-01-21
---

# back/foreground 둘 다 작동하는 로컬 푸시 알림 설정하기

[Apple Developer Documentation](https://developer.apple.com/documentation/usernotifications)

## 전체적인 과정

1.  `UserNotificationCenter` 객체 생성
    이 친구가 앞으로 Notification 관련된 모든 일을 수행하게 됩니다.
    
2. Toggle 버튼을 최초로 눌렀을 때 `request Authorization` 알림/배지/소리 등 노티피케이션 권한 묻기
3. 유저가 설정한 권한 상태에 따라 작업 수행
    1. 권한을 받으면 → Toggle On
    2. 권한을 받지 못하면 → Toggle Off
    3. 에러시 → 따로 처리
4. Toggle ON인 경우
    1. UI 상에 DatePicker 표시
    2. 최초 on인 경우 → 현재 시간으로 `add new notification`
    3. 피커를 이용해 시간을 설정하면 → `add new notification`
5. 권한을 받지 않은 상태에서(system에서 OFF 되어 있는 상태) 노티피케이션을 추가하려고 할 때
    1. Alert 발생
        1. System Settings로 이동 & Toggle Off
        2. 취소 → Toggle Off

구구절절인데 3줄요약하면

1. 권한 묻기
2. 결과에 따라 UI 상태 바꾸기
3. 시간 입력받으면 새 notification 추가하기

## 권한 묻기

앱을 다운받고 첫 시작시에 알림/소리/배지 등의 권한을 묻는 팝업... 그것이 바로 `requestAuthorization(options:completionHandler:)` 메서드이다.

이 메서드는 `UNUserNotificationCenter` 클래스 안에 정의된 함수로, 우리는 공유된 UNUserNotificationCenter 객체를 사용해  Notification 관련 작업을 할 수 있다.

이 객체와 메서드는 `UserNotifications` 라는 프레임워크를 추가해 사용할 수 있다.

```swift
import UserNotifications

// .current() -> 공유된 노티피케이션 센터 객체를 반환
let notiCenter = UNUserNotificationCenter.current()

// 이런 느낌으로 코드 작성
notiCenter.requestAuthorization(options: [.alert, .badge, .sound]) { granted, error in
        if let error = error {
            print("Error : \(error.localizedDiscription)")
        }

        if granted {
            // 노티피케이션 승인
        }
        else {
            // 노티피케이션 거부
        }
}
```

`option` 으로 권한을 묻고자 하는 요소들의 배열을 넘겨 주면 된다.

`completionHandler` 블록에서 `granted` , `error` 인자가 넘어오는데, 각각 승인됐는지 여부와 에러를 담고 있다. 이 블록은 비동기적으로 실행돼 백그라운드 쓰레드에서 실행될 수 있기 때문에, 이후 UI 관련 작업을 해당 블록 안에서 하려면 **메인 쓰레드**에서 할 수 있도록 처리 해 줘야 한다.

## 결과에 따라 UI 상태 바꾸기

Toggle을 눌러 On이 된 경우, 최초에 request를 하고, 이후 어떤 상태인지 확인해 DatePicker를 통해 받아온 시간을 가지고 notification을 추가하거나, Alert을 띄우거나 하는 식으로 운영할 것이다.

그래서 일단 Toggle 버튼과 DatePicker가 필요하다. 재빠르게 뷰를 만들어보면,

```swift
struct NotiView: View {
    @State private var isToggleOn: Bool = false
    @State private var notiTime: Date = Date()

    var body: some View {
        HStack {
            Toggle("", isOn: $isToggleOn)
            if isToggleOn {
                DatePicker("", selection: $notiTime, displayedComponents: .hourAndMinute)
            }
        }
    }
}
```

이런 느낌이 된다.

Toggle과 DatePicker는 UI가 바뀔 때마다 바인딩된 변수의 값이 바뀌고, 버튼이나 텍스트필드와 달리 따로 액션을 지정할 수 없다. 그래서 바인딩한 변수의 값이 바뀌는지 관찰하고, 바뀌는 시점에서 내가 원하는 동작을 해 주면 된다.

Toggle과 DatePicker에 각각 `.onReceive` 메서드를 사용해 변화를 감지해도 되지만, Notification 관련 모델을 만들고 그 안에 관찰 가능한 변수를 만들어 그 변수가 바뀌는 시점에 따라 값을 달리 지정해주는 방식을 택했다.

이유는.. 굳이 뷰를 더럽혀(?)가며 코드를 늘리지 않고 Notification 모델 내에서 처리할 수 있기 때문이다.

그래서 Notification 모델을 만들고, 뷰에 선언된 State 변수들을 삭제하고 대치시켰다.

```swift
// Noti Manager
class NotificationManager: ObservableObject {

    let notiCenter = UNUserNotificationCenter.current()

    @Published var isToggleOn: Bool = UserDefaults.standard.bool(forKey: "hasUserAgreedNoti") {
        didSet {
            if isToggleOn {
                // On Action - 1
                UserDefaults.standard.set(true, forKey: "hasUserAgreedNoti")
                requestNotiAuthorization()
            }
            else {
                // Off Action - 2
                UserDefaults.standard.set(false, forKey: "hasUserAgreedNoti")
                removeAllNotifications()
            }
        }
    }

    @Published var notiTime: Date = Date() {
        didSet {
            // Set Notification with the Time
        }
    }

    @Published var isAlertOccurred: Bool = false

    func removeAllNotifications() {
        notiCenter.removeAllDeliveredNotifications()
        notiCenter.removeAllPendingNotificationRequests()
    }

    func requestNotiAuthorization() {
        // 노티피케이션 설정을 가져오기
        // 상태에 따라 다른 액션 수행
        notiCenter.getNotificationSettings { settings in

            // 승인되어있지 않은 경우 request
            if settings.authorizationStatus != .authorized {
                notiCenter.requestAuthorization(options: [.alert, .badge, .sound]) { granted, error in
                    if let error = error {
                        print("Error : \(error.localizedDiscription)")
                    }
            
                    // 노티피케이션 최초 승인
                    if granted {
                        self.addNotification(with: self.notiTime)
                    }
                    // 노티피케이션 최초 거부
                    else {
                        DispatchQueue.main.async {
                            self.isNotiOn = false
                        }
                    }
                }
            }

            // 거부되어있는 경우 alert
            if settings.authorizationStatus == .denied {
                // 알림 띄운 뒤 설정 창으로 이동
                DispatchQueue.main.async {
                    self.isAlertOccurred = true
                }
            }
    }
}
```

```swift
// View
struct NotiView: View {
    @ObservedObject private var notiManager = NotificationManager()

    var body: some View {
        HStack {
            Toggle("", isOn: $notiManager.isToggleOn)
            if isToggleOn {
                DatePicker("", selection: $notiManager.notiTime, displayedComponents: .hourAndMinute)
            }
        }
        .alert(isPresented: $notificationManager.isAlertOccurred) {
          Alert(...)
        }
}
```

변수가 바뀔 때 실행되는 `didSet` 블록을 통해 Toggle과 DatePicker의 변화를 추적할 수 있다.

### Toggle On

첫번째 코드블록에서 1은 스위치가 On 됐을 때, 즉 알림을 사용할 때 실행된다. 따라서 최초에 request Authorization을 하고, 그 다음부턴 디바이스 설정상에 해당 앱의 알림이 승인되어있는지 거부되어있는지에 따라 다른 액션을 한다.

승인되어있으면 현재 DatePicker가 담고 있는 시간 `notiTime` 을 통해 새 노티피케이션을 추가하고, 거부되어있으면 UI 상에서 알림을 발생시키는 변수를 변화시켜 디바이스의 설정 창으로 넘어갈 수 있게 알림을 띄운다.

여기서 UI의 상태를 변화시키는 `self.isNotiOn = fasle` 와 `self.isAlertOccurred = true` 코드는 UI 관련 액션이기 때문에 메인 쓰레드에서 실행되어야 한다. 그런데, `requestAuthorization` 메서드의 컴플리션핸들러 부분은 비동기적으로 처리된다. 그래서 알람을 발생시키는 코드를 `DispatchQueue.main.async` 로 메인 쓰레드에서 실행되게끔 했다.

Toggle On과 Off는 그 상태를 유지해야 하기 때문에 isNotiOn 변수의 값을 UserDefaults에 저장했다.

### Toggle Off

2는 스위치가 Off됐을 때, 즉 알림을 사용하지 않을 때 실행된다. 초기 상태는 무조건 Off이므로, On이다가 Off가 되는 상황만 생각 해 주면 된다. 지금까지 추가한 모든 알림을 삭제 해 주면 된다.

처음엔 `removeAllDeliveredNotifications()` 만 사용했는데, 이 메서드는 스케줄링됐지만, 아직 노티피케이션 센터에 전달되지 않은 요청에 대해선 아무 영향을 미치지 않아 스위치를 Off 했음에도 불구하고 알림이 왔다.

스케줄링 된 모든 요청을 삭제하는 `removeAllPendingNotificationRequests()` 메서드를 추가해 지금까지 추가된 모든 요청을 삭제하도록 했다.

## 시간 입력받으면 새 노티피케이션 추가하기

앞서 Toggle의 상태가 바뀔 때마다 특정한 동작을 수행한 것처럼, DatePicker도 새로운 값으로 업데이트될때마다 이전의 값을 삭제하고 새 것을 만들어 추가하는 동작을 수행한다.

```swift
// Noti Manager
class NotificationManager: ObservableObject {

    let notiCenter = UNUserNotificationCenter.current()
    
    ...

    @Published var notiTime: Date = Date() {
        didSet {
            // Set Notification with the Time - 3
            removeAllNotifications()
            addNotification(with: notiTime)
        }
    }

    func removeAllNotifications() {
        notiCenter.removeAllDeliveredNotifications()
        notiCenter.removeAllPendingNotificationRequests()
    }

    ...

    // time에 반복되는 노티피케이션 추가
  func addNotification(with time: Date) {
      let content = UNMutableNotificationContent()
        
      content.title = "Let's Git It!"
    content.subtitle = "오늘도 커밋 하셨나요?"
    content.sound = UNNotificationSound.default
        
    let dateComponent = Calendar.current.dateComponents([.hour, .minute], from: time)
    let trigger = UNCalendarNotificationTrigger(dateMatching: dateComponent, repeats: true)
    let request = UNNotificationRequest(identifier: UUID().uuidString, content: content, trigger: trigger)
        
    userNotificationCenter.add(request)
  }
}
```

### DatePicker Changed

3은 스위치가 On 됐을 때 나오는 DatePicker를 움직였을 때 실행된다. 최초에 등장할 땐 현재 설정되어있는 시간대로 노티피케이션을 추가하고, 이후에는 지금까지 설정된 모든 노티피케이션을 삭제 해 주고 수정된 시간으로 새 노티피케이션을 추가 해 준다.

### Notification Request

먼저 `UNMutableNotificationContent()` 인스턴스를 하나 생성해준다. 이 인스턴스의 세부사항을 우리가 원하는대로 바꾸고 반복할 시간과 함께 요청으로 만들어준 뒤 노티피케이션 센터에 추가해주면 된다.

다음으로 입력받은 시간을 가지고 `dateComponent` 를 만든 뒤, 이를 가지고 해당 시간마다 스케줄링을 해 주는 `UNCalendarNotificationTrigger` 메서드를 구성해준다.

노티피케이션을 구성할 요소들을 모두 만들어준 뒤, 요청으로 만들어준다. 여기선 `identifier` 를 UUID로 설정 해 줬는데, 특별한 식별자를 직접 부여해준다면 나중에 해당 식별자로 요청을 찾고, 삭제할 수 있다.

## +) 디바이스 설정 앱으로 자동 이동

```swift
func openSettings() {
   if let bundle = Bundle.main.bundleIdentifier,
      let settings = URL(string: UIApplication.openSettingsURLString + bundle) {
        if UIApplication.shared.canOpenURL(settings) {
           UIApplication.shared.open(settings)
        }
    }
}
```

`UIApplication` 에 정의되어있는 `openSettingsURLString` 프로퍼티를 이용해 이동할 수 있다.