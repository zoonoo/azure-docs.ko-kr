<properties 
	pageTitle="Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증(iOS) | 모바일 개발자 센터" 
	description="iOS 응용 프로그램에서 ADAL을 사용하여 SSO(Single Sign-On) 사용자를 인증하는 방법에 대해 알아봅니다." 
	documentationCenter="ios" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="mahender"/>

# Active Directory 인증 라이브러리 Single Sign-On으로 앱 인증

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##개요

이 자습서에서는 Active Directory 인증 라이브러리를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다.

사용자를 인증하려면 먼저 AAD(Azure Active Directory)에 응용 프로그램을 등록해야 합니다. 이 작업은 다음 두 단계로 수행됩니다. 먼저, 모바일 서비스를 등록하고 그에 대한 사용 권한을 표시합니다. 두 번째로 iOS 앱을 등록하여 해당 사용 권한에 대한 액세스를 부여해야 합니다.


>[AZURE.NOTE]이 자습서는 모바일 서비스를 통해 iOS 앱의 Single Sign-On Azure Active Directory 인증을 수행할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 모바일 서비스를 처음 사용하는 경우 [모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.


##필수 조건


이 자습서를 사용하려면 다음이 필요합니다.

* XCode 4.5 및 iOS 6.0(또는 이후 버전)
* [모바일 서비스 시작] 또는 [데이터 시작] 자습서 완료
* Microsoft Azure 모바일 서비스 SDK
* [iOS용 Active Directory 인증 라이브러리]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##인증을 요구하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##클라이언트 앱에 인증 코드 추가

1. [iOS용 Active Directory 인증 라이브러리]를 다운로드하고 이를 프로젝트에 포함합니다. 또한 ADAL 소스에서 스토리보드를 추가해야 합니다.

2. QSTodoListViewController에서 다음에 ADAL을 포함합니다.

        #import "ADALiOS/ADAuthenticationContext.h"

2. 그 후에 다음 메서드를 추가합니다.

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-AUTHORITY-HERE**를 응용 프로그램이 프로비전된 테넌트의 이름으로 바꿉니다. https://login.windows.net/tenant-name.onmicrosoft.com 형식이어야 합니다. 이 값은 [Azure 관리 포털]에서 Azure Active Directory의 도메인 탭에서 복사할 수 있습니다.

7. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-RESOURCE-URI-HERE**를 모바일 서비스의 **앱 ID URI**로 바꿉니다. [Azure Active Directory에 등록하는 방법] 항목을 따르면 앱 ID URI가 https://todolist.azure-mobile.net/login/aad와(과) 유사합니다.

8. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

9. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-REDIRECT-URI-HERE**를 모바일 서비스에 대한 /login/done 끝점으로 바꿉니다. 이 항목은 https://todolist.azure-mobile.net/login/done와(과) 유사합니다.


3. QSTodoListViewController에서 `ViewDidLoad`을(를) 다음으로 바꿔서 `[self refresh]`을(를) 수정합니다.

        [self loginAndGetData];

##인증을 사용하여 클라이언트 테스트

1. 제품 메뉴에서 실행을 클릭하여 앱을 시작합니다.
2. Azure Active Directory에 대한 로그인 메시지가 표시됩니다.  
3. 앱이 인증하며 todo 항목을 반환합니다.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[데이터 시작]: mobile-services-ios-get-started-data.md
[모바일 서비스 시작]: mobile-services-dotnet-backend-ios-get-started.md
[Azure Active Directory에 등록하는 방법]: mobile-services-how-to-register-active-directory-authentication.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[iOS용 Active Directory 인증 라이브러리]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 

<!---HONumber=August15_HO6-->