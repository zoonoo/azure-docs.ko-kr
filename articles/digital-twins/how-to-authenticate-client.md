---
title: 클라이언트 애플리케이션 인증
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 서비스에 대해 클라이언트 응용 프로그램을 인증 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85390825"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Azure Digital Twins를 사용 하 여 클라이언트 응용 프로그램 인증

[Azure Digital Twins 인스턴스를 만든](how-to-set-up-instance.md)후에는 인스턴스와 상호 작용 하는 데 사용할 클라이언트 응용 프로그램을 만들 수 있습니다. 스타터 클라이언트 프로젝트를 설정 하 고 나면이 문서에서는 Azure Digital Twins 인스턴스를 사용 하 여 해당 클라이언트 응용 프로그램을 올바르게 인증 하는 방법을 보여 줍니다.

이 반환은 두 단계로 수행됩니다.
1. 앱 등록 만들기
2. 클라이언트 응용 프로그램에서 인증 코드 작성

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>앱 등록 만들기

클라이언트 응용 프로그램에서 Azure Digital Twins에 대해 인증 하려면 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)에서 **앱 등록** 을 설정 해야 합니다.

이 앱 등록에서는 [Azure Digital Twins api](how-to-use-apis-sdks.md)에 대 한 액세스 권한을 구성 합니다. 클라이언트 앱은 앱 등록에 대해 인증 하며, 그 결과에는 Api에 대해 구성 된 액세스 권한이 부여 됩니다.

앱 등록을 만들려면 Azure Digital Twins Api에 대 한 리소스 Id와 API에 대 한 기준 권한을 제공 해야 합니다. 작업 디렉터리에서 새 파일을 열고 다음 JSON 코드 조각을 입력 하 여 이러한 세부 정보를 구성 합니다. 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

이 파일을 *manifest.js*로 저장 합니다.

> [!NOTE] 
> "친숙 한" 사람이 인식할 수 있는 문자열을 `https://digitaltwins.azure.net` GUID 대신 Azure 디지털 Twins 리소스 앱 ID에 사용할 수 있는 몇 가지 위치가 있습니다 `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . 예를 들어이 설명서의 많은 예제에서는 MSAL 라이브러리를 사용 하 여 인증을 사용 하 고이에 대 한 문자열을 사용할 수 있습니다. 그러나 앱 등록을 만드는이 단계에서 ID의 GUID 형식은 위에 표시 된 것 처럼 필요 합니다. 

Cloud Shell 창에서 "파일 업로드/다운로드" 아이콘을 클릭 하 고 "업로드"를 선택 합니다.

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="업로드 옵션 선택 항목을 보여 주는 Cloud Shell 창":::
방금 만든 *manifest.js* 로 이동 하 고 "열기"를 누릅니다.

다음 명령을 실행 하 여 앱 등록을 만듭니다 (필요에 따라 자리 표시자 바꾸기).

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

이 명령의 출력은 다음과 같습니다.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="새 AAD 앱 등록":::

앱 등록을 만든 후 [이 링크](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 에 따라 AZURE PORTAL에서 AAD 앱 등록 개요 페이지로 이동 합니다.

이 개요에서는 목록에서 방금 만든 앱 등록을 선택 합니다. 그러면 다음과 같은 페이지에서 세부 정보를 엽니다.

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure Portal: 인증 Id":::

**페이지에** 표시 된 *응용 프로그램 (클라이언트) id* 및 *디렉터리 (테 넌 트) id* 를 기록해 둡니다. 나중에 이러한 값을 사용 하 여 Azure Digital Twins Api에 대해 클라이언트 앱을 인증 합니다.

> [!NOTE]
> 시나리오에 따라 앱 등록을 추가로 변경 해야 할 수 있습니다. 다음은 충족 해야 할 몇 가지 일반적인 요구 사항입니다.
> * 공용 클라이언트 액세스 활성화
> * 웹 및 데스크톱 액세스에 대 한 특정 회신 Url 설정
> * 암시적 OAuth2 인증 흐름 허용
> * Live, Xbox 또는 Hotmail과 같은 Microsoft 계정를 사용 하 여 Azure 구독을 만든 경우 개인 계정을 지원 하도록 앱 등록에 *signInAudience* 를 설정 해야 합니다.
> 이러한 설정을 설정 하는 가장 쉬운 방법은 [Azure Portal](https://portal.azure.com/)를 사용 하는 것입니다. 이 프로세스에 대 한 자세한 내용은 [Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](https://docs.microsoft.com/graph/auth-register-app-v2)을 참조 하세요.

## <a name="write-client-app-authentication-code-net-c-sdk"></a>클라이언트 앱 인증 코드 작성: .NET (c #) SDK

이 섹션에서는 .NET (c #) SDK를 사용 하 여 인증 프로세스를 완료 하기 위해 클라이언트 응용 프로그램에 포함 해야 하는 코드에 대해 설명 합니다.
Azure Digital Twins c # SDK는 Azure SDK for .NET의 일부입니다. [.Net 용 Azure IoT 디지털 쌍 클라이언트 라이브러리에](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)있습니다.

### <a name="prerequisites"></a>사전 요구 사항

아직 시작 클라이언트 앱 프로젝트를 설정 하지 않은 경우이 자습서에 사용할 기본 .NET 프로젝트를 만듭니다.

.NET SDK를 사용 하려면 프로젝트에 다음 패키지를 포함 해야 합니다.
* `Azure.DigitalTwins.Core`(버전 `1.0.0-preview.2` )
* `Azure.Identity`

선택한 도구에 따라 Visual Studio 패키지 관리자 또는 명령줄 도구를 사용 하 여이 작업을 수행할 수 있습니다 `dotnet` . 

### <a name="authentication-and-client-creation-net"></a>인증 및 클라이언트 만들기: .NET

.NET SDK를 사용 하 여 인증 하려면 [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) library에서 정의한 자격 증명 가져오기 방법 중 하나를 사용 합니다.

일반적으로 사용 되는 두 가지는 다음과 같습니다. 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). 이 메서드는 대화형 응용 프로그램을 위한 것 이며 인증을 위해 웹 브라우저를 표시 합니다.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). 이 방법은 [관리 id (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)가 필요한 경우 (예: Azure Functions 사용 하는 경우에 유용 합니다. 

또한 다음 using 문을 사용 해야 합니다.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

대화형 브라우저 자격 증명을 사용 하 여 인증 된 SDK 클라이언트를 만들려면 다음 코드를 추가 합니다.

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> 위와 같이 클라이언트 ID, 테 넌 트 ID 및 인스턴스 URL을 코드에 직접 저장할 수 있지만, 코드에서 구성 파일 또는 환경 변수 로부터 이러한 값을 가져오는 것이 좋습니다.

Azure 함수에서 다음과 같이 관리 되는 id 자격 증명을 사용할 수 있습니다.

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

방법: 함수 컨텍스트에서 중요 한 구성 옵션 중 일부를 설명 하는 보다 완전 한 예제에 대 한 [데이터 처리를 위한 Azure 함수 설정을](how-to-create-azure-function.md) 참조 하세요.

또한 함수에서 인증을 사용 하려면 다음을 수행 해야 합니다.
* [관리 ID 사용](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [환경 변수](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* 디지털 쌍 Api에 액세스할 수 있도록 하는 함수 앱에 사용 권한을 할당 합니다. 자세한 내용은 [방법: 데이터 처리를 위한 Azure 함수 설정을](how-to-create-azure-function.md) 참조 하세요.

## <a name="authentication-in-an-autorest-generated-sdk"></a>AutoRest에서 생성 된 SDK의 인증

.NET을 사용 하지 않는 경우 [방법: AutoRest를 사용 하 여 Azure Digital Twins 용 사용자 지정 Sdk 만들기](how-to-create-custom-sdks.md)에 설명 된 대로 원하는 언어로 SDK 라이브러리를 빌드할 수 있습니다.

이 섹션에서는이 경우 인증 하는 방법을 설명 합니다.

### <a name="prerequisites"></a>사전 요구 사항

이 예제에서는 AutoRest를 사용 하 여 생성 된 Typescript SDK를 사용 합니다. 그 결과 다음도 필요 합니다.
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>최소 인증 코드 샘플

Azure 서비스를 사용 하 여 .NET 앱을 인증 하기 위해 클라이언트 앱 내에서 다음 최소 코드를 사용할 수 있습니다.

이전에는 *응용 프로그램 (클라이언트) id* 및 *디렉터리 (테 넌 트) Id* 와 Azure DIGITAL twins 인스턴스의 URL이 필요 합니다.

> [!TIP]
> Azure Digital Twins 인스턴스의 URL은 Azure Digital Twins 인스턴스의 *호스트 이름*앞에 *https://* 를 추가 하 여 수행 됩니다. 인스턴스의 모든 속성과 함께 *호스트 이름을*보려면를 실행할 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . 명령을 사용 하 여 `az account show --query tenantId` *디렉터리 (테 넌 트) ID*를 볼 수 있습니다. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

위의 코드에서 간단 하 게 클라이언트 ID, 테 넌 트 ID 및 인스턴스 URL을 코드에 직접 저장 하는 경우 코드에서 구성 파일 또는 환경 변수 로부터 이러한 값을 가져오는 것이 좋습니다.

MSAL에는 캐싱 및 기타 인증 흐름과 같은 작업을 구현 하는 데 사용할 수 있는 더 많은 옵션이 있습니다. 이에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리) 개요](../active-directory/develop/msal-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 보안이 작동 하는 방식에 대해 자세히 알아보세요.
* [개념: Azure Digital Twins 솔루션에 대 한 보안](concepts-security.md)

또는 이제 인증이 설정 되었으므로 인스턴스에서 모델 만들기로 이동 합니다.
* [방법: 사용자 지정 모델 관리](how-to-manage-model.md)