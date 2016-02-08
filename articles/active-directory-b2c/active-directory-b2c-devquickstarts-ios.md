<properties pageTitle="Azure AD B2C Preview: Calling a Web API from an iOS application | Microsoft Azure" description="This article will show you how to create a iOS "To-Do List" app that calls a node.js web API using OAuth 2.0 bearer tokens. iOS 앱과 Web API 모두는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다." services="active-directory-b2c" documentationCenter="ios" authors="brandwe" manager="mbaldwin" editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="objectivec"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="brandwe"/>

# Azure AD B2C 미리 보기: iOS 응용 프로그램에서 Web API 호출하기

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure AD B2C를 사용하여 몇가지 간단한 단계에서 강력한 셀프 서비스 ID 관리 기능을 iOS 앱 및 Web API에 추가할 수 있습니다. 이 문서에서는 OAuth 2.0 전달자 토큰을 사용하여 node.js Web API를 호출하는 iOS "할 일 목록" 앱을 만드는 방법을 보여줍니다. iOS 앱 및 Web API는 Azure AD B2C를 사용하여 사용자 ID를 관리하고 사용자를 인증합니다.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	이 빠른 시작이 완벽하게 작동하기 위해 B2C를 사용하여 Azure AD에서 Web API를 보호하는 필수 구성 요소를 포함합니다. .Net 및 node.js 모두에 사용할 수 있도록 만들었습니다. 이 연습에서는 node.js Web API 샘플 구성되었다고 가정합니다. [Node.js 샘플에 대한 Azure Active Directory Web API](active-directory-b2c-devquickstarts-api-node.md`)를 참조하세요.

> [AZURE.NOTE]
	이 문서는 Azure AD B2C를 사용하여 등록, 로그인 및 프로필 관리를 구현하는 방법을 다루지 않습니다. 사용자를 인증한 후에 Web API를 호출하는 데 집중합니다. 아직 준비되지 않은 경우 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작하여 Azure AD B2C의 기본 사항에 대해 알아봅니다.

## 1\. Azure AD B2C 디렉터리 가져오기

Azure AD B2C를 사용하기 전에 디렉터리 또는 테넌트를 만들어야 합니다. 디렉터리는 모든 사용자, 앱, 그룹 등을 위한 컨테이너입니다. 디렉터리가 없는 경우 넘어가기 전에 [B2C 디렉터리 만들기](active-directory-b2c-get-started.md)로 이동합니다.

## 2\. 응용 프로그램 만들기

이제 B2C 디렉터리에 앱을 만들어야 하며 Azure AD가 앱과 안전하게 통신해야 한다는 일부 정보를 제공합니다. 이 경우 하나의 논리 앱을 구성하기 때문에 앱과 Web API 모두는 단일 **응용 프로그램 ID**에서 표현됩니다. 앱을 만들려면 [다음 지침](active-directory-b2c-app-registration.md)에 따릅니다. 반드시

- 응용 프로그램에서 **웹앱/웹 API** 포함
- `http://localhost:3000/auth/openid/return`을 **회신 URL**로 입력 - 이 코드 샘플에 대한 기본 URL입니다.
- 응용 프로그램에 **응용 프로그램 암호**를 만들고 복사합니다. 곧 필요합니다.
- 앱에 할당된 **응용 프로그램 ID**를 복사합니다. 또한 곧 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. 정책 만들기

Azure AD B2C에서 모든 사용자 환경은 [**정책**](active-directory-b2c-reference-policies.md)에 의해 정의됩니다. 이 앱은 등록, 로그인 및 Facebook으로 로그인 등 세 가지 ID 환경을 포함합니다. [정책 참조 문서](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy)에서 설명한 대로 각 형식에 하나의 정책을 만들어야 합니다. 세 가지 정책을 만들 때 다음을 확인합니다.

- 등록 정책에서 **표시 이름** 및 다른 몇 가지 등록 특성을 선택합니다.
- 모든 정책에서 **표시 이름** 및 **개체 ID** 응용 프로그램 클레임을 선택합니다. 물론 다른 클레임을 선택할 수 있습니다.
- 각 정책을 만든 후에 **이름**을 복사합니다. 접두사 `b2c_1_`이 있어야 합니다. 이러한 정책 이름이 곧 필요합니다.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

세 가지 정책을 성공적으로 만들었다면 앱을 빌드할 준비가 되었습니다.

이 문서는 방금 만든 정책을 사용하는 방법을 다루지 않습니다. Azure AD B2C에서 정책 작동 방법을 알아보려면 [.NET 웹앱 시작 자습서](active-directory-b2c-devquickstarts-web-dotnet.md)로 시작해야 합니다.

## 4\. 코드 다운로드

이 자습서에 대한 코드는 [GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS)에서 유지 관리됩니다. 진행하면서 샘플을 빌드하려면 [골격 프로젝트를 .zip으로 다운로드](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip)하거나 골격을 복제합니다.

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **이 자습서를 완료하려면 골격을 다운로드해야 합니다.** iOS에서 완벽하게 작동하는 응용 프로그램을 구현하는 것이 복잡하기 때문에 다음 자습서를 완료하면 실행될 UX 코드가 **골격**에 포함되어 있습니다. 개발자를 위한 시간 절약 측정입니다. UX 코드는 B2C를 iOS 응용 프로그램에 추가하는 항목과 밀접한 관련이 없습니다.

또한 완성된 앱은 [.zip으로 사용할 수 있거나](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) 동일한 리포지토리의 `complete`분기에 사용할 수 있습니다.


이제 cocoapods를 사용하여 podfile를 로드합니다. 로드하려는 새 XCode 작업 영역을 만듭니다. Cocoapods가 없다면 [cocoapods를 설치하는 웹 사이트](https://cocoapods.org)를 방문하세요.

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## 5\. iOS 작업 응용 프로그램 구성

iOS 작업 앱이 Azure AD B2C와 통신하도록 하기 위해 제공해야 할 몇 가지 공통 매개 변수 사항이 있습니다. `Microsoft Tasks` 폴더에서 프로젝트의 루트에서 `settings.plist` 파일을 열고 `<dict>` 섹션의 값을 바꿉니다. 이러한 값은 앱 전체에서 사용됩니다.

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

## 6\. 액세스 토큰을 가져오고 작업 API를 호출합니다.

이 섹션은 Microsoft의 라이브러리 및 프레임워크를 사용하여 웹앱에서 OAuth 2.0 토큰 교환을 완료하는 방법을 보여줍니다. **인증 코드** 및 **액세스 토큰**에 익숙하지 않은 경우 [OAuth 2.0 프로토콜 참조](active-directory-b2c-reference-protocols.md)를 가볍게 읽어 보는 것이 좋습니다.

#### 사용할 메서드로 헤더 파일을 만듭니다.

선택한 정책을 사용하여 토큰을 가져온 다음 작업 서버를 호출하는 메서드가 필요합니다. 지금 설정해 보세요.

XCode 프로젝트의 /Microsoft 작업에서 `samplesWebAPIConnector.h`라는 파일 만들기

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

API의 간단한 CRUD 작업 뿐만 아니라 원하는 정책을 사용하여 토큰을 가져올 수 있는 `doPolicy` 메서드가 있습니다.

또한 다른 두 가지 헤더 파일 있으므로 작업 항목 및 정책 데이터의 보유를 정의해야 합니다. 지금 만들어 보세요.

다음 코드를 사용하여 `samplesTaskItem.h`라는 파일을 만듭니다.

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

또한 `samplesPolicyData.h` 파일을 만들어 정책 데이터를 확보해 보세요.

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
#### 작업 및 정책 항목에 구현을 추가합니다.

헤더 파일이 준비되었으므로 이제 샘플에 사용할 데이터를 저장하기 위해 일부 코드를 작성해야 합니다.

다음 코드를 사용하여 `samplesPolicyData.m`라는 파일을 만듭니다.

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

#### iOS 용 ADAL로 호출하는 설치 코드 작성

UI에 개체를 저장하기 위한 빠른 코드가 완료됩니다. 작업 서버에 제공하도록 액세스 토큰을 가져오기 위해 코드를 작성하여 `settings.plist` 파일에 둔 매개 변수로 iOS용 ADAL에 액세스합니다. 자세한 정보를 얻을 수 있으므로 계속 집중합니다.

이제 모든 작업이 `samplesWebAPIConnector.m`에서 수행됩니다.

우선 `samplesWebAPIConnector.h` 헤더 파일에서 작성한 `doPolicy()` 구현을 만들어 보겠습니다.

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

메서드가 상당히 간단한 것을 표시합니다. 잠시 전에 만든 `samplesPolicyData` 개체, 부모 ViewController 및 콜백을 입력으로 사용합니다. 흥미로운 다시 호출을 살펴봅니다.

1. `completionBlock`는 ADProfileInfo를 `userInfo` 개체로 반환되는 형식으로 보유합니다. ADProfileInfo는 특정 클레임의 서버에서 모든 응답을 보유하는 형식입니다.
2. `readApplicationSettings`임을 표시합니다. `settings.plist`에서 제공한 데이터를 읽습니다.
3. 마지막으로 큰 `getClaimsWithPolicyClearingCache` 메서드가 있습니다. 작성해야 하는 iOS 용 ADAL에 대한 실제 호출입니다. 나중에 수행합니다.

이제 큰 메서드 `getClaimsWithPolicyClearingCache`를 작성해 봅시다. 고유한 섹션을 사용할 만큼 충분히 큽니다.

#### iOS 용 ADAL에 호출 만들기

GitHub에서 골격을 다운로드한 경우 샘플 응용 프로그램에 도움이 되는 여러 가지가 이미 준비되었음을 표시합니다. `get(Claims|Token)With<verb>ClearningCache`의 패턴을 따릅니다. 목표 C 규칙을 사용하여 영어와 같이 읽습니다. 예를 들어 "제공한 추가 매개 변수로 토큰을 가져오고 캐시를 지웁니다." `getTokenWithExtraParamsClearingCache()`입니다. 아주 간단합니다.

"제공한 정책으로 클레임 및 토큰을 가져오고 캐시를 지우지 않습니다." 또는 `getClaimsWithPolicyClearingCache`을 작성하겠습니다. 항상 ADAL에서 토큰을 다시 가져오므로 "클레임 및 토큰"을 메서드에서 지정할 필요가 없습니다. 그러나 클레임을 구문 분석하는 오버헤드 없이 토큰이 필요한 경우도 있으므로 골격에서 `getTokenWithPolicyClearingCache`라는 클레임 없이 메서드를 제공했습니다.

이제 이 코드를 작성해 보겠습니다.

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

첫 번째 부분은 친숙해 보여야 합니다. `Settings.plist`에서 제공하는 설정을 로드하고 `data`에 할당합니다. iOS용 ADAL에서 발생한 오류를 처리하는 `ADAuthenticationError`를 설정합니다. 또한 ADAL에 호출을 설정하는 `authContext`를 만듭니다. 이를 *기관*에 전달하여 작업을 시작합니다. 또한 부모 컨트롤러에 `authContext`를 참조로 제공하여 돌아갈 수 있도록 합니다. 또한 `settings.plist`에서 문자열인 `redirectURI`을 예상된 NSURL 형식 ADAL로 변환합니다. 마지막으로 클라이언트에서 서버에, 그리고 반대로 호출을 수행할 수 있는 UUID인 `correlationId`를 설정합니다. 디버깅에 유용합니다.

이제 ADAL에 대한 실제 호출을 가져오면 호출이 iOS 용 ADAL을 이전에 사용했을 때 예상한 대로 변경하는 위치입니다.

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

**scopes** - 서버에서 사용자 로그인에 대해 요청하려는 서버에 전달하는 범위입니다. B2C 미리 보기의 경우 client\_id를 전달합니다. 그러나 나중에 범위를 읽도록 변경됩니다. 이 문서는 업데이트됩니다. **addtionalScopes** - 응용 프로그램에 사용하려는 추가 범위입니다. 나중에 사용됩니다. **clientId** - 포털에서 가져온 응용 프로그램 ID입니다. **redirectURI** - 토큰이 다시 게시될 리디렉션입니다. **identifier** - 사용자를 식별하는 방법으로 캐시에 사용할 수 있는 토큰이 있는지 또는 항상 다른 토큰에 서버를 요청하는지를 확인할 수 있습니다. `ADUserIdentifier`라는 형식에 전달되며 무엇을 ID로 것인지 지정할 수 있습니다. 사용자 이름을 사용해야 합니다. **promptBehavior** - 더 이상 사용되지 않으며 AD\_PROMPT\_ALWAYS가 되어야 합니다. **extraQueryParameters** - URL로 인코딩된 형식인 서버에 전달하려는 추가 데이터입니다. **정책** - 호출하는 정책입니다. 이 연습에 대한 중요한 부분을 게시합니다.

토큰 및 프로필 정보가 있는 `ADAuthenticationResult`를 전달한 completionBlock에서 확인할 수 있습니다(호출에 성공한 경우).

위 코드를 사용하여 제공하는 정책에 대한 토큰을 획득할 수 있습니다. 이 토큰을 사용하여 API를 호출합니다.

#### 서버에 작업 호출 만들기

토큰이 있으므로 승인을 위해 API에 제공해야 합니다.

다시 호출하는 경우 구현해야 할 세 가지 메서드가 있습니다.

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

`getTasksList`는 서버에서 작업을 나타내는 배열을 제공합니다. `addTask` 및 `deleteTask`는 후속 동작을 수행하고 성공하면 TRUE 또는 FALSE를 반환합니다.

우선 `getTaskList`를 작성해 보겠습니다.

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

작업 코드를 설명하는 것은 이 실습의 범위 밖이지만 작업 URL을 사용하는 `craftRequest` 메서드 등 사용자가 유의해야 할 점이 있습니다. 이 메서드는 서버에 대해 수신한 액세스 토큰으로 요청을 만들기 위해 사용합니다. 지금 작성해 보겠습니다.

'samplesWebAPIConnector.m' 파일에 다음 코드를 추가하겠습니다.

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

표시된 바와 같이 웹 URI가 필요하고 토큰을 HTTP의 `Bearer` 헤더를 사용하여 추가한 다음 반환합니다. `getTokenClearingCache` API를 호출하며 처음에는 이상하게 보일 수 있지만 이 호출을 사용하여 캐시에서 토큰을 가져오고 토큰이 여전히 유효한지 확인합니다(getToken* 호출은 ADAL을 요청하여 이를 수행함). 각 호출에 이 코드를 사용합니다. 이제으로 추가 작업 메서드를 만드는 데로 돌아가겠습니다.

`addTask`를 작성해 보겠습니다.

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

동일한 패턴을 따르지만 구현해야 하는 다른(그리고 최종!) 메서드를 소개합니다. `convertTaskToDictionary`은 배열을 사용하고 서버에 전달해야 하는 쿼리 매개 변수를 보다 쉽게 변경하는 사전 개체를 만듭니다. 이 코드는 매우 간단합니다.

```
// Here we have some converstion helpers that allow us to parse passed items in to dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

마지막으로 `deleteTask`를 작성해 보겠습니다.

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

### 로그 웃을 응용 프로그램에 추가합니다.

마지막으로 할 일은 응용 프로그램에 대한 로그아웃 구현입니다. 상당히 간단합니다. 다시 `sampleWebApiConnector.m` 파일 내부입니다.

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

## 9\. 샘플 앱 실행

마지막으로 xCode의 앱을 빌드 및 실행합니다. 앱에 등록 또는 로그인하고 로그인된 사용자에 대한 작업을 만듭니다. 해당 사용자에 대한 작업을 만들면서 로그아웃하고 다른 사용자 권한으로 다시 로그인합니다.

API 가 받는 액세스 토큰에서 사용자의 ID를 추출하므로 API에 사용자 당 작업이 저장됩니다.

참조를 위해 완성된 샘플이 [여기서 .zip으로 제공](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip)되거나 GitHub에서 복제할 수 있습니다.

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## 다음 단계

이제 좀 더 고급 B2C 항목으로 이동할 수 있습니다. 다음 작업을 시도할 수 있습니다.

[Node.js 웹앱에서 node.js Web API 호출 >>]()

[B2C 앱의 UX 사용자 지정 >>]()

<!---HONumber=AcomDC_0128_2016-->