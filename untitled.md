---
description: Stream に流れる値を検証したいとき
---

# Stream に流れる値の検証

とりあえず以下のような class があったとして、body の値を確認したいとします。

```dart
class ProfileViewModel {
  final StreamController<String> _body = StreamController.broadcast();

  Stream<String> get body => _body.stream;

  void onBodyEdited(String body) {
    _body.add(body);
  }
}
```

RxDart の `Subject` を使うような場合でも基本的には同じ方法を取ることが出来ます。

いくつか方法があります。

### expectLater

```dart
test('test', () {
  final viewModel = ProfileViewModel();

  expectLater(viewModel.body, emits('body'));

  viewModel.onBodyEdited('body');
});
```

複数の値が add される場合には `emitsInOrder`, `emitsInAnyOrder` が使用できます。

```dart
test('test', () {
  final viewModel = ProfileViewModel();

  expectLater(
    viewModel.body,
    emitsInOrder([
      emits('body'),
      emits('body2'),
    ]),
  );

  viewModel.onBodyEdited('body');
  viewModel.onBodyEdited('body2');
});
```

### expectAsync

```dart
test('test', () {
  final viewModel = ProfileViewModel();

  viewModel.body.listen(expectAsync1((v) {
    expect(v, 'body');
  }));

  viewModel.onBodyEdited('body');
});
```

複数回 add される場合は count を指定することが出来ます。

```dart
  viewModel.body.listen(expectAsync1((v) {
    expect(v, 'body');
  }, count: 2));
```



もちろん `Completer` などを使用して `expectLater` などを使わずに自分で書くことも出来ます。

