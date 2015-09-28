<properties
        pageTitle="Azure Active Directory Single Sign-On으로 iOS 앱의 사용자 인증"
        description="Active Directory 인증 라이브러리를 통해 사용자를 iOS 응용 프로그램으로 로그인하는 방법을 알아봅니다."
        documentationCenter="Mobile"
        authors="mattchenderson"
        services="app-service\mobile"
        manager="dwrede" />

<tags ms.service="app-service-mobile"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios"
ms.devlang="objective-c"
ms.topic="article"
ms.date="09/14/2015"
ms.author="mahender" />

# iOS 앱에 Azure Active Directory Single Sign-On 추가

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

이 자습서에서는 Active Directory 인증 라이브러리(ADAL)를 사용하여 빠른 시작 프로젝트에 인증을 추가합니다. [앱에 인증 추가]자습서에 나와 있는 것처럼 모바일 앱 SDK만 사용하여적은 구성으로 인증을 활성화할 수도 있습니다. ADAL에서 이 항목을 사용하면 최종 사용자를 위해 보다 통합된 인증 환경을 제공하고, ADAL은 다른 AAD 보호 리소스에 액세스하기 위해 보다 다양한 기능을 제공합니다.

ADAL을 사용하여 사용자를 인증하려면 먼저 AAD(Azure Active Directory) 테넌트에 응용 프로그램을 등록해야 합니다. 이 작업은 다음 두 단계로 수행됩니다. 먼저, 앱 서비스를 등록하고 해당 사용 권한을 표시해야 합니다. 두 번째로 iOS 앱을 등록하고 이러한 사용 권한에 대한 액세스 권한을 부여해야 합니다.

이 자습서를 사용하려면 다음이 필요합니다.

* XCode 4.5 및 iOS 6.0(또는 이후 버전)
* [모바일 앱 시작 자습서] 완료
* Microsoft Azure 모바일 서비스 SDK
* [iOS용 Active Directory 인증 라이브러리]

##<a name="review"></a>서버 프로젝트 구성 검토(옵션)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

## <a name="register-application"></a>Azure Active Directory에 응용 프로그램 등록

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>인증을 요구하도록 응용 프로그램 구성

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>Active Directory 인증 라이브러리에 대한 참조 추가

1. [iOS용 Active Directory 인증 라이브러리]를 다운로드합니다.

2. Xcode Navigator에서 프로젝트를 선택하고 **파일**을 클릭한 다음 **파일 추가...**를 선택합니다. 라이브러리를 다운로드한 위치로 이동한 다음 **ADALiOS.xcodeproj**를 선택합니다.

3. 프로젝트를 다시 선택하고 **빌드 설정** 탭을 엽니다. **연결** 섹션으로 이동한 다음 **기타 링커 플래그**에 `-ObjC`를 추가합니다.

4. **빌드 단계** 탭을 선택합니다. **대상 종속성** 아래에 `ADALiOS`를 추가합니다.

5. **이진과 라이브러리 연결** 아래에 `libADALiOS.a`를 추가합니다.

이제 프로젝트에서 Active Directory 인증 라이브러리를 참조할 수 있습니다.

## <a name="add-authentication-code"></a>클라이언트 앱에 인증 코드 추가

2. QSTodoListViewController에서 다음에 ADAL을 포함합니다.

        #import "ADALiOS/ADAuthenticationContext.h"

3. 그 후에 다음 메서드를 추가합니다.

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

4. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-AUTHORITY-HERE**를 응용 프로그램이 프로비전된 테넌트의 이름으로 바꿉니다. https://login.windows.net/tenant-name.onmicrosoft.com 형식이어야 합니다. 이 값은 [Azure 관리 포털]에서 Azure Active Directory의 도메인 탭에서 복사할 수 있습니다.

5. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-RESOURCE-URI-HERE**를 모바일 앱에 대한 **앱 ID URI**로 바꿉니다. [Azure Active Directory로 모바일 앱을 구성하는 방법] 항목을 따른 경우 앱 ID URI는 https://contosogateway.azurewebsites.net/login/aad와 유사해야 합니다.

6. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

7. 위의 `loginAndGetData` 메서드에 대한 코드에서 **INSERT-REDIRECT-URI-HERE**를 앱 서비스 게이트웨이에 대한 /login/done 끝점으로 바꿉니다. 이 항목은 https://contosogateway.azurewebsites.net/login/done과 유사합니다.

8. QSTodoListViewController에서 `[self refresh]`를 다음으로 바꿔서 `viewDidLoad`를 수정합니다.

        [self loginAndGetData];

## <a name="test-client"></a>인증을 사용하여 클라이언트 테스트

1. 제품 메뉴에서 실행을 클릭하여 앱을 시작합니다.
2. Azure Active Directory에 대한 로그인 메시지가 표시됩니다.  
3. 앱이 인증하며 todo 항목을 반환합니다.

<!-- URLs. -->
[Azure Active Directory로 모바일 앱을 구성하는 방법]: app-service-mobile-how-to-configure-active-directory-authentication-preview.md
[Azure 관리 포털]: https://manage.windowsazure.com/
[iOS용 Active Directory 인증 라이브러리]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [모바일 앱 시작 자습서]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
 [앱에 인증 추가]: app-service-mobile-dotnet-backend-ios-get-started-users-preview.md

<!---HONumber=Sept15_HO3-->