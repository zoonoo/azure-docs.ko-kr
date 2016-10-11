<properties
	pageTitle="Azure Active Directory B2C: 다사 라이브러리를 사용하여 iOS 응용 프로그램에서 Web API 호출 | Microsoft Azure"
	description="이 문서에서는 타사 라이브러리를 사용하는 OAuth 2.0 전달자 토큰을 사용하여 Node.js Web API를 호출하는 iOS ‘할 일 목록’ 앱을 만드는 방법을 보여 줍니다."
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="07/26/2016"
	ms.author="brandwe"/>

# Azure AD B2C : 타사 라이브러리를 사용하여 iOS 응용 프로그램에서 Web API 호출

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Microsoft ID 플랫폼은 OAuth2 및 OpenID Connect와 같은 개방형 표준을 사용합니다. 이를 통해 개발자는 서비스와 통합하려는 모든 라이브러리를 활용할 수 있습니다. 개발자가 플랫폼을 다른 라이브러리와 함께 사용할 수 있도록 돕기 위해, 타사 라이브러리를 Microsoft ID 플랫폼에 연결하도록 구성하는 방법을 설명하는 이와 같은 연습 몇 가지를 작성했습니다. [RFC6749 OAuth2 사양](https://tools.ietf.org/html/rfc6749)을 구현하는 대부분의 라이브러리는 Microsoft ID 플랫폼에 연결할 수 있습니다.


OAuth2 또는 OpenID Connect를 처음 접하는 경우 이 샘플 구성 대부분이 잘 이해되지 않을 수도 있습니다. [여기서 추천한 프로토콜에 관한 개요](active-directory-b2c-reference-protocols.md)를 간략히 살펴볼 것을 추천합니다.

> [AZURE.NOTE]
    조건부 액세스 및 Intune 정책 관리 등과 같은 이런 표준의 식을 사용하는 플랫폼의 일부 기능은 수행하려면 오픈 소스인 Microsoft Azure ID 라이브러리를 사용해야 합니다.
   
일부 Azure Active Directory 시나리오 및 기능만 B2C 플랫폼에서 지원합니다. B2C 플랫폼 사용 여부를 결정하려면 [B2C 제한 사항](active-directory-b2c-limitations.md)을 참조하세요.


## Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 계속하기 전에 [B2C 디렉터리를 만듭니다](active-directory-b2c-get-started.md).

## 응용 프로그램 만들기

다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 앱과 Web API는 모두 단일 **응용 프로그램 ID**에서 나타납니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

- 응용 프로그램에서 **모바일 장치**를 포함합니다.
- 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 ID는 나중에도 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 하나의 ID 환경: 결합된 로그인 및 등록을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 이 정책을 만들어야 합니다. 정책을 만들 때 다음을 확인합니다.

- 정책에서 **표시 이름** 및 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 정책 이름이 나중에 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

정책을 만들었다면 앱을 빌드할 준비가 되었습니다.


## 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)에서 유지 관리됩니다. 자습서에 따라 [.zip으로 앱을 다운로드](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-b2c)(/archive/master.zip)하거나 복제할 수 있습니다.

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

또는 완성된 코드를 다운로드하고 지금 바로 시작합니다.

```
git clone --branch complete git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-b2c.git
```

## 타사 라이브러리 nxoauth2를 다운로드하고 작업 영역을 실행합니다.

이 연습에서는 Mac OS X 및 iOS (Cocoa & Cocoa touch)에 대한 OAuth2 라이브러리인 GitHub에서 OAuth2Client를 사용합니다. 이 라이브러리는 OAuth2 사양의 초안 10에 기반을 둡니다. 네이티브 응용 프로그램 프로필을 구현하고 최종 사용자 권한 부여 끝점을 지원합니다. Microsoft ID 플랫폼과 통합하기 위해 필요한 것은 이것들이 전부입니다.

### CocoaPods를 사용하여 프로젝트에 라이브러리 추가하기

CocoaPods는 Xcode 프로젝트에 대한 종속성 관리자입니다. 위의 설치 단계를 자동으로 관리합니다.

```
$ vi Podfile
```
이 podfile에 다음을 추가합니다.

```
 platform :ios, '8.0'
 
 target 'SampleforB2C' do
 
 pod 'NXOAuth2Client'
 
 end
```

이제 cocoapods를 사용하여 podfile를 로드합니다. 로드하려는 새 XCode 작업 영역을 만듭니다.

```
$ pod install
...
$ open SampleforB2C.xcworkspace

```

## 프로젝트의 구조

골격에 있는 프로젝트에 대한 다음 구조 설정이 있습니다.

* 작업 창으로 **마스터 보기**
* 선택한 작업과 관련된 데이터에 대한 **작업 추가 보기**
* 사용자가 앱에 로그인하도록 하는 **로그인 보기**

인증을 추가할 프로젝트에서 다양한 파일로 이동합니다. 시각적 코드와 같은 코드의 다른 부분은 Id와 밀접한 관련이 없으며 사용자에게 제공되지 않습니다.

## 응용 프로그램에 대한 `settings.plist` 파일 만들기

구성 값을 입력한 중앙 집중화된 위치가 있는 경우 응용 프로그램을 쉽게 구성할 수 있습니다. 또한 응용 프로그램에서 각 설정의 기능을 이해할 수 있습니다. *속성 목록*을 응용 프로그램에 이러한 값을 제공하는 방법으로 활용합니다.

* 응용 프로그램 작업 영역의 `Supporting Files`에서 `settings.plist` 파일 만들기/열기

* 다음 값을 입력합니다(곧 자세히 살펴봄).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>accountIdentifier</key>
	<string>B2C_Acccount</string>
	<key>clientID</key>
	<string><client ID></string>
	<key>clientSecret</key>
	<string></string>
	<key>authURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/authorize?p=<policy name></string>
	<key>loginURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/login</string>
	<key>bhh</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>tokenURL</key>
	<string>https://login.microsoftonline.com/<tenant name>/oauth2/v2.0/token?p=<policy name></string>
	<key>keychain</key>
	<string>com.microsoft.azureactivedirectory.samples.graph.QuickStart</string>
	<key>contentType</key>
	<string>application/x-www-form-urlencoded</string>
	<key>taskAPI</key>
	<string>https://aadb2cplayground.azurewebsites.net</string>
</dict>
</plist>
```

이것들을 자세히 설명해 보겠습니다.


`authURL`, `loginURL`, `bhh`, `tokenURL`의 경우 테넌트 이름을 입력해야 합니다. 사용자에게 할당된 B2C 테넌트의 이름입니다. 예를 들어 `kidventusb2c.onmicrosoft.com`입니다. 오픈 소스 Microsoft Azure Identity 라이브러리를 사용하는 경우 메타데이터 끝점을 사용하려면 이 데이터를 가져옵니다. 사용자를 위해 이러한 값을 추출하는 어려운 작업을 마쳤습니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

`keychain` 값은 NXOAuth2Client 라이브러리가 토큰을 저장하기 위해 키 집합을 만드는데 사용할 컨테이너입니다. 다양한 앱에서 SSO를 가져오려 한다면 각 응용 프로그램에서 동일한 키 집합을 지정하는 것은 물론 XCode 자격에서 그 키 집합의 사용을 요청할 수 있습니다. 이 내용은 Apple 설명서에서 다룹니다.

각 URL의 끝에 있는 `<policy name>`은 위에서 만든 정책을 배치한 위치입니다. 앱은 흐름에 따라 이러한 정책을 호출합니다.

`taskAPI`은 작업을 추가하거나 기존 작업을 쿼리하기 위해 B2C 토큰으로 호출하는 REST 끝점입니다. 이 샘플에 적합하도록 설정되었습니다. 샘플을 작동시키기 위해 변경할 필요가 없습니다.

나머지 이런 값들은 라이브러리를 사용하여 값을 컨텍스트로 옮길 위치를 만드는 것이 필요합니다.

이제 `settings.plist` 파일을 만들었으므로 읽을 코드가 필요합니다.

## AppData 클래스를 설정하여 설정을 읽습니다.

위에서 만든 `settngs.plist` 파일을 구문 분석하는 간단한 파일을 만들고 해당 설정을 나중에 클래스에 사용할 수 있도록 하겠습니다. 클래스가 요청할 때마다 데이터의 새 복사본을 만들지 않으려면 단일 항목 패턴을 사용하고 설정에 대한 요청이 만들어질 때마다 만든 동일한 인스턴스를 반환하기만 합니다.

* `AppData.h` 파일을 만듭니다.

```objc
#import <Foundation/Foundation.h>

@interface AppData : NSObject

@property(strong) NSString *accountIdentifier;
@property(strong) NSString *taskApiString;
@property(strong) NSString *authURL;
@property(strong) NSString *clientID;
@property(strong) NSString *loginURL;
@property(strong) NSString *bhh;
@property(strong) NSString *keychain;
@property(strong) NSString *tokenURL;
@property(strong) NSString *clientSecret;
@property(strong) NSString *contentType;

+ (id)getInstance;

@end
```

* `AppData.m` 파일을 만듭니다.

```objc
#import "AppData.h"

@implementation AppData

+ (id)getInstance {
  static AppData *instance = nil;
  static dispatch_once_t onceToken;

  dispatch_once(&onceToken, ^{
    instance = [[self alloc] init];

    NSDictionary *dictionary = [NSDictionary
        dictionaryWithContentsOfFile:[[NSBundle mainBundle]
                                         pathForResource:@"settings"
                                                  ofType:@"plist"]];
    instance.accountIdentifier = [dictionary objectForKey:@"accountIdentifier"];
    instance.clientID = [dictionary objectForKey:@"clientID"];
    instance.clientSecret = [dictionary objectForKey:@"clientSecret"];
    instance.authURL = [dictionary objectForKey:@"authURL"];
    instance.loginURL = [dictionary objectForKey:@"loginURL"];
    instance.bhh = [dictionary objectForKey:@"bhh"];
    instance.tokenURL = [dictionary objectForKey:@"tokenURL"];
    instance.keychain = [dictionary objectForKey:@"keychain"];
    instance.contentType = [dictionary objectForKey:@"contentType"];
    instance.taskApiString = [dictionary objectForKey:@"taskAPI"];

  });

  return instance;
}
@end
```

이제 아래와 같이 클래스 중 하나에서 `  AppData *data = [AppData getInstance];`을 호출하여 쉽게 데이터를 가져올 수 있습니다.



## AppDelegate에서 NXOAuth2Client 라이브러리 설정

NXOAuthClient 라이브러리는 시작하기 위해 일부 값을 필요로 합니다. 완료되면 REST API를 호출하는 데 획득한 토큰을 사용할 수 있습니다. 응용 프로그램을 로드할 때마다 `AppDelegate`이 호출되리라는 것을 알고 있으므로 구성 값을 해당 파일에 두는 것이 좋습니다.
* `AppDelegate.m` 파일을 엽니다.

* 나중에 사용할 몇 가지 헤더 파일을 가져옵니다.

```objc
#import "NXOAuth2.h" // the Identity library we are using
#import "AppData.h" // the class we just created we will use to load the settings of our application
```

* AppDelegate에 `setupOAuth2AccountStore` 메서드를 추가합니다.

AccountStore를 만들고 방금 `settings.plist` 파일에서 읽은 데이터를 피드해야 합니다.

이제 B2C 서비스와 관련하여 알아야 할 몇 가지 사항이 있습니다. 이를 통해 코드를 쉽게 이해할 수 있습니다.


1. Azure AD B2C는 쿼리 매개 변수에서 제공한 대로 *정책*을 사용하여 요청을 처리합니다. 이렇게 하면 Azure Active Directory에서 응용 프로그램에 대해서만 독립된 서비스의 역할을 할 수 있습니다. 이러한 추가 쿼리 매개 변수를 제공하기 위해 사용자 지정 정책 매개 변수와 함께 `kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:` 메서드를 제공해야 합니다.

2. Azure AD B2C는 OAuth2 서버와 거의 동일한 방식으로 범위를 사용합니다. 그러나 B2C를 사용하는 작업은 리소스에 액세스하는 작업만큼 많은 사용자를 인증하는 것이기 때문에 흐름이 제대로 작동하기 위해 일정 범위가 절대적으로 필요합니다. `openid` 범위입니다. Microsoft ID SDK는 `openid` 범위를 자동으로 제공하므로 SDK 구성에서 표시되지 않습니다. 타사 라이브러리를 사용하지만 이 범위를 지정해야 합니다.

```objc
- (void)setupOAuth2AccountStore {
  AppData *data = [AppData getInstance]; // The singleton we use to get the settings

  NSDictionary *customHeaders =
      [NSDictionary dictionaryWithObject:@"application/x-www-form-urlencoded"
                                  forKey:@"Content-Type"];

  // Azure B2C needs
  // kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters for
  // sending policy to the server,
  // therefore we use -setConfiguration:forAccountType:
  NSDictionary *B2cConfigDict = @{
    kNXOAuth2AccountStoreConfigurationClientID : data.clientID,
    kNXOAuth2AccountStoreConfigurationSecret : data.clientSecret,
    kNXOAuth2AccountStoreConfigurationScope :
        [NSSet setWithObjects:@"openid", data.clientID, nil],
    kNXOAuth2AccountStoreConfigurationAuthorizeURL :
        [NSURL URLWithString:data.authURL],
    kNXOAuth2AccountStoreConfigurationTokenURL :
        [NSURL URLWithString:data.tokenURL],
    kNXOAuth2AccountStoreConfigurationRedirectURL :
        [NSURL URLWithString:data.bhh],
    kNXOAuth2AccountStoreConfigurationCustomHeaderFields : customHeaders,
    //      kNXOAuth2AccountStoreConfigurationAdditionalAuthenticationParameters:customAuthenticationParameters
  };

  [[NXOAuth2AccountStore sharedStore] setConfiguration:B2cConfigDict
                                        forAccountType:data.accountIdentifier];
}
```
다음으로 `didFinishLaunchingWithOptions:` 메서드의 AppDelegate에서 호출하는지 확인합니다.

```
[self setupOAuth2AccountStore];
```


## 인증 요청을 처리하는 데 사용하는 `LoginViewController` 클래스를 만듭니다.

계정 로그인에 웹 보기를 사용합니다. 사용자에게 SMS 텍스트 메시지(구성된 경우)와 같은 추가 요소에 대한 메시지를 표시하거나 사용자에게 오류 메시지를 돌려줍니다. 여기서 웹 보기를 설정한 후에 Microsoft ID 서비스의 WebView에서 발생할 콜백을 처리할 코드를 작성합니다.

* `LoginViewController.h` 클래스를 만듭니다.

```objc
@interface LoginViewController : UIViewController <UIWebViewDelegate>
@property(weak, nonatomic) IBOutlet UIWebView *loginView; // Our webview that we will use to do authentication

- (void)handleOAuth2AccessResult:(NSURL *)accessResult; // Allows us to get a token after we've received an Access code.
- (void)setupOAuth2AccountStore; // We will need to add to our OAuth2AccountStore we setup in our AppDelegate
- (void)requestOAuth2Access; // This is where we invoke our webview.
```

이러한 방법은 각각 아래와 같이 만듭니다.

> [AZURE.NOTE] 
    `loginView`을 스토리 보드 내에 있는 실제 웹 보기에 바인딩하도록 합니다. 그렇지 않으면 인증할 때 팝업할 수 있는 웹 보기가 없을 수도 있습니다.

* `LoginViewController.m` 클래스를 만듭니다.

* 인증한 대로 상태를 가져오는 일부 변수를 추가합니다.

```objc
NSURL *myRequestedUrl; \\ The URL request to Azure Active Directory 
NSURL *myLoadedUrl; \\ The URL loaded for Azure Active Directory
bool loginFlow = FALSE; 
bool isRequestBusy; \\ A way to give status to the thread that the request is still happening
NSURL *authcode; \\ A placeholder for our auth code.
```

* 인증을 처리하는 WebView 메서드 재정의

위에서 설명한 것처럼 사용자가 로그인할 필요가 있을 때 웹 보기에 원하는 동작을 지시할 필요가 있습니다. 단순히 아래 코드를 잘라내어 붙여넣습니다.

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {
  // We get the auth token from a redirect so we need to handle that in the
  // webview.

  if (![NSThread isMainThread]) {
    [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:)
                           withObject:URL
                        waitUntilDone:YES];
    return;
  }

  NSURLRequest *hostnameURLRequest =
      [NSURLRequest requestWithURL:URL
                       cachePolicy:NSURLRequestUseProtocolCachePolicy
                   timeoutInterval:10.0f];
  isRequestBusy = YES;
  [self.loginView loadRequest:hostnameURLRequest];

  NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)",
        self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView
    shouldStartLoadWithRequest:(NSURLRequest *)request
                navigationType:(UIWebViewNavigationType)navigationType {
  AppData *data = [AppData getInstance];

  NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL,
        (long)navigationType);

  // The webview is where all the communication happens. Slightly complicated.

  myLoadedUrl = [webView.request mainDocumentURL];
  NSLog(@"***Loaded url: %@", myLoadedUrl);

  // if the UIWebView is showing our authorization URL or consent URL, show the
  // UIWebView control
  if ([request.URL.absoluteString rangeOfString:data.authURL
                                        options:NSCaseInsensitiveSearch]
          .location != NSNotFound) {
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.loginURL
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = NO;
  } else if ([request.URL.absoluteString rangeOfString:data.bhh
                                               options:NSCaseInsensitiveSearch]
                 .location != NSNotFound) {
    // otherwise hide the UIWebView, we've left the authorization flow
    self.loginView.hidden = YES;
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  } else {
    self.loginView.hidden = NO;
    // read the Location from the UIWebView, this is how Microsoft APIs is
    // returning the
    // authentication code and relation information. This is controlled by the
    // redirect URL we chose to use from Microsoft APIs
    // continue the OAuth2 flow
    // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
  }

  return YES;
}

```

* OAuth2 요청의 결과를 처리할 코드를 작성합니다.

WebView에서 반환되는 redirectURL를 처리할 코드가 필요합니다. 성공적이지 않았다면, 다시 시도합니다. 한편 라이브러리는 콘솔에서 보거나 비동기식으로 처리할 수 있는 오류를 제공합니다.

```objc
- (void)handleOAuth2AccessResult:(NSURL *)accessResult {
  // parse the response for success or failure
  if (accessResult)
  // if success, complete the OAuth2 flow by handling the redirect URL and
  // obtaining a token
  {
    [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
  } else {
    // start over
    [self requestOAuth2Access];
  }
}
```

* 알림 팩터리를 설정합니다.

위의 `AppDelegate`에서 수행한 것과 동일한 방법을 만들지만 이번에는 `NSNotification`를 추가하여 이 서비스에서 무엇이 발생하는지 알아봅니다. 토큰과 함께 무언가가 변경될 때 알려주도록 관찰자를 설정합니다. 일단 토큰을 가져오면 사용자를 `masterView`로 돌려보냅니다.



```objc
- (void)setupOAuth2AccountStore {
  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                if (aNotification.userInfo) {
                  // account added, we have access
                  // we can now request protected data
                  NSLog(@"Success!! We have an access token.");
                  dispatch_async(dispatch_get_main_queue(), ^{

                    MasterViewController *masterViewController =
                        [self.storyboard
                            instantiateViewControllerWithIdentifier:@"master"];
                    [self.navigationController
                        pushViewController:masterViewController
                                  animated:YES];
                  });
                } else {
                  // account removed, we lost access
                }
              }];

  [[NSNotificationCenter defaultCenter]
      addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                  object:[NXOAuth2AccountStore sharedStore]
                   queue:nil
              usingBlock:^(NSNotification *aNotification) {
                NSError *error = [aNotification.userInfo
                    objectForKey:NXOAuth2AccountStoreErrorKey];
                NSLog(@"Error!! %@", error.localizedDescription);
              }];
}

```
* 로그인을 기본으로 요청이 시작될 때마다 사용자를 처리하는 코드를 추가합니다.

인증에 대한 요청이 있을 때마다 호출되는 메서드를 만들어 보겠습니다. 실제로 웹 보기를 만드는 메서드입니다.

```objc
- (void)requestOAuth2Access {
  AppData *data = [AppData getInstance];

  // in order to login to Mircosoft APIs using OAuth2 we must show an embedded
  // browser (UIWebView)
  [[NXOAuth2AccountStore sharedStore]
           requestAccessToAccountWithType:data.accountIdentifier
      withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
        // navigate to the URL returned by NXOAuth2Client

        NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
        [self.loginView loadRequest:r];
      }];
}
```

* 마지막으로 `LoginViewController`이 로드될 때마다 위에서 작성한 이러한 모든 메서드를 호출하겠습니다. 이러한 메서드를 Apple이 제공한 `viewDidLoad` 메서드에 추가하여 이 작업을 수행합니다.

```objc
  [super viewDidLoad];
  // Do any additional setup after loading the view.

  // OAuth2 Code

  self.loginView.delegate = self;
  [self requestOAuth2Access];
  [self setupOAuth2AccountStore];
  NSURLCache *URLCache =
      [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                    diskCapacity:20 * 1024 * 1024
                                        diskPath:nil];
  [NSURLCache setSharedURLCache:URLCache];
```

이제 로그인하기 위해 응용 프로그램과 상호 작용하는 기본적인 방법을 만들었습니다. 로그인한 후에 받은 토큰을 사용해야 합니다. 이를 위해 이 라이브러리를 사용하여 REST API를 호출하는 도우미 코드를 만듭니다.


## `GraphAPICaller` 클래스를 만들어서 REST API에 대한 요청을 처리합니다.

앱을 로드할 때마다 로드된 구성이 있습니다. 이제 토큰이 있으면 작업을 수행해야 합니다.

* `GraphAPICaller.h` 파일을 만듭니다.

```objc
@interface GraphAPICaller : NSObject <NSURLConnectionDataDelegate>

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock;

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *error))completionBlock;

@end
```

이 코드에서는 API에서 작업을 가져오는 메서드와 API에 작업을 추가하는 메서드 등 두 개의 메서드를 만드는 작업을 확인합니다.

이제 인터페이스를 설정했으므로 실제 구현에 추가해 보겠습니다.

* `GraphAPICaller.m file`을 만듭니다.

```objc
@implementation GraphAPICaller

// 
// Gets the tasks from our REST endpoint we specified in settings
//

+ (void)getTaskList:(void (^)(NSMutableArray *, NSError *))completionBlock

{
  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSMutableArray *Tasks = [[NSMutableArray alloc] init];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"GET"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:nil
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (!error) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          if ([dataReturned count] != 0) {

            for (NSMutableDictionary *theTask in dataReturned) {

              Task *t = [[Task alloc] init];
              t.name = [theTask valueForKey:@"Text"];

              [Tasks addObject:t];
            }
          }

          completionBlock(Tasks, nil);
        } else {
          completionBlock(nil, error);
        }

      }];
}

// 
// Adds a task from our REST endpoint we specified in settings
//

+ (void)addTask:(Task *)task
completionBlock:(void (^)(bool, NSError *error))completionBlock {

  AppData *data = [AppData getInstance];

  NSString *taskURL =
      [NSString stringWithFormat:@"%@%@", data.taskApiString, @"/api/tasks"];

  NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
  NSDictionary *params = [self convertParamsToDictionary:task.name];

  NSArray *accounts = [store accountsWithAccountType:data.accountIdentifier];
  [NXOAuth2Request performMethod:@"POST"
      onResource:[NSURL URLWithString:taskURL]
      usingParameters:params
      withAccount:accounts[0]
      sendProgressHandler:^(unsigned long long bytesSend,
                            unsigned long long bytesTotal) {
        // e.g., update a progress indicator
      }
      responseHandler:^(NSURLResponse *response, NSData *responseData,
                        NSError *error) {
        // Process the response
        if (responseData) {
          NSDictionary *dataReturned =
              [NSJSONSerialization JSONObjectWithData:responseData
                                              options:0
                                                error:nil];
          NSLog(@"Graph Response was: %@", dataReturned);

          completionBlock(TRUE, nil);
        } else {
          completionBlock(FALSE, error);
        }

      }];
}

+ (NSDictionary *)convertParamsToDictionary:(NSString *)task {
  NSMutableDictionary *dictionary = [[NSMutableDictionary alloc] init];

  [dictionary setValue:task forKey:@"Text"];

  return dictionary;
}

@end
```

## 샘플 앱 실행

마지막으로 XCode에서 앱을 작성하고 실행합니다. 앱에 등록 또는 로그인하고 로그인한 사용자에 대한 태스크를 만듭니다. 로그아웃하고 다른 사용자 권한으로 다시 로그인한 다음 해당 사용자에 대한 태스크를 만듭니다.

API가 수신한 액세스 토큰에서 사용자의 ID를 추출하므로 태스크가 API에 대해 사용자 단위로 저장됩니다.


## 다음 단계

이제 좀 더 고급 B2C 항목으로 이동할 수 있습니다. 다음을 시도해 볼 수 있습니다.

[Node.js 웹앱에서 Node.js Web API 호출]()

[B2C 앱을 위한 UX 사용자 지정]()

<!---HONumber=AcomDC_1005_2016-->