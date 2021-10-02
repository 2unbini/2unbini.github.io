---
title: "SpriteKit, 제자리에서 움직이는 이미지 만들기"
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
last_modified_at: 2021-09-17
---

# 움직이는 이미지 만들기

## 준비물

Xcode, 움직임을 줄 이미지 묶음, 인터넷

## 과정

1. 뷰와 장면 생성
2. 장면 구성하기
	- 텍스처 만들기
	- 노드 만들기
	- 노드에 움직임 주기
3. 감상하기

# 뷰, 장면 생성

## 프로젝트 생성 & 파일 정리

Xcode를 열어 `Game`용 프로젝트 하나를 생성한다.

`Game`으로 프로젝트를 생성하면 자동으로 코드들이 생성되어 있다. 우선 `.sks` 확장자를 가진 파일들을 삭제해준다. `.sks`는 장면 에디터라고 생각하면 될 것 같다. 나는 그냥 코드로 장면을 만들어줄 것이기 때문에 삭제 해 주었다.

뷰와 장면을 생성하려면 `Controller`로 가야 한다. 기본적으로 생성된 `GameViewController.swift`의 `viewDidLoad()`함수에 `super.viewDidLoad()`만 제외하고 모두 삭제 해 주었다.

## 뷰, 장면 생성 코드 추가

깔--끔한 컨트롤러에 뷰와 씬을 넣어준다.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    if let view = self.view as! SKView? {
        // 움직이는 이미지가 만들어질 장면
        let scene = GameScene()

        // 장면과 뷰 설정
        scene.scaleMode = .resizeFill
        view.ignoreSiblingOrder = true
        view.showsFPS = true
        view.showsNodeCount = true

        // 뷰에 장면 띄우기
        view.presentScene(scene)
    }
}
```

`view`의 속성으로 세 불리언 값을 주었다. 직관적으로 이름을 따라간다고 생각하면 된다.

- `ignoreSiblingOrder`는 부모-자식 혹은 형제 관계가 노드들이 렌더링될 때 그 순서에 영향을 미치는지 아닌지에 대한 설정값이다.
- `showsFPS`는 Frame Per Second를 보여주는 것으로, 1초에 화면이 얼마나 그려지는지에 대한 단위를 화면에 표시할지 아닌지에 대한 것이다.
- `showsNodeCount`역시 노드의 개수를 보여줄지 말지를 결정하는 부분이다.

`scene`의 속성으로 `scaleMode`를 주었는데, 장면의 크기를 결정하는 부분이다. `resizeFill`을 하면 뷰에 딱 맞게 장면이 재조정된다.

# 장면 구성하기

## 코드 정리하기

뭐가 많이 쓰여있을텐데, 아무고토 필요 없다. `didMove` 틀만 남기고 다 지워준다.

## 텍스처 만들기

### atlas 폴더

준비해 놓은 이미지 묶음을 프로젝트파일에 끌어다 놓는다. 그냥 놓으면 안되고, 이미지들을 `.atlas`로 끝나는 폴더에 묶고 그걸 넣어준다. 먼말이고?

![atlas](/assets/images/spriteKit/atlas.gif)

위와 같이 해 주고, 해당 이미지를 사용해준다.

아래에서 사용할 `SKTextureAtlas`클래스는 `.atlas`폴더를 알아서 찾아 만들어주기 때문에, 폴더 이름에 `.atlas`를 붙여주었다.

### SKTextureAtlas

불러온 이미지를 텍스처화시켜 배열로 만들어준 후, 배열을 영원히 반복하면서 움직이는 것처럼 보이게 할 것이다.

앞서 말한 `SKTextureAtlas`를 이용해 이미지를 텍스처화한다.

```swift
override func didMove(to view: SKView) {
    let textureAtlas = SKTextureAtlas(named: "images")
    var textureArray = [SKTexture]()
}
```

[`SKTextureAtlas`](https://developer.apple.com/documentation/spritekit/sktextureatlas/)는 이미지를 렌더링할 때 저장공간과 이미지 그리기를 최적화할 수 있는 텍스처 컬렉션이라고 한다.

지금은 텍스처 컬렉션이기 때문에, 이를 하나씩 텍스처로 만들어서 앞에 선언한 `textureArray`에 추가할 것이다. 역시 하나씩 뭘 하는 덴 `for`문을 쓰면 된다.

```swift
override func didMove(to view: SKView){
    ...

    // 반복문을 돌되, 텍스처 컬렉션의 개수만큼 해 준다.
    // 나는 이미지 이름이 1부터 시작해서 1로 시작했다.
    for i in 1...textureAtlas.textureNames.count {
    
        // 이 이름은 앞에서 불러온 폴더 안 이미지 이름, 확장자명과 동일하다.
        let name = "image_\(i).png"
        textureArray.append(SKTexture(imageNamed: name))
    }
}
```

## 노드 만들기

### 노드 만들고 첫 번째 이미지 지정해주기

텍스처 준비는 끝났다. 노드를 선언하고, 그에 첫 번째 이미지를 지정해준다. 나는 슬라임 이미지를 가져왔기 때문에 노드의 이름을 `slime`이라고 했다.

```swift
override func didMove(to view: SKView) {
    ...
    let firstImage = textureAtlas.textureNames[0]
    var slime = SKSpriteNode(imageNamed: firstImage)
}
```

textureAtlas.textureNames[0] 대신 textureArray[0] 혹은 첫 번째로 삼고 싶은 이미지의 인덱스 혹은 그 이미지의 이름을 바로 넣어도 된다.

라고 했는데 안 된다. 왜 안되냐면 firstImage에 들어가는 요소는 String 타입이어야 하는데, textureArray에 있는 요소는 SKTexture기 때문. 이를 문자열로 바꾸는 방법을 찾아봤는데 아직 모르겠다.

### 노드 설정하기

노드의 크기나 위치, 이름등을 정해줄 수 있다.

적당한 크기로, 장면의 정 가운데에 위치할 수 있도록 해 주었다.

```swift
override func didMove(to view: SKView) {
    ...
    slime.size = CGSIZE(width: 80, heigth: 80)
    slime.position = CGPoint(x: self.size.width / 2, y: self.size.height / 2)
    slime.name = "slime"
}
```

> 노드에 이름을 추가해주면 노드가 많을 때 이름으로 노드를 탐색할 수 있다.

## 노드에 움직임 주기

### SKAction

장면에 있는 노드에 의해 실행되는 애니메이션을 [`SKAction`](https://developer.apple.com/documentation/spritekit/skaction/)이라고 한다. 특정 애니메이션을 줄 텍스처 배열과 프레임 하나당 유지할 시간을 인자로 받는 `animate`메서드를 이용하면 된다.

```swift
override func didMove(to view: SKView) {
    ...
    let slimeAnimation = SKAction.animate(with: textureArray, timePerFrame: 0.2)
    slime.run(SKAction.repeatForever(slimeAnimation))
}
```

만들어진 액션을 `slime`이 영원히 실행할 수 있도록 해 준다.

### 터치로 움직임을 주고 싶다면?

방금 구현한 움직임 부분을 `touchesBegan`함수에 오버라이드 해 주면 된다.

```swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    let slimeAnimation = SKAction.animate(with: textureArray, timePerFrame: 0.2)
	slime.run(SKAction.repeatForever(slimeAnimation))
}
```

이렇게 하면 `slime`이 없다고 에러가 뜰 것이다. `didMove` 함수 안에서 선언한 `slime`을 함수 밖, `GameScene` 클래스 안에 선언해주면 해결된다.

# 소스코드와 결과

<details>
<summary>실행해본 소스코드</summary>
<div markdown="1">

```swift
// GameViewController.swift

override func viewDidLoad() {
    super.viewDidLoad()

    if let view = self.view as! SKView? {

        let scene = GameScene()

        scene.scaleMode = .resizeFill
        view.ignoreSiblingOrder = true
        view.showsFPS = true
        view.showsNodeCount = true
        view.presentScene(scene)
    }
}
```

```swift
// GameScene.swift

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
```

</div>
</details>

![gif](/assets/images/spriteKit/slime.gif)

### slime asset은?!

[여기서 다운받았습니다!](https://stealthix.itch.io/animated-slimes?download)

잘못된 부분이나 더 나은 방향이 있다면 언제든 알려주세요!