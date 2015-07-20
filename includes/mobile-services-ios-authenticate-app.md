* **QSTodoListViewController.m**을 열고 다음 메서드를 추가합니다. Facebook을 ID 공급자로 사용하지 않는 경우 _facebook_을 _microsoftaccount_, _twitter_, _google_ 또는 _windowsazureactivedirectory_로 변경합니다.

```
        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }
```

* `viewDidLoad`의 `[self refresh]`를 다음으로 바꿉니다.

```
        [self loginAndGetData];
```

* **Run**을 눌러 앱을 시작한 다음 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.

<!---HONumber=July15_HO2-->