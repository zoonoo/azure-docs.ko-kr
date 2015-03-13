
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 모바일 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 모바일 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다. 

>[AZURE.NOTE] 클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 모바일 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.

1. iOS 클라이언트에서 인증 암호를 암호화하고 저장할 때는 Keychain을 사용하는 것이 좋습니다. 이렇게 하려면 KeychainWrapper 클래스를 만들어 [KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) 및 [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h)를 [LensRocket sample](https://github.com/WindowsAzure-Samples/iOS-LensRocket)에서 복사합니다. 이 KeychainWrapper를 사용하는 이유는 Apple의 설명서에 정의된 KeychainWrapper는 ARC(Automatic Reference Counting)를 지원하지 않기 때문입니다.

2. 프로젝트 파일 **QSTodoListViewController.m**을 열고 다음 코드를 추가합니다.
		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		
3. **QSTodoListViewController.m**의 **viewDidAppear** 메서드 끝에 saveAuthInfo에 대한 호출을 추가합니다. 이 호출을 사용하면 userId 및 token 속성을 쉽게 저장할 수 있습니다.  

		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}
  
4. 지금까지 사용자 토큰 및 ID를 캐시하는 방법을 살펴보았으므로 이제 앱이 시작될 때 이를 로드하는 방법을 알아보겠습니다. **self.todoService**가 초기화된 후 **QSTodoListViewController.m**의 **viewDidLoad** 메서드에서 loadAuthInfo에 대한 호출을 추가합니다. 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. 앱에서 모바일 서비스에 사용자가 인증되었으므로 전달되어야 하는 요청을 보낸 경우 401 응답(권한 없음 오류)이 반환되면 이는 전달한 사용자 토큰이 만료되었음을 의미합니다. 모바일 서비스와 상호 작용하는 모든 메서드에 대한 완료 처리기에서 401 응답을 확인하거나 모든 작업을 한 곳, 즉 MSFilter의 handleRequest 메서드에서 처리할 수 있습니다.  이 시나리오를 처리하는 방법에 대한 자세한 내용은 [이 블로그 게시물](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)을 참조하세요.

<!--HONumber=42-->
