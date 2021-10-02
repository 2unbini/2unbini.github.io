---
title: "SpriteKit, 장면 바꾸기"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - swift
tags:
    - swift
    - 스위프트
    - SpriteKit
last_modified_at: 2021-09-26
---

# SpriteKit으로 장면 바꾸기

특정한 버튼을 누르면 다른 장면으로 바뀌게끔 하고 싶었다.

뭐든간에 SpriteKit을 사용해서 보이게 하려면, `View`를 생성하고, `Scene`을 생성하고, `view`에서 `scene`을 `present`하게 하면 됐다. 이를 생각해봤을 때, 장면을 바꾸려면 새로운 `scene`을 만들고, `view`에서 `present` 하게 하면 되지 않을까? 라는 생각이 들었다. 검색과 직접 실험을 통해 그렇게 하면 된다는 것을 검증했다.

근데 아직 `view`에 대한 이해가 부족해서 의문점이 남아 있다.

## 장면 바꾸기

뷰가 생성되어있다는 걸 가정하고, 특정 이벤트(터치)가 일어났을 때 장면을 바꿔보았다.

```swift
// FirstScene.swift

import Foundation
import SpriteKit

class FirstScene: SKScene {
    override func didMove(to view: SKView) {
        
        // 중심점 가운데로 맞추기
        self.anchorPoint = CGPoint(x: 0.5, y: 0.5)

        // 배경화면 색깔 바꾸기
        self.backgroundColor = .yellow

        // FirstScene에 띄울 라벨 노드
        let title = SKLabelNode(text: "🌚")
        title.fontSize = 100
        addChild(title)
    }

    // 터치 이벤트가 일어났을 때, SecondScene으로 이동
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        let touch = touches.first

        if (touch?.location(in: self)) != nil {
            let secondScene = SecondScene()
            
            secondScene.scaleMode = .resizeFill
            view?.presentScene(secondScene)
        }
    }
}
```

```swift
// SecondScene.swift

import SpriteKit
import GameplayKit

class SecondScene: SKScene {
    override func didMove(to view: SKView) {
        
        // 중심점 가운데로 맞추기
        self.anchorPoint = CGPoint(x: 0.5, y: 0.5)

        // 배경화면 색깔 바꾸기
        self.backgroundColor = .gray

        // FirstScene에 띄울 라벨 노드
        let title = SKLabelNode(text: "🌝")
        title.fontSize = 100
        addChild(title)
    }

    // 터치 이벤트가 일어났을 때, FirstScene으로 이동
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        let touch = touches.first

        if (touch?.location(in: self)) != nil {
            let firstScene = FirstScene()

            firstScene.scaleMode = .resizeFill
            view?.presentScene(firstScene)
        }
    }
}
```

이렇게 하면, 터치가 일어났을 때 첫 번째는 두 번째를, 두 번째는 첫 번째를 불러오는 방식으로 변화가 일어난다.

## 결과

![scene](/assets/images/spriteKit/scene.gif)