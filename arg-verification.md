---
description: 引数について検証したいとき
---

# 引数の検証

例えば以下のような class があり、`UpdateProfileUseCase` に渡された profile が正しいか test したいとします。

```dart
class ProfileViewModel {
  ProfileViewModel({
    required Profile profile,
    required UpdateProfileUseCase updateProfileUseCase,
  })  : _profile = profile,
        _updateProfileUseCase = updateProfileUseCase;

  Profile _profile;
  final UpdateProfileUseCase _updateProfileUseCase;

  void onNameChanged(String name) {
    _profile = _profile.copyWith(name: name);
  }

  void onSubmitTapped() {
    _updateProfileUseCase.execute(_profile);
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

class UpdateProfileUseCase {
  void execute(Profile profile) {}
}
```

引数の検証としては主に 2 つ方法があります。

## Recipe

### argThat

`argThat` を使用することで `Matcher` を渡すことができます。

```dart
test('test', () {
  viewModel.onNameChanged('name');

  viewModel.onSubmitTapped();

  verify(updateProfileUseCase.execute(argThat(isNotNull)));
});
```

{% hint style="info" %}
任意の条件を指定したい場合は `predicate` が使えます。

```dart
test('test', () {
  viewModel.onNameChanged('name');

  viewModel.onSubmitTapped();

  verify(updateProfileUseCase
      .execute(argThat(predicate<Profile>((v) => v.name == 'name'))));
});
```
{% endhint %}

### captureAny

`captureAny` によって引数を取得することができます。

```dart
test('test', () {
  viewModel.onNameChanged('name');

  viewModel.onSubmitTapped();

  final profile =
      verify(updateProfileUseCase.execute(captureAny)).captured.first;

  expect(profile.name, 'name');
});
```

## Result

```dart
@GenerateMocks([
  UpdateProfileUseCase,
])
void main() {
  late MockUpdateProfileUseCase updateProfileUseCase;
  late ProfileViewModel viewModel;

  setUp(() {
    updateProfileUseCase = MockUpdateProfileUseCase();
    viewModel = ProfileViewModel(
      profile: Profile(
        id: 0,
        groupId: 0,
        name: '',
        thumbnailUrl: '',
        location: '',
      ),
      updateProfileUseCase: updateProfileUseCase,
    );
  });

  test('test', () {
    viewModel.onNameChanged('name');
  
    viewModel.onSubmitTapped();
  
    verify(updateProfileUseCase.execute(argThat(isNotNull)));
  });
  
  test('test', () {
    viewModel.onNameChanged('name');
  
    viewModel.onSubmitTapped();
  
    verify(updateProfileUseCase
        .execute(argThat(predicate<Profile>((v) => v.name == 'name'))));
  });
  
  test('test', () {
    viewModel.onNameChanged('name');
  
    viewModel.onSubmitTapped();
  
    final profile =
        verify(updateProfileUseCase.execute(captureAny)).captured.first;
  
    expect(profile.name, 'name');
  });
}
```

