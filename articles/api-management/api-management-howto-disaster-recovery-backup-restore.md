---
title: Azure API Management에서 백업 및 복원을 사용하여 재해 복구 구현 | Microsoft Docs
description: Azure API Management에서 백업 및 복원을 사용하여 재해 복구를 수행하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: e0c02829a2fef6e281794fdba6c9fb5d9b8a736b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241713"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management에서 서비스 백업 및 복원을 사용하여 재해 복구를 구현하는 방법

Azure API Management를 통해 API를 게시 및 관리하면 기존에는 수동으로 설계, 구현 및 관리하던 내결함성 및 인프라 기능을 활용할 수 있습니다. Azure 플랫폼은 매우 적은 비용으로 상당한 잠재적 오류를 완화합니다.

API Management 서비스를 호스트하는 지역에 영향을 주는 가용성 문제로부터 서비스를 복구하려면 언제든지 다른 지역에서 서비스를 다시 구축할 준비를 하세요. 가용성 및 복구 시간 목표에 따라서는 하나 이상의 지역에서 백업 서비스를 예약할 수 있습니다. 또한 해당 구성과 콘텐츠를 활성 서비스와 동기화 상태로 유지 관리할 수 있습니다. 서비스 "백업 및 복원" 기능은 재해 복구 전략을 구현하는 데 필요한 구성 요소를 제공합니다.

이 가이드는 Azure Resource Manager 요청을 인증하는 방법을 보여 줍니다. 또한 API Management 서비스 인스턴스를 백업 및 복원하는 방법을 보여 줍니다.

> [!NOTE]
> 재해 복구를 위한 API Management 서비스 인스턴스를 백업 및 복원하는 프로세스는 스테이징과 같은 시나리오에 대한 API Management 서비스 인스턴스를 복제하는 데 사용할 수도 있습니다.
>
> 각 백업은 30일 후에 만료됩니다. 30일의 만료 기간이 만료된 후에 백업을 복원하려고 하면 `Cannot restore: backup expired` 메시지와 함께 복원이 실패합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure 리소스 관리자 요청 인증

> [!IMPORTANT]
> 백업 및 복원을 위한 REST API는 Azure Resource Manager를 사용하며 API Management 엔터티를 관리하기 위한 REST API와는 다른 인증 메커니즘입니다. 이 섹션의 단계에는 Azure 리소스 관리자 요청을 인증하는 방법을 설명합니다. 자세한 내용은 [Azure 리소스 관리자 요청 인증](/rest/api/index)을 참조하세요.

Azure Resource Manager를 사용하여 리소스에서 수행하는 모든 작업은 다음 단계를 사용하여 Azure Active Directory에서 인증되어야 합니다.

* 애플리케이션을 Azure Active Directory 테넌트에 추가합니다.
* 추가한 애플리케이션에 대한 권한을 설정합니다.
* Azure 리소스 관리자에 대한 요청을 인증하기 위한 토큰을 가져옵니다.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory 애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. API Management 서비스 인스턴스를 포함하는 구독을 사용하여 **Azure Active Directory**의 **앱 등록** 탭으로 이동합니다(Azure Active Directory > [관리/앱 등록]).

    > [!NOTE]
    > Azure Active Directory 기본 디렉터리에 사용자의 계정이 표시되지 않는 경우, 계정에 필요한 권한을 부여하려면 Azure 구독의 관리자에게 문의하세요.
3. **새 애플리케이션 등록**을 클릭합니다.

    **만들기** 창이 오른쪽에 나타납니다. 여기에 AAD 앱 관련 정보를 입력합니다.
4. 애플리케이션의 이름을 입력합니다.
5. 애플리케이션 형식에서 **네이티브**를 선택합니다.
6. **리디렉션 URI**로 `http://resources`와 같은 자리 표시자 URL을 입력하고, 필수 필드지만 값은 나중에 사용되지 않습니다. 애플리케이션을 저장하려면 이 확인란을 클릭합니다.
7. **만들기**를 클릭합니다.

### <a name="add-an-application"></a>애플리케이션 추가

1. 애플리케이션을 만들면 **설정**을 클릭합니다.
2. **필수 사용 권한**을 클릭합니다.
3. **+추가**를 클릭합니다.
4. **API 선택**을 누릅니다.
5. **Windows** **Azure Service Management API**를 선택합니다.
6. **선택**을 누릅니다.

    ![권한 추가](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. 새로 추가된 애플리케이션 옆에 있는 **위임된 권한**을 클릭하고, **Azure 서비스 관리 액세스(미리 보기)** 에서 상자를 선택합니다.
8. **선택**을 누릅니다.
9. **사용 권한 부여**를 클릭합니다.

### <a name="configuring-your-app"></a>앱 구성

백업을 생성하고 복원하는 API를 호출하기 전에 토큰을 가져와야 합니다. 다음 예제는 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet 패키지를 사용하여 토큰을 검색합니다.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

다음 지침을 사용하여 `{tenant id}`, `{application id}` 및 `{redirect uri}`을 바꿉니다.

1. `{tenant id}`를 사용자가 만든 Azure Active Directory 애플리케이션의 테넌트 ID로 바꿉니다. **앱 등록** -> **엔드포인트**를 클릭하여 ID에 액세스할 수 있습니다.

    ![엔드포인트][api-management-endpoint]
2. **설정** 페이지로 이동하여 `{application id}`를 가져온 값으로 바꿉니다.
3. `{redirect uri}`를 Azure Active Directory 애플리케이션의 **리디렉션 URI** 탭에 있는 값으로 바꿉니다.

    값이 지정되면 코드 예제에서는 다음 예제와 유사한 토큰을 반환해야 합니다.

    ![토큰][api-management-arm-token]

    > [!NOTE]
    > 토큰은 특정 기간 후에 만료될 수 있습니다. 코드 샘플을 다시 실행하여 새 토큰을 생성합니다.

## <a name="calling-the-backup-and-restore-operations"></a>백업 및 복원 작업 호출

REST API는 [API Management 서비스 - 백업](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) 및 [API Management 서비스 - 복원](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)입니다.

다음 섹션에서 설명한 "백업 및 복원 작업"을 호출하기 전에 REST 호출에 대한 권한 부여 요청 헤더를 설정합니다.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>API Management 서비스 백업

API Management 서비스를 백업하려면 다음 HTTP 요청을 실행합니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

각 항목이 나타내는 의미는 다음과 같습니다.

* `subscriptionId` - 백업하려는 API Management 서비스를 포함하는 구독의 ID입니다.
* `resourceGroupName` - Azure API Management 서비스의 리소스 그룹 이름입니다.
* `serviceName` - 백업을 만드는 API Management 서비스를 만들 때 지정하는 이름입니다.
* `api-version` - `2018-06-01-preview`(으)로 대체

요청 본문에서 대상 Azure 저장소 계정 이름, 액세스 키, Blob 컨테이너 이름 및 백업 이름을 지정합니다.

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

`Content-Type` 요청 헤더의 값을 `application/json`으로 설정합니다.

백업은 완료하는 데 1분 이상 걸릴 수 있는 장기 실행 작업입니다.  요청이 성공하고 백업 프로세스가 시작된 경우 `Location` 헤더가 포함된 `202 Accepted` 응답 상태 코드를 받게 됩니다.  `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 백업이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK` 이면 백업 작업이 정상적으로 완료된 것입니다.

백업 요청을 수행할 때는 다음의 제약 조건에 유의하세요.

* 요청 본문에 지정된 **Container**가 **있어야 합니다**.
* 백업이 진행되는 동안에는 SKU 업그레이드 또는 다운그레이드, 도메인 이름 변경과 같은 **서비스 관리의 변경을 피하세요**.
* 백업 복원은 생성 시점부터 **30일 동안만 보장**됩니다.
* 분석 보고서를 만드는 데 사용되는 **사용 현황 데이터**는 백업에 **포함되지 않습니다**. [Azure API Management REST API][Azure API Management REST API] 를 사용하여 분석 보고서를 주기적으로 검색한 다음 안전하게 보관하세요.
* 서비스 백업을 수행하는 빈도는 복구 지점 목표에 영향을 줍니다. 영향을 최소화하려면 정기 백업을 구현함과 동시에 API Management 서비스에 대한 변경을 수행한 후 요청 시 백업도 수행하는 것이 좋습니다.
* 백업 작업이 진행되는 동안 API, 정책 및 개발자 포털 모양 등의 서비스 구성을 **변경**하는 경우 **해당 내용이 백업에서 제외되고 손실될 수 있습니다**.

### <a name="step2"></a>API Management 서비스 복원

이전에 만든 백업에서 API Management 서비스를 복원하려면 다음 HTTP 요청을 실행합니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

각 항목이 나타내는 의미는 다음과 같습니다.

* `subscriptionId` - 백업을 복원할 API Management 서비스를 포함하는 구독의 ID입니다.
* `resourceGroupName` - 백업을 복원할 Azure API Management 서비스를 포함하는 리소스 그룹의 이름입니다.
* `serviceName` - 백업을 복원할 API Management 서비스를 만들 때 지정한 이름입니다.
* `api-version` - `2018-06-01-preview`(으)로 대체

요청 본문에서 백업 파일 위치를 지정합니다. 즉, Azure Storage 계정 이름, 액세스 키, Blob 컨테이너 이름 및 백업 이름을 추가합니다.

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

`Content-Type` 요청 헤더의 값을 `application/json`으로 설정합니다.

복원은 오랫동안 실행되는 작업으로, 완료되려면 30분 이상이 걸릴 수 있습니다. 요청이 성공하고 복원 프로세스가 시작된 경우 `Location` 헤더가 포함된 `202 Accepted` 응답 상태 코드를 받게 됩니다. `Location` 헤더에서 URL에 대한 'GET' 요청을 수행하면 작업 상태를 확인할 수 있습니다. 복원이 진행 중인 동안에는 '202 수락됨' 상태 코드가 계속 수신됩니다. 응답 코드가 `200 OK` 이면 복원 작업이 정상적으로 완료된 것입니다.

> [!IMPORTANT]
> 백업을 복원할 서비스의 **SKU**는 복원하려는 백업된 서비스의 SKU와 **일치해야** 합니다.
>
> 복원 작업이 진행되는 동안 API, 정책, 개발자 포털 모양 등의 서비스 구성에 적용된 **변경 내용**을 **덮어쓸 수 있습니다**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> PowerShell을 사용 하 여 백업 및 복원 작업을 수행할 수도 있습니다 *백업 AzApiManagement* 하 고 *복원 AzApiManagement* 각각 명령입니다.

## <a name="next-steps"></a>다음 단계

백업/복원 프로세스의 다른 연습을 위해 다음 리소스를 확인합니다.

* [Azure API Management 계정 복제](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Logic Apps로 API Management 백업 및 복원 자동화](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Azure API Management: 구성 백업 및 복원](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *Stuart에서 구체화된 접근 방식은 공식 지침과 일치하지 않지만 흥미롭습니다.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
