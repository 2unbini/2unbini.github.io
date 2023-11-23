---
title: "Flutter, Nested navigation push/pop with go router"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - flutter
    - error
    - sapjil
tags:
    - flutter
    - 플러터
    - AOS
    - iOS
last_modified_at: 2023-11-15
---

### Nested Navigation을 구성했을 때 push/pop이 어려운 문제

- Navigation 화면을 구성하는 main widget에서 현재 location에 대해 확인하고, 분기처리를 통해 bottom navigation을 표시/미표시 한다.
- location getter를 GoRouter extension으로 추출해내면 사용하기 용이하다.

```dart
import 'package:go_router/go_router.dart';

extension GoRouterLocation on GoRouter {
  String get location {
    final RouteMatch lastMatch = routerDelegate.currentConfiguration.last;
    final RouteMatchList matchList = lastMatch is ImperativeRouteMatch
        ? lastMatch.matches
        : routerDelegate.currentConfiguration;
    return matchList.uri.toString();
  }
}
```

- ref: https://github.com/flutter/flutter/issues/129833#issuecomment-1766029209
