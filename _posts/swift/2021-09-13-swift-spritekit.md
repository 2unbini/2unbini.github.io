---
title: "스위프트 그래픽 프레임워크, SpriteKit"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - 프레임워크
    - SpriteKit
last_modified_at: 2021-09-13
---

# SpriteKit

애플 [공식 문서](https://developer.apple.com/documentation/spritekit/)에 따르면 SpriteKit는 앱에 자연스러운 2D 애니메이션 요소를 넣거나, 2D 기반 게임을 개발할 때 사용하는 프레임워크다.

물체, 파티클, 문자, 이미지, 영상을 2차원에 그릴 수 있는 프레임워크다. 고품질의 렌더링을 위해 Metal을 사용하면서 개발하기 용이한 인터페이스를 제공한다고 한다. SpriteKit은 iOS, macOS, tvOS, watchOS에 지원하며, `GameplayKit`과 `SceneKit`과 같은 다른 프레임워크들과 잘 혼용하여 사용할 수 있다.


# SpriteKit 으로 이미지 띄우기

해당 내용은 [공식 문서](https://developer.apple.com/documentation/spritekit/drawing_spritekit_content_in_a_view)를 기반으로 한 내용입니다.

## 씬 생성하기

SpriteKit를 통해 보여지는 모든 것은 `SKScene`의 인스턴스인 씬 객체를 통해 보여진다.

`SKScene`을 초기화할 때 `size`에 초깃값을 주면, 그게 뷰의 크기가 된다. 또한, 이 씬 객체에 `anchorPoint`를 주면 해당 씬의 고정점을 바꿔줄 수 있다. 다음의 코드를 `viewDidLoad()` 함수에 추가하면 고정점이 정 중앙에 놓인, `size` 만큼의 씬이 생성된다.

```swift
let scene = SKScene(size: skView.bounds.size)

scene.anchorPoint = CGPoint(x: 0.5, y: 0.5)
```

## 씬의 위에 콘텐츠 씌우기

SpriteKit 뷰를 통해 그래픽을 띄우려면 노드 객체를 사용할 것이다.

여기서 `노드`는 SpriteKit 를 통해 콘텐츠를 렌더링할 때 가장 기반이 되는 주춧돌이고, 이 모든 노드들의 기본 클래스가 되는 게 `SKNode`라고 한다. 즉, 뷰에 보여지는 모든 요소들은 `SKNode`거나 그의 하위클래스인 것이다. 앞서 씬을 생성할 때 봤던 `SKScene`은 이 노드 중 가장 **루트에 해당하는 노드**로, 다른 노드들이 여기에 추가될 수 있는 것이다. 이렇게 추가된 **자식 노드들을 씬(루트 노드)이 렌더링하여 뷰를 통해 보여준다**고 한다. 이 노드들이 모여 씬이 되고, 이 씬이 최종적으로 `SKView`를 통해 보여지는 것이다.

뷰에 이미지를 보여지게 하려면 `SKSpriteNode`를 사용하면 된다. 콘텐츠를 씬에 보여지게 하는 함수들에 대한 더 많은 정보는 [노드 트리에 접근하고 수정하는 법](https://developer.apple.com/documentation/spritekit/sknode/accessing_and_modifying_the_node_tree)에서 확인할 수 있다.

```swift
let image = SKSpriteNode(imageNamed: "image.png")

scene.addChild(image)
```

`imageNamed`에 들어갈 이미지는 `Assets.xcassets`에 추가하면 된다.

## 뷰 안에서 씬 나타내기

`presentScene(_:)`를 사용하여 설정한 씬을 뷰에 보여지게 할 수 있다. 이 모든 과정에서 나온 코드들은 뷰를 만드는 것이기 때문에, 뷰 컨트롤러의 `viewDidLoad()` 함수 안에 해당 코드들을 삽입하면 된다.

```swift
if let skView = self.view as? SKView {
	skView.presentScene(scene)
}
```

근데 여기서 의문인 점. 왜 노드를 만들고 장면을 만들고 그걸 뷰에 올리는 순서로 하면 제대로 작동이 안 될까?

라는 의문이 있었는데, 결국 UIKit에 대한 이해 부족때문이었다. `storyboard`에서 `View`의 클래스를 `UIView`에서 `SKView`로 바꾸어줬더니 해결되었다.


### 소스코드와 결과

<details>
<summary>실행해본 소스코드</summary>
<div markdown="1">

```swift
// ViewController.swift

import UIKit
import SpriteKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        
        view = SKView(frame: view.bounds)
        
        if let skView = self.view as? SKView {
            
            let scene = SKScene(size: skView.bounds.size)
            let image = SKSpriteNode(imageNamed: "siri")
            
            scene.anchorPoint = CGPoint(x: 0.5, y: 0.5)
            scene.scaleMode = .aspectFill
            scene.addChild(image)
            
            skView.presentScene(scene)
            print("scene presented")
        }
    }
}

//
// ViewController.swift
// storyboard의 View 클래스 UIView -> SKView

import UIKit
import SpriteKit

class ViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        
        let scene = SKScene(size: view.bounds.size)
        let image = SKSpriteNode(imageNamed: "siri")
        
        scene.anchorPoint = CGPoint(x: 0.5, y: 0.5)
        scene.addChild(image)
        
        if let skView = self.view as? SKView {
            skView.presentScene(scene)
            print("scene presented")
        }
    }
}


```

</div>
</details>


![siri](/assets/images/all/siri.png)

### 참고

[SpriteKit Basics: Nodes](https://code.tutsplus.com/tutorials/spritekit-basics-nodes--cms-28785)

### 후기

`UIKit`공부가 필요하다..!!
