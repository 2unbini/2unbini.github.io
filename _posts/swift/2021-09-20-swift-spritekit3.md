---
title: "SpriteKit, 탭 한 위치로 캐릭터 움직이기"
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
last_modified_at: 2021-09-20
---

# 탭 한 위치로 캐릭터 움직이기

[앞서 만든](https://2unbini.github.io/swift/swift-spritekit2/) 움직이는 캐릭터를 사용자가 탭 한 위치로 움직일 수 있게 해 보았다.

## 과정

1. 탭한 위치 받아오기
2. 노드의 실시간 위치 받아오기
3. 해당 위치로 노드 움직이기

# 탭 위치 알아내기

우선, `touchesBegan` 함수를 오버라이드한다.

```swift
override func toucesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    let touch = touches.first

    if let location = touch?.location(in: self) {
        let destX: CGFloat = location.x
        let destY: CGFloat = 0.0
	}
}
```

## touch

터치가 감지됐을 때 그 정보를 `touch`에 담고, 그 곳에서 위치정보 `location`을 얻어낸다.
각 x, y 좌표는 location의 x, y 좌표에 해당한다. 나는 x 좌표로만 노드를 움직일 것이기 떄문에 y 좌표를 0으로 고정했다.


# 실시간 노드 위치 알아내기

실시간으로 변화하는 노드의 위치를 알아내기 위해선 `update` 함수를 활용해야 한다.

`update` 함수가 호출되면, 해당 함수 안에 있는 특정한 로직을 매 프레임마다 실행해 그야말로 '업데이트'하게 한다.

해당 함수를 통해 노드가 움직일 때마다 변화하는 위치를 `lastPosition`이라는 변수에 저장했다.

```swift
class GameScene: SKScene {
    ....

    var slime = SKSpriteNode()
    var lastPosition = CGPoint()

	...

    override func update(_ currentTime: TimeInterval) {
        let currentPositionX = slime.position.x
    
        lastPosition.x = currentPositionX
    }

    ...
}
```

역시 x좌표만 필요하므로 x좌표만 저장했다.

# 해당 위치로 움직이기

터치했을 때의 위치정보와, 실시간으로 변화하는 노드의 위치정보를 알았으니 이제 움직임을 정의해주면 된다.

```swift
override func toucesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    let touch = touches.first

    if let location = touch?.location(in: self) {
        let destX: CGFloat = location.x
        let destY: CGFloat = 0.0
        let moveAction = SKAction.move(to: CGPoint(x: destX, destY), duration: 1)
        childNode(withName: "slime")?.run(moveAction)
	}
}
```

## SKAction

`SKAction`을 정의해준다. 터치가 시작된 곳의 좌표로 1의 시간만큼 움직임을 주는 것이다.

여기서 `duration`을 1과 같은 상수로 적어주면 현재 노드가 있는 곳에서 터치가 일어나는 곳까지의 거리에 따라 그 속도가 바뀐다. 일정하게 속도를 주고 싶다면, `속/시/거`를 이용해 계산해서 시간을 정해 주면 된다.

```swift
let distance = destX - lastPosition.x > 0 ? destX - lastPosition.x : -(destX - lostPosition.x)
let moveAction = SKAction.move(to: CGPoint(x: destX, y: destY), duration: Double(distance) / 80)
```

이런 식으로 노드의 마지막 위치에서, 목적지 위치까지의 거리를 계산해 일정한 속도로 따라갈 수 있게 했다.

# 더 해볼만한 것

## 방향에 따라 노드 이미지 바꾸기

`touchesBegan`에서 `moveAction`을 하는 자식 노드의 이름을 방향에 따라 달리 저장해준 뒤, 현재 방향을 추적해 그 이름을 넣으면 된다. 예를 들어,

```swift
func currentSlimeDirection(_ currPos: CGPoint) -> String {
    if (currPos.x > lastPosition.x) { return "right" }
    else if (currPos.x < lastPosition.x) { return "left" }
    else { return "front" }
}
```

이런 방식으로 노드의 방향을 얻어내고, 이 방향에 따라 노드를 새로 생성해주고, 그 이름을 방향으로 지정한다. 그러면 방향에 따라 방향 이름을 갖는 노드를 장면에 보여지게 할 수 있다.

## 이동 제한 구역 두기

앞서, y 좌표를 0으로 고정시킨 것과 같이, 어떠한 범위 이상을 넘어가면 더 이상 움직일 수 없게 값을 고정시키면 된다.

```swift
func restrictSlimePosition(_ destX: CGFloat) -> CGFloat {
    if destX >= 100 { return 100 }
    else if destX <= -100 { return -100 }
    else { return destX }
}
```

# 소스코드

<details>
<summary>실행해본 소스코드</summary>
<div markdown="1">

```swift
// GameScene.swift

class GameScene: SKScene {

    var slime = SKSpriteNode()
    var lastPosition = CGPoint()

    override func didMove(to view: SKView) {
        let textureAtlas = SKTextureAtlas(named: "images")
        var textureArray = [SKTexture]()
    
        for i in 1...textureAtlas.textureNames.count {
            let name = "image_\(i).png"
            textureArray.append(SKTexture(imageNamed: name))
        }
    
        let firstImage = textureAtlas.textureNames[0]
        var slime = SKSpriteNode(imageNamed: firstImage)
    
        slime.size = CGSIZE(width: 80, heigth: 80)
        slime.position = CGPoint(x: self.size.width / 2, y: self.size.height / 2)
        slime.name = "slime"
    
        let slimeAnimation = SKAction.animate(with: textureArray, timePerFrame: 0.2)
        slime.run(SKAction.repeatForever(slimeAnimation))
    }

    override func toucesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        let touch = touches.first
    
        if let location = touch?.location(in: self) {
            let destX: CGFloat = location.x
            let destY: CGFloat = 0.0
            let moveAction = SKAction.move(to: CGPoint(x: destX, destY), duration: 1)
            childNode(withName: "slime")?.run(moveAction)
    	}
    }

    override func update(_ currentTime: TimeInterval) {
        let currentPositionX = slime.position.x
    
        lastPosition.x = currentPositionX
    }
}

```

</div>
</details>

잘못된 부분이나 더 나은 방향이 있다면 언제든 알려주세요!
