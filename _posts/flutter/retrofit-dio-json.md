---
title: "Flutter | Retrofit, Dio, Json Serializable 사용법"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - flutter
tags:
    - flutter
    - 플러터
    - 패키지
last_modified_at: 2023-11-15
---

# Retrofit, Dio, JSON Serializable

REST API 호출을 위해 Retrofit, dio, Json serializable 을 사용할 수 있다.

## 의존성 추가

Retrofit 과 dio , json_serializable 및 annotation, code generator 등을 위한 의존성을 추가한다.

```bash
flutter pub add {package_names}
```

## 데이터 클래스 작성

### JSON Serializable 을 사용한  Data Class 작성

1. 의존성 추가
            
[json_serializable | Dart Package](https://pub.dev/packages/json_serializable/install)
            
```bash
flutter pub add json_serializable
```
            
2. Data Class 작성 

```dart
import 'package:json_annotation/json_annotation.dart';

part 'example.g.dart';

@JsonSerializable()
class Person {
  /// The generated code assumes these values exist in JSON.
  final String firstName, lastName;

  /// The generated code below handles if the corresponding JSON value doesn't
  /// exist or is empty.
  final DateTime? dateOfBirth;

  Person({required this.firstName, required this.lastName, this.dateOfBirth});

  /// Connect the generated [_$PersonFromJson] function to the `fromJson`
  /// factory.
  factory Person.fromJson(Map<String, dynamic> json) => _$PersonFromJson(json);

  /// Connect the generated [_$PersonToJson] function to the `toJson` method.
  Map<String, dynamic> toJson() => _$PersonToJson(this);
}
```
            
3. Build Runner 를 실행해서 Data Class ↔ Json 을 서로 바꿔주는 코드 생성
            
```powershell
dart run build_runner build
```
            
⇒ `build_runner` 가 없다는 에러가 발생하는 경우, 해당 패키지를 add 해줍니다.

```bash
flutter pub add build_runner
```
            
생성된 코드:
            
```dart
part of 'example.dart';

Person _$PersonFromJson(Map<String, dynamic> json) => Person(
      firstName: json['firstName'] as String,
      lastName: json['lastName'] as String,
      dateOfBirth: json['dateOfBirth'] == null
          ? null
          : DateTime.parse(json['dateOfBirth'] as String),
    );

Map<String, dynamic> _$PersonToJson(Person instance) => <String, dynamic>{
      'firstName': instance.firstName,
      'lastName': instance.lastName,
      'dateOfBirth': instance.dateOfBirth?.toIso8601String(),
    };
```
            
## Retrofit 인터페이스를 작성. 

### Retrofit 을 사용한 인터페이스 작성
1. 의존성 추가

[retrofit | Dart Package](https://pub.dev/packages/retrofit)
            
```bash
flutter pub add retrofit
```
            
2. API 인터페이스 작성

```dart
import 'package:dio/dio.dart';
import 'package:json_annotation/json_annotation.dart';
import 'package:retrofit/retrofit.dart';

part 'example.g.dart';

@RestApi(baseUrl: 'https://5d42a6e2bc64f90014a56ca0.mockapi.io/api/v1/')
abstract class RestClient {
  factory RestClient(Dio dio, {String baseUrl}) = _RestClient;

  @GET('/tasks')
  Future<List<Task>> getTasks();
}

@JsonSerializable()
class Task {
  const Task({this.id, this.name, this.avatar, this.createdAt});

  factory Task.fromJson(Map<String, dynamic> json) => _$TaskFromJson(json);

  final String? id;
  final String? name;
  final String? avatar;
  final String? createdAt;

  Map<String, dynamic> toJson() => _$TaskToJson(this);
}
```
⇒ Nested Model인 경우, **`@JsonSerializable(explicitToJson: true)`** 를 해주어 내부 모델에 대해서 자동적으로 toJson을 할 수 있도록 합니다. [(reference)](https://stackoverflow.com/questions/71961968/nested-json-serialise-with-custom-class)
            
3. Generator 실행
            
```powershell
# dart
dart pub run build_runner build
            
# flutter	
flutter pub run build_runner build
```

## 이후 작업
- Dio 인스턴스를 생성하고 헤더, Http 옵션 등을 설정.
- 정의한 인터페이스를 필요한 곳에서 사용.
