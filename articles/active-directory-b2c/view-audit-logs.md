---
title: 감사 로그 액세스 및 검토
titleSuffix: Azure AD B2C
description: Azure AD B2C 감사 로그에 프로그래밍 방식으로 및 Azure 포털에서 액세스하는 방법
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264220"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Azure AD B2C 감사 로그 액세스

Azure Active Directory B2C(Azure AD B2C)는 B2C 리소스, 발급된 토큰 및 관리자 액세스에 대한 활동 정보가 포함된 감사 로그를 내보섭니다. 이 문서에서는 감사 로그에서 사용할 수 있는 정보에 대한 간략한 개요와 Azure AD B2C 테넌트에 대해 이 데이터에 액세스하는 방법에 대한 지침을 제공합니다.

감사 로그 이벤트는 **7일 동안만**유지됩니다. 보존 기간이 더 오래 필요한 경우, 아래 표시된 방법 중 하나를 사용하여 로그를 다운로드하고 저장하도록 플랜하세요.

> [!NOTE]
> Azure **포털의 Azure Active Directory** 또는 **Azure AD B2C** 페이지의 사용자 섹션 아래에 개별 Azure AD B2C 응용 프로그램에 대한 **사용자** 로그인을 볼 수 없습니다. 로그인 이벤트는 사용자 활동을 표시하지만 사용자가 로그인한 B2C 응용 프로그램과 다시 상호 연관될 수 없습니다. 이 문서에서 자세히 설명한 대로 감사 로그를 사용해야 합니다.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>감사 로그의 B2C 범주에서 사용 가능한 작업 개요

감사 로그의 **B2C** 범주에는 다음 유형의 작업이 포함됩니다.

|활동 유형 |설명  |
|---------|---------|
|권한 부여 |B2C 리소스에 액세스하는 사용자의 권한 부여와 관련된 활동(예: B2C 정책 목록에 액세스하는 관리자).         |
|디렉터리 |관리자가 Azure 포털을 사용하여 로그인할 때 검색되는 디렉터리 특성과 관련된 활동입니다. |
|애플리케이션 | B2C 응용 프로그램에서 CRUD(CRUD) 작업을 생성, 읽기, 업데이트 및 삭제합니다. |
|Key |B2C 키 컨테이너에 저장된 키에 대한 CRUD 작업입니다. |
|리소스 |B2C 리소스에 대한 CRUD 작업. 예를 들어 정책 및 ID 공급자입니다.
|인증 |사용자 자격 증명 및 토큰 발급 의 유효성 검사.|

사용자 개체 CRUD 활동의 경우 **핵심 디렉터리** 범주를 참조하세요.

## <a name="example-activity"></a>예제 활동

Azure 포털의 이 예제 이미지는 사용자가 외부 ID 공급자(이 경우 Facebook)와 로그인할 때 캡처된 데이터를 보여 주며, 이 경우 Facebook은 다음과 같은 작업을 수행합니다.

![Azure 포털의 감사 로그 활동 세부 정보 페이지의 예](./media/view-audit-logs/audit-logs-example.png)

활동 세부 정보 패널에는 다음과 같은 관련 정보가 포함되어 있습니다.

|섹션|필드|설명|
|-------|-----|-----------|
| 활동 | 이름 | 어떤 활동이 이루어졌는가. 예를 들어 실제 사용자 *로그인을*마무리하는 응용 프로그램에 id_token 발급합니다. |
| 초기자(작업자) | ObjectId | 사용자가 로그인하는 B2C 응용 프로그램의 **개체 ID입니다.** 이 식별자는 Azure 포털에 표시되지 않지만 Microsoft 그래프 API를 통해 액세스할 수 있습니다. |
| 초기자(작업자) | Spn | 사용자가 로그인하는 B2C 응용 프로그램의 응용 프로그램 **ID입니다.** |
| 대상 | ObjectId | 로그인하는 사용자의 **개체 ID입니다.** |
| 추가 세부 정보 | TenantId | Azure AD B2C **테넌트의 테넌트 ID입니다.** |
| 추가 세부 정보 | PolicyId | 사용자를 로그인하는 데 사용되는 사용자 흐름(정책)의 **정책 ID입니다.** |
| 추가 세부 정보 | ApplicationId | 사용자가 로그인하는 B2C 응용 프로그램의 응용 프로그램 **ID입니다.** |

## <a name="view-audit-logs-in-the-azure-portal"></a>Azure 포털에서 감사 로그 보기

Azure 포털은 Azure AD B2C 테넌트의 감사 로그 이벤트에 대한 액세스를 제공합니다.

1. [Azure 포털에](https://portal.azure.com) 로그인
1. Azure AD B2C 테넌트를 포함하는 디렉터리로 전환한 다음 **Azure AD B2C로**찾아봅차 로 이동합니다.
1. 왼쪽 메뉴의 **활동에서** **감사 로그를 선택합니다.**

지난 7일 동안 기록된 활동 이벤트 목록이 표시됩니다.

![Azure 포털에서 두 개의 활동 이벤트가 있는 예](./media/view-audit-logs/audit-logs-example-filter.png)

다음과 같은 몇 가지 필터링 옵션을 사용할 수 있습니다.

* **활동 리소스 유형** - 사용 가능한 활동 [개요 섹션의](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) 표에 표시된 활동 유형별로 필터링합니다.
* **날짜** - 표시된 활동의 날짜 범위를 필터링합니다.

목록에서 행을 선택하면 이벤트에 대한 활동 세부 정보가 표시됩니다.

CSV(쉼표로 구분된 값) 파일에서 활동 이벤트 목록을 다운로드하려면 **다운로드를**선택합니다.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Azure AD 보고 API를 통해 감사 로그 받기

감사 로그는 Azure Active Directory에 대한 다른 활동과 동일한 파이프라인에 게시되므로 [Azure Active Directory 보고 API](https://docs.microsoft.com/graph/api/directoryaudit-list)를 통해 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory 보고 API를 시작합니다.](../active-directory/reports-monitoring/concept-reporting-api.md)

### <a name="enable-reporting-api-access"></a>보고 API 액세스 사용

Azure AD 보고 API에 대한 스크립트 또는 응용 프로그램 기반 액세스를 허용하려면 다음 API 권한이 있는 Azure AD B2C 테넌트에 등록된 응용 프로그램이 필요합니다. B2C 테넌트 내의 기존 응용 프로그램 등록에서 이러한 권한을 활성화하거나 감사 로그 자동화와 함께 사용할 새 권한을 만들 수 있습니다.

* 감사로그 > AuditLog.Read.All을 > Microsoft 그래프 > 응용 프로그램 권한

다음 문서의 단계를 수행하여 필요한 사용 권한으로 응용 프로그램을 등록합니다.

[마이크로소프트 그래프로 Azure AD B2C 관리](microsoft-graph-get-started.md)

적절한 사용 권한으로 응용 프로그램을 등록한 후 이 문서의 PowerShell 스크립트 섹션을 참조하여 스크립트를 사용해 활동 이벤트를 얻을 수 있는 방법에 대한 예제를 참조하세요.

### <a name="access-the-api"></a>API 액세스

API를 통해 Azure AD B2C 감사 로그 이벤트를 `B2C` 다운로드하려면 범주의 로그를 필터링합니다. 범주별로 필터링하려면 Azure `filter` AD 보고 API 끝점을 호출할 때 쿼리 문자열 매개 변수를 사용합니다.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>PowerShell 스크립트

다음 PowerShell 스크립트는 Azure AD 보고 API를 쿼리하는 방법의 예를 보여 주며 있습니다. API를 쿼리한 후 기록된 이벤트를 표준 출력으로 인쇄한 다음 JSON 출력을 파일에 씁니다.

[Azure 클라우드 셸에서](overview.md)이 스크립트를 시도할 수 있습니다. 응용 프로그램 ID, 클라이언트 보안 및 Azure AD B2C 테넌트의 이름으로 업데이트해야 합니다.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```

다음은 문서의 앞에 표시된 예제 활동 이벤트의 JSON 표현입니다.

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

예를 들어 [Microsoft 그래프를 통해 Azure AD B2C 사용자 계정을 관리하는](manage-user-accounts-graph-api.md)것과 같은 다른 관리 작업을 자동화할 수 있습니다.
