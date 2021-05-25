---
title: 앱 인증 코드 쓰기
titleSuffix: Azure Digital Twins
description: 클라이언트 애플리케이션에서 인증 코드를 작성하는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1b7a846ee92da001ea2ac3ddd02efa9a870f72c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102501909"
---
# <a name="write-client-app-authentication-code"></a>클라이언트 앱 인증 코드 작성

[Azure Digital Twins 인스턴스와 인증을 설정](how-to-set-up-instance-portal.md)한 후에는 인스턴스와 상호 작용하는 데 사용할 클라이언트 애플리케이션을 만들 수 있습니다. 시작 클라이언트 프로젝트를 설정한 후에는 Azure Digital Twins 인스턴스에 대해 **클라이언트 앱에서 인증하는 코드를 작성** 해야 합니다.

Azure Digital Twins는 [OAUTH 2.0을 기반으로 하는 Azure AD 보안 토큰](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims)을 사용하여 인증을 수행합니다. SDK를 인증하려면 Azure Digital Twins에 대한 올바른 사용 권한이 있는 전달자 토큰을 가져와서 API 호출과 함께 전달해야 합니다. 

이 문서에서는 `Azure.Identity` 클라이언트 라이브러리를 사용하여 자격 증명을 가져오는 방법을 설명합니다. 이 문서에서는 [.NET(C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)에 대해 작성하는 것과 같은 C#의 코드 예제를 보여 주지만, 사용 중인 SDK에 관계없이 `Azure.Identity` 버전을 사용할 수 있습니다. Azure Digital Twins에 사용할 수 있는 SDK에 대한 자세한 내용은 [*방법: Azure Digital Twins API 및 SDK 사용*](how-to-use-apis-sdks.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

먼저 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-portal.md)에서 설정 단계를 완료합니다. 이렇게 하면 Azure Digital Twins 인스턴스가 있고 사용자에게 액세스 권한이 있는지 확인할 수 있습니다. 설정한 후에는 클라이언트 앱 코드를 작성할 준비가 된 것입니다.

계속하려면 코드를 작성하는 클라이언트 앱 프로젝트가 필요합니다. 클라이언트 앱 프로젝트를 아직 설정하지 않은 경우 이 자습서에서 사용할 수 있도록 선택한 언어로 기본 프로젝트를 만듭니다.

## <a name="common-authentication-methods-with-azureidentity"></a>Azure.Identity를 사용하는 일반적인 인증 방법

`Azure.Identity`는 전달자 토큰을 가져오고 SDK를 사용하여 인증하는 데 사용할 수 있는 몇 가지 자격 증명 가져오기 방법을 제공하는 클라이언트 라이브러리입니다. 이 문서에서는 C#의 예제를 제공하지만 다음을 비롯한 여러 언어에 대한 `Azure.Identity`를 볼 수 있습니다.

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

`Azure.Identity`의 세 가지 일반적인 자격 증명 가져오기 메서드는 다음과 같습니다.

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)은 Azure에 배포되는 애플리케이션에 대한 기본 `TokenCredential` 인증 흐름을 제공하며 **로컬 개발에 권장** 되는 옵션입니다. 또한 이 문서에서 권장하는 다른 두 가지 메서드를 사용하는 데 활용할 수 있습니다. 즉, `ManagedIdentityCredential`을 래핑하고 구성 변수를 사용하여 `InteractiveBrowserCredential`에 액세스할 수 있습니다.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential)은 [MSI(관리 ID)](../active-directory/managed-identities-azure-resources/overview.md)를 필요로 하는 경우에 적합하며, Azure Functions에서 사용 및 Azure 서비스 배포에 적합합니다.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential)은 대화형 애플리케이션을 위한 것이며 인증된 SDK 클라이언트를 만드는 데 사용할 수 있습니다.

다음 예제에서는 이러한 각각을 .NET(C#) SDK와 함께 사용하는 방법을 보여 줍니다.

## <a name="authentication-examples-net-c-sdk"></a>인증 예: .NET(C#) SDK

이 섹션에서는 제공된 .NET SDK를 사용하여 인증 코드를 작성하는 C#의 예제를 보여 줍니다.

먼저 프로젝트에 SDK 패키지 `Azure.DigitalTwins.Core` 및 `Azure.Identity` 패키지를 포함합니다. 선택한 도구에 따라 Visual Studio 패키지 관리자 또는 `dotnet` 명령줄 도구를 사용하여 패키지를 포함할 수 있습니다. 

또한 다음 using 문을 프로젝트 코드에 추가해야 합니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

그런 다음, `Azure.Identity`의 메서드 중 하나를 사용하여 자격 증명을 가져오는 코드를 추가합니다.

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential 메서드

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)은 Azure에 배포되는 애플리케이션에 대한 기본 `TokenCredential` 인증 흐름을 제공하며 **로컬 개발에 권장** 되는 옵션입니다.

기본 Azure 자격 증명을 사용하려면 Azure Digital Twins 인스턴스의 URL([찾아볼 수 있는 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))이 필요합니다.

프로젝트에 `DefaultAzureCredential`을 추가하는 코드 샘플은 다음과 같습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>로컬 Azure 자격 증명 설정

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 메서드

[ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) 메서드는 [관리 ID(MSI)](../active-directory/managed-identities-azure-resources/overview.md)가 필요한 경우(예: Azure Functions에서 사용하는 경우)에 유용합니다.

즉, `DefaultAzureCredential` 또는 `InteractiveBrowserCredential`과 동일한 프로젝트에서 `ManagedIdentityCredential`을 사용하여 프로젝트의 다른 부분을 인증할 수 있습니다.

기본 Azure 자격 증명을 사용하려면 Azure Digital Twins 인스턴스의 URL([찾아볼 수 있는 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))이 필요합니다.

Azure 함수에서 다음과 같은 관리 ID 자격 증명을 사용할 수 있습니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 메서드

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) 메서드는 대화형 애플리케이션을 위한 것이며 인증을 위해 웹 브라우저를 엽니다. 대화형 인증이 필요한 경우 `DefaultAzureCredential` 대신 이를 사용할 수 있습니다.

대화형 브라우저 자격 증명을 사용하려면 Azure Digital Twins API에 대한 사용 권한을 갖게 되는 **앱 등록** 과정이 필요합니다. 이 앱 등록을 설정하는 방법에 대한 단계는 [*방법: 앱 등록 만들기*](how-to-create-app-registration.md)를 참조하세요. 앱 등록을 설정한 후에는 다음이 필요합니다.
* 앱 등록의 *애플리케이션(클라이언트) ID*([찾아볼 수 있는 지침](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* 앱 등록의 *디렉터리(테넌트) ID*([찾아볼 수 있는 지침](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Azure Digital Twins 인스턴스의 URL([찾아볼 수 있는 지침](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

다음은 `InteractiveBrowserCredential`을 사용하여 인증된 SDK 클라이언트를 만드는 코드의 예제입니다.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> 위와 같이 클라이언트 ID, 테넌트 ID, 인스턴스 URL을 코드에 직접 저장할 수 있지만, 코드에서 구성 파일 또는 환경 변수로부터 이러한 값을 가져오는 것이 좋습니다.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Azure Functions 인증에 대한 기타 참고 사항

함수 컨텍스트에서 중요한 구성 옵션 중 일부를 설명하는 보다 완전한 예제는 [*방법: 데이터 처리를 위한 Azure 함수 설정*](how-to-create-azure-function.md)을 참조하세요.

또한 함수에서 인증을 사용하려면 다음을 수행해야 합니다.
* [관리 ID 사용](../app-service/overview-managed-identity.md?tabs=dotnet)
* [환경 변수](/sandbox/functions-recipes/environment-variables?tabs=csharp)를 적절하게 사용
* 디지털 트윈 API에 액세스할 수 있도록 하는 함수 앱에 사용 권한 할당. Azure Functions 프로세스에 대한 자세한 내용은 [*방법: 데이터 처리를 위한 Azure 함수 설정*](how-to-create-azure-function.md)을 참조하세요.

## <a name="other-credential-methods"></a>기타 자격 증명 방법

위에서 강조한 인증 시나리오가 사용자 앱의 요구 사항을 해결하지 못하는 경우 [**Microsoft ID 플랫폼**](../active-directory/develop/v2-overview.md#getting-started)에서 제공하는 다른 유형의 인증을 찾아볼 수 있습니다. 이 플랫폼에 대한 문서는 애플리케이션 유형별로 구성된 추가 인증 시나리오를 다룹니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 보안이 작동하는 방식에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션 보안*](concepts-security.md)

또는 이제 인증이 설정되었으므로 인스턴스에서 모델 만들기 및 관리로 이동합니다.
* [*방법: DTDL 모델 관리*](how-to-manage-model.md)