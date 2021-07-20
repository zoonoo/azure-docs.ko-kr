---
title: Teams 액세스 토큰 설정 및 만들기
titleSuffix: An Azure Communication Services quickstart
description: Teams 액세스 토큰을 제공하는 서비스 빌드
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e9e58659cfaa5b459a28278362aac002a1a87db5
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113223863"
---
# <a name="quickstart-set-up-and-manage-teams-access-tokens"></a>빠른 시작: Teams 액세스 토큰 설정 및 관리

> [!IMPORTANT]
> 사용자 지정 Teams 엔드포인트 환경을 사용하거나 사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/r/B8p5KqCH19)을 작성합니다.

이 빠른 시작에서는 MSAL 라이브러리를 사용하여 AAD 사용자 토큰을 인증하는 .NET 콘솔 애플리케이션을 빌드합니다. 그런 다음, 이 토큰을 Azure Communication Services Identity SDK를 통해 Teams 액세스 토큰으로 교환합니다. 그러면 Azure Communication Services Calling SDK에서 Teams 액세스 토큰을 사용하여 사용자 지정 Teams 엔드포인트를 빌드할 수 있습니다.

> [!NOTE]
> 프로덕션 환경에서는 교환에 대한 요청이 비밀로 서명되기 때문에 백 엔드 서비스에서 이 교환 메커니즘을 구현하는 것이 좋습니다.


## <a name="prerequisites"></a>필수 구성 요소
- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](./create-communication-resource.md).
- [이 양식](https://forms.office.com/r/B8p5KqCH19)을 통해 사용자 지정 Teams 엔드포인트 환경 사용
- Teams 라이선스가 있는 사용자의 Azure Active Directory

## <a name="introduction"></a>소개

Teams ID는 Azure Active Directory에 있는 테넌트에 바인딩됩니다. 애플리케이션은 동일한 테넌트의 사용자 또는 어떠한 테넌트의 사용자도 사용할 수 있습니다. 이 빠른 시작에서는 가상 회사인 Contoso 및 Fabrikam의 사용자, 개발자 및 관리자를 통해 다중 테넌트 사용 사례를 살펴보겠습니다. 이 사용 사례에서 Contoso는 Fabrikam에 대한 SaaS 솔루션을 빌드하는 회사입니다. 

다음 섹션에서는 관리자, 개발자 및 사용자를 위한 단계를 안내합니다. 다이어그램에 다중 테넌트 사용 사례가 설명되어 있습니다. 단일 테넌트를 사용하는 경우 단일 테넌트의 Contoso 및 Fabrikam에서 모든 단계를 실행합니다.

## <a name="admin-actions"></a>관리 작업

관리자 역할은 AAD에서 확장된 사용 권한을 가집니다. 이 역할의 멤버는 리소스를 프로비전할 수 있으며 Azure Portal의 정보를 읽을 수 있습니다. 다음 다이어그램에서는 관리자가 실행해야 하는 모든 작업을 볼 수 있습니다.

![사용자 지정 Teams 엔드포인트 환경을 사용하도록 설정하는 관리자 작업](./media/teams-identities/teams-identity-admin-overview.png)

1. Contoso의 관리자는 Azure Active Directory에서 기존 *애플리케이션* 을 만들거나 선택합니다. 속성 *지원되는 계정 유형* 은 다른 테넌트의 사용자가 *애플리케이션* 에 인증할 수 있는지 여부를 정의합니다. 속성 *리디렉션 URI* 는 성공적 인증 요청을 Contoso의 *서버* 로 리디렉션합니다.
1. Contoso의 관리자는 *애플리케이션* 의 매니페스트를 Azure Communication Services의 VoIP 권한으로 확장합니다. 
1. Contoso의 관리자는 *애플리케이션* 에 대한 퍼블릭 클라이언트 흐름을 허용합니다.
1. Contoso의 관리자는 선택적으로 업데이트를 수행할 수 있습니다.
1. Contoso의 관리자는 [이 양식](https://forms.office.com/r/B8p5KqCH19)을 통해 환경을 사용하도록 설정합니다.
1. Contoso의 관리자는 교환 요청을 인증하는 데 사용되는 기존 Communication Services를 만들거나 선택합니다. AAD 사용자 토큰은 Teams 액세스 토큰에 대해 교환됩니다. [여기서 새 Azure Communication Services 리소스](./create-communication-resource.md) 만들기에 대한 자세한 내용을 확인할 수 있습니다.
1. Fabrikam의 관리자는 Fabrikam 테넌트의 Azure Communication Services에 대한 새 서비스 주체를 프로비전합니다.
1. Fabrikam의 관리자는 Contoso의 *애플리케이션* 에게 Azure Communication Services VoIP 사용 권한을 부여합니다. 이 단계는 Contoso의 *애플리케이션* 이 확인되지 않은 경우에만 필요합니다.

### <a name="1-create-aad-application-registration-or-select-aad-application"></a>1. AAD 애플리케이션 등록을 만들거나 AAD 애플리케이션을 선택합니다. 

사용자는 Azure Communication Service의 `VoIP` 권한을 사용하여 AAD 애플리케이션에 대해 인증되어야 합니다. 이 빠른 시작에 사용할 기존 애플리케이션이 없는 경우 새 애플리케이션 등록을 만들 수 있습니다. 

환경에 영향을 주는 애플리케이션 설정은 다음과 같습니다.
- 속성 *지원되는 계정 유형* 은 *애플리케이션* 이 단일 테넌트(“이 조직 디렉터리에만 있는 계정”) 또는 다중 테넌트(“모든 조직 디렉터리의 계정”)인지 여부를 정의합니다. 이 시나리오의 경우 다중 테넌트를 사용할 수 있습니다.
- *리디렉션 URI* 는 인증 후 인증 요청이 리디렉션되는 URI를 정의합니다. 이 시나리오의 경우 “퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)”를 사용하고 URI로 “http://localhost”를 채울 수 있습니다.

[여기에서 자세한 설명서를 찾을 수 있습니다](/azure/active-directory/develop/quickstart-register-app#register-an-application). 

*애플리케이션* 이 등록되면 개요에 식별자가 표시됩니다. 이 식별자는 다음 단계인 **애플리케이션(클라이언트) ID** 에서 사용됩니다.

### <a name="2-allow-public-client-flows"></a>2. 퍼블릭 클라이언트 흐름 허용

*애플리케이션* 의 *인증* 창에서 *localhost* 를 가리키는 리디렉션 URI를 사용하여 *퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)* 에 대해 구성된 플랫폼을 볼 수 있습니다. 화면 아래쪽에서 *퍼블릭 클라이언트 흐름 허용* 의 설정/해제를 찾을 수 있습니다(이 빠른 시작에서는 **예** 로 설정됨).

### <a name="3-update-publisher-domain-optional"></a>3. 게시자 도메인 업데이트(선택 사항)
*브랜딩* 창에서 *애플리케이션* 에 대한 게시자 도메인을 업데이트할 수 있습니다. 이는 애플리케이션이 Azure에서 확인된 것으로 표시되는 다중 테넌트 애플리케이션에 유용합니다. 게시자를 확인하는 방법과 애플리케이션의 도메인을 업데이트하는 방법에 대한 자세한 내용은 [여기](/azure/active-directory/develop/howto-configure-publisher-domain)에서 확인할 수 있습니다.

### <a name="4-define-azure-communication-services-voip-permission-in-application"></a>4. 애플리케이션에서 Azure Communication Services의 VoIP 사용 권한 정의

*애플리케이션* 의 세부 사항으로 이동하고 “매니페스트” 창을 선택합니다. 매니페스트에서 *“requiredResourceAccess”* 라고 하는 속성을 찾습니다. *애플리케이션* 의 사용 권한을 정의하는 개체의 배열입니다. 자사 애플리케이션 Azure Communication Services에 대한 VoIP 사용 권한으로 매니페스트를 확장합니다. 다음 개체를 배열에 추가합니다.

> [!NOTE] 
> 애플리케이션 및 사용 권한을 고유하게 식별하기 때문에 코드 조각에서 GUID를 변경하지 마십시오.

```json
{
   "resourceAppId": "1fd5118e-2576-4263-8130-9503064c837a",
   "resourceAccess": [
      {
         "id": "31f1efa3-6f54-4008-ac59-1bf1f0ff9958",
         "type": "Scope"
      }
   ]
}
```

그런 다음, *저장* 단추를 선택하여 변경 내용을 유지합니다. 이제 *API 사용 권한* 창에서 Azure Communication Services - VoIP 사용 권한을 볼 수 있습니다.

### <a name="5-enable-custom-teams-endpoint-experience-for-application"></a>5. *애플리케이션* 에 대한 사용자 지정 Teams 엔드포인트 환경 사용

AAD 관리자는 다음과 같은 [양식](https://forms.office.com/r/B8p5KqCH19)을 채워서 *애플리케이션* 에 대한 사용자 지정 Teams 엔드포인트 환경을 사용하도록 설정합니다.

### <a name="6-create-or-select-communication-services-resource"></a>6. Communication Services 리소스 만들기 또는 선택

Azure Communication Services 리소스는 Teams 액세스 토큰에 대한 AAD 사용자 토큰 교환을 위한 모든 요청을 인증하는 데 사용됩니다. 이 교환은 액세스 키 또는 Azure RBAC로 인증되는 Azure Communication Services ID SDK를 통해 트리거될 수 있습니다. Azure Portal에서 선택 키를 얻거나 *액세스 제어(IAM)* 창을 통해 Azure RBAC를 구성할 수 있습니다.

[새 Communication Services 리소스를 만들려는 경우 이 가이드를 따르세요](./create-communication-resource.md).

### <a name="7-provision-azure-communication-services-service-principal"></a>7. Azure Communication Services 서비스 주체 프로비전

Fabrikam 테넌트의 사용자 지정 Teams 엔드포인트 환경을 사용하도록 설정하려면 Fabrikam의 AAD 관리자가 애플리케이션 ID가 *1fd5118e-2576-4263-8130-9503064c837a* 인 Azure Communication Services라는 서비스 주체를 프로비전해야 합니다. Azure Active Directory의 Enterprise 애플리케이션 창에 이 애플리케이션이 표시되지 않으면 수동으로 추가해야 합니다.

Fabrikam의 AAD 관리자는 PowerShell을 통해 Azure 테넌트에 연결합니다. 

> [!NOTE]
> [Tenant_ID]를 AAD의 개요 페이지에 있는 Azure Portal에서 찾을 수 있는 테넌트의 ID로 바꿉니다.

```azurepowershell
Connect-AzureAD -TenantId "[Tenant_ID]"
```

명령을 찾을 수 없는 경우 AzureAD 모듈은 PowerShell에 설치되지 않습니다. PowerShell을 닫고 관리 권한으로 실행합니다. 그런 다음, Azure-AD 패키지를 다음 명령으로 설치할 수 있습니다.

```azurepowershell
Install-Module AzureAD
```

Azure에 연결하고 인증한 후 다음 명령을 실행하여 Communication Services의 서비스 주체를 프로비전합니다. 

> [!NOTE]
> 매개 변수 AppId는 자사 애플리케이션 Azure Communication Services를 참조합니다. 이 값은 변경하지 마세요.

```azurepowershell
New-AzureADServicePrincipal -AppId "1fd5118e-2576-4263-8130-9503064c837a"
```

### <a name="8-provide-admin-consent"></a>8. 관리자 동의 제공

Contoso의 *애플리케이션* 이 확인되지 않은 경우 AAD 관리자는 Azure Communication Services의 VoIP 사용 권한에 대해 Contoso의 *애플리케이션* 에 사용 권한을 부여해야 합니다. Fabrikam의 AAD 관리자는 고유한 링크를 통해 동의를 제공합니다. 관리자 동의 링크를 구성하려면 다음 지침을 따르세요.

1. 다음 링크를 사용합니다. *https://login.microsoftonline.com/{Tenant_ID}/adminconsent?client_id={Application_ID}*
1. {Tenant_ID}를 Fabrikam의 테넌트 ID로 바꿉니다.
1. {Application_ID}를 Contoso의 애플리케이션 ID로 바꿉니다.
1. Fabrikam의 AAD 관리자가 브라우저에서 링크로 이동합니다. 
1. Fabrikam의 AAD 관리자가 로그인하고 조직을 대신하여 사용 권한을 부여합니다.

동의가 부여되면 Fabrikam의 테넌트에 있는 Contoso의 *애플리케이션* 의 서비스 주체가 만들어집니다. Fabrikam의 관리자는 다음과 같이 AAD에서 동의를 검토할 수 있습니다.

1. Azure Portal에 관리자로 로그인합니다.
1. Azure Active Directory로 이동합니다.
1. “Enterprise 애플리케이션” 창으로 이동합니다.
1. “애플리케이션 유형” 필터를 “모든 애플리케이션”으로 설정합니다.
1. 애플리케이션을 필터링할 필드에서 Contoso의 애플리케이션의 이름을 삽입합니다.
1. “적용”을 선택하여 결과를 필터링합니다.
1. 필수 이름을 사용하여 서비스 주체를 선택합니다. 
1. *사용 권한* 창으로 이동합니다.

이제 Azure Communication Services의 VoIP 사용 권한이 *{Directory_name}에 대해 허가됨* 상태가 된 것을 확인할 수 있습니다.

## <a name="developer-actions"></a>개발자 작업

Contoso의 개발자는 사용자 인증을 위해 *클라이언트 애플리케이션* 을 설정해야 합니다. 그런 다음, 개발자는 리디렉션 후에 AAD 사용자 토큰을 처리하기 위해 백 엔드 *서버* 에서 엔드포인트를 만들어야 합니다. AAD 사용자 토큰을 받으면 Teams 액세스 토큰에 대해 교환되고 *클라이언트 애플리케이션* 으로 다시 반환됩니다. 개발자에게 필요한 작업은 다음 다이어그램에 나와 있습니다.

![사용자 지정 Teams 엔드포인트 환경을 사용하도록 설정하는 개발자 작업](./media/teams-identities/teams-identity-developer-overview.png)

1. Contoso의 개발자는 관리자가 Azure Communication Services의 VoIP 사용 권한에 대해 이전 단계에서 만든 *애플리케이션* 에 대한 사용자를 인증하도록 MSAL 라이브러리를 구성합니다.
1. Contoso의 개발자는 ACS ID SDK를 초기화하고 SDK를 통해 Teams의 액세스 토큰에 대해 들어오는 AAD 사용자 토큰을 교환합니다. 그러면 Teams의 액세스 토큰이 *클라이언트 애플리케이션* 에 반환됩니다.

MSAL(Microsoft 인증 라이브러리)을 통해 개발자는 사용자를 인증하고 보안 웹 API에 액세스하기 위해 Microsoft ID 플랫폼 엔드포인트에서 AAD 사용자 토큰을 획득할 수 있습니다. Azure Communication Services에 대한 보안 액세스를 제공하는 데 사용할 수 있습니다. MSAL은 .NET, JavaScript, Java, Python, Android 및 iOS를 비롯한 다양한 애플리케이션 아키텍처 및 플랫폼을 지원합니다.

다른 환경을 설정하는 방법에 대한 자세한 내용은 공용 설명서에서 확인할 수 있습니다. [MSAL(Microsoft 인증 라이브러리) 개요](/azure/active-directory/develop/msal-overview)

> [!NOTE]
> 다음 섹션에서는 .NET에서 콘솔 애플리케이션을 위해 Teams 액세스 토큰에 대해 AAD 액세스 토큰을 교환하는 방법에 대해 설명합니다.

### <a name="create-new-application"></a>새 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 dotnet new 명령을 사용하여 *TeamsAccessTokensQuickstart* 라는 새 콘솔 앱을 만듭니다. 이 명령은 *Program.cs* 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o TeamsAccessTokensQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 dotnet build 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd TeamsAccessTokensQuickstart
dotnet build
```
#### <a name="install-the-package"></a>패키지 설치
애플리케이션 디렉터리에 있는 동안 dotnet add package 명령을 사용하여 .NET 패키지용 Azure Communication Services ID 라이브러리를 설치합니다.

```console
dotnet add package Azure.Communication.Identity
dotnet add package Microsoft.Identity.Client
```

#### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

- 텍스트 편집기에서 Program.cs 파일 열기
- using 지시문을 추가하여 다음 네임스페이스를 포함합니다. 
    - Azure.Communication
    - Azure.Communication.Identity
    - Microsoft.Identity.Client
- 비동기 코드를 지원하도록 Main 메서드 선언을 업데이트합니다.

시작하려면 다음 코드를 사용합니다.

```csharp
using System;
using System.Text;
using Azure.Communication;
using Azure.Communication.Identity;
using Microsoft.Identity.Client;

namespace TeamsAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services – Teams access tokens quickstart");

            // Quickstart code goes here
        }
    }
}
```

### <a name="1-receive-aad-user-token-via-msal-library"></a>1. MSAL 라이브러리를 통해 AAD 사용자 토큰 수신

MSAL 라이브러리를 사용하여 Azure Communication Services의 VoIP 사용 권한으로 Contoso의 *애플리케이션* 에 대한 AAD 사용자를 인증합니다. 퍼블릭 클라우드(*매개 변수 authority*)에서 Contoso의 *애플리케이션*(*매개 변수 applicationId*)에 대한 클라이언트를 구성합니다. AAD 사용자 토큰은 리디렉션 URI(*매개 변수 redirectUri*)로 반환됩니다. 자격 증명은 기본 브라우저에서 열리는 대화형 팝업 창에서 가져옵니다.

> [!NOTE] 
> 리디렉션 URI는 *애플리케이션* 에 정의된 값과 일치해야 합니다. 리디렉션 URI를 구성하는 방법은 관리자 가이드의 첫 번째 단계를 확인하세요.

```csharp
const string applicationId = "Contoso's_Application_ID";
const string authority = "https://login.microsoftonline.com/common";
const string redirectUri = "http://localhost";

var client = PublicClientApplicationBuilder
                .Create(applicationId)
                .WithAuthority(authority)
                .WithRedirectUri(redirectUri)
                .Build();

const string scope = "https://auth.msft.communication.azure.com/VoIP";

var aadUserToken = await client.AcquireTokenInteractive(new[] { scope }).ExecuteAsync();

Console.WriteLine("\nAuthenticated user: " + aadUserToken.Account.Username);
Console.WriteLine("AAD user token expires on: " + aadUserToken.ExpiresOn);
```

이제 변수 *aadUserToken* 이 교환에 사용되는 유효한 Azure Active Directory 사용자 토큰을 전달합니다.

### <a name="2-exchange-aad-user-token-for-teams-access-token"></a>2. Teams 액세스 토큰에 대한 AAD 사용자 토큰 교환

유효한 AAD 사용자 토큰은 Azure Communication Services의 VoIP 사용 권한을 통해 타사 애플리케이션의 AAD에 대해 사용자를 인증합니다. 다음 코드는 Teams 액세스 토큰에 대한 AAD 사용자 토큰 교환을 용이하게 하는 데 사용되는 ACS ID SDK입니다.

> [!NOTE]
> “&lt;Connection-String&gt;” 값을 유효한 연결 문자열로 바꾸거나 인증을 위해 Azure RBAC를 사용합니다. 자세한 내용은 [이 빠른 시작](./access-tokens.md)에서 확인할 수 있습니다.

```csharp
var identityClient = new CommunicationIdentityClient("<Connection-String>");
var teamsAccessToken = identityClient.ExchangeTeamsToken(aadUserToken.AccessToken);

Console.WriteLine("\nTeams access token expires on: " + teamsAccessToken.Value.ExpiresOn);
```

요구 사항에 정의된 모든 조건이 충족되는 경우 24시간 동안 유효한 Teams 액세스 토큰을 가져올 수 있습니다.

#### <a name="run-the-code"></a>코드 실행
dotnet run 명령을 사용하여 응용 프로그램 디렉터리에서 응용 프로그램을 실행합니다.

```console
dotnet run
```

앱의 출력은 완료된 각 작업을 설명합니다.

```console
Azure Communication Services - Teams access tokens quickstart

Authenticated user: john.smith@contoso.com
AAD user token expires on: 6/10/2021 10:13:17 AM +00:00

Teams access token expires on: 6/11/2021 9:13:18 AM +00:00
```

## <a name="user-actions"></a>사용자 작업

Contoso의 *애플리케이션* 에 대한 Fabrikam의 사용자를 나타냅니다. 사용자 환경은 다음 다이어그램에 표시되어 있습니다.

![사용자 지정 Teams 엔드포인트 환경을 사용하도록 설정하는 사용자 작업](./media/teams-identities/teams-identity-user-overview.png)

1. Fabrikam의 사용자는 Contoso의 *클라이언트 애플리케이션* 을 사용하며, 인증하라는 메시지가 표시됩니다.
1. Contoso의 *클라이언트 애플리케이션* 은 MSAL 라이브러리를 사용하여 Azure Communication Services의 VoIP 사용 권한이 있는 Contoso의 *애플리케이션* 에 대한 Fabrikam의 Azure Active Directory 테넌트에 대해 사용자를 인증합니다. 
1. MSAL 및 Contoso의 *애플리케이션* 에서 속성 *리디렉션 URI* 에 정의된 대로 인증이 *서버* 로 리디렉션됩니다.
1. Contoso의 *서버* 는 ACS ID SDK를 사용하여 Teams의 액세스 토큰에 대한 AAD 사용자 토큰을 교환하고 *클라이언트 애플리케이션* 에 Teams의 액세스 토큰을 반환합니다.

*클라이언트 애플리케이션* 에서 유효한 Teams의 액세스 토큰을 사용하여 개발자는 ACS 호출 SDK를 통합하고 사용자 지정 Teams 엔드포인트를 빌드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure Active Directory에서 애플리케이션 만들기 및 구성
> * MSAL 라이브러리를 사용하여 Azure Active Directory 사용자 토큰 발행
> * ACS ID SDK를 사용하여 Teams 액세스 토큰에 대해 Azure Active Directory 사용자 토큰 교환

다음 문서는 사용자에게 유용할 수 있습니다.

- [사용자 지정 Teams 엔드포인트](../concepts/teams-endpoint.md) 알아보기
- [Teams 상호 운용성](../concepts/teams-interop.md)에 대해 알아보기
