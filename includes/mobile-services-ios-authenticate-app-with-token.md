
위 예제에서는 앱을 시작할 때마다 ID 공급자와 모바일 서비스 모두에 연결합니다. 대신, 권한 부여 토큰을 캐시하여 이를 먼저 사용할 수 있습니다.

* iOS 클라이언트에서 인증 토큰을 암호화하고 저장할 때는 iOS Keychain을 사용하는 것이 좋습니다. [SSKeychain](https://github.com/soffes/sskeychain) -- iOS 키 집합에 대한 간단한 래퍼를 사용합니다. SSKeychain 페이지의 지시에 따라 프로젝트에 추가합니다. **모듈 사용** 설정이 프로젝트의 **빌드 설정**(**Apple LLVM - 언어 - 모듈** 섹션)에서 사용하도록 설정되었는지 확인합니다.

* **QSTodoListViewController.m**을 열고 다음 코드를 추가합니다.

```
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
```

* `loginAndGetData`에서 `loginWithProvider:controller:animated:completion:`의 완료 블록을 수정합니다. `[self refresh]` 바로 앞에 다음 줄을 추가하여 사용자 ID 및 토큰 속성을 저장합니다.

```
				[self saveAuthInfo];
```

* 앱이 시작되면 사용자 ID 및 토큰을 로드합니다. **QSTodoListViewController.m**의 `viewDidLoad`에서 `self.todoService`가 초기화되는 즉시 다음을 추가합니다.

```
				[self loadAuthInfo];
```

<!---HONumber=July15_HO2-->