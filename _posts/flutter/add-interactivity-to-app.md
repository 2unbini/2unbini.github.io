---
title: "Flutter | Add Interactivity to App"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - flutter
tags:
    - flutter
    - 플러터
last_modified_at: 2023-10-06
---

# Add Interactivity to App - Stateful/Stateless Widget

Based on this documentation:

[Add interactivity to your Flutter app](https://docs.flutter.dev/ui/interactivity)

[Flutter Widgets 101](https://youtube.com/playlist?list=PLOU2XLYxmsIJyiwUPCou_OVTpRIn_8UMd&si=ndyIZgrpnzfR6e02)

# Initial project

Flutter로 새 프로젝트를 생성하면 다음과 같은 기본 앱/웹이 생성된다.

<img width="501" alt="1" src="https://github.com/2unbini/2unbini.github.io/assets/75126613/8d52ab11-4897-4163-ac31-b79f4c31ec9b">

단순히 화면을 표시하는 부분이 있는가 하면, 어떤 값을 변화시키는 부분도 있다.

‘You have pushed…’와 같은 텍스트는 어떤 상황에서도 **변하지 않는다.**

반면, 그 아래 숫자 텍스트는 오른쪽 하단의 + 버튼을 누를 때마다 **그 값이 바뀐다.**

단순히 화면을 표시하는 Widget 들은 `StatelessWidget` class를,

사용자 인터랙션에 반응하는 Widget 들은 `StatefulWidget` class를 따른다.

# StatelessWidget

변하지 않는 Widget.

Custom StatelessWidget Class는 다음과 같이 작성한다.

```dart
class FullScreenView extends StatelessWidget {
  const FullScreenView({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: const Text('Flutter Demo Home Page'),
      ),
      body: const Center(
        child: Column(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
							DescriptionText(),
						]),
      ),
    );
  }
}

// 변하지 않는 부분이므로 StatelessWidget
class DescriptionText extends StatelessWidget {
  const DescriptionText({super.key});

  @override
  Widget build(BuildContext context) {
    return const Text('You have pushed the button this many times:');
  }
}
```

이렇게 하면 위 기본 앱의 stateless한 부분들이 다음과 같이 만들어진다.

<img width="501" alt="2" src="https://github.com/2unbini/2unbini.github.io/assets/75126613/630fb6c4-a849-4248-b3f3-30b699f95a9c">


# StatefulWidget

사용자 인터랙션이 일어났을 때, 데이터를 받았을 때에 변화될 수 있는 Widget.

Custom StatefulWidget Class의 작성 순서는 다음과 같다.

1. 어떤 객체가 Widget의 state를 관리할 것인지 정한다.
2. StatefulWidget을 서브클래싱한다.
3. State를 서브클래싱한다.
4. 전체 Widget 트리에 Stateful Widget을 추가한다.

이를 위 프로젝트에 적용시켜보면,

1. Text에 띄울 숫자 state가 + 버튼에 의해 변경되어야 하며, 이를 관리하는 객체를 만들기로 한다.
2. 이 객체의 이름을 IncreasementWidget이라고 짓고, StatefulWidget을 서브클래싱 한다.
3. IncreasementWidget의 State를 서브클래싱하는 _IncreasementWidgetState 객체를 만든다.
4. IncreasementWidget을 FullScreenView의 children에 추가한다.

다음과 같이 작성한다.

```dart
class IncreasementWidget extends StatefulWidget {
  const IncreasementWidget({super.key});

  @override
  State<IncreasementWidget> createState() => _IncreasementWidgetState();
}

class _IncreasementWidgetState extends State<IncreasementWidget> {
  int _counter = 0;

  void _incrementCounter() {
    **setState**(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text(
          '$_counter',
          style: Theme.of(context).textTheme.headlineMedium,
        ),
        const SizedBox(
          height: 50,
        ),
        FloatingActionButton(
          onPressed: _incrementCounter,
          tooltip: 'Increment',
          child: const Icon(Icons.add),
        ),
      ],
    );
  }
}
```

## StatefulWidget and State

[StatefulWidget](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html) 인스턴스는 그 자체로는 수정 불가능(immutable)하다. 그렇기 때문에 수정 가능(mutable)한 state를 [State](https://api.flutter.dev/flutter/widgets/State-class.html) 객체에서 관리하거나, [Stream](https://api.flutter.dev/flutter/dart-async/Stream-class.html) or [ChangeNotifier](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html) 객체와 같은 State subscribes를 사용해 관리한다.

State는 (1) Widget이 빌드될 때 동기적으로 읽을 수 있고 (2) Widget lifetime동안 변경될 수 있는 정보로, [State](https://api.flutter.dev/flutter/widgets/State-class.html) 객체는 [StatefulWidget](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)의 내부 state 혹은 그 로직을 가지고 있다. [State](https://api.flutter.dev/flutter/widgets/State-class.html) 는 [State.setState](https://api.flutter.dev/flutter/widgets/State/setState.html) 를 사용해 state가 변경됐음을 즉각 알아차리고, 변경된 상태를 화면에 표시한다.

Flutter에서 화면을 구성하는 Widget들은 Stateful/StatelessWidget을 서브클래싱 해야 하는데 수정 가능한 Widget을 구성하기 위해선 StatefulWidget을 사용한다.

하지만 StatefulWidget 역시 Widget의 서브클래스이기 때문에 수정 불가능하다.

이를 해결하기 위해 StatefulWidget 타입의 State 객체를 만들어 그 객체 안에서 상태 관리를 한다.

결국 **(1) Widget (2) Mutable** 두 조건을 모두 만족시키기 위해 `StatefulWidget` 과 `State` 객체를 생성해야 하는 것이다.

### StatefulWidget

[StatefulWidget class - widgets library - Dart API](https://api.flutter.dev/flutter/widgets/StatefulWidget-class.html)

```dart
// StatefulWidget
class IncreasementWidget extends StatefulWidget {
  const IncreasementWidget({super.key});

  @override
  State<IncreasementWidget> createState() => _IncreasementWidgetState();
	// createState() 메서드로 State 객체를 생성한다.
}
```

### **State<T extends StatefulWidget>**

[State class - widgets library - Dart API](https://api.flutter.dev/flutter/widgets/State-class.html)

```dart
// State<T extends StatefulWidget>
class _IncreasementWidgetState extends State<IncreasementWidget> {

	// 변수
  int _counter = 0;

	// 버튼을 눌렀을 때 변수에 +1을 해 주어야 한다.
	// stateState() 메서드를 이용해 state를 변화시킨다.
  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text(
          '$_counter', 
          style: Theme.of(context).textTheme.headlineMedium,
        ),
        const SizedBox(
          height: 50,
        ),
        FloatingActionButton(
          onPressed: _incrementCounter, // Increament Action
          tooltip: 'Increment',
          child: const Icon(Icons.add),
        ),
      ],
    );
  }
}
```

이렇게 하면 버튼을 눌러 올라간 숫자를 화면에 동적으로 표시할 수 있다.


https://github.com/2unbini/2unbini.github.io/assets/75126613/8b30eeac-fe66-4025-ad41-ee4b06cbef2a



### See Also

- underscore(`_`)로 시작하는 멤버나 클래스는 private(정의된 dart 파일 내에서 사용 가능)하다.
- `$`, `${ }` 를 사용해 String Interpolation을 할 수 있다.
- const WidgetName({super.key}); Widget 트리상 어디에 위치해있는지 key를 통해 알 수 있다.
- State Management는 Page routing, Form filling 등 여러 사용자 인터랙션에 사용된다.

# Managing State

Stateful/Stateless Widget을 사용하는 것은 State 관리 방식 중 하나에 해당한다.

이는 Widget이 직접 state를 관리하는 방식으로, 부모 Widget이나 나머지 전체 UI에 영향을 미치지 않는 범위에서 주로 사용된다.

부모-자식 Widget 간, 혹은 App 전체 UI에 연관된 state 관리에도 사용하지만, Widget 트리가 복잡해지고 다양한 상태를 관리해야 할 때는 상태 관리를 위한 다른 방식(Provider, Riverpod, …)을 주로 사용한다.

# Full Code

```dart
// main.dart

import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: const FullScreenView(),
    );
  }
}

class FullScreenView extends StatelessWidget {
  const FullScreenView({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: const Text('Flutter Demo Home Page'),
      ),
      body: const Center(
        child: Column(mainAxisAlignment: MainAxisAlignment.center, children: [
          DescriptionText(),
          IncreasementWidget(),
        ]),
      ),
    );
  }
}

class DescriptionText extends StatelessWidget {
  const DescriptionText({super.key});

  @override
  Widget build(BuildContext context) {
    return const Text('You have pushed the button this many times:');
  }
}

class IncreasementWidget extends StatefulWidget {
  const IncreasementWidget({super.key});

  @override
  State<IncreasementWidget> createState() => _IncreasementWidgetState();
}

class _IncreasementWidgetState extends State<IncreasementWidget> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      mainAxisAlignment: MainAxisAlignment.center,
      children: [
        Text(
          '$_counter',
          style: Theme.of(context).textTheme.headlineMedium,
        ),
        const SizedBox(
          height: 50,
        ),
        FloatingActionButton(
          onPressed: _incrementCounter,
          tooltip: 'Increment',
          child: const Icon(Icons.add),
        ),
      ],
    );
  }
}
```
