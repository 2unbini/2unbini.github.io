---
title: "RIBs 아키텍처 이해하기"
toc: true
toc_sticky: true
categories:
  - 📂 all
  - study
tags:
  - RIBs
  - Architecture
last_modified_at: 2025-08-31
---


## 요약

- RIBs는 대규모 iOS 앱에서 기능을 **세분 모듈화**하고 트리 구조로 연결해 확장성과 테스트 용이성을 극대화하는 아키텍처다.[^16]
- MVP/MVVM/Clean Architecture와 목표는 유사하지만, 구성 단위를 트리로 조직하고 라우팅을 명시적으로 분리한다는 점이 다르다.[^14][^17]
- 트리 구조는 독립 개발, 명확한 의존성, 안전한 화면 플로우 관리에 유리하며, DI는 이 구조를 실현하는 수단으로 함께 쓰인다.[^1][^15]


## 왜 RIBs인가

RIBs는 Router, Interactor, Builder를 핵심으로 하여, 화면 전환과 모듈 생명주기 관리( Router ), 비즈니스 로직( Interactor ), 모듈 생성과 의존성 주입( Builder )을 분리한다. 이로써 수십~수백 개의 기능을 병렬로 개발하고 안정적으로 통합하기 좋다.[^16]

## 다른 패턴과 비교

- MVP: View-Presenter 1:1 중개로 UI와 로직을 분리하되, 화면 수 증가 시 Presenter 관리가 늘어난다.[^18]
- MVVM: ViewModel을 통한 상태/바인딩 중심으로 테스트가 쉽지만, 화면 흐름/내비게이션의 명시적 구조화는 별개다.[^18]
- Clean Architecture: Presentation/Domain/Data 계층 분리로 의존성 역전을 강조하나, 기능 단위를 트리로 구성하고 라우팅을 모듈 경계로 올려두는 점은 RIBs의 고유 특징이다.[^17]

결론적으로, RIBs는 다른 패턴의 장점을 취하면서도 트리형 라우팅과 모듈 경계를 더 강하게 규정해 대규모 앱에서 구조적 안정성을 높인다.[^14][^16]

## 트리형 모듈의 강점

- 독립 개발·교체 용이: 부모-자식 경계가 명확하여 특정 기능을 안전하게 수정/교체 가능.[^1]
- 명시적 플로우: attach/detach로 서브트리를 관리해 화면 전환과 상태 흐름이 추적 가능.[^14]
- 협업 최적화: 여러 팀이 다른 서브트리를 병렬로 개발하고 테스트할 수 있어 생산성 향상.[^11]
- 재사용·테스트성: RIB 단위 테스트가 쉬워 회귀 위험을 낮춘다.[^16]


## 트리 구조와 의존성 주입은 어떻게 다른가

- 트리 구조: 모듈 간 관계와 생명주기를 부모-자식 계층으로 모델링해 플로우와 경계를 조직한다.[^10]
- 의존성 주입(DI): 객체 의존을 외부에서 주입해 결합도를 낮추고 테스트 가능성을 높인다.[^15]

실무에서는 두 개념이 보완적이다. 트리 구조로 모듈을 나누고, Builder/Component를 통해 상·하위 RIB 간 의존을 안전하게 주입한다.[^10]

## 한 줄 정리: RIBs의 목적

질문: “결국 RIBs는 상세한 모듈화를 위한 것인가?”
답: 예. 기능을 세분 모듈화하고 트리로 구성해 확장성과 테스트성을 높이는 데 초점이 있다.[^3][^1]

## 역할 정리: Builder · Router · Interactor · View(Presenter)

- Builder: RIB 모듈을 구성하는 인스턴스(Interactor, Router, View 등) 생성과 DI 구성을 담당한다.[^3][^1]
- Router: 부모-자식 RIB를 attach/detach 하여 논리 트리를 만들고, 화면 전환과 표시/해제를 제어한다.[^4]
- Interactor: 비즈니스 로직을 수행하고, 필요 시 Router에 라우팅 변경을 요청한다.[^5]
- View/Presenter: UI 렌더링과 사용자 입력을 다루며, 비즈니스 로직은 갖지 않도록 단순하게 유지한다.[^5]

즉, 화면의 표시·전환 등 플로우 제어는 Router에서 수행되며, View(Presenter)는 모듈 내부에서 Builder가 함께 생성해 Router의 관리 하에 연결된다.[^4][^14]

## 코드 예시

### 1) 트리 개념 이해를 위한 간단 트리 자료구조 (Swift)

```swift
class TreeNode<T> {
    var value: T
    var children: [TreeNode] = []
    init(value: T) { self.value = value }
    func addChild(_ child: TreeNode) { children.append(child) }
}

class Tree<T> {
    var root: TreeNode<T>?
    init(rootValue: T) { root = TreeNode(value: rootValue) }
}

// 구성 예시: A -> B -> D, C
let tree = Tree<String>(rootValue: "A")
let nodeB = TreeNode(value: "B")
let nodeC = TreeNode(value: "C")
let nodeD = TreeNode(value: "D")
tree.root?.addChild(nodeB)
tree.root?.addChild(nodeC)
nodeB.addChild(nodeD)
```

이 구조가 RIBs의 attach/detach로 구성되는 화면 트리의 개념적 기반과 유사하다.[^13]

### 2) RIBs에서 Builder와 Router

Builder는 모듈 생성과 DI를 담당한다. Router는 자식 RIB attach/detach 및 화면 전환을 담당한다.

Builder 예시:

```swift
protocol SomeBuildable {
    func build(withListener listener: SomeListener) -> SomeRouting
}

final class SomeBuilder: SomeBuildable {
    func build(withListener listener: SomeListener) -> SomeRouting {
        let viewController = SomeViewController()
        let interactor = SomeInteractor(presenter: viewController)
        interactor.listener = listener
        let router = SomeRouter(interactor: interactor, viewController: viewController)
        return router
    }
}
```

Builder가 View/Interactor/Router를 조립해 완전한 RIB을 반환한다.[^1][^3]

Router 예시(플로우 제어와 자식 RIB 관리):

```swift
final class MainRouter: ViewableRouter<MainInteractor, MainViewController>, MainRouting, MainListener {
    private let detailBuilder: DetailBuildable
    private var detailRouter: DetailRouting?

    init(interactor: MainInteractor, viewController: MainViewController, detailBuilder: DetailBuildable) {
        self.detailBuilder = detailBuilder
        super.init(interactor: interactor, viewController: viewController)
        interactor.listener = self
    }

    // Interactor 이벤트에 따라 플로우 전환
    func showDetailRequested() {
        guard detailRouter == nil else { return }
        let detailRIB = detailBuilder.build(withListener: interactor)
        attachChild(detailRIB)
        detailRouter = detailRIB
        viewController.present(detailRIB.viewControllable)
    }

    func detailFinished() {
        guard let detailRouter = detailRouter else { return }
        detachChild(detailRouter)
        viewController.dismiss()
        self.detailRouter = nil
    }
}
```

Interactor가 비즈니스 이벤트를 발생시키면 Router가 자식 RIB를 build/attach 하거나 detach 하며 화면을 전환한다. 이로써 화면 플로우와 비즈니스 로직이 깔끔히 분리된다.[^4][^14]

### 3) 흐름 상호작용 요약

- Interactor: 사용자 액션·도메인 상태 변화 → 라우팅 변경 필요 시 Router 호출.[^5]
- Router: 자식 Builder 사용 → 자식 RIB 생성 및 attach/detach, View 표시·해제.[^4]
- Builder: 필요한 의존성(상위 RIB의 Component 포함)을 모아 RIB을 조립.[^3][^1]


## 실무 팁

- 라우팅은 철저히 Router로: 화면 표시/해제, 네비게이션 스택, 컨테이너 전환은 Interactor가 아닌 Router에서 처리한다.[^4]
- Interactor 로직 슬림화: 외부 I/O, 상태 관리, 정책 결정에 집중하고, 화면 제어는 요청만 보낸다.[^5]
- DI 일원화: Builder/Component를 통해 정적·동적 의존성을 명확히 주입하고, 테스트에서는 Mock 컴포넌트를 대체 주입한다.[^15][^3]
- 트리 경계 유지: attach/detach 시점과 부모-자식 Listener 연결을 일관성 있게 관리해 메모리/플로우 누수를 예방한다.[^14][^4]


## 마무리

RIBs는 “세분 모듈화 + 트리 라우팅 + 명시적 DI”를 결합해 대규모 iOS 앱의 복잡도를 제어한다. 화면 플로우를 Router에, 비즈니스 로직을 Interactor에, 모듈 생성과 의존성 구성을 Builder에 위임함으로써, 변경에 강하고 테스트 가능한 구조를 만든다. 대규모 협업과 장기 유지보수를 고려한다면 RIBs는 강력한 선택지다.[^1][^16][^14]
<span style="display:none">[^12][^2][^6][^7][^8][^9]</span>

## 출처
[Perplexity로 RIBs에 대해 질문과 답변을 한 뒤 이를 요약함](https://www.perplexity.ai/search/ribs-paeteoneul-clean-architec-vvBVjAeTQqy6bLy8r4uoww)

<div style="text-align: center">⁂</div>

[^1]: https://blog.mathpresso.com/1-ios-ribs-architecture-af9834956daf

[^2]: https://velog.io/@wansook0316/RIBs

[^3]: https://hasensprung.tistory.com/190

[^4]: https://blog.mathpresso.com/4-ribs-router-32b320c9669d

[^5]: https://swifty-cody.tistory.com/121

[^6]: https://pokers.tistory.com/16

[^7]: https://velog.io/@dongklee42/RIBs-도입-스토리

[^8]: https://soojin.ro/blog/unit-testing-ribs

[^9]: https://vapor3965.tistory.com/120

[^10]: https://www.codenary.co.kr/techstack/detail/ribs

[^11]: https://f-lab.kr/insight/understanding-and-applying-multi-module-structure-in-android-app-development

[^12]: https://visuresolutions.com/ko/plm-guide/modular-design/

[^13]: https://conandevdaily.tistory.com/32

[^14]: https://www.hohyeonmoon.com/blog/uikit-ribs

[^15]: https://baechukim.tistory.com/102

[^16]: https://github.com/uber/RIBs

[^17]: https://www.cmarix.com/blog/clean-architecture-ios/

[^18]: https://sonmo.tistory.com/75

