---
title: "UIKit으로 어플 개발하기 - PiggyBank 개발일지 4"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - UIKit
    - 개발일지
    - iOS
last_modified_at: 2022-03-15
---

# 개발일지 4

멋진 어플 개발을 하게 되었다. 일종의 타임캡슐 어플이다.

멋진 피드백을 기반으로 새로운 백로그를 작성했고, 뷰를 더 단순하고 직관적인, iOS 감성의 디자인으로 바꾸었다. 그에 따라 타임캡슐을 새로 추가하는 팝업을 다시 만들게 되었다.

## 뷰 간 데이터 공유하기

이름을 입력받는 뷰와 타임캡슐 개봉 시점을 선택하는 뷰 간에 데이터가 이동되도록 구현하고 싶었다.

두 개의 뷰를 넘나들어도 마지막으로 입력한/선택한 값이 남아 있어야 앱을 쓰기가 편할 것이기 때문이다.

뷰 간 데이터 공유 방식에 대해서 생각해봤다.

### Navigation Controller

스토리보드를 사용하지 않았을 때엔 뷰 간 이동을 `NavigationController`가 담당했다. 이동 전의 화면에서 이동할 화면 객체를 생성한 뒤, 이동할 화면 객체의 데이터를 정의해주고, 내비게이션 컨트롤러에 의해 화면을 push했다.

``` swift
// PreviousVC.swift

func pushNextView() {
    var nextVC = NextViewController()

    nextVC.data = self.data
    self.navigationController?.pushViewController(nextVC, animated: true)
}
```

이동된 화면에서 이동 전의 화면으로 되돌아가면서 다시 데이터를 전달해주려면 어떻게 해야 할까?

내비게이션 컨트롤러는 뷰 컨트롤러를 `push`와 `pop`한다. 즉, 내비게이션 컨트롤러 스택에 뷰가 쌓이는 것이다. navigationController.viewControllers를 print해 보면 뷰 컨트롤러들의 배열이 나타난다.

이동된 화면을 배열에서 pop하고, 이 배열에 있는 이동 전 뷰 컨트롤러에 데이터를 넘겨주면 된다.

```swift
// NextVC.swift

func popNextView() {
    self.navigationController?.popViewController(animated: true)
    
    guard let previousVC = self.navigationController?.viewControllers.last
    as? PreviousViewController
    else { return }

    previousVC.data = self.data
}
```

### Segue

스토리보드를 사용하고 나서 뷰 간 이동은 `Segue`가 담당하게 됐다. 이동 전의 화면에서 이동할 화면으로 넘어갈 때 준비하는 함수 `prepare(for:sender:)`를 오버라이드해 이동할 화면의 데이터를 설정해주고, perfromSegue로 화면을 이동했다.

```swift
// PreviousVC.swift

let segueIdentifier: String = "presentNextView"

override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if segue.identifier == segueIdentifier {
        guard let nextVC = segue.destination as? NextViewController
        else { return }

        nextVC.data = self.data
    }
}

@IBAction func nextButtonDidTap(_ sender: UIButton) {
    self.performSegue(withIdentifier: segueIdentifier, sender: sender)
}
```

전으로 돌아갈 때는 [unwind Segue](https://developer.apple.com/documentation/uikit/resource_management/dismissing_a_view_controller_with_an_unwind_segue)를 이용하면 된다.

```swift
// PreviousVC.swift

@IBAction func unwindToPreviousView(sender: UIStoryboardSegue) {
    if let nextVC = sender.sourceViewController as? NextViewController {
        self.data = nextVC.data
    }
}
```

## 결합도

위 두 방식으로 하는 것도 괜찮지만, PreviousVC와 NextVC의 코드 내부에 서로가 너무 많이 침투되어있다.

각 객체 내부의 프로퍼티에 값을 저장하려면 그 프로퍼티가 정의된 객체를 구체적으로 알려 주어야 한다. 코드상으로 서로 결합되어있는 것은 나중에 그 객체가 없거나 제대로 기능을 하지 못할 때에 예기치 못한 에러를 발생시킬 수 있다.

그래서 두 객체 간의 결합도를 낮추기 위해 델리게이트 패턴을 사용하기로 했다. 실제로 'passing data between view controllers' 이런 식으로 검색을 하다 보면 델리게이트 패턴으로 구현하라는 답변들이 많이 나오고, 더 많은 개발 경험이 있는 멘토들이 델리게이트를 추천하기도 했다.

### 구조

Previous -> Next 로 이동하는 것은 segue로 정의해뒀기 때문에 segue를 통해 데이터를 넘겨 줬다. 여기에서 Previous의 델리게이트를 따르는 Next의 델리게이트를 지정해준다. 왜? Next가 Previous에게 데이터를 넘겨줘야 하는데 그 넘겨주는 로직을 Previous에서 짤거니까. 그리고 Next에선 그냥 '아 Previous 델리게이트가 해줌ㅋㅋ' 하고 델리게이트 함수 호출만 해준다.

그래서 Next -> Previous 로 되돌아올때 unwind를 사용하지 않고 델리게이트 함수를 호출해주고, 실제로 동작은 Previous의 구현부에 따르도록 만들어줬다.

즉,

1. Delegate 정의
2. Previous가 Delegate 채택, 함수 구현
3. Next가 위임할 delegate 변수 정의 및 Delegate 함수 호출
4. Previous에서 Next로 넘어갈 때 Next의 delegate 변수에 Delegate 넘겨주기

이런 구조로 만들어내면 되는 것이다.

### Delegate 정의

우선 대신 동작을 해 줄 델리게이트 객체를 정의해줘야 한다. 이 델리게이트를 채택해 안에 있는 함수나 프로퍼티에 접근해 대신 동작을 할 수 있게 된다.

여기서는 데이터를 넘겨주기 때문에 `DataProviding`으로 이름을 지어줬다.

> 프로토콜 이름을 짓는 규칙이 있다. [API Guidelines](https://www.swift.org/documentation/api-design-guidelines/) '무엇인지'에 대한 프로토콜일 때엔 명사형으로(e.g. Collection), '어떤 걸 할 수 있는지'에 대한 프로토콜일 때엔 `able`, `ible`, `ing`를 뒤에 붙여서(e.g. Equatable, ProgressReporting) 표시한다.

```swift
// DataProviding.swift

protocol DataProviding {
    func sendData(data: SomeData)
}
```

이렇게 정의하면 끝이다. 세부 내용은 위임받아 실제로 동작하는 객체에서 구현한다.

### Delegate 함수 정의

DataProviding 델리게이트를 채택하는 것은 Previous다. 즉, Previous 안에서 sendData 함수의 구현부를 작성해줘야 한다는 것.

```swift
// PreviousVC.swift

extension PreviousViewController: DataProviding {
    func sendData(data: SomeData) {
        self.data = data
    }
}
```

Previous의 data에 sendData 함수의 인자로 전달된 data를 대입하게 코드를 작성했다.

### Delegate 객체 정의

그럼 이제 데이터를 전해주는 함수까지 정의되었으니 Next가 이 델리게이트를 통해 자신의 데이터를 전달해주기만 하면 된다.

그러려면 Next는 함수를 호출할 델리게이트가 있어야 한다.

```swift
// NextVC.swift

class NextViewController {
    var delegate: DataProviding?
    var data: SomeData
    ...

    // Previous로 돌아가는 버튼
    @IBAction func previousButtonDidTap(_ sender: UIButton) {
        self.delegate?.sendData(self.data)
        self.dismiss(animated: true)
    }
}
```

이런 식으로 Next에 DataProviding 델리게이트 변수를 하나 정의해준다. Previous에서 delegate 변수에 DataProviding 델리게이트를 넘겨주지 않으면 Next에선 data를 넘겨주지 않는다. 넘겨주지 못하는 게 맞다. 위임해주는 애가 없으니까! 하지만 델리게이트를 정상적으로 넘겨주는 Next에 있는 delegate가 열심히 일하게 된다.

```swift
// PreviousVC.swift

let segueIdentifier: String = "presentNextView"

override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if segue.identifier == segueIdentifier {
        guard let nextVC = segue.destination as? NextViewController
        else { return }

        nextVC.delegate = self
        nextVC.data = self.data
    }
}

@IBAction func nextButtonDidTap(_ sender: UIButton) {
    self.performSegue(withIdentifier: segueIdentifier, sender: sender)
}
```

segue로 넘어가기 전에 준비동작에서 nextVC의 delegate를 넘겨주면 준비 완료. 이렇게 하면 Previous와 Next 간에 data를 서로 주고받을 수 있다.


## 결론

뷰 간 이동에 있어 Delegate를 사용해보았다.

사실상 Previous에서는 Next에 델리게이트를 주입해주기 위해 Next의 타입을 분명히 알고 있어야 한다. 그래서 어차피 Previous는 Next와 결합되어 있다고 느낀다.

그래도 델리게이트가 주는 이점은 더 있다. 어떤 객체에서 데이터를 제공받는지 명확해지고(왜냐하면 데이터를 제공받는 쪽에서 델리게이트를 채택하고 함수 구현을 하기 때문이다), 데이터를 전달해주는 쪽에선 델리게이트만 믿고 있으면 된다. 코드가 명확해지고, 데이터의 흐름이 직관적으로 보인다. 결합도 역시 서로의 타입을 코드 내에 가지고 있는 것 보단 약해진다.

생소한 개념이었고, 활용하기 조금 어려웠는데 간단하게 데이터를 주고받는 형태를 구현하며 좀 더 지식이 구체화된 것 같다. 이전에 텍스트필드의 델리게이트 함수를 구현해놓고 delegate 위임을 안해서 '왜 안돼'를 몇 번이나 외쳤는데, 이제 델리게이트에 대해 조금 더 익숙해져서 그런 실수는 안 할 것 같다.