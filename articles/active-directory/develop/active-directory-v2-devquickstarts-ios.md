---
title: Azure AD v 2.0 끝점을 사용하여 iOS 응용 프로그램에 로그인 추가 | Microsoft Docs
description: 타사 라이브러리를 사용하여 개인 Microsoft 계정과 회사 또는 학교 계정 둘 다로 사용자를 로그인하는 iOS 앱을 빌드하는 방법입니다.
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7476417e6585976ea2404a83602a6d9aa77d9c7a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2018
---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>v2.0 끝점을 사용하는 Graph API와 함께 타사 라이브러리를 사용하여 iOS 앱에 로그인 추가
Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 개발자는 서비스와 통합하려는 모든 라이브러리를 사용할 수 있습니다. 개발자가 플랫폼을 다른 라이브러리와 함께 사용할 수 있도록 돕기 위해, 타사 라이브러리를 Microsoft ID 플랫폼에 연결하도록 구성하는 방법을 설명하는 이와 같은 연습 몇 가지를 작성했습니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749) 을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.

이 연습에서 만드는 응용 프로그램을 사용하여 해당 조직에 로그인한 다음 Graph API를 사용하여 조직에서 다른 사용자를 검색할 수 있습니다.

OAuth2 또는 OpenID Connect를 처음 접하는 경우 이 샘플 구성 대부분이 잘 이해되지 않을 수도 있습니다. 배경 지식을 위해 [v2.0 프로토콜 - OAuth 2.0 권한 부여 코드 흐름](active-directory-v2-protocols-oauth-code.md)을 읽어보는 것이 좋습니다.

> [!NOTE]
> 조건부 액세스 및 Intune 정책 관리 등과 같은 OAuth2 또는 OpenID Connect 표준의 식을 사용하는 플랫폼의 일부 기능은 수행하려면 오픈 소스인 Microsoft Azure ID 라이브러리를 사용해야 합니다.
> 
> 

v2.0 끝점에서는 일부 Azure Active Directory 시나리오 및 기능만 지원합니다.

> [!NOTE]
> v2.0 끝점을 사용해야 하는지 확인하려면 [v2.0 제한 사항](active-directory-v2-limitations.md)을 참조하세요.
> 
> 

## <a name="download-code-from-github"></a>GitHub에서 코드 다운로드
이 자습서에 대한 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱 구조를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) 하거나 구조를 복제할 수 있습니다.

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

샘플을 다운로드할 수도 있고 지금 바로 시작할 수도 있습니다.

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>앱 등록
[응용 프로그램 등록 포털](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)에서 새 앱을 만들거나 [v2.0 끝점을 사용하여 앱을 등록하는 방법](active-directory-v2-app-registration.md)의 자세한 단계를 따르세요. 다음을 수행해야 합니다.

* 앱에 할당된 **응용 프로그램 ID** 는 곧 필요하므로 적어둡니다.
* 앱용 **Mobile** 플랫폼을 추가합니다.
* 포털에서 **리디렉션 URI** 를 복사합니다. `urn:ietf:wg:oauth:2.0:oob`의 기본값을 사용해야 합니다.

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>타사 NXOAuth2 라이브러리 다운로드 및 작업 영역 만들기
이 연습에서는 Mac OS X 및 iOS(Cocoa 및 Cocoa touch)에 대한 OAuth2 라이브러리인 GitHub의 OAuth2Client를 사용합니다. 이 라이브러리는 OAuth2 사양의 초안 10에 기반을 둡니다. 네이티브 응용 프로그램 프로필을 구현하고 사용자의 권한 부여 끝점을 지원합니다. 이것이 바로 Microsoft ID 플랫폼과 통합하기 위해 필요한 모든 것입니다.

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>CocoaPods를 사용하여 프로젝트에 라이브러리 추가하기
CocoaPods는 Xcode 프로젝트에 대한 종속성 관리자입니다. 이전 설치 단계를 자동으로 관리합니다.

```
$ vi Podfile
```
1. 이 podfile에 다음을 추가합니다.
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. CocoaPods를 사용하여 podfile를 로드합니다. 로드하려는 새 XCode 작업 영역을 만듭니다.
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>프로젝트의 구조 탐색
프로젝트의 기본 골격 구조는 다음과 같이 설정되어 있습니다.

* UPN 검색으로 마스터 보기
* 선택한 사용자에 관한 데이터 세부 정보 보기
* 사용자가 앱에 로그인하여 그래프를 쿼리할 수 있도록 하는 로그인 뷰

인증 추가를 위해 골격 구조의 다양한 파일로 이동합니다. 시각적 코드와 같은 코드의 다른 부분은 ID와 밀접한 관련이 없으나 사용자에게 제공됩니다.

## <a name="set-up-the-settingsplst-file-in-the-library"></a>라이브러리의 settings.plst 파일 설정
* 빠른 시작 프로젝트에서 `settings.plist` 파일을 엽니다. Azure 포털에 사용한 값을 반영하도록 섹션의 요소 값을 바꿉니다. 코드는 Active Directory 인증 라이브러리를 사용할 때마다 이러한 값을 참조합니다.
  * `clientId` 는 포털에서 복사한 응용 프로그램의 클라이언트 ID입니다.
  * `redirectUri` 는 포털에서 제공한 리디렉션 URL입니다.

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>LoginViewController의 NXOAuth2Client 라이브러리 설정
NXOAuth2Client 라이브러리는 시작하기 위해 일부 값을 필요로 합니다. 해당 작업을 마친 후 획득한 토큰을 사용하여 Graph API를 호출할 수 있습니다. `LoginView` 는 인증이 필요할 때마다 호출되므로 해당 파일에 구성 값을 입력하는 것이 좋습니다.

* `LoginViewController.m` 파일에 일부 값을 추가하여 인증 및 권한 부여에 대한 컨텍스트를 설정해 보겠습니다. 코드 다음에는 값에 대한 세부 정보가 나옵니다.
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

코드에 대한 세부 정보를 살펴보겠습니다.

첫 번째 문자열은 `scopes`에 대한 것입니다. `User.Read` 값을 사용하여 로그인한 사용자의 기본 프로필을 읽을 수 있습니다.

[Microsoft Graph 권한 범위](https://graph.microsoft.io/docs/authorization/permission_scopes)에서 사용 가능한 모든 범위에 대해 알아볼 수 있습니다.

`authURL`, `loginURL`, `bhh` 및 `tokenURL`에서는 앞서 제공된 값을 사용해야 합니다. 오픈 소스 Microsoft Azure Identity 라이브러리를 사용하는 경우 메타데이터 끝점을 사용하여 이 데이터를 가져옵니다. 사용자를 위해 이러한 값을 추출하는 어려운 작업을 마쳤습니다.

`keychain` 값은 NXOAuth2Client 라이브러리가 토큰을 저장하기 위해 키 집합을 만드는데 사용할 컨테이너입니다. 다양한 앱에서 SSO(Single Sign-On)를 가져오려 한다면 각 응용 프로그램에서 동일한 키 집합을 지정하는 것은 물론 Xcode 자격에서 그 키 집합의 사용을 요청할 수 있습니다. 이 내용은 Apple 설명서에서 다룹니다.

나머지 이런 값들은 라이브러리를 사용하여 값을 컨텍스트로 옮길 위치를 만드는 것이 필요합니다.

### <a name="create-a-url-cache"></a>URL 캐시 만들기
항상 뷰를 로드한 후에 호출되는 `(void)viewDidLoad()`내부에서 다음 코드는 캐시를 사용할 수 있게 준비합니다.

다음 코드를 추가합니다.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>로그인을 위한 WebView 만들기
WebView는 사용자에게 SMS 텍스트 메시지(구성된 경우)와 같은 추가 요소에 대한 메시지를 표시하거나 사용자에게 오류 메시지를 반환할 수 있습니다. 여기서 WebView를 설정한 후에 ID 서비스의 WebView에서 발생할 콜백을 처리할 코드를 작성합니다.

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>인증을 처리하는 WebView 메서드 재정의
사용자가 이전에 설명한 대로 로그인해야 할 때 발생하는 상황을 WebView에 알리려면 다음 코드를 붙여넣을 수 있습니다.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>OAuth2 요청의 결과를 처리할 코드를 작성합니다.
다음 코드는 WebView에서 반환되는 redirectURL을 처리합니다. 인증이 실패하면 코드는 재시도합니다. 한편 라이브러리는 콘솔에서 보거나 비동기식으로 처리할 수 있는 오류를 제공합니다.

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>OAuth 컨텍스트 설정(호출된 계정 저장소)
응용 프로그램에서 액세스할 수 있게 하려는 각 서비스에 대한 공유 계정 저장소의 `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` 을 호출할 수 있습니다. 계정 유형은 특정 서비스에 대한 식별자로 사용되는 문자열입니다. Graph API에 액세스하게 되므로 코드는 `"myGraphService"`로 참조합니다. 그런 다음 토큰과 함께 뭔가가 변경될 때 알려주도록 관찰자를 설정합니다. 토큰을 가져온 후에는 사용자를 `masterView`로 다시 반환합니다.

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Graph API에서 사용자를 검색 및 표시하도록 MasterView 설정
눈금에 반환된 데이터를 표시하는 MVC(Master-View-Controller) 앱은 이 연습에서 다루지 않으며 다양한 온라인 자습서에 해당 빌드 방법이 설명되어 있습니다. 이러한 모든 코드는 기본 골격 파일입니다. 그러나 이 MVC 응용 프로그램에서 몇 가지를 다룰 필요가 있습니다.

* 사용자가 뭔가를 검색 필드에 입력할 때 가로채기
* 결과를 눈금에 표시할 수 있도록 데이터의 개체를 MasterView에 다시 제공합니다.

이를 아래와 같이 수행할 것입니다.

### <a name="add-a-check-to-see-if-youre-logged-in"></a>로그인되었는지 보기 위한 확인란 추가
사용자가 로그인하지 않으면 응용 프로그램이 하는 일이 거의 없기 때문에 캐시에 토큰이 이미 있는지 확인하는 것이 현명합니다. 그렇지 않은 경우 사용자가 로그인하도록 LoginView로 리디렉션합니다. 다시 말해서 뷰가 로드될 때 작업을 수행할 가장 좋은 방법은 Apple이 제공한 `viewDidLoad()` 메서드를 사용하는 것입니다.

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>데이터를 수신할 때 Table View 업데이트
Graph API에서 데이터를 반환할 때 해당 데이터를 표시해야 합니다. 편의상 여기에 테이블을 업데이트하기 위한 모든 코드가 있습니다. 오른쪽 값을 MVC 상용구 코드에 붙여넣을 수 있습니다.

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>누군가가 검색 필드에 입력할 때 Graph API를 호출하는 방법을 제공합니다.
사용자가 검색 상자에 입력할 때 입력된 내용을 Graph API로 넣을 필요가 있습니다. 다음 코드에서 빌드하게 되는 `GraphAPICaller` 클래스는 프레젠테이션에서 조회 기능을 분리합니다. 이제, Graph API에 검색 문자를 공급하는 코드를 작성해 보겠습니다. 검색할 문자열을 받는 `lookupInGraph`라는 메서드를 제공하여 이 작업을 수행합니다.

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Graph API에 액세스할 도우미 클래스 작성
이것이 응용 프로그램의 핵심입니다. 나머지는 Apple에서 기본 MVC 패턴으로 코드를 삽입한 반면, 여기서는 그래프를 사용자 유형으로 쿼리하고 그 데이터를 반환하는 코드를 작성합니다. 아래에는 코드와 자세한 설명이 차례대로 나와 있습니다.

### <a name="create-a-new-objective-c-header-file"></a>새 Objective C 헤더 파일 만들기
파일 이름을 `GraphAPICaller.h`로 지정하고 다음 코드를 추가합니다.

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

여기서 지정된 메서드는 문자열을 가져와 completionBlock을 반환합니다. 이 completionBlock은 짐작할 수 있듯이 사용자가 검색할 때 실시간으로 데이터를 채워 넣는 개체를 제공하여 테이블을 업데이트합니다.

### <a name="create-a-new-objective-c-file"></a>새 Objective C 파일 만들기
파일 이름을 `GraphAPICaller.m`로 지정하고 다음 메서드를 추가합니다.

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
                           }

                           completionBlock(Users, nil);
                       }
                       else
                       {
                           completionBlock(nil, error);
                       }

                   }];
}

```

이 메서드를 자세히 살펴보겠습니다.

이 코드의 핵심은 초기에 settings.plist 파일 내에 미리 정의한 매개 변수를 사용하는 `NXOAuth2Request`메서드에 있습니다.

첫 번째 단계는 적절한 Graph API 호출을 생성하는 것입니다. `/users`를 호출하게 되므로 버전과 함께 Graph API 리소스에 추가하여 지정합니다. 이것들이 API가 진화함에 따라 변화할 수 있으므로 외부 설정 파일에 놓는 것이 좋습니다.

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

다음으로 Graph API 호출에 제공할 매개 변수를 지정해야 합니다. 런타임 시 모든 URI 비합치 문자는 삭제됨으로 리소스 끝점에 매개 변수를 삽입하지 않는 것이 *매우 중요* 합니다. 모든 쿼리 코드는 매개 변수에 제공되어야 합니다.

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

이것이 아직 작성하지 않은 메서드 `convertParamsToDictionary` 을 호출하는 것을 볼 수 있습니다. 이제 파일 끝에서 실행해 보겠습니다.

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
다음으로 데이터를 API에서 JSON 형식으로 다시 가져오기 위해 `NXOAuth2Request` 메서드를 사용해 보겠습니다.

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

마지막으로, 데이터를 MasterViewController에 반환하는 방법을 살펴보겠습니다. 데이터는 직렬화되어 반환되며 MainViewController가 사용할 수 있는 개체에 역직렬화되어 로드되어야 합니다. 이런 목적으로 기본 골격 구조에 User 개체를 만드는 `User.m/h` 파일이 있습니다. 그래프의 정보로 User 개체를 채웁니다.

```objc
                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                           // Don't be thrown off by the key name being "value". It really is the name of the
                           // first node. :-)

                           //each object is a key value pair
                           NSDictionary *keyValuePairs;
                           NSMutableArray* Users = [[NSMutableArray alloc]init];

                           for(int i =0; i < graphDataArray.count; i++)
                           {
                               keyValuePairs = [graphDataArray objectAtIndex:i];

                               User *s = [[User alloc]init];
                               s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>샘플 실행
기본 구조를 사용하거나 연습을 따라했다면 응용 프로그램이 이제 실행됩니다. 시뮬레이터를 시작하고 **로그인** 을 클릭하여 응용 프로그램을 사용합니다.

## <a name="get-security-updates-for-our-product"></a>당사 제품에 대한 보안 업데이트 가져오기
[Security TechCenter](https://technet.microsoft.com/security/dd252948) 를 방문해서 보안 공지 경고를 구독하여 보안 사건이 발생할 때 알림을 받는 것이 좋습니다.

