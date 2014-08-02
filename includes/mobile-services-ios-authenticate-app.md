

1.  프로젝트 파일 QSTodoListViewController.m을 열고 **viewDidLoad** 메서드에서 테이블로 데이터를 다시 로드하는 다음 코드를 제거합니다.

         [self refresh];

2.  **viewDidLoad** 메서드 바로 뒤에 다음 코드를 추가합니다.

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

    **참고**

    Facebook 이외의 ID 공급자를 사용하는 경우 위의 **loginWithProvider**에 전달된 값을 다음 중 하나로 변경합니다. *microsoftaccount*, *facebook*, *twitter* 또는 *google*.

3.  **실행** 단추를 눌러 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작한 후 선택한 ID 공급자로 로그온합니다.

	로그인하고 나면 앱이 오류 없이 실행되며 모바일 서비스를 쿼리하고 데이터를 업데이트할 수 있게 됩니다.


