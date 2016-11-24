**Objective-C**: 

1. Mac에서 Xcode를 사용하여 *QSTodoListViewController.m* 을 열고 다음 메서드를 추가합니다. Google을 ID 공급자로 사용하지 않는 경우 *google*을 *microsoftaccount*, *twitter*, *facebook* 또는 *windowsazureactivedirectory*로 변경합니다. Facebook을 사용하는 경우 [앱에서 Facebook 도메인을 허용 목록에 추가해야 합니다](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
   
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }
2. *QSTodoListViewController.m*에서 `viewDidLoad`의 `[self refresh]`을 다음으로 바꿉니다.
   
            [self loginAndGetData];
3. *Run* 을 눌러 앱을 시작한 다음 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.

**Swift**:

1. Mac의 Xcode에서 *ToDoTableViewController.swift*를 열고 다음 메서드를 추가합니다. Google을 ID 공급자로 사용하지 않는 경우 *google*을 *microsoftaccount*, *twitter*, *facebook* 또는 *windowsazureactivedirectory*로 변경합니다. Facebook을 사용하는 경우 [앱에서 Facebook 도메인을 허용 목록에 추가해야 합니다](https://developers.facebook.com/docs/ios/ios9#whitelist).
   
            func loginAndGetData() {
   
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
   
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }
2. *ToDoTableViewController.swift*에 있는 `viewDidLoad()`의 끝에서 `self.refreshControl?.beginRefreshing()` 및 `self.onRefresh(self.refreshControl)` 줄을 제거합니다. 그 자리에 `loginAndGetData()` 에 대한 호출을 추가합니다.
   
            loginAndGetData()
3. *Run* 을 눌러 앱을 시작한 다음 로그인합니다. 로그인할 때 할 일 목록을 보고 업데이트할 수 있어야 합니다.



<!--HONumber=Nov16_HO3-->


