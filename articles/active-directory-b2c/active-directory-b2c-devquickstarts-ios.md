<properties
	pageTitle="Azure Active Directory B2C 미리 보기: iOS 응용 프로그램에서 Web API 호출 | Microsoft Azure"
	description="이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 Node.js Web API를 호출하는 iOS ‘할 일 목록’ 앱을 만드는 방법을 보여줍니다. iOS 앱 및 Web API는 Azure Active Directory B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다."
	services="active-directory-b2c"
	documentationCenter="ios"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

	ms.date="05/31/2016"
	ms.author="brandwe"/>

# Azure AD B2C 미리 보기: iOS 응용 프로그램에서 Web API 호출하기

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD(Azure Active Directory) B2C를 사용하여 몇 가지 간단한 단계로 강력한 셀프 서비스 ID 관리 기능을 iOS 앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 Node.js Web API를 호출하는 iOS "할 일 목록" 앱을 만드는 방법을 보여줍니다. iOS 앱 및 Web API는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	제대로 작동하려면 이 빠른 시작은 Azure AD B2C으로 보호되는 Web API가 필요합니다. .NET 및 NET.js 모두에 사용할 수 있도록 만들었습니다. 이 연습에서는 Node.js Web API 샘플이 구성되었다고 가정합니다. 자세한 내용은 [Node.js 샘플에 대한 Azure Active Directory Web API](active-directory-b2c-devquickstarts-api-node.md)를 참조합니다.

> [AZURE.NOTE]
	이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작하여 Azure AD B2C의 기본 사항에 대해 알아봅니다.

## Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 계속하기 전에 [B2C 디렉터리를 만듭니다](active-directory-b2c-get-started.md).

## 응용 프로그램 만들기

다음으로 B2C 디렉터리에서 앱을 만들어야 합니다. 앱과 안전하게 통신하는 데 필요한 Azure AD 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 앱과 Web API는 모두 단일 **응용 프로그램 ID**에서 나타납니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 다음을 수행해야 합니다.

- 응용 프로그램에서 **웹앱/웹 API**를 포함합니다.
- **회신 URL**로 `http://localhost:3000/auth/openid/return`을 입력합니다. 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호**를 만들고 복사합니다. 이 시간은 나중에 필요합니다.
- 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 이 ID는 나중에도 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [정책](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 등록, 로그인 및 Facebook을 사용하여 로그인 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- 등록 정책에서 **표시 이름** 및 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 나중에 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작합니다.

## 코드 다운로드

이 자습서에 대한 코드는 [GitHub에서 유지 관리됩니다](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). 진행하면서 샘플을 작성하기 위해 [골격 프로젝트를 .zip 파일로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip)할 수 있습니다. 기본 구조를 복제할 수도 있습니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **이 자습서를 완료하려면 골격을 다운로드해야 합니다.** iOS에서 완벽하게 작동하는 응용 프로그램을 구현하는 것이 복잡하기 때문에 다음 자습서를 완료한 후에 실행될 UX 코드가 **골격**에 포함되어 있습니다. 개발자를 위한 시간 절약 측정입니다. UX 코드는 iOS 응용 프로그램에 B2C를 추가하는 방법 항목과 밀접한 관련이 없습니다.

완성된 앱도 [.zip 파일로 다운로드하거나](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) 동일한 리포지토리의 `complete` 분기에서 사용할 수 있습니다.

다음으로 CocoaPods를 사용하여 `podfile`을 로드합니다. 로드하려는 새 XCode 작업 영역을 만듭니다. CocoaPods가 없다면 [웹 사이트를 방문하여 설치하세요](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## iOS 작업 응용 프로그램 구성

iOS 태스크 앱이 Azure AD B2C와 통신하도록 하려면 몇 가지 공통 매개 변수를 제공해야 합니다. `Microsoft Tasks` 폴더에서 프로젝트의 루트에서 `settings.plist` 파일을 열고 `<dict>` 섹션의 값을 바꿉니다. 이러한 값은 앱 전체에서 사용됩니다.

```
<dict>
	<key>authority</key>
	<string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
	<key>clientId</key>
	<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	<key>scopes</key>
	<array>
		<string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
	</array>
	<key>additionalScopes</key>
	<array>
	</array>
	<key>redirectUri</key>
	<string>urn:ietf:wg:oauth:2.0:oob</string>
	<key>taskWebAPI</key>
	<string>http://localhost/tasks:3000</string>
	<key>emailSignUpPolicyId</key>
	<string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
	<key>faceBookSignInPolicyId</key>
	<string><your sign in policy for FB></string>
	<key>emailSignInPolicyId</key>
	<string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
	<key>fullScreen</key>
	<false/>
	<key>showClaims</key>
	<true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## 액세스 토큰을 가져오고 작업 API를 호출합니다.

이 섹션은 Microsoft의 라이브러리 및 프레임워크를 사용하여 웹앱에서 OAuth 2.0 토큰 교환을 완료할 수 있는 방법을 설명합니다. 인증 코드 및 액세스 토큰에 익숙하지 않은 경우 [OAuth 2.0 프로토콜 참조](active-directory-b2c-reference-protocols.md)에서 자세히 알아볼 수 있습니다.

### 메서드를 사용하여 헤더 파일 만들기

선택한 정책을 사용하여 토큰을 가져온 다음 태스크 서버를 호출하는 메서드가 필요합니다. 이제 이를 설정합니다.

XCode 프로젝트의 `/Microsoft Tasks`에서 `samplesWebAPIConnector.h`이라는 파일 만들기

다음 코드를 추가하여 수행해야 하는 작업을 정의합니다.

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

`doPolicy` 메서드 뿐만 아니라 API에서 간단한 만들기, 읽기, 업데이트 및 삭제(CRUD) 작업입니다. 이 메서드를 사용하여 원하는 정책으로 토큰을 가져올 수 있습니다.

두 개의 헤더 파일을 정의해야 합니다. 태스크 항목 및 정책 데이터를 확보합니다. 지금 만들어 보세요.

다음 코드를 사용하여 `samplesTaskItem.h` 파일을 만듭니다.

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

또한 `samplesPolicyData.h` 파일을 만들어 정책 데이터를 확보합니다.

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### 태스크 및 정책 항목에 구현을 추가합니다.

헤더 파일을 저장했으므로 샘플에 사용할 데이터를 저장하는 코드를 작성할 수 있습니다.

다음 코드를 사용하여 `samplesPolicyData.m` 파일을 만듭니다.

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### iOS 용 ADAL로 호출하는 설치 코드 작성

UI에 개체를 저장하는 빠른 코드가 이제 완료됩니다. 다음으로 `settings.plist`에 배치한 매개 변수를 사용하여 iOS용 ADAL(Active Directory 인증 라이브러리)에 액세스하는 코드를 작성해야 합니다. 액세스 토큰을 가져와서 이 태스크 서버에 제공합니다.

이제 모든 작업이 `samplesWebAPIConnector.m`에서 수행됩니다.

먼저 `samplesWebAPIConnector.h` 헤더 파일에서 작성한 `doPolicy()` 구현을 만듭니다.

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

이 메서드는 간단합니다. 만든 `samplesPolicyData` 개체, 상위 `ViewController` 및 콜백을 입력으로 사용합니다. 콜백을 자세히 살펴보겠습니다.

- `completionBlock`은 `userInfo` 개체를 사용하여 반환되는 형식으로 `ADProfileInfo`를 갖습니다. `ADProfileInfo`는 클레임을 포함하여 서버에서 모든 응답을 확보하는 형식입니다.
- 또한 `readApplicationSettings`입니다. `settings.plist`에서 제공한 데이터를 읽습니다.
- 마지막으로 대량 `getClaimsWithPolicyClearingCache` 메서드가 있습니다. 작성해야 하는 iOS 용 ADAL에 대한 실제 호출입니다. 나중에 여기로 돌아옵니다.

다음으로 대량 메서드 `getClaimsWithPolicyClearingCache`을 작성합니다. 고유한 섹션을 사용할 만큼 충분히 큽니다.

### iOS 용 ADAL에 호출 만들기

GitHub에서 골격을 다운로드한 후에 샘플 응용 프로그램에 도움이 되는 이러한 호출이 이미 준비되었음을 확인할 수 있습니다. `get(Claims|Token)With<verb>ClearningCache`의 패턴을 따릅니다. 목표 C 규칙을 사용하여 영어처럼 읽습니다. 예를 들어 "제공한 추가 매개 변수로 토큰을 가져오고 캐시를 지웁니다."는 `getTokenWithExtraParamsClearingCache()`입니다.

"제공한 정책으로 클레임 및 토큰을 가져오고 캐시를 지우지 않습니다." 또는 `getClaimsWithPolicyClearingCache`이라고 작성합니다. 항상 ADAL에서 토큰을 다시 가져오므로 "클레임 및 토큰"을 메서드에서 지정할 필요가 없습니다. 그러나 클레임을 구문 분석하는 오버헤드 없이 토큰이 필요한 경우도 있으므로 골격에서 `getTokenWithPolicyClearingCache`라는 클레임 없이 메서드를 제공합니다.

이제 이 코드를 작성합니다.

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

첫 번째 부분은 친숙해 보여야 합니다.

- `settings.plist`에서 제공하는 설정을 로드하고 `data`에 할당합니다.
- `ADAuthenticationError`을 설치하며 이는 iOS용 ADAL에서 나온 오류를 처리합니다.
- `authContext`을 만들며 이는 ADAL에 대한 호출을 설정합니다. 기관에 전달하여 작업을 시작합니다.
- 상위 컨트롤러에 `authContext`을 참조로 제공하여 돌아갈 수 있도록 합니다.
- `redirectURI`을 변환하며 이는 ADAL이 예상하는 NSURL 형식인 `settings.plist`의 문자열입니다.
- `correlationId`을 설정합니다. 클라이언트에서 서버 및 반대로 호출을 수행할 수 있는 UUID입니다. 디버깅에 유용합니다.

다음으로, ADAL에 대한 실제 호출로 가져옵니다. iOS용 ADAL을 이전에 사용했을 때 예상한 대로 호출이 변경되는 위치입니다.

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

호출이 상당히 단순하다는 점을 알 수 있습니다.

`scopes`: 로그인할 사용자에 대해 서버에서 요청하려는 서버에 전달하는 범위입니다. B2C 미리 보기에 `client_id`를 전달합니다. 그러나 나중에 범위를 읽도록 변경됩니다. 그런 다음이 문서를 업데이트할 계획입니다. `additionalScopes`: 응용 프로그램에 사용할 수 있는 추가 범위입니다. 나중에 사용될 것이라고 예상합니다. `clientId`: 포털에서 가져온 응용 프로그램 ID입니다. `redirectURI`: 토큰을 다시 게시하려는 리디렉션입니다. `identifier`: 캐시에서 토큰을 사용할 수 있는지 확인할 수 있도록 사용자를 식별하는 방법입니다. 항상 다른 토큰에 대한 서버의 요청을 방지합니다. `ADUserIdentifier`라는 형식에 전달되며 ID로 사용하려는 것을 지정할 수 있습니다. `username`을 사용해야 합니다. `promptBehavior`: 사용되지 않습니다. `AD_PROMPT_ALWAYS`이어야 합니다. `extraQueryParameters`: URL로 인코딩된 형식인 서버에 전달하려는 추가 데이터입니다. `policy`: 호출하는 정책입니다. 이 연습에 대한 중요한 부분입니다.

`completionBlock`에서 `ADAuthenticationResult`를 전달하는 것을 확인할 수 있습니다. (호출이 성공적인 경우)사용자의 토큰 및 프로필 정보를 갖습니다.

위 코드를 사용하여 제공하는 정책에 대한 토큰을 획득할 수 있습니다. 이 토큰을 사용하여 API를 호출할 수 있습니다.

### 서버에 작업 호출 만들기

토큰이 있으므로 승인을 위해 API에 제공해야 합니다.

세 가지 메서드를 구현해야 합니다.

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList`는 서버에서 태스크를 나타내는 배열을 제공합니다. `addTask` 및 `deleteTask`는 후속 동작을 수행하고 성공하면 `true` 또는 `false`를 반환합니다.

우선 `getTaskList`을 작성합니다.

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

태스크 코드에 대한 설명은 이 연습의 범위를 벗어납니다. 하지만 흥미로운 점은 태스크 URL을 사용하는 `craftRequest` 메서드입니다. 이 메서드는 수신한 액세스 토큰을 사용하여 서버에 대한 요청을 만들기 위해 사용합니다. 지금 작성합니다.

`samplesWebAPIConnector.m` 파일에 다음 코드를 추가합니다.

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

웹 URI(Uniform Resource Identifier)가 필요하고 HTTP의 `Bearer` 헤더를 사용하여 토큰을 추가한 다음 사용자에게 반환합니다. `getTokenClearingCache` API를 호출합니다. 이상해 보이지만 단순히 호출을 사용하여 캐시에서 토큰을 가져오고 여전히 유효한지 확인합니다. (`getToken` 호출은 ADAL을 요청하여 이를 수행합니다.) 각 호출에 이 코드를 사용합니다. 다음으로 추가 작업 메서드를 확인합니다.

`addTask`을 작성합니다.

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

동일한 패턴을 따르지만 `convertTaskToDictionary`을 구현해야 하는 최종 메서드에 대해서 소개합니다. 배열을 사용하며 사전 개체로 만듭니다. 이 개체는 서버에 전달해야 하는 쿼리 매개 변수로 보다 쉽게 변경됩니다. 코드는 간단합니다.

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

다음으로 `deleteTask`을 작성합니다.

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### 로그아웃을 응용 프로그램에 추가합니다.

마지막으로 응용 프로그램에 대한 로그아웃을 구현합니다. 이 작업은 간단합니다. `sampleWebApiConnector.m` 파일 내에서:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## 샘플 앱 실행

마지막으로 XCode에서 앱을 작성하고 실행합니다. 앱에 등록 또는 로그인하고 로그인한 사용자에 대한 태스크를 만듭니다. 로그아웃하고 다른 사용자 권한으로 다시 로그인한 다음 해당 사용자에 대한 태스크를 만듭니다.

API가 수신한 액세스 토큰에서 사용자의 ID를 추출하므로 태스크가 API에 대해 사용자 단위로 저장됩니다.

참조를 위해 전체 샘플은 [.zip 파일로 제공됩니다](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip). 또한 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## 다음 단계

이제 좀 더 고급 B2C 항목으로 이동할 수 있습니다. 다음을 시도해 볼 수 있습니다.

[Node.js 웹앱에서 Node.js Web API 호출]()

[B2C 앱을 위한 UX 사용자 지정]()

<!---HONumber=AcomDC_0608_2016-->