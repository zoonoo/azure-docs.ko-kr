---
title: Azure CLI에서 Azure Active Directory 관리 id 응용 프로그램 만들기
titleSuffix: An Azure Communication Services quickstart
description: 관리 id를 사용 하면 azure Vm, 함수 앱 및 기타 리소스에서 실행 되는 응용 프로그램에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다. 이 빠른 시작은 Azure CLI를 사용 하 여 id를 관리 하는 데 중점을 두었습니다.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: e708536395807fc74dc5bfd73836e050832cca39
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493342"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>개발 환경에서 관리 id를 사용 하 여 통신 리소스에 대 한 액세스 권한 부여

Azure Id 클라이언트 라이브러리는 azure SDK에 대 한 Azure Active Directory (Azure AD) 토큰 인증을 지원 합니다. .NET, Java, Python 및 JavaScript 용 Azure Communication Services 클라이언트 라이브러리의 최신 버전은 azure Id 라이브러리와 통합 되어 azure Communication Services 요청 권한 부여를 위한 OAuth 2.0 토큰을 획득 하는 간단 하 고 안전한 방법을 제공 합니다.

Azure Id 클라이언트 라이브러리의 장점은 응용 프로그램이 개발 환경에서 실행 되 든 Azure에서 실행 되는지에 관계 없이 동일한 코드를 사용 하 여 여러 서비스에서 인증할 수 있다는 점입니다. Azure Id 클라이언트 라이브러리는 보안 주체를 인증 합니다. 코드가 Azure에서 실행 되는 경우 보안 주체는 Azure 리소스에 대 한 관리 되는 id입니다. 개발 환경에서 관리 id가 존재 하지 않으므로 클라이언트 라이브러리는 테스트 목적으로 사용자 또는 등록 된 응용 프로그램을 인증 합니다.

## <a name="prerequisites"></a>필수 구성 요소

 - Azure CLI [설치 가이드](https://docs.microsoft.com/cli/azure/install-azure-cli)
 - 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)

## <a name="setting-up"></a>설치

권한을 부여 하는 Azure 리소스에서 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [앱 서비스](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>개발 환경에서 등록 된 응용 프로그램 인증

개발 환경에서 웹 브라우저를 통해 Single Sign-On 또는 로그인을 지원 하지 않는 경우 등록 된 응용 프로그램을 사용 하 여 개발 환경에서 인증할 수 있습니다.

### <a name="creating-an-azure-active-directory-registered-application"></a>Azure Active Directory 등록 된 응용 프로그램 만들기

Azure CLI에서 등록 된 응용 프로그램을 만들려면 작업을 수행 하려는 Azure 계정에 로그인 해야 합니다. 이렇게 하려면 명령을 사용 하 여 `az login` 브라우저에 자격 증명을 입력 합니다. CLI에서 Azure 계정에 로그인 한 후에는 `az ad sp create-for-rbac` 명령을 호출 하 여 등록 된 응용 프로그램을 만들 수 있습니다.

다음 예에서는 Azure CLI를 사용 하 여 등록 된 새 응용 프로그램을 만듭니다.

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac`이 명령은 JSON 형식의 서비스 사용자 속성 목록을 반환 합니다. 이러한 값을 복사 하 여 다음 단계에서 필요한 환경 변수를 만들 수 있습니다.

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

Azure Id 클라이언트 라이브러리는 런타임에 세 가지 환경 변수에서 값을 읽어 응용 프로그램을 인증 합니다. 다음 표에서는 각 환경 변수에 대해 설정할 값을 설명 합니다.

|환경 변수|값
|-|-
|`AZURE_CLIENT_ID`|`appId` 생성 된 JSON의 값 
|`AZURE_TENANT_ID`|`tenant` 생성 된 JSON의 값
|`AZURE_CLIENT_SECRET`|`password` 생성 된 JSON의 값

> [!IMPORTANT]
> 환경 변수를 설정한 후 콘솔 창을 닫았다가 다시 엽니다. Visual Studio 또는 다른 개발 환경을 사용 하는 경우 새 환경 변수를 등록 하기 위해 다시 시작 해야 할 수 있습니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증에 대한 자세한 정보](../concepts/authentication.md)

다음을 수행할 수도 있습니다.

- [Azure Id 라이브러리에 대 한 자세한 정보](/dotnet/api/overview/azure/identity-readme)
