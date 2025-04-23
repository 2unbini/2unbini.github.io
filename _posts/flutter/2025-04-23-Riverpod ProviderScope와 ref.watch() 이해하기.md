---
title: "Riverpod ProviderScope와 ref.watch(), Consumer와 StateNotifier 이해하기"
toc: true
toc_sticky: true
categories:
  - 📂 all
  - flutter
tags:
  - flutter
  - 플러터
  - riverpod
last_modified_at: 2025-04-23
---

# 에러 사례로 배워보자...

## ❗️ 문제 상황

Flutter에서 다음과 같은 Riverpod 구조를 사용했을 때, 애니메이션 관련 Provider에서 다음과 같은 에러가 발생했다:

```
Tried to read Provider<Animation<double>> from a place where one of its dependencies were overridden but the provider is not.
```

### 💥 코드 구성

```dart
class IntroItemWidget extends ConsumerWidget {
  final String text;
  final int index;

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final animation = ref.watch(introItemAnimationProvider(index)); // ❗️ 에러 발생
    ...
  }
}
```

- 이 위젯은 `List.generate()`를 통해 HomeScreen에서 생성되었다.
- `introItemAnimationProvider`는 내부적으로 `animationControllerProvider`를 `watch`하고 있음.
- 이 `animationControllerProvider`는 HomeScreen 내에서 `override` 되었음.

## ❓ 나의 의문점

### 🤔 왜 ref.watch를 썼는데 Provider를 못 찾는다고 할까?

### 🤔 List.generate()로 만든 위젯이 왜 문제가 되는 거지?

### 🤔 그럼 ProviderScope 안에 있는 것과 밖에 있는 건 어떻게 구분돼?

### 🤔 위젯을 따로 안 만들고 코드만 직접 썼다면 에러가 안 났을까?

## 🔍 원인 분석

| 개념              | 설명                                                                               |
| ----------------- | ---------------------------------------------------------------------------------- |
| `ProviderScope`   | 특정 Provider를 override하거나 주입하는 "범위(scope)" 역할                         |
| `ref.watch()`     | 현재 context 안의 Provider를 조회함. **그 context가 ProviderScope 안에 있어야 함** |
| `ConsumerWidget`  | ref를 자동으로 주입해주는 구조지만, 이 위젯이 어디서 build되는지가 중요            |
| `List.generate()` | 위젯을 단순 생성만 하고, 그 build가 정확히 어떤 ProviderScope 안인지 보장되지 않음 |

---

## ✅ 해결 방식

### ✅ 방법 1: `Consumer` 사용

```dart
class IntroItemWidget extends StatelessWidget {
  final int index;
  final String text;

  @override
  Widget build(BuildContext context) {
    return Consumer(
      builder: (context, ref, _) {
        final animation = ref.watch(introItemAnimationProvider(index));
        return FadeTransition(opacity: animation, child: Text(text));
      },
    );
  }
}
```

> `Consumer`는 **ref를 명확히 직접 받기 때문에, 어떤 위치에서도 안전하게 작동**

---

### ✅ 방법 2: 코드 분리 없이 HomeScreen 내에서 직접 처리

```dart
Column(
  children: List.generate(introItems.length, (index) {
    final animation = ref.watch(introItemAnimationProvider(index));
    return FadeTransition(opacity: animation, child: Text(introItems[index]));
  }),
);
```

> `ref`는 HomeScreen 내의 `build()` 함수에서 왔기 때문에, **ProviderScope 안에서 안정적으로 연결됨**

---

## 📌 핵심 요약

| 항목                                                               | 설명 |
| ------------------------------------------------------------------ | ---- |
| `ref.watch()`는 반드시 ProviderScope 내에서 사용해야 함            |
| `ConsumerWidget`은 그 자체가 scope 안에서 build되어야 함           |
| `Consumer(builder: ...)`는 ref를 "그 자리에서" 받기 때문에 안전    |
| ProviderScope 밖에서 ref.watch하면 의존성 오류 발생 가능           |
| 분리된 위젯은 context 보장을 위해 `Consumer`를 쓰는 것이 가장 안전 |

---

## 💡 기억하면 좋은 구조 요약

```
ProviderScope (override: animationController)
│
├── HomeScreen (ConsumerStatefulWidget)
│   ├── List.generate(...) → Consumer ❗️ 안전
│   ├── IntroItemWidget → ConsumerWidget ❌ 위험 (build 타이밍 불명확)
```

## 첫번째 결론

이상 챗 지피티와 내가 한 질문과 답변을 요약하면,

1. provider를 정의해서 사용하려고 했으나
2. ProviderScope 밖이라 ref.watch를 하지 못함
3. 그래서 Consumer를 사용해 Scope 밖에서도 context를 알아먹을 수 있도록 함

근데 그래서 에러 해결이 됐나? 응 아니...

# `Consumer`로 바꿔도 에러가 해결되지 않았던 이유 vs `StateNotifier`로 해결된 이유

## ❓ 문제 상황

- `animationControllerProvider`를 `ProviderScope(overrides: [...])`로 override 했지만
- `IntroItemWidget` 내부에서 `ref.watch(...)` 했을 때 에러 발생
- `ConsumerWidget → Consumer(builder: ...)` 로 바꿔도 여전히 에러 발생

## ⚠️ 에러가 해결되지 않았던 이유 (Consumer 사용)

### 문제 핵심

> `animationControllerProvider`는 override 되었지만,  
> `IntroItemWidget`이 **build되는 시점에 해당 provider가 아직 null이거나 연결이 안 된 상태**였음.

### 원인 요약

- `ProviderScope`에서 override한 controller가 아직 완전히 준비되지 않음
- 위젯 트리가 build될 때 ref가 provider를 보려고 했지만, 아직 controller가 없었음
- `Consumer`로 감싸도 **context는 연결됐지만 값이 없으니 null → 에러 발생**

## ✅ 해결된 이유 (StateNotifierProvider + setController)

### 구조 변화

```dart
final animationControllerNotifierProvider = StateNotifierProvider<...>

WidgetsBinding.instance.addPostFrameCallback((_) {
  ref.read(animationControllerNotifierProvider.notifier).setController(_controller);
});
```

### 왜 해결되었는가?

- **AnimationController를 runtime에 동적으로 설정 가능**
- ProviderScope 안에서 watch할 때 값이 null이면 → fallback or graceful handling 가능
- build와 초기화의 타이밍을 분리하여 **애니메이션 초기화 → 주입 → 사용 흐름을 명확히 만듦**

## 🧠 핵심 비교 요약

| 항목               | Consumer 사용                      | StateNotifier 사용                 |
| ------------------ | ---------------------------------- | ---------------------------------- |
| ref context 연결   | ✅ 됨                              | ✅ 됨                              |
| controller 값 보장 | ❌ 시점에 따라 null                | ✅ 런타임에 직접 주입              |
| 안정성             | ❌ 초기 build 시 에러 발생 가능    | ✅ 값이 준비되었을 때만 사용       |
| 흐름 제어          | ❌ ProviderScope와 build 순서 민감 | ✅ 명시적으로 controller 세팅 가능 |

## 📌 결론

> `Consumer`는 context 연결만 보장할 뿐,  
> **provider 내부 값의 초기화 상태까지 보장하지는 않음.**
>
> 그래서 **초기화가 필요한 값(예: AnimationController)** 는 `StateNotifier`처럼  
> **시점 제어가 가능한 구조에서 관리하는 것이 안정적.**

어렵다.
