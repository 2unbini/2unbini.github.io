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

	nextVC.data = data
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

	previousVC.data = data
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

		nextVC.data = data
	}
}

@IBAction func segueNextView(_ sender: UIButton) {
	self.performSegue(withIdentifier: segueIdentifier, sender: sender)
}
```

전으로 돌아갈 때는 [unwind Segue](https://developer.apple.com/documentation/uikit/resource_management/dismissing_a_view_controller_with_an_unwind_segue)를 이용하면 된다.

```swift
// PreviousVC.swift
@IBAction func unwindToPreviousView(sender: UIStoryboardSegue) {
	if let nextVC = sender.sourceViewController as? NextViewController {
		data = nextVC.data
	}
}
```

## 결합도

위 두 방식으로 하는 것도 괜찮지만, PreviousVC와 NextVC의 코드 내부에 서로가 너무 많이 침투되어있다.

각 객체 내부의 프로퍼티에 값을 저장하려면 그 프로퍼티가 정의된 객체를 구체적으로 알려 주어야 한다. 코드상으로 서로 결합되어있는 것은 나중에 그 객체가 없거나 제대로 기능을 하지 못할 때에 예기치 못한 에러를 발생시킬 수 있다.

그래서 두 객체 간의 결합도를 낮추기 위해 델리게이트 패턴을 사용하기로 했다. 실제로 'passing data between view controllers' 이런 식으로 검색을 하다 보면 델리게이트 패턴으로 구현하라는 답변들이 많이 나오고, 더 많은 개발 경험이 있는 멘토들이 델리게이트를 추천하기도 했다.

### 구조

Previous -> Next 로 갈 때 데이터를 넘겨주는 방식은 우선 segue를 이용하기로 했다.



### Protocol 정의



## 결론

뷰 간 이동에 있어 Delegate를 사용해