---
description: mockito の when で返り値を制御する際に渡された引数に応じて返り値を変えたいとき
---

# 引数に応じた返り値の変更

例えば以下のような場合に、`FindProfileUseCase#execute` から返される Profile を id に応じて変えたいとします。

```dart
class ProfileViewModel {
  ProfileViewModel({
    required FindProfileUseCase findProfileUseCase,
  }) : _findProfileUseCase = findProfileUseCase;

  final FindProfileUseCase _findProfileUseCase;

  Profile _profile = Profile.empty;

  int get id => _profile.id;

  void onIdChanged(int id) {
    _profile = _findProfileUseCase.execute(id: id);
  }
}

class Profile {
  const Profile({
    required this.id,
    required this.groupId,
    required this.name,
    required this.thumbnailUrl,
    required this.location,
  });

  ...
}

class FindProfileUseCase {
  Profile execute({required int id}) { ... }
}
```

## thenAnswer

`thenAnswer` を使用して `realInvocation` を見ることで引数を取得することができます。

```dart
test('test', () {
  when(findProfileUseCase.execute(id: anyNamed('id'))).thenAnswer(
    (realInvocation) => Profile(
      id: realInvocation.namedArguments[#id] as int,
      groupId: 0,
      name: 'name',
      thumbnailUrl: 'thumbnailUrl',
      location: 'location',
    ),
  );

  viewModel.onIdChanged(2);

  expect(viewModel.id, 2);
});
```

## Result

```dart
@GenerateMocks([
  FindProfileUseCase,
])
void main() {
  late MockFindProfileUseCase findProfileUseCase;
  late ProfileViewModel viewModel;

  setUp(() {
    findProfileUseCase = MockFindProfileUseCase();
    viewModel = ProfileViewModel(
      findProfileUseCase: findProfileUseCase,
    );
  });

  test('test', () {
    when(findProfileUseCase.execute(id: anyNamed('id'))).thenAnswer(
      (realInvocation) => Profile(
        id: realInvocation.namedArguments[#id] as int,
        groupId: 0,
        name: 'name',
        thumbnailUrl: 'thumbnailUrl',
        location: 'location',
      ),
    );

    viewModel.onIdChanged(2);

    expect(viewModel.id, 2);
  });
}
```

