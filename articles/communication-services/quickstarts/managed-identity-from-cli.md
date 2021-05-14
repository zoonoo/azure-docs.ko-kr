---
title: Azure CLI에서 Azure Active Directory 관리 ID 애플리케이션 만들기
titleSuffix: An Azure Communication Services quickstart
description: 관리 ID를 사용하면 Azure VM, 함수 앱, 기타 리소스에서 실행되는 애플리케이션에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다. 이 빠른 시작은 Azure CLI를 사용하여 ID를 관리하는 데 중점을 둡니다.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 2ef5a3b162d62fa79ed01a156345070ee12b4862
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110681"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>개발 환경에서 관리 ID를 사용하여 통신 리소스에 대한 액세스 권한 부여

Azure ID SDK는 Azure SDK에 대해 Azure AD(Azure Active Directory) 토큰 인증 지원을 제공합니다. 최신 버전의 .NET, Java, Python, JavaScript용 Azure Communication Services SDK는 Azure ID 라이브러리와 통합되어 Azure Communication Services 요청의 권한 부여를 위해 OAuth 2.0 토큰을 획득하는 간단하고 안전한 방법을 제공합니다.

Azure ID SDK의 장점은 애플리케이션이 개발 환경 또는 Azure에서 실행되고 있는지와 관계없이 동일한 코드를 사용하여 여러 서비스에 걸쳐 인증할 수 있도록 지원한다는 점입니다. Azure ID SDK는 보안 주체를 인증합니다. 코드가 Azure에서 실행되는 경우 보안 주체는 Azure 리소스에 대한 관리 ID입니다. 개발 환경에는 관리 ID가 존재하지 않기 때문에 SDK가 테스트 목적으로 사용자 또는 등록된 애플리케이션을 인증합니다.

## <a name="prerequisites"></a>필수 구성 요소

 - Azure CLI. [설치 가이드](/cli/azure/install-azure-cli)
 - 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)

## <a name="setting-up"></a>설치

권한을 부여하는 Azure 리소스에서 관리 ID의 사용이 설정되어 있어야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager SDK](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [앱 서비스](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>개발 환경에서 등록된 애플리케이션 인증

개발 환경에서 웹 브라우저를 통한 Single Sign-On 또는 로그인을 지원하지 않는 경우 등록된 애플리케이션을 사용하여 개발 환경에서 인증할 수 있습니다.

### <a name="creating-an-azure-active-directory-registered-application"></a>Azure Active Directory 등록된 애플리케이션 만들기

Azure CLI에서 등록된 애플리케이션을 만들려면 작업을 수행하려는 Azure 계정에 로그인해야 합니다. 해당 작업을 하기 위해 `az login` 명령을 사용하여 브라우저에 자격 증명을 입력합니다. CLI에서 Azure 계정에 로그인하고 나면 `az ad sp create-for-rbac` 명령을 호출하여 등록된 애플리케이션을 만들 수 있습니다.

다음 예에서는 Azure CLI를 사용하여 새 등록된 애플리케이션을 만듭니다.

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac` 명령은 JSON 형식으로 서비스 주체 속성 목록을 반환합니다. 해당 값을 복사하면 다음 단계에 필요한 환경 변수를 만드는 데 사용할 수 있습니다.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="set-environment-variables"></a>환경 변수 설정

Azure ID SDK는 런타임의 세 가지 환경 변수에서 값을 읽어 애플리케이션을 인증합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|생성된 JSON의 `appId` 값 
|`AZURE_TENANT_ID`|생성된 JSON의 `tenant` 값
|`AZURE_CLIENT_SECRET`|생성된 JSON의 `password` 값

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용하는 경우 새 환경 변수를 등록하기 위해 다시 시작해야 할 수 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증에 대한 자세한 정보](../concepts/authentication.md)

다음을 수행할 수도 있습니다.

- [Azure ID 라이브러리에 대한 자세한 정보](/dotnet/api/overview/azure/identity-readme)