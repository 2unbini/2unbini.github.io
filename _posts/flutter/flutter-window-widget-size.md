---
title: "Flutter | Window와 Widget Size"
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
last_modified_at: 2023-10-06
---

# Window와 Widget Size

<aside>
💡 chrome 윈도우의 minimum size와 macOS App 윈도우의 minimum size가 다르다.
하지만 Widget size는 동일해 후자에선 overflow error가 발생한다.

</aside>

### Method Channel

[Writing custom platform-specific code](https://docs.flutter.dev/platform-integration/platform-channels)

- window size와 같은 platform-specific한 API 사용은 `method channel`로 할 수 있다.
- 각 플랫폼에 맞는 코드를 작성하고, Flutter project에서 `MethodChannel` 클래스를 생성해 접근하면 된다.

macOS App의 윈도우 사이즈가 달라 오버플로우 에러가 나는 등의 상황은 다음과 같이 해결할 수 있다.

- sample code
    
    ```dart
    // In Flutter Project... (VSCode)
    
    import 'dart:io' show Platform;
    import 'package:flutter/material.dart';
    import 'package:flutter/services.dart';
    
    void main() {
      WidgetsFlutterBinding.ensureInitialized();
    
      if (Platform.isMacOS) {
        // Set macOS window size
        setMacOSWindowSize(400, 300); // Replace with your desired size
      }
    
      runApp(MyApp());
    }
    
    // Function to set macOS window size
    void setMacOSWindowSize(double width, double height) {
      const channel = MethodChannel('set_window_size');
      channel.invokeMethod('setWindowSize', {
        'width': width,
        'height': height,
      });
    }
    
    class MyApp extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return MaterialApp(
          title: 'Window Sized App',
          home: Scaffold(
            appBar: AppBar(
              title: Text('Window Sized App'),
            ),
            body: Center(
              child: Container(
                width: 400, // Width of your desired widget size
                height: 300, // Height of your desired widget size
                color: Colors.blue,
                child: Center(
                  child: Text(
                    'Match Window Size',
                    style: TextStyle(color: Colors.white),
                  ),
                ),
              ),
            ),
          ),
        );
      }
    }
    ```
    
    ```swift
    // In iOS Project... (Xcode)
    
    import Cocoa
    import FlutterMacOS
    
    @main
    class AppDelegate: FlutterAppDelegate {
      override func applicationDidFinishLaunching(_ aNotification: Notification) {
        let controller = window!.rootViewController as! FlutterViewController
    
        let channel = FlutterMethodChannel(name: "set_window_size", binaryMessenger: controller.binaryMessenger)
        channel.setMethodCallHandler { (call: FlutterMethodCall, result: @escaping FlutterResult) in
          if call.method == "setWindowSize" {
            if let args = call.arguments as? Dictionary<String, Any>,
               let width = args["width"] as? Double,
               let height = args["height"] as? Double {
              // Set macOS window size here
              self.window!.setContentSize(NSSize(width: width, height: height))
            }
          }
        }
    
        super.applicationDidFinishLaunching(aNotification)
      }
    }
    ```
    

### MediaQuery

[MediaQuery class - widgets library - Dart API](https://api.flutter.dev/flutter/widgets/MediaQuery-class.html)

- 현재 media에 대한 데이터를 얻는 방법으로 `MediaQuery` 클래스를 생성할 수 있다.
- `MediaQuery.sizeOf(context)` 와 같이 사용하면 현재 media에 대한 크기를 알 수 있다.
