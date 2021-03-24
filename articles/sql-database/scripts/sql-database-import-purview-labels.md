---
title: Azure Purview 레이블을 사용하여 Azure SQL 데이터 분류
description: Azure SQL Database 및 Azure Synpase Analytics에 Azure Purview의 분류 가져오기
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: azurepowershell
ms.topic: sample
author: davidtrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/17/2021
ms.openlocfilehash: 2eab7c535ff0c68da772e8a45ead12420734279c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714904"
---
# <a name="classify-your-azure-sql-data-using-azure-purview-labels"></a>Azure Purview 레이블을 사용하여 Azure SQL 데이터 분류
[!INCLUDE[appliesto-sqldb-asa](../../azure-sql/includes/appliesto-sqldb-asa.md)]

이 문서에서는 Azure SQL Database 및 Azure Synapse Analytics(이전의 SQL DW)에 Azure Purview 레이블을 추가하는 방법에 대해 설명합니다.

## <a name="create-an-application"></a>애플리케이션 만들기

1. Azure Portal에서 **Azure Active Directory** 를 엽니다.
2. **관리** 아래에서 **앱 등록** 을 선택합니다.
3. **새 애플리케이션** 을 선택하여 새 Azure Active Directory 앱을 만듭니다.
4. 애플리케이션에 사용할 이름을 입력하고 **등록** 을 선택합니다.
5. 애플리케이션을 만든 후 **관리자** 에서 **인증서 및 비밀** 을 엽니다.
6. **클라이언트 암호** 에서 **새 클라이언트 암호** 를 선택하여 새 클라이언트 암호를 만듭니다.
7. 클라이언트 암호에 대한 설명을 추가하고 만료 기간을 선택한 후 **추가** 를 선택합니다.
8. 나중에 사용할 수 있게 **값** 을 보관합니다.

   > [!NOTE]
   > 페이지를 닫으면 값이 마스킹됩니다. 페이지로 돌아가면 클라이언트 암호의 값을 검색할 수 없습니다. 새 클라이언트 암호를 생성해야 합니다.

9. 새로 만든 애플리케이션의 개요 페이지로 돌아가서 나중에 사용할 수 있게 다음 값을 복사합니다.
    1. 애플리케이션(클라이언트) ID
    1. 디렉터리(테넌트) ID

## <a name="provide-permissions-to-the-application"></a>애플리케이션에 권한 할당

1. Azure Portal에서 **Purview 계정** 을 검색합니다.
2. SQL 데이터베이스 및 Synapse가 분류된 Purview 계정을 선택합니다.
3. **액세스 제어(IAM)** 를 열고 **추가** 를 선택합니다.

4. **역할 할당 추가** 를 선택합니다.
5. **역할** 섹션에서 **Purview 데이터 판독기** 를 검색하고 선택합니다.
6. **선택** 섹션에서 이전에 만든 애플리케이션을 검색하여 선택하고 **저장** 을 누릅니다.

## <a name="extract-the-classification-from-azure-purview"></a>Azure Purview에서 분류 추출

1. Purview 계정을 열고 홈 페이지에서 레이블을 복사하려는 Azure SQL Database 또는 Azure Synapse Analytics를 검색합니다.
2. **속성** 에서 qualifiedName을 복사하고 나중에 사용할 수 있게 보관합니다.
3. PowerShell 셸을 엽니다.

4. SQL 자산 유형(Azure SQL Database 또는 Azure Synapse)에 따라 아래 스크립트 중 하나를 복사합니다.
5. 매개 변수를 위에서 복사한 값으로 채웁니다.

   a. $TenantID: 섹션 1, 9단계
   
   b. $ClientID: 섹션 1, 9단계
   
   다. $SecretID: 섹션 1, 8단계
   
   d. $purviewAccountName: 섹션 2, 2단계
   
   e. $sqlDatabaseName: 섹션 3, 2단계

6. 나중에 사용할 수 있게 스크립트의 출력을 복사합니다.

### <a name="for-azure-sql-database"></a>Azure SQL 데이터베이스의 경우

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$sqlDatabaseName="mssql://sql_server_name.database.windows.net/db_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_db?attr:qualifiedName=" + $sqlDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

### <a name="for-azure-synapse-analytics"></a>Azure Synapse Analytics의 경우

```powershell
# Replace the values below with the relevant values for your environment
$TenantID = 'your_tenant_id'
$ClientID = 'your_client_id'
$SecretID = 'your_secret_id'
$purviewAccountName='purview_account_name'
$dwDatabaseName="mssql://dw_server_name.database.windows.net/dw_name"

###############################################################################
# Get an access accessToken, and build REST Request Header.
###############################################################################
$cmdletParameters = @{
  Method  = "POST"
  URI     = "https://login.microsoftonline.com/$TenantID/oauth2/token"
  Headers = @{
    "Content-Type" = 'application/x-www-form-urlencoded'
  }
  Body    = "client_id=$ClientID&client_secret=$SecretID&resource=https://purview.azure.net&grant_type=client_credentials"
}

$invokeResult = Invoke-RestMethod @cmdletParameters;
$accessToken = $invokeResult.access_token; 

$restRequestHeader = @{
  "authorization" = "Bearer $accessToken"
};

###############################################################################
# Get database entity.
###############################################################################
$getDatabaseEntityEntryEndpoint = "https://" + $purviewAccountName + ".catalog.purview.azure.com/api/atlas/v2/entity/uniqueAttribute/type/azure_sql_dw?attr:qualifiedName=" + $dwDatabaseName;
$cmdletParameters = @{
  Method      = "Get"
  Uri         = $getDatabaseEntityEntryEndpoint
  Headers     = $restRequestHeader
  ContentType = "application/json"        
};

$invokeResult =  Invoke-RestMethod @cmdletParameters;
$referredEntities = $invokeResult.referredEntities;
if ($null -eq $referredEntities) {
  Write-Output "No referred entities found under database entity!";
  exit;
}

###############################################################################
# Iterate database referred entities, find classified columns, and build T-SQL.
###############################################################################
foreach ($referredEntity in $referredEntities.psobject.Properties.GetEnumerator()) {
  $typeName = $referredEntity.Value.typeName;
  if ($null -eq $typeName -or $typeName -ne 'azure_sql_dw_column') {
    continue;
  }
  
  $classifications = $referredEntity.Value.classifications;
  if ($null -eq $classifications) {
    continue;
  }
  
  foreach ($classification in $classifications.GetEnumerator())
  {
    if ($classification.typeName -notmatch 'Microsoft\.Label\.(?<labelId>.+)') { 
        continue;
    }

      $labelId = $Matches.labelId -replace "_", "-";

      $attributes = $referredEntity.Value.attributes;
      if ($null -eq $attributes) {
        continue;
      }
    
      $qualifiedName = $attributes.qualifiedName;
      if ($qualifiedName -notmatch '.+\.database\.windows\.net\/.+\/(?<schemaName>.+)\/(?<tableName>.+)#(?<columnName>.+)') {
        continue;
      }

      $schemaName = $Matches.schemaName;
      $tableName = $Matches.tableName;
      $columnName = $Matches.columnName;
      
      Write-Output "ADD SENSITIVITY CLASSIFICATION TO ${schemaName}.${tableName}.${columnName} WITH (LABEL='Azure Purview Label', LABEL_ID='${labelId}');";
  }
}
```

## <a name="run-the-t-sql-command-on-your-sql-asset"></a>SQL 자산에서 T-SQL 명령 실행

1. 선택한 도구를 사용하여 Azure SQL Database 또는 Azure Synapse에 연결합니다.
2. 이전 섹션에서 복사한 T-SQL 명령을 실행합니다.

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

Azure Purview에 대한 자세한 내용은 [Azure Purview 설명서](../../purview/index.yml)를 참조하세요.