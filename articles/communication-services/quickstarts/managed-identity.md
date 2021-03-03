---
title: 통신 서비스 (.NET)에서 관리 되는 id 사용
titleSuffix: An Azure Communication Services quickstart
description: 관리 id를 사용 하면 azure Vm, 함수 앱 및 기타 리소스에서 실행 되는 응용 프로그램에서 Azure Communication Services 액세스 권한을 부여할 수 있습니다.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 7e8d9b56077819fc404d6c2bdc39f9f697224136
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692184"
---
# <a name="use-managed-identities-net"></a>관리 ID 사용(.NET)

.NET에서 관리 되는 id를 사용 하 여 Azure 통신 서비스를 시작 합니다. 통신 서비스 관리 및 SMS 클라이언트 라이브러리는 [azure 리소스에 대 한 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 azure AD (Azure Active Directory) 인증을 지원 합니다.

이 빠른 시작에서는 관리 되는 id를 지 원하는 Azure 환경에서 관리 및 SMS 클라이언트 라이브러리에 대 한 액세스 권한을 부여 하는 방법을 보여 줍니다. 개발 환경에서 코드를 테스트 하는 방법에 대해서도 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

 - 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free)
 - 활성 Communication Services 리소스 및 연결 문자열. [Communication Services 리소스를 만듭니다](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>설치

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>가상 컴퓨터 또는 App service에서 관리 되는 id 사용

권한을 부여 하는 Azure 리소스에서 관리 되는 id를 사용 하도록 설정 해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [앱 서비스](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure 역할 할당

1. Azure Portal로 이동합니다.
1. Azure 통신 서비스 리소스로 이동 합니다.
1. Access Control (IAM) 메뉴-> + 추가 > 역할 할당 추가로 이동 합니다.
1. 역할 "참여자" (유일 하 게 지원 되는 역할)를 선택 합니다.
1. "사용자 할당 관리 id" (또는 "시스템 할당 관리 id")를 선택 하 고 원하는 id를 선택 합니다. 선택 항목을 저장 합니다.

![관리 id 역할](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>PowerShell을 사용 하 여 Azure 역할 할당

PowerShell을 사용 하 여 역할 및 사용 권한을 할당 하려면 [Azure PowerShell을 사용 하 여 Azure 역할 할당 추가 또는 제거](../../../articles/role-based-access-control/role-assignments-powershell.md) 를 참조 하세요.

## <a name="add-managed-identity-to-your-communication-services-solution"></a>통신 서비스 솔루션에 관리 id 추가

### <a name="install-the-client-library-packages"></a>클라이언트 라이브러리 패키지 설치

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>클라이언트 라이브러리 패키지 사용

`using`Azure id 및 Azure Storage 클라이언트 라이브러리를 사용 하는 다음 지시문을 코드에 추가 합니다.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

아래 예제에서는 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)를 사용 합니다. 이 자격 증명은 프로덕션 환경과 개발 환경에 적합 합니다.

### <a name="create-an-identity-and-issue-a-token"></a>Id 만들기 및 토큰 발급

다음 코드 예제에서는 Azure Active Directory 토큰을 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 새 사용자에 대 한 토큰을 발급 하는 방법을 보여 줍니다.

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Azure Active Directory 토큰이 포함 된 SMS 보내기

다음 코드 예제에서는 Azure Active Directory 토큰을 사용 하 여 서비스 클라이언트 개체를 만든 다음 클라이언트를 사용 하 여 SMS 메시지를 보내는 방법을 보여 줍니다.

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [인증에 대한 자세한 정보](../concepts/authentication.md)

다음을 수행할 수도 있습니다.

- [Azure 역할 기반 access control에 대 한 자세한 정보](../../../articles/role-based-access-control/index.yml)
- [.NET 용 Azure id 라이브러리에 대해 자세히 알아보기](/dotnet/api/overview/azure/identity-readme)
- [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)
- [SMS 메시지 보내기](../quickstarts/telephony-sms/send.md)
- [SMS에 대한 자세한 정보](../concepts/telephony-sms/concepts.md)