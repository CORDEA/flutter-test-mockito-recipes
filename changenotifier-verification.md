---
description: ChangeNotifier を使用した場合に、値の更新を待って検証したいとき
---

# ChangeNotifier で更新される値の検証

以下のような class で値を検証したいとします。

```dart
class ProfileViewModel extends ChangeNotifier {
  ProfileViewModel(this._updateProfileBodyUseCase);

  final UpdateProfileBodyUseCase _updateProfileBodyUseCase;

  String _body = '';

  String get body => _body;

  void onBodyEdited(String body) {
    _updateProfileBodyUseCase.execute(body: body).then((value) {
      _body = value;
      notifyListeners();
    });
  }
}

class UpdateProfileBodyUseCase {
  Future<String> execute({required String body}) { ... }
}

```

`onBodyEdited` が `Future` であれば待って検証することができますが、この場合、`#execute` から`Future.value` を返してもそれより先に検証されてしまうため、待たなければ test が通りません。

## Recipe

これは私の知る限りでは決まった方法があるわけではないため、`Completer` での対処を一例として記載しておきます。

```dart
test('test', () async {
  when(updateProfileBodyUseCase.execute(body: anyNamed('body')))
      .thenAnswer((_) => Future.value('body'));

  final completer = Completer<void>();
  viewModel.addListener(completer.complete);

  viewModel.onBodyEdited('body');

  await completer.future;
  expect(viewModel.body, 'body');
});
```

## Result

```dart
@GenerateMocks([
  UpdateProfileBodyUseCase,
])
void main() {
  late MockUpdateProfileBodyUseCase updateProfileBodyUseCase;
  late ProfileViewModel viewModel;

  setUp(() {
    updateProfileBodyUseCase = MockUpdateProfileBodyUseCase();
    viewModel = ProfileViewModel(updateProfileBodyUseCase);
  });

  test('test', () async {
    when(updateProfileBodyUseCase.execute(body: anyNamed('body')))
        .thenAnswer((_) => Future.value('body'));

    final completer = Completer<void>();
    viewModel.addListener(completer.complete);

    viewModel.onBodyEdited('body');

    await completer.future;
    expect(viewModel.body, 'body');
  });
}
```

