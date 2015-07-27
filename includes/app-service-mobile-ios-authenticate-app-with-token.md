
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 앱 서비스 둘 다에 접근해야 하는 표준 로그인을 보여줍니다. 이 메서드는 비효율적이며 앱 서비스에서 반환된 권한 부여 토큰을 캐시하고 공급자 기반 로그인을 사용하기 전에 먼저 사용하는 것이 좋습니다.

1. iOS 클라이언트에서 인증 토큰을 암호화하고 저장할 때는 iOS Keychain을 사용하는 것이 좋습니다. 이 자습서에서는 iOS Keychain에 대한 간단한 래퍼인 [SSKeychain](https://github.com/soffes/sskeychain)을 사용합니다. SSKeychain 페이지의 지시에 따라 프로젝트에 추가합니다. **모듈 사용** 설정이 프로젝트의 **빌드 설정**(**Apple LLVM - 언어 - 모듈** 섹션)에서 사용하도록 설정되었는지 확인합니다.

2. **QSTodoListViewController.m**을 열고 다음 코드를 추가합니다.


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. `loginAndGetData` 메서드에서 `[self refresh]` 줄 바로 앞 `saveAuthInfo`에 호출을 추가하여 `loginWithProvider:controller:animated:completion:` 호출의 완료 블록을 수정합니다. 이 호출을 통해 사용자 ID 및 토큰 속성을 간단하게 저장합니다.

				[self saveAuthInfo];

4. 앱이 시작되면 사용자 ID 및 토큰도 로드합니다. **QSTodoListViewController.m**의 `viewDidLoad` 메서드에서 `self.todoService`이(가) 초기화된 후 바로 loadAuthInfo 호출을 추가합니다.

				[self loadAuthInfo];

<!---HONumber=July15_HO3-->