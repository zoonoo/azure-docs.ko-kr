

1. **QSTodoListViewController.m** 및 **viewDidLoad** 메소드에서 열고 다음 행을 제거합니다.

        [자체 새로 고침];

2.	**viewDidLoad** 메서드 바로 뒤에 다음 코드를 추가합니다.  

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;

            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    > [AZURE.NOTE] Facebook 이외의 ID 공급자를 사용하는 경우, **loginWithProvider**에 전달된 값을 변경합니다. 지원되는 값은 다음과 같습니다. _microsoftaccount_, _facebook_, _twitter_, _google_ 또는 _windowsazureactivedirectory_.

3. **실행**을 눌러 앱을 시작한 다음 선택한 ID 공급자를 사용하여 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.

<!--HONumber=49-->