---
title: "UIKit으로 어플 개발하기 - PiggyBank 개발일지 1"
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
last_modified_at: 2022-02-17
---

# 개발일지 1

멋진 어플 개발을 하게 되었다. 일종의 타임캡슐 어플이다.

아직 어플 디자인 요소들이 제대로 나오지 않은 관계로 우선 화면 구성부터 시작했다.

오늘은 메인 화면에 해당하는 `홈 뷰`를 만들고, 이 위에 올라갈 UI 요소들(버튼, 라벨, 이미지 등)을 구현했다.

스토리보드를 쓰지 않고 구현하기, 그리고 styleshare의 [swift style guide](https://github.com/StyleShare/swift-style-guide)에 맞추어 개발하기를 작은 목표로 삼았다.

## Home View

### 기능 정의

우리의 홈 뷰는 이러한 기능을 해야 한다.

- 오른쪽 위 '설정' 버튼을 눌러 설정 창으로 이동한다.
- 왼쪽 아래 '망치' 버튼을 눌러 기간이 다 되기 전 타임머신을 연다.
- 왼쪽 아래 '망치' 버튼 옆 '리스트' 버튼을 눌러 타임머신의 목록 창으로 이동한다.
- 오른쪽 아래 '라벨'은 타임머신에 들어간 요소의 개수와 전체 목표 개수를 보여 준다.
- 생성된 타임머신은 양 옆으로 스와이프해서 볼 수 있다.
- 중앙을 기준으로 왼쪽으로 스와이프하면 과거의 타임머신이 나오고 오른쪽으로 스와이프하면 타임머신 생성 창으로 이동한다.
- 생성된 타임머신이 하나도 없으면, '망치'와 '리스트'버튼, 그리고 '라벨'은 가려진다.
- 생성된 타임머신이 하나도 없으면, 중앙에 '타임머신을 생성하세요'라고 쓰여 있는 이미지를 눌러 타임머신을 생성할 수 있다.

타임머신의 모양은 병 모양이므로 이에 대한 네이밍은 Bottle로, 타임머신 안에 들어갈 요소들은 쪽지 형식이므로 이에 대한 네이밍은 Note로 하기로 했다.

### 구조

iOS 개발 팀원 sun과 함께 홈 뷰에서 '스와이프'해 이미지를 보여줄 수 있는 방법에 대해 이야기했다. 각자 연구하는 시간을 갖고, 나는 '스크롤 뷰'를, sun은 '페이지 뷰'를 제안했다.

스와이프해서 새로 업데이트 되는 것은 단지 `이미지`이기 때문에 다수의 뷰 컨트롤러를 만들어 페이징할 수 있는 페이지 뷰에 대해 회의적이었다. 그런데, sun이 하나의 페이지 뷰 컨트롤러를 사용하는 방법을 찾아와 이를 적용시키기로 했다.

```markdown
  button, labels                                      image
        |                                               |
    home view                view                  content view
        |                      |                        |
homeViewController --  pageViewController  -- pageContentViewController
```

homeViewController가 pageViewController를 자식으로 가지고, 해당 pageViewController에서 스와이프 동작이 일어날 때마다 각 페이지에 대한 내용을 담는 pageContentViewController를 새로 생성해 그 안의 image를 변화하도록 만들었다.

### 개발 순서

1. homeViewController를 만든다.
2. 생성된 타임머신의 존재유무로 서로 다른 초기 설정을 진행한다.
3. pageViewController를 자식으로 추가한다.
4. pageContentViewController를 만든다.
5. 페이지를 이동할 때마다 pageContentViewController를 업데이트하도록 만든다.
6. homeView에 들어갈 UI요소들을 추가한다.

이번 개발일지에서는 1~5까지의 내용을 정리했다.

## HomeViewController

생성된 타임머신의 존재유무, 즉 없으면 초기 상태, 하나라도 있으면 진행 상태로 뷰를 구성한다. 이를 판별하는 변수로 `hasBottle`이라는 Bool 타입 변수를 두었다.

hasBottle이면, pageViewController를 설정하고 스와이프하여 페이지를 옮겨다닐 수 있도록 했고, hasBottle이 아니면 이를 나타내는 별도의 ImageView를 homeView에 추가하기로 했다.

이에 따라 아래와 같이 HomeViewController의 구조를 구성했다.

```swift
class HomeViewController: UIViewController {
    // UI 요소들(Button, Label) 생략

    var homeView: UIView!
    var pageViewController: UIPageViewController!
    var pageImageArray: [String]! = []
    var currentIndex: Int = 0
    var hasBottle: Bool = true

    override viewDidLoad() {
        super.viewDidLoad()
        configureMainView()
        configureUIComponents()
        if hasBottle {
            configurePageViewController()
        }
        if !hasBottle {
            hideUnusedUIComponents()
            setInitialImage()
        }
    }

    private func configureMainView() {
        // 초깃값이 있으면 -> PageViewController의 뷰를 자식 뷰로 가지고,
        // 초깃값이 없으면 -> ImageView를 자식 뷰로 가지는 homeView.
        self.homeView = UIView()
        self.view.addSubview(self.homeView)
    }

    private func configureUIComponents() {
        // Button, Label 등 UI 요소들 설정
        // 생략
    }

    private func configurePageViewController() {
        // PageViewController 설정
        // 하단 설명
    }

    private func hideUnusedUIComponents() {
        // 초기 화면일 때, 하단 버튼과 라벨 hidden 처리
        // 생략
    }

    private func setInitialImage() {
        // 초기 화면일 때, 탭 제스처 감지 가능한 ImageView로 설정
        // 하단 설명
    }
}
```

### configurePageViewController()

![configurePageViewController](/assets/images/piggy/1-1.png)

현재 인덱스로 PageContentViewController를 만들고 이를 pageViewController에서 옮겨다닐 수 있는 Controller 배열로 만든 뒤 homeViewController의 자식으로 추가하는 과정이다.

수평 방향으로 움직이는 pageViewController를 만들기 위해 해당 초기화 구문을 사용했다. 그냥 만들면 진짜로 페이지를 넘기는 것 같은 트랜지션이 적용되어, 일반적으로 스크롤되는 모습만 보이게 하려고 transitionStyle도 scroll로 지정했다.

pageViewController의 dataSource와 delegate를 `self` 즉, homeViewController에게 위임하고 있다. 이에 따라 homeViewController는 페이지가 좌우로 움직이는 것을 감지할 때마다 새로운 pageContentViewController로 업데이트하는 함수를 실행시킬 것이다. dataSource 프로토콜과 delegate 프로토콜을 채택하기 위해서 homeViewController의 선언부에 아래 내용을 추가해야 한다.

```swift
class HomeViewController: UIViewController, UIPageViewControllerDataSource, UIPageViewControllerDelegate {
    ...
}
```

이렇게 추가하면 UIPageViewControllerDataSource 프로토콜에 따라 필수적으로 구현해야 하는 함수들이 나온다.

### UIPageViewControllerDataSource

styleshare의 스타일 가이드에 따라 프로토콜은 extension으로 따로 분리했다.

해당 프로토콜을 따르면 필수로 구현해야 하는 함수가 두 가지 있는데, 페이지를 이전으로 옮길 때와 다음으로 옮길 때에 실행되는 함수이다.

왼쪽으로 옮기면 인덱스가 하나씩 줄어들면서 그 인덱스에 맞는 PageContentViewController를 생성하고, 오른쪽으로 옮기면 그와 반대로 인덱스가 하나씩 늘어나면서 생성된다. 또, 맨 마지막 인덱스에서 한 번 더 오른쪽으로 옮기면 새로운 타임머신을 추가하기로 했기 때문에 그 부분에 임시로 print문을 써 놓았다.

```swift
extension HomeViewController: UIPageViewControllerDataSource {
    func pageViewController(
        _ pageViewController: UIPageViewController,
        viewControllerBefore viewController: UIViewController
    ) -> UIViewController? {
        let viewController: PageContentViewController = viewController as! PageContentViewController
        var index = viewController.index as Int
        
        if index == 0 || index == NSNotFound {
            return nil
        }
        index -= 1
        return self.makePageContentViewController(with: index)
    }
    
    func pageViewController(
        _ pageViewController: UIPageViewController,
        viewControllerAfter viewController: UIViewController
    ) -> UIViewController? {
        let viewController: PageContentViewController = viewController as! PageContentViewController
        var index = viewController.index as Int
        
        if index == NSNotFound {
            return nil
        }
        
        index += 1
        
        if index == self.pageImages.count {
            print("Add New Jar")
            return nil
        }
        return self.makePageContentViewController(with: index)
    }
}
```

### UIPageViewControllerDelegate

해당 프로토콜에서 구현할 수 있는 함수가 몇 가지 있는데, 그 중 트랜지션이 완료됐음을 알리는 해당 함수를 구현했다. HomeViewController의 currentIndex를 맞추기 위해서다.

```swift
extension HomeViewController: UIPageViewControllerDelegate {
    func pageViewController(
        _ pageViewController: UIPageViewController,
        didFinishAnimating finished: Bool,
        previousViewControllers: [UIViewController],
        transitionCompleted completed: Bool
    ) {
        if completed {
            if let currentViewController = pageViewController.viewControllers![0] as? PageContentViewController {
                currentIndex = currentViewController.index
            }
        }
    }
}
```

이렇게 해서 PageViewController의 역할을 하는 HomeViewController를 세팅했다.

## setInitialImage()

![setInitialImage](/assets/images/piggy/1-2.png)

다음은 초기 이미지를 새로 만들고, 여기에 탭 제스처를 추가하고 각종 설정을 해 주는 부분이다.

이미지나 라벨에 제스처를 추가하는 방법으로, storyboard 상에서 UI 요소를 만들고 이를 @IBAction으로 레퍼런스를 걸어주는 방법이 있다. 하지만 이번 프로젝트에서 스토리보드를 사용하지 않고 개발을 해야 했기 때문에 코드로 제스처를 추가해야 했다.

### UITapGestureRecognizer

UITapGestureRecognizer는 UIGestureRecognizer의 서브클래스 중 하나로, 다양한 제스처 중 탭 제스처를 감지하는 객체이다.

공식문서에 따르면 뷰에서, 그리고 그 하위 뷰에서 일어나는 모든 제스처를 감지하기 위해 `addGestureRecognizer(_:)` 메서드를 사용하라고 되어 있다. gesture recognizer는 하나 이상의 target-action 쌍과 연관되어 있고, 복수의 target-action 쌍은 서로 분리되어있다. 여기서 action에 해당하는 부분을 정의하기 위해선 다음 두 가지 형식으로 메서드를 정의해 주어야 한다.

```swift
@IBAction func myActionMethod()
@IBAction func myActionMethod(_ sender: UIGestureRecognizer)
```

후자로 `sender`에 UIGestureRecognizer를 넘겨주면 gesture recognizer가 추가적인 정보를 전달해준다고 한다. 제스처에 대한 더 자세한 것(예를 들어 위치 등)을 알고 싶을 때 sender를 넘겨 주어 함수 내부에서 사용할 수 있는 것이다.

제스처를 감지하고 실행되는 액션인 `initialImageViewDidTap` 함수에 sender로 UITapGestureRecognizer를 넘겨줬지만, 이를 넘겨주지 않고도 Tap 제스처를 감지하는 것은 가능하다.

나는 여기서 UI**Tap**GestureRecognizer를 생성해 addGestureRecognizer로 추가해주고서, 액션 함수에서 sender로 넘겨 주는 gesture recognizer를 그냥 UIGestureRecognizer로 넘겨줘서 처음에 작동이 안 됐다. 황당한 휴먼에러...

## 결론

어쨌든, HomeViewController가 PageViewController의 DataSource, Delegate 프로토콜을 채택하고, 페이지 스크롤을 감지해 그 때 마다 인덱스에 맞는 image를 갖는 PageContentViewController를 생성하게 했다.

또, 초기 값이 없을 때 TapGesture를 감지할 수 있는 UIImage를 띄우도록 했다.

### 참고자료

[UIPageViewController 사용하기](https://g-y-e-o-m.tistory.com/119)

[UIGestureRecognizer - Apple Documentation](https://developer.apple.com/documentation/uikit/uigesturerecognizer)

[UIPageViewController - Apple Documentation](https://developer.apple.com/documentation/uikit/uipageviewcontroller)