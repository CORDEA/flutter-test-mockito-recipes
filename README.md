---
description: flutter_test + mockito recipes
---

# はじめに

これは flutter\_test や mockito を使用した Unit test を書く際、やりたいことに対してどのような書き方をすればいいのか分からず、結果として Unit test を苦手としてしまう人が多いのではないかと思い作るものです。

## 環境

```text
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, 2.2.1, on macOS 11.4 20F5055c darwin-x64, locale en-US)
[✓] Android toolchain - develop for Android devices (Android SDK version 30.0.2)
[✓] Xcode - develop for iOS and macOS
[✓] Chrome - develop for the web
[✓] Android Studio (version 4.2)
[✓] IntelliJ IDEA Ultimate Edition (version 2021.1.1)
[✓] VS Code (version 1.55.2)
[✓] Connected device (1 available)
```

```yaml
dev_dependencies:
  flutter_test:
    sdk: flutter
  mockito: ^5.0.9
```

