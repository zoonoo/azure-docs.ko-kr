---
title: 전국 클라우드 앱에서 MSAL 사용 | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)을 사용하면 응용 프로그램 개발자가 보안 웹 API를 호출하기 위해 토큰을 획득할 수 있습니다. 이러한 웹 API는 Microsoft 그래프, 다른 Microsoft API, 파트너 웹 API 또는 사용자 고유의 웹 API일 수 있습니다. MSAL은 다수의 애플리케이션 아키텍처와 플랫폼을 지원합니다.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f3bb4dd1c564e5f6c4a8ee1bb5bf7424a74a339e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533992"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>국가 클라우드 환경에서 MSAL 사용

주권 클라우드라고도 하는 [국가 클라우드는](authentication-national-cloud.md)Azure의 물리적으로 격리된 인스턴스입니다. 이러한 Azure 리전은 데이터 상주, 주권 및 규정 준수 요구 사항이 지리적 경계 내에서 준수되도록 하는 데 도움이 됩니다.

Microsoft 전 세계 클라우드 외에도 MSAL(Microsoft 인증 라이브러리)을 사용하면 국가 클라우드의 응용 프로그램 개발자가 토큰을 획득하여 보안 웹 API를 인증하고 호출할 수 있습니다. 이러한 웹 API는 Microsoft 그래프 또는 기타 Microsoft API일 수 있습니다.

전역 클라우드를 포함하여 Azure Active Directory(Azure AD)는 다음과 같은 국가 클라우드에 배포됩니다.  

- Azure Government
- Azure China 21Vianet
- Azure Germany

이 가이드에서는 회사 및 학교 계정에 로그인하고, 액세스 토큰을 받고, Azure [정부 클라우드](https://azure.microsoft.com/global-infrastructure/government/) 환경에서 Microsoft 그래프 API를 호출하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 이러한 필수 구성 조건을 충족하는지 확인하십시오.

### <a name="choose-the-appropriate-identities"></a>적절한 ID 선택

[Azure 정부](https://docs.microsoft.com/azure/azure-government/) 응용 프로그램은 Azure AD 정부 ID 및 Azure AD Public ID를 사용하여 사용자를 인증할 수 있습니다. 이러한 ID를 사용할 수 있으므로 시나리오에 대해 선택해야 하는 권한 끝점을 결정해야 합니다.

- Azure AD Public: 조직에 Office 365(공용 또는 GCC) 또는 다른 응용 프로그램을 지원하는 Azure AD 공용 테넌트가 이미 있는 경우 일반적으로 사용됩니다.
- Azure AD 정부: 조직에 Office 365(GCC High 또는 DoD)를 지원하기 위해 Azure AD 정부 테넌트가 이미 있거나 Azure AD 정부에서 새 테넌트를 만드는 경우 일반적으로 사용됩니다.

결정한 후에는 앱 등록을 수행하는 위치가 특별히 고려됩니다. Azure 정부 응용 프로그램에 대한 Azure AD 공용 ID를 선택하는 경우 Azure AD 공용 테넌트에 응용 프로그램을 등록해야 합니다.

### <a name="get-an-azure-government-subscription"></a>Azure 정부 구독 받기

Azure 정부 구독을 받으려면 Azure 정부 에서 [구독 관리 및 연결 을](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)참조하십시오.

Azure 정부 구독이 없는 경우 시작하기 전에 [무료 계정을](https://azure.microsoft.com/global-infrastructure/government/request/) 만드세요.

특정 프로그래밍 언어가 있는 국가 클라우드 사용에 대한 자세한 내용은 해당 언어와 일치하는 탭을 선택합니다.

## <a name="net"></a>[.NET](#tab/donet)

MSAL.NET 사용하여 사용자에 로그인하고 토큰을 획득하며 국가 클라우드에서 Microsoft 그래프 API를 호출할 수 있습니다.

다음 자습서에서는 .NET Core 2.2 MVC 웹 앱을 빌드하는 방법을 보여 줍니다. 앱은 OpenID Connect를 사용하여 국가 클라우드에 속한 조직의 직장 및 학교 계정으로 사용자를 로그인합니다.

- 사용자를 로그인하고 토큰을 획득하려면 이 자습서를 따르십시오: [Microsoft ID 플랫폼을 사용하여 주권 클라우드에서 ASP.NET 코어 웹 앱 로그인 사용자 빌드.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)
- Microsoft 그래프 API를 호출하려면 다음 자습서를 따르십시오: [Microsoft ID 플랫폼을 사용하여 Microsoft 내셔널 클라우드에서 사용자 로그인을 사용하여 로그인하는 사용자를 대신하여 ASP.NET 코어 2.x 웹 앱에서 Microsoft 그래프 API를 호출합니다.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)

## <a name="javascript"></a>[JavaScript](#tab/javascript)

주권 클라우드에 대한 MSAL.js 응용 프로그램을 사용하려면 다음을 수행하십시오.

### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록

1. [Azure Portal](https://portal.azure.us/)에 로그인합니다.

   다른 국가 클라우드에 대한 Azure 포털 끝점을 찾으려면 [앱 등록 끝점을](authentication-national-cloud.md#app-registration-endpoints)참조하십시오.

1. 계정에서 두 개 이상의 테넌트에 대한 액세스 권한을 부여하는 경우 오른쪽 상단 모서리에서 계정을 선택하고 포털 세션을 원하는 Azure AD 테넌트로 설정합니다.
1. 개발자를 위한 Microsoft ID 플랫폼의 [앱 등록](https://aka.ms/ra/ff) 페이지로 이동합니다.
1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
1. **지원되는 계정 유형에서** **모든 조직 디렉터리에서 계정을**선택합니다.
1. URI **리디렉션** 섹션에서 **웹** 플랫폼을 선택하고 웹 서버를 기반으로 응용 프로그램의 URL로 값을 설정합니다. Visual Studio 및 Node에서 리디렉션 URL을 설정하고 가져오는 방법에 대한 지침은 다음 섹션을 참조하십시오.
1. **등록**을 선택합니다.
1. 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
1. 이 자습서에서는 [암시적 권한 부여 흐름을](v2-oauth2-implicit-grant-flow.md)사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 창에서 **인증**을 선택합니다.
1. **고급 설정**의 **암시적 허용**에서 **ID 토큰** 및 **액세스 토큰** 확인란을 선택합니다. ID 토큰 및 액세스 토큰은 이 앱이 사용자를 로그인하고 API를 호출해야 하기 때문에 필요합니다.
1. **저장**을 선택합니다.

### <a name="step-2--set-up-your-web-server-or-project"></a>2단계: 웹 서버 또는 프로젝트 설정

- 노드와 같은 로컬 웹 서버에 대한 [프로젝트 파일을 다운로드합니다.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

  또는

- [비주얼 스튜디오 프로젝트를 다운로드합니다.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

그런 다음 [JavaScript SPA를 구성하여](#step-4-configure-your-javascript-spa) 실행하기 전에 코드 샘플을 구성하도록 건너뜁니다.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>3단계: Microsoft 인증 라이브러리를 사용하여 사용자 로그인

[JavaScript 자습서의](tutorial-v2-javascript-spa.md#create-your-project) 단계를 수행하여 프로젝트를 만들고 MSAL과 통합하여 사용자에 로그인합니다.

### <a name="step-4-configure-your-javascript-spa"></a>4 단계 : 자바 스크립트 SPA 구성

프로젝트 설정 중에 생성된 `index.html` 파일에서 애플리케이션 등록 정보를 추가합니다. 맨 위에 있는 다음 코드를 `index.html` 파일 본문의 `<script></script>` 태그 내에 추가합니다.

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

해당 코드에서 다음을 수행합니다.

- `Enter_the_Application_Id_here`은 등록한 **응용 프로그램의 응용 프로그램(클라이언트) ID** 값입니다.
- `Enter_the_Tenant_Info_Here`다음 옵션 중 하나로 설정됩니다.
    - 응용 프로그램이 **이 조직 디렉터리에서 계정을**지원하는 경우 이 값을 테넌트 ID 또는 테넌트 이름(예: contoso.microsoft.com)으로 바꿉니다.
    - 응용 프로그램이 **모든 조직 디렉터리에서 계정을**지원하는 `organizations`경우 이 값을 로 바꿉습니다.

    모든 국가 클라우드에 대한 인증 끝점을 찾으려면 [Azure AD 인증 끝점을](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)참조하십시오.

    > [!NOTE]
    > 개인 Microsoft 계정은 국가 클라우드에서 지원되지 않습니다.

- `graphEndpoint`는 미국 정부의 마이크로소프트 클라우드용 마이크로소프트 그래프 엔드포인트입니다.

   모든 국가 클라우드에 대한 Microsoft 그래프 끝점을 찾으려면 [전국 클라우드에서 Microsoft 그래프 끝점을](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)참조하십시오.

## <a name="python"></a>[Python](#tab/python)

주권 클라우드에 대한 MSAL 파이썬 응용 프로그램을 사용하려면 다음을 수행하십시오.

- 클라우드에 따라 특정 포털에 응용 프로그램을 등록합니다. 포털을 선택하는 방법에 대한 자세한 내용은 [앱 등록 끝점을](authentication-national-cloud.md#app-registration-endpoints) 참조하십시오.
- 다음에 언급된 클라우드에 따라 구성을 약간 변경하여 리포지토리의 [샘플을](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) 사용합니다.
- 응용 프로그램을 등록한 클라우드에 따라 특정 권한을 사용합니다. 다른 클라우드에 대한 기관에 대한 자세한 내용은 [Azure AD 인증 끝점을](authentication-national-cloud.md#azure-ad-authentication-endpoints)참조하십시오.

    예제 권한은 다음과 같습니다.

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```

- Microsoft 그래프를 호출하려면 사용 중인 클라우드에 따라 특정 그래프 끝점 URL이 필요합니다. 모든 국가 클라우드에 대한 Microsoft 그래프 끝점을 찾으려면 [Microsoft 그래프 및 그래프 탐색기 서비스 루트 끝점을](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)참조하십시오.

    다음은 범위가 있는 그래프 끝점의 예입니다.

    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```

## <a name="java"></a>[Java](#tab/java)

주권 클라우드에 대한 Java 응용 프로그램에 대한 MSAL을 사용하려면 다음을 수행하십시오.

- 클라우드에 따라 특정 포털에 응용 프로그램을 등록합니다. 포털을 선택하는 방법에 대한 자세한 내용은 [앱 등록 끝점을](authentication-national-cloud.md#app-registration-endpoints) 참조하십시오.
- 다음에 언급된 클라우드에 따라 구성을 약간 변경하여 리포지토리의 [샘플을](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) 사용합니다.
- 응용 프로그램을 등록한 클라우드에 따라 특정 권한을 사용합니다. 다른 클라우드에 대한 기관에 대한 자세한 내용은 [Azure AD 인증 끝점을](authentication-national-cloud.md#azure-ad-authentication-endpoints)참조하십시오.

예제 권한은 다음과 같습니다.

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Microsoft 그래프를 호출하려면 사용 중인 클라우드에 따라 특정 그래프 끝점 URL이 필요합니다. 모든 국가 클라우드에 대한 Microsoft 그래프 끝점을 찾으려면 [Microsoft 그래프 및 그래프 탐색기 서비스 루트 끝점을](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)참조하십시오.

다음은 범위가 있는 그래프 끝점의 예입니다.

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Objective-C](#tab/objc)

iOS 및 macOS용 MSAL은 국가 클라우드에서 토큰을 획득하는 데 `MSALPublicClientApplication`사용할 수 있지만 을 만들 때 추가 구성이 필요합니다.

예를 들어, 응용 프로그램을 국가 클라우드(여기 미국 정부)에서 다중 테넌트 응용 프로그램으로 지정하려면 다음을 작성할 수 있습니다.

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

iOS 및 macOS용 MSAL은 국가 클라우드에서 토큰을 획득하는 데 `MSALPublicClientApplication`사용할 수 있지만 을 만들 때 추가 구성이 필요합니다.

예를 들어, 응용 프로그램을 국가 클라우드(여기 미국 정부)에서 다중 테넌트 응용 프로그램으로 지정하려면 다음을 작성할 수 있습니다.

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

- [내셔널 클라우드의 인증](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure 독일](https://docs.microsoft.com/azure/germany/)
