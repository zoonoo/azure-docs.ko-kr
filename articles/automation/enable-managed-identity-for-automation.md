---
title: Azure Automation 계정에 관리 ID 사용 설정(미리 보기)
description: 이 문서에서는 Azure Automation 계정에 관리 ID를 설정하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 93c55c21bf740f2851cac1926bc673cebcd914b0
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514804"
---
# <a name="enable-a-managed-identity-for-your-azure-automation-account-preview"></a>Azure Automation 계정에 관리 ID 사용 설정(미리 보기)

이 항목에서는 Azure Automation 계정에 관리 ID를 만드는 방법과 이 ID를 잉요해 다른 리소스에 액세스하는 방법을 설명합니다. 관리 ID가 Azure Automation에서 어떻게 작용하는지에 대한 자세한 내용은 [관리 ID](automation-security-overview.md#managed-identities-preview)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Runbook에서 해당 ID를 사용하여 관리하는 관리 ID와 대상 Azure 리소스가 같은 Azure 구독에 있어야 합니다.

- Azure Automation 계정 모듈의 최신 버전입니다. 현재 최신 버전은 1.6.0입니다. (이 버전에 대한 자세한 내용은 [Az.Automation 1.6.0](https://www.powershellgallery.com/packages/Az.Automation/1.6.0)을 참조하세요.)

- Automation Runbook에서 액세스할 Azure 리소스입니다. 이 리소스에는 Automation Runbook에서 리소스에 대한 액세스를 인증하는 데 도움이 되는 관리 ID에 정의된 역할이 있어야 합니다. 역할을 추가하려면 대응하는 Azure AD 테넌트 리소스의 소유자여야 합니다.

- 관리 ID를 사용하여 하이브리드 작업을 실행하려는 경우에는 Hybrid Runbook Worker를 최신 버전으로 업데이트합니다. 필요한 최소 버전:

   - Windows Hybrid Runbook Worker: 버전 7.3.1125.0
   - Linux Hybrid Runbook Worker: 버전 1.7.4.0

## <a name="enable-system-assigned-identity"></a>시스템이 할당한 ID 사용

>[!IMPORTANT]
>새 Automation 계정 수준 ID는 ([관리 ID를 통한 Runbook 인증 사용](/automation-hrw-run-runbooks#runbook-auth-managed-identities)에서 자세하게 설명하는) 이전 VM 수준 시스템이 할당한 ID를 재정의합니다. VM의 시스템 할당 ID를 이용해 Runbook 리소스에 액세스하는 하이브리드 작업을 Azure VM에서 실행한다면, 하이브리드 작업에는 Automation 계정 ID를 사용합니다. 따라서 Automation 계정의 고객 관리형 키(CMK) 기능을 사용 하면 기존 작업 실행이 영향을 받을 수 있습니다.<br/><br/>VM의 관리 ID를 계속 사용하고 싶다면 Automation 계정 수준 ID를 사용 설정하지 않아야 합니다. 이미 사용 설정한 경우에는 Automation 계정 관리 ID를 사용하지 않도록 설정해야 합니다. [Azure Automation 계정 관리형 ID 사용 안 함](https://docs.microsoft.com/azure/automation/disable-managed-identity-for-automation)을 참조하세요.

Azure Automation에 시스템 할당 ID를 설정하는 작업은 두 가지 방법으로 수행할 수 있습니다. Azure Portal이나 REST API를 사용하면 됩니다.

>[!NOTE]
>사용자 할당 ID는 아직 지원되지 않습니다.

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure Portal에서 시스템이 할당한 ID 사용

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Automation 계정으로 이동하고 **계정 설정** 에서 **ID** 를 선택합니다.

1. **시스템 할당** 옵션을 **On** 으로 설정하고 **저장** 을 누릅니다. 확인하라는 메시지가 나타나면 **예** 를 선택합니다.

:::image type="content" source="media/managed-identity/managed-identity-on.png" alt-text="Azure Portal에서 시스템이 할당한 ID 사용.":::

이제 Automation 계정에서 시스템이 할당한 ID를 사용할 수 있습니다. ID는 Azure Active Directory(Azure AD)에 등록되고 개체 ID로 표시됩니다.

:::image type="content" source="media/managed-identity/managed-identity-object-id.png" alt-text="관리 ID 개체 ID.":::

### <a name="enable-system-assigned-identity-through-the-rest-api"></a>REST API를 통해 시스템 할당 ID 사용

다음 REST API 호출을 사용하면 Automation 계정에 대한 시스템 할당 관리 ID를 구성할 수 있습니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

요청 본문
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 .
 .
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
.
.
}
```

|속성(JSON) | 값 | Description|
|----------|-----------|------------|
| principalid | \<principal-ID\> | Azure AD 테넌트에서 Automation 계정 앱을 나타내는 관리 ID에 대한 서비스 주체 개체의 GUID(Globally Unique Identifier)입니다. 이 GUID는 ‘개체 ID’ 또는 objectID로 표시되기도 합니다. |
| tenantid | \<Azure-AD-tenant-ID\> | Automation 계정이 현재 멤버로 속해 있는 Azure AD 테넌트를 나타내는 GUID(Globally Unique Identifier)입니다. Azure AD 테넌트 내부에서 서비스 주체는 Automation 계정과 이름이 같습니다. |

## <a name="give-identity-access-to-azure-resources-by-obtaining-a-token"></a>토큰을 얻어 Azure 리소스에 대한 ID 액세스 가져오기

Automation 계정은 자체 관리 ID를 사용하여 Azure Key Vault 같은 Azure AD로 보호되는 다른 리소스에 액세스하는 토큰을 가져올 수 있습니다. 이러한 토큰은 애플리케이션의 특정 사용자를 나타내지 않습니다. 리소스에 액세스하는 애플리케이션을 나타냅니다. 예를 들어 이 경우 토큰은 Automation 계정을 나타냅니다.

시스템이 할당한 관리 ID를 인증에 사용하려면 먼저 ID를 사용하려는 Azure 리소스에서 해당 ID에 대한 액세스 권한을 설정합니다. 이 작업을 완료하려면 대상 Azure 리소스에서 해당 ID에 적절한 역할을 할당합니다.

이 예제에서는 Azure PowerShell을 사용하여 구독의 기여자 역할을 대상 Azure 리소스에 할당하는 방법을 보여줍니다. 기여자 역할은 예시이며 사용자의 경우에는 필요하지 않을 수도 있습니다.

```powershell
New-AzRoleAssignment -ObjectId <automation-Identity-object-id> -Scope "/subscriptions/<subscription-id>" -RoleDefinitionName "Contributor"
```

## <a name="authenticate-access-with-managed-identity"></a>관리 ID를 사용하여 액세스 인증

Automation 계정에 관리 ID를 사용 설정하고 대상 리소스에 ID 액세스를 부여하면, 관리 ID를 지원하는 리소스를 대상으로 해당 ID를 Runbook에서 지정할 수 있습니다. ID를 지원하려면 Az cmdlet `Connect-AzAccount` cmdlet를 사용합니다. PowerShell 참조에서 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount)를 참조하세요.

```powershell
Connect-AzAccount -Identity
```

>[!NOTE]
>조직에서 아직 전용 AzureRM cmdlet을 사용한다면 `Connect-AzureRMAccount -Identity`를 사용할 수 있습니다.

## <a name="generate-an-access-token-without-using-azure-cmdlets"></a>Azure cmdlet을 사용 하지 않고 액세스 토큰 생성

HTTP 엔드포인트의 경우에는 다음을 확인합니다.
- 메타데이터 헤더가 있고 ‘true’로 설정되어야 합니다.
- 리소스는 GET 요청에 대한 쿼리 매개 변수로서, 그리고 POST 요청에 대한 양식 데이터로서 요청과 함께 전달되어야 합니다.
- X-IDENTITY-HEADER는 Hybrid Runbook Worker용 환경 변수인 IDENTITY_HEADER의 값으로 설정되어야 합니다. 
- Post 요청의 콘텐츠 형식은 'application/x-www-form-urlencoded'여야 합니다. 

### <a name="sample-get-request"></a>샘플 GET 요청

```powershell
$resource= "?resource=https://management.azure.com/" 
$url = $env:IDENTITY_ENDPOINT + $resource 
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$accessToken = Invoke-RestMethod -Uri $url -Method 'GET' -Headers $Headers
Write-Output $accessToken.access_token
```

### <a name="sample-post-request"></a>샘플 POST 요청
```powershell
$url = $env:IDENTITY_ENDPOINT  
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$headers.Add("Metadata", "True") 
$body = @{resource='https://management.azure.com/' } 
$accessToken = Invoke-RestMethod $url -Method 'POST' -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $body 
Write-Output $accessToken.access_token
```

## <a name="sample-runbooks-using-managed-identity"></a>관리 ID를 사용하는 샘플 Runbook

### <a name="sample-runbook-to-access-a-sql-database-without-using-azure-cmdlets"></a>Azure cmdlet을 사용하지 않고 SQL 데이터베이스에 액세스하는 샘플 Runbook

```powershell
$queryParameter = "?resource=https://database.windows.net/" 
$url = $env:IDENTITY_ENDPOINT + $queryParameter
$Headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$Headers.Add("X-IDENTITY-HEADER", $env:IDENTITY_HEADER) 
$Headers.Add("Metadata", "True") 
$content =[System.Text.Encoding]::Default.GetString((Invoke-WebRequest -UseBasicParsing -Uri $url -Method 'GET' -Headers $Headers).RawContentStream.ToArray()) | ConvertFrom-Json 
$Token = $content.access_token 
echo "The managed identities for Azure resources access token is $Token" 
$SQLServerName = "<ServerName>"    # Azure SQL logical server name  
$DatabaseName = "<DBname>"     # Azure SQL database name 
Write-Host "Create SQL connection string" 
$conn = New-Object System.Data.SqlClient.SQLConnection  
$conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30" 
$conn.AccessToken = $Token 
Write-host "Connect to database and execute SQL script" 
$conn.Open()  
$ddlstmt = "CREATE TABLE Person( PersonId INT IDENTITY PRIMARY KEY, FirstName NVARCHAR(128) NOT NULL)" 
Write-host " " 
Write-host "SQL DDL command" 
$ddlstmt 
$command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn) 
Write-host "results" 
$command.ExecuteNonQuery() 
$conn.Close()
```

### <a name="sample-runbook-to-access-a-key-vault-using-azure-cmdlets"></a>Azure cmdlet을 사용하지 않고 키 자격 증명 모음에 액세스하는 샘플 Runbook

```powershell
Write-Output "Connecting to azure via  Connect-AzAccount -Identity" 
Connect-AzAccount -Identity 
Write-Output "Successfully connected with Automation account's Managed Identity" 
Write-Output "Trying to fetch value from key vault using MI. Make sure you have given correct access to Managed Identity" 
$secret = Get-AzKeyVaultSecret -VaultName '<KVname>' -Name '<KeyName>' 

$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue) 
try { 
  $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr) 
    Write-Output $secretValueText 
} finally { 
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr) 
}
```

### <a name="sample-python-runbook-to-get-a-token"></a>토큰을 가져오는 샘플 Python Runbook
 
```python
#!/usr/bin/env python3 
import os 
import requests  
# printing environment variables 
endPoint = os.getenv('IDENTITY_ENDPOINT')+"?resource=https://management.azure.com/" 
identityHeader = os.getenv('IDENTITY_HEADER') 
payload={} 
headers = { 
  'X-IDENTITY-HEADER': identityHeader,
  'Metadata': 'True' 
} 
response = requests.request("GET", endPoint, headers=headers, data=payload) 
print(response.text) 
```

## <a name="next-steps"></a>다음 단계

- 관리 ID를 사용하지 않도록 설정해야 한다면, [Azure Automation 계정 관리 ID 사용 안 함(미리 보기)](disable-managed-identity-for-automation.md)을 참조하세요.

- Azure Automation 계정 보안에 대한 개요는 [Automation 계정 인증 개요](automation-security-overview.md)를 참조하세요.