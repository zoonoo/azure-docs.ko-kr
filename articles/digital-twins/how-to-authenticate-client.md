---
title: 앱 인증 코드 쓰기
titleSuffix: Azure Digital Twins
description: 클라이언트 응용 프로그램에서 인증 코드를 작성 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893597"
---
# <a name="write-client-app-authentication-code"></a>클라이언트 앱 인증 코드 작성

[Azure Digital Twins 인스턴스와 인증을 설정한](how-to-set-up-instance-portal.md)후에는 인스턴스와 상호 작용 하는 데 사용할 클라이언트 응용 프로그램을 만들 수 있습니다. 시작 클라이언트 프로젝트를 설정한 후에 **는 해당 클라이언트 앱에서 코드를 작성** 하 여 Azure Digital twins 인스턴스에 대해 인증 해야 합니다.

Azure Digital Twins는 [OAUTH 2.0을 기반으로 하는 AZURE AD 보안 토큰을](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims)사용 하 여 인증을 수행 합니다. SDK를 인증 하려면 Azure 디지털 쌍에 대 한 올바른 사용 권한이 있는 전달자 토큰을 가져와서 API 호출과 함께 전달 해야 합니다. 

이 문서에서는 클라이언트 라이브러리를 사용 하 여 자격 증명을 가져오는 방법을 설명 합니다 `Azure.Identity` . 이 문서에서는 [.net (c #) sdk](https://www.nuget.org/packages/Azure.DigitalTwins.Core)에 대해 작성 하는 것과 같은 c #의 코드 예제를 보여 주지만, 사용 중인 sdk에 관계 없이 버전을 사용할 수 있습니다. `Azure.Identity` Azure digital twins에 사용할 수 있는 sdk에 대 한 자세한 내용은 [*방법: azure 디지털 쌍 api 및 sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

먼저 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)에서 설정 단계를 완료 합니다. 이렇게 하면 Azure Digital Twins 인스턴스가 있고 사용자에 게 액세스 권한이 있으며 클라이언트 응용 프로그램에 대 한 사용 권한을 설정 하 게 됩니다. 이 설정이 완료 되 면 클라이언트 앱 코드를 작성할 준비가 된 것입니다.

계속 하려면 코드를 작성 하는 클라이언트 앱 프로젝트가 필요 합니다. 클라이언트 앱 프로젝트를 아직 설정 하지 않은 경우이 자습서에서 사용할 수 있도록 선택한 언어로 기본 프로젝트를 만듭니다.

## <a name="common-authentication-methods-with-azureidentity"></a>Azure. Identity를 사용 하는 일반적인 인증 방법

`Azure.Identity` 는 전달자 토큰을 가져오고 SDK를 사용 하 여 인증 하는 데 사용할 수 있는 몇 가지 자격 증명 가져오기 방법을 제공 하는 클라이언트 라이브러리입니다. 이 문서에서는 c #의 예제를 제공 하지만 다음 `Azure.Identity` 을 비롯 한 여러 언어를 볼 수 있습니다.
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

의 세 가지 일반적인 자격 증명 가져오기 메서드 `Azure.Identity` 는 다음과 같습니다.
* [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) 는 `TokenCredential` Azure에 배포 되는 응용 프로그램에 대 한 기본 인증 흐름을 제공 하며 **로컬 개발에 권장**되는 옵션입니다. 또한이 문서에서 권장 하는 다른 두 가지 방법을 사용해 볼 수 있습니다. 래핑하고 `ManagedIdentityCredential` `InteractiveBrowserCredential` 구성 변수를 사용 하 여에 액세스할 수 있습니다.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) [는 MSI (관리 id)](../active-directory/managed-identities-azure-resources/overview.md)를 필요로 하는 경우에 적합 하며, Azure Functions 및 Azure 서비스에 배포 하는 데 적합 한 후보입니다.
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 는 대화형 응용 프로그램을 위한 것 이며 인증 된 SDK 클라이언트를 만드는 데 사용할 수 있습니다.

다음 예제에서는 이러한 각각을 .NET (c #) SDK와 함께 사용 하는 방법을 보여 줍니다.

## <a name="authentication-examples-net-c-sdk"></a>인증 예: .NET (c #) SDK

이 섹션에서는 제공 된 .NET SDK를 사용 하 여 인증 코드를 작성 하는 c #의 예제를 보여 줍니다.

먼저 `Azure.DigitalTwins.Core` 프로젝트에 SDK 패키지 및 패키지를 포함 `Azure.Identity` 합니다. 선택한 도구에 따라 Visual Studio 패키지 관리자 또는 명령줄 도구를 사용 하 여 패키지를 포함할 수 있습니다 `dotnet` . 

또한 다음 using 문을 프로젝트 코드에 추가 해야 합니다.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

그런 다음의 메서드 중 하나를 사용 하 여 자격 증명을 가져오는 코드를 추가 `Azure.Identity` 합니다.

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 메서드

[DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) 는 `TokenCredential` Azure에 배포 되는 응용 프로그램에 대 한 기본 인증 흐름을 제공 하며 **로컬 개발에 권장**되는 옵션입니다.

기본 Azure 자격 증명을 사용 하려면 Azure Digital Twins 인스턴스의 URL ([찾을 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))이 필요 합니다.

프로젝트에을 추가 하는 코드 샘플은 `DefaultAzureCredential` 다음과 같습니다.

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 메서드

[ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) 메서드는 [관리 id (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)가 필요한 경우 (예: Azure Functions 사용 하는 경우에 유용 합니다.

즉 `ManagedIdentityCredential` , 또는와 동일한 프로젝트에서를 사용 하 여 `DefaultAzureCredential` `InteractiveBrowserCredential` 프로젝트의 다른 부분을 인증할 수 있습니다.

기본 Azure 자격 증명을 사용 하려면 Azure Digital Twins 인스턴스의 URL ([찾을 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))이 필요 합니다.

Azure 함수에서 다음과 같이 관리 되는 id 자격 증명을 사용할 수 있습니다.

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 메서드

[InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) 메서드는 대화형 응용 프로그램을 위한 것 이며 인증을 위해 웹 브라우저를 엽니다. 대화형 인증을 요구 하는 경우 대신이를 사용할 수 있습니다 `DefaultAzureCredential` .

대화형 브라우저 자격 증명을 사용 하려면 Azure Digital Twins Api에 대 한 사용 권한이 있는 **앱 등록이** 필요 합니다. 이 앱 등록을 설정 하는 방법에 대 한 단계 *는 방법: 인스턴스 및 인증 설정*의 [*클라이언트 응용 프로그램에 대 한 액세스 권한 설정*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) 섹션을 참조 하세요. 앱 등록을 설정한 후에는 다음이 필요 합니다.
* 앱 등록의 *응용 프로그램 (클라이언트) ID*
* 앱 등록의 *디렉터리 (테 넌 트) ID*
* Azure Digital Twins 인스턴스의 URL ([찾을 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

다음은를 사용 하 여 인증 된 SDK 클라이언트를 만드는 코드의 예제입니다 `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Azure Functions 인증에 대 한 기타 참고 사항

방법: 함수 컨텍스트에서 중요 한 구성 옵션 중 일부를 설명 하는 보다 완전 한 예제에 대 한 [*데이터 처리를 위한 Azure 함수 설정을*](how-to-create-azure-function.md) 참조 하세요.

또한 함수에서 인증을 사용 하려면 다음을 수행 해야 합니다.
* [관리 ID 사용](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [환경 변수](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) 를 적절 하 게 사용
* 디지털 쌍 Api에 액세스할 수 있도록 하는 함수 앱에 사용 권한을 할당 합니다. Azure Functions 프로세스에 대 한 자세한 내용은 [*방법: 데이터 처리를 위한 Azure 함수 설정을*](how-to-create-azure-function.md)참조 하세요.

## <a name="other-credential-methods"></a>기타 자격 증명 방법

위의 강조 표시 된 인증 시나리오에서 앱의 요구 사항을 다루지 않는 경우 [**Microsoft id 플랫폼**](../active-directory/develop/v2-overview.md#getting-started)에서 제공 하는 다른 유형의 인증을 탐색할 수 있습니다. 이 플랫폼에 대 한 설명서는 응용 프로그램 유형별로 구성 된 추가 인증 시나리오를 다룹니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 보안이 작동 하는 방식에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션에 대 한 보안*](concepts-security.md)

또는 이제 인증이 설정 되었으므로 인스턴스에서 모델 만들기로 이동 합니다.
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)
