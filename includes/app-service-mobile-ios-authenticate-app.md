

1. **QSTodoListViewController.m**을 열고 다음 메서드를 추가합니다.


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


    > [AZURE.NOTE]Facebook 이외의 ID 공급자를 사용하는 경우 **loginWithProvider**에 전달된 값을 변경합니다. 지원되는 값은 다음과 같습니다. _microsoftaccount_, _facebook_, _twitter_, _google_ 또는 _windowsazureactivedirectory_.


2. 끝에 있는 `[self refresh]`을(를) 다음으로 바꿔 `viewDidLoad`을(를) 수정합니다.

        [self loginAndGetData];

3. **Run**을 눌러 앱을 시작한 다음 선택한 ID 공급자를 사용하여 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.

<!---HONumber=July15_HO4-->