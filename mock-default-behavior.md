---
description: MissingStubError の代わりに null を返す Mock を作りたいとき
---

# Error の代わりに null を返す Mock の作成

具体的な使い所が思いつかない程度に使用頻度が低いものですが、tracking など、仕様上影響はないためデフォルトの返り値が欲しいときに使うことがあるかもしれません。

## Recipe

### customMocks

以下のように定義し、`MockTrackerRelaxed` を使用します。

```dart
@GenerateMocks([
  Tracker,
], customMocks: [
  MockSpec<Tracker>(
    as: #MockTrackerRelaxed,
    returnNullOnMissingStub: true,
  )
])
void main() {
}
```



