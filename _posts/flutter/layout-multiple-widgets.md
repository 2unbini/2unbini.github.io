---
title: "Flutter | Layout Multiple Widgets - List, Grid"
toc: true
toc_sticky: true
categories:
    - 📂 all
    - flutter
tags:
    - flutter
    - 플러터
last_modified_at: 2023-10-11
---

# Layout Multiple Widgets - List, Grid

<aside>
📖 Reference

</aside>

[Layouts in Flutter](https://docs.flutter.dev/ui/layout)

[Lists & grids](https://docs.flutter.dev/ui/layout/lists)


# Widget

Flutter의 중심 레이아웃 매커니즘은 `widget` 이다.

거의 모든 UI 요소들은 `widget` 이며, 각 위젯을 결합해 복잡한 widget을 생성할 수 있다.

## build()

Flutter 앱에서 위젯을 생성하기 위해선 `build` 메소드를 사용해야 한다.

```dart
@override
Widget build(BuildContext context) {
	return Widget(...)
}
```

### Material / Non-Material App

[Material Design](https://m3.material.io/styles)을 따르는 위젯들은 Flutter의 `Material Library` 에 정의되어있다.

Material app은 색상, 스타일 등 기본적으로 디자인이 되어 있는 것들을 말하고, Non-Material app은 커스터마이징을 더 자유롭게 할 수 있는 것들을 말한다.

즉, UI 커스터마이징을 원한다면 `Non-Material` 을, 기본적으로 스타일이 고정되어있는(만들어져있는) UI 컴포넌트를 원한다면 `Material` 을 사용한다.

기본적으로 initial project를 생성했을 때 작성된 `import ['package:flutter/material.dart'](https://api.flutter.dev/flutter/widgets/widgets-library.html);` 을 통해 `MateralApp` 을 불러와 사용할 수 있다.

# Layout Multiple Widgets

## Vertically / Horizontally
![4](https://github.com/2unbini/2unbini.github.io/assets/75126613/00ee6ca9-418d-41f7-81f8-068998af8265)


복수의 위젯을 수직/수평으로 위치시키는 방식으로 `[Column](https://api.flutter.dev/flutter/widgets/Column-class.html)`, `[Row](https://api.flutter.dev/flutter/widgets/Row-class.html)` 를 많이 사용한다.

Column, Row 위젯은 복수의 위젯을 커스터마이징하기 좋은 위젯이지만, 때로는 `[ListTile](https://api.flutter.dev/flutter/material/ListTile-class.html)` 혹은 `[ListView](https://api.flutter.dev/flutter/widgets/ListView-class.html)` 처럼 leading/trailing 아이콘을 손쉽게 삽입할 수 있고, 자동적으로 스크롤이 가능한 위젯을 사용하는게 더 효과적일 수 있다.

## Alignment
<img width="661" alt="Screenshot 2023-11-23 at 8 16 49 PM" src="https://github.com/2unbini/2unbini.github.io/assets/75126613/f56e950c-851e-4779-90ff-f2d688f58e65">

Row와 Column의 자식 위젯들을 정렬하고 싶을 때 `Axis` 를 설정해준다.

`Main` 과 `Cross` Axis가 있는데, Row와 Column의 각 속성(Horizontal, Vertical)에 따른다.

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  children: [
    Image.asset('images/pic1.jpg'),
    Image.asset('images/pic2.jpg'),
    Image.asset('images/pic3.jpg'),
  ],
);

Column(
  mainAxisAlignment: MainAxisAlignment.center,
  children: [
    Image.asset('images/pic1.jpg'),
    Image.asset('images/pic2.jpg'),
    Image.asset('images/pic3.jpg'),
  ],
);
```

# **Common layout widgets**

## Standard widgets

- [Container](https://docs.flutter.dev/ui/layout#container): padding, margins, borders, background color 등 위젯에 꾸밈 요소를 추가할 수 있다.
- [GridView](https://docs.flutter.dev/ui/layout#gridview): 스크롤 가능한 그리드 형태로 위젯을 나열한다.
- [ListView](https://docs.flutter.dev/ui/layout#listview): 스크롤 가능한 리스트 형태로 위젯을 나열한다.
- [Stack](https://docs.flutter.dev/ui/layout#stack): 위젯을 스택처럼 위에 쌓을 수 있다.

## Material widgets

- [Card](https://docs.flutter.dev/ui/layout#card): rounded corner와 drop shadow를 가진 박스로 관련된 정보를 정렬할 수 있다.
- [ListTile](https://docs.flutter.dev/ui/layout#listtile): 하나의 행에 leading/trailing 아이콘을 넣을 수 있고, 3줄 이상의 텍스트를 정렬할 수 있다.

# GridView

- **스크롤 가능한 2차원 리스트**를 만들고자 할 때는 GridView를 사용한다.
- GridView 안의 content가 너무 길면 자동으로 스크롤 가능하게 렌더링된다.
- 2차원 리스트를 만들 때, 각 행과 열에 어떤 내용이 들어가야 하는지 식별되어야 한다면, [Table](https://api.flutter.dev/flutter/widgets/Table-class.html) 이나 [DataTable](https://api.flutter.dev/flutter/material/DataTable-class.html) 을 사용하는 편이 좋다.

## Grid

커스텀 그리드를 만들거나, Flutter에서 제공하는 두 가지 기본 옵션을 사용한다.

### GridView.count

하나의 행에 몇 개의 열이 들어갈지 그 개수를 입력한다.

- code
    
    ```dart
    ...
    @override
      Widget build(BuildContext context) {
        return Scaffold(
          appBar: AppBar(
            automaticallyImplyLeading: false,
            title: Text(GalleryLocalizations.of(context)!.demoGridListsTitle),
          ),
          body: **GridView.count(**
            restorationId: 'grid_view_demo_grid_offset',
            **crossAxisCount: 2,**
            mainAxisSpacing: 8,
            crossAxisSpacing: 8,
            padding: const EdgeInsets.all(8),
            childAspectRatio: 1,
            children: _photos(context).map<Widget>((photo) {
              return _GridDemoPhotoItem(
                photo: photo,
                tileStyle: type,
              );
            }).toList(),
          ),
        );
      }
    }
    ```


### GridView.extent

타일의 max pixel width를 정해 그에 따라 개수가 자동적으로 정해진다.

- code
    
    ```dart
    Widget _buildGrid() => **GridView.extent(**
        **maxCrossAxisExtent: 150,**
        padding: const EdgeInsets.all(4),
        mainAxisSpacing: 4,
        crossAxisSpacing: 4,
        children: _buildGridTileList(30));
    
    // The images are saved with names pic0.jpg, pic1.jpg...pic29.jpg.
    // The List.generate() constructor allows an easy way to create
    // a list when objects have a predictable naming pattern.
    List<Container> _buildGridTileList(int count) => List.generate(
        count, (i) => Container(child: Image.asset('images/pic$i.jpg')));
    ```
    

# ListView

- 박스에 대한 리스트를 정리하는 특화된 [Column](https://api.flutter.dev/flutter/widgets/Column-class.html)
- 수직/수평 방향 모두 스크롤이 가능하다.
- GridView와 마찬가지로 컨텐츠가 rederbox를 벗어나면 자동 스크롤을 지원한다.
- Column에 비해 커스텀할 수 있는 것들이 적지만 그만큼 쉽게 사용할 수 있고, 스크롤이 가능하다는 장점이 있다.
- leading, trailing 아이콘과 텍스트를 설정할 수 있는 ListTile을 이용하여 많이 구성된다.

- code
    
    ```dart
    Widget _buildList() {
      return ListView(
        children: [
          _tile('CineArts at the Empire', '85 W Portal Ave', Icons.theaters),
          _tile('The Castro Theater', '429 Castro St', Icons.theaters),
          _tile('Alamo Drafthouse Cinema', '2550 Mission St', Icons.theaters),
          _tile('Roxie Theater', '3117 16th St', Icons.theaters),
          _tile('United Artists Stonestown Twin', '501 Buckingham Way',
              Icons.theaters),
          _tile('AMC Metreon 16', '135 4th St #3000', Icons.theaters),
          const Divider(),
          _tile('K\'s Kitchen', '757 Monterey Blvd', Icons.restaurant),
          _tile('Emmy\'s Restaurant', '1923 Ocean Ave', Icons.restaurant),
          _tile(
              'Chaiya Thai Restaurant', '272 Claremont Blvd', Icons.restaurant),
          _tile('La Ciccia', '291 30th St', Icons.restaurant),
        ],
      );
    }
    
    ListTile _tile(String title, String subtitle, IconData icon) {
      return ListTile(
        title: Text(title,
            style: const TextStyle(
              fontWeight: FontWeight.w500,
              fontSize: 20,
            )),
        subtitle: Text(subtitle),
        leading: Icon(
          icon,
          color: Colors.blue[500],
        ),
      );
    }
    ```
    

## ListView는 ScrollView + Column + Row?

결국 Row의 정형화된 버전인 ListTile, 스크롤 가능한 Column의 정형화된 버전이 ListView인 셈인데… ScrollView { Column { Row… } } 이런 식으로 래핑해 쓰는 것보다 어떤 점이 좋을까?

실제로 SingleChildScrollView에 Column을 추가해서 사용하는 것과 ListView의 차이를 물어보는 글들이 많다.

### ListView를 구성하는 네가지 옵션

[ListView](https://api.flutter.dev/flutter/widgets/ListView-class.html)를 구성하는 옵션에는 네가지가 있다.

`default` constructor, `ListView.builder` constructor, `ListView.separated` constructor, `ListView.custom` constructor.

이 중 `**ListView.builder**` 는 엄청 많은 리스트(혹은 무한 스크롤)를 생성하는데 적합하다. 실제로 보여지는 자식 위젯들만 호출하기 때문이다.

### ListView.builder

무한 스크롤을 구현해야 하거나, 표시해야 하는 요소의 개수가 많을 땐 해당 constructor를 사용하는 것이 권장된다. 적은 양의 리스트를 구현할 때엔 default constructor를 사용하여 만들어도 큰 차이가 없는 것 같다.

결국 ScrollView + Column 콤보보다 ListView를 추천하는 사람들이 많은데, 그 근거로 ListView.builder가 lazily하게 생성된다는 것을 든다. 적은 양의 리스트를 표시할 땐 전자나 후자나 큰 차이는 없을 것 같다. 보여지는 요소 뿐만 아니라 리스트의 모든 요소를 전부 화면에 그리기 때문이다.
