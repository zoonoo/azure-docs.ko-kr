---
title: "Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리 | Microsoft Docs"
description: "Data Lake Analytics 계정, 데이터 원본, 작업 및 카탈로그 항목을 관리하는 방법을 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: mahi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 86648ebc5e13570050bb142158bf1aa356d466b3
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure PowerShell을 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 작업 및 카탈로그 항목을 관리하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>필수 조건

Data Lake Analytics 계정을 만들 때는 다음을 알아두어야 합니다.

* **구독 ID**: Data Lake Analytics 계정이 상주하는 Azure 구독 ID입니다.
* **리소스 그룹**: Data Lake Analytics 계정이 포함된 Azure 리소스 그룹의 이름입니다.
* **Data Lake Analytics 계정 이름**: 이 계정 이름은 소문자와 숫자만을 포함해야 합니다.
* **기본 Data Lake Store 계정**: 각 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 있습니다. 이러한 계정은 동일한 위치에 있어야 합니다.
* **위치**: Data Lake Analytics 계정의 위치입니다(예: "미국 동부 2" 또는 다른 지원되는 위치). 지원되는 위치는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-lake-analytics/)에서 확인할 수 있습니다.

이 자습서의 PowerShell 코드 조각은 이러한 변수를 사용하여 이 정보를 저장합니다.

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>로그인

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

> [!NOTE]
> 나중에 사용할 수 있도록 로그인 세션을 저장하려면 ``Save-AzureRmProfile``을 사용합니다. 로그인 세션을 다시 로드하려면 ``Select-AzureRmProfile``을 사용합니다.

## <a name="managing-accounts"></a>계정 관리

### <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기

사용할 [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)이 아직 없는 경우 만듭니다. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

모든 Data Lake Analytics 계정에는 로그를 저장하는 데 사용할 기본 Data Lake Store 계정이 필요합니다. 기존 계정을 다시 사용하거나 새 계정을 만들 수 있습니다. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

리소스 그룹 및 Data Lake Store 계정을 사용할 수 있게 되면 Data Lake Analytics 계정을 만듭니다.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>계정에 대한 정보 가져오기

계정에 대한 세부 정보를 가져옵니다.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

특정 Data Lake Analytics 계정의 존재 여부를 확인합니다. 이 cmdlet은 `True` 또는 `False`를 반환합니다.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

### <a name="listing-accounts"></a>계정 나열

현재 구독 내의 Data Lake Analytics 계정을 나열합니다.

```powershell
Get-AdlAnalyticsAccount
```

특정 리소스 그룹 내의 Data Lake Analytics 계정을 나열합니다.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>방화벽 규칙 관리

### <a name="add-or-remove-firewall-rules"></a>방화벽 규칙 추가 또는 제거

방화벽 규칙을 나열합니다.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

방화벽 규칙을 추가합니다.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

방화벽 규칙을 변경합니다.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

방화벽 규칙을 제거합니다.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="enable-or-disable-firewall-rules"></a>방화벽 규칙 사용 또는 사용 안 함

방화벽 규칙을 사용합니다.

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
```

Azure IP 주소를 허용합니다.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

방화벽 규칙을 사용하지 않습니다.

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>데이터 원본 관리
Azure Data Lake Analytics는 현재 다음 데이터 원본을 지원합니다.

* [Azure 데이터 레이크 저장소](../data-lake-store/data-lake-store-overview.md)
* [Azure 저장소](../storage/storage-introduction.md)

Analytics 계정을 만들 때 Data Lake Store 계정이 기본 데이터 원본이 되도록 지정해야 합니다. 기본 데이터 레이크 저장소 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. Data Lake Analytics 계정을 만든 후 Data Lake Store 계정 및/또는 Azure 저장소 계정을 더 추가할 수 있습니다. 

### <a name="find-the-default-data-lake-store-account"></a>기본 데이터 레이크 저장소 계정 찾기

```powershell
$dataLakeStoreName = (Get-AdlAnalyticsAccount -Name $adla).DefaultDataLakeAccount
```

### <a name="add-data-sources"></a>데이터 원본 추가

추가 저장소(Blob) 계정을 추가합니다.

```powershell
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"

Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey
```

추가 Data Lake Store 계정을 추가합니다.

```powershell
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>데이터 원본 나열

모든 연결된 데이터 원본을 나열합니다.

```powershell
Get-AdlAnalyticsDataSource -Name $adla
```

연결된 Data Lake Store 계정을 나열합니다.

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"
```

연결된 저장소 계정을 나열합니다.

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="managing-jobs"></a>작업 관리

### <a name="submit-a-job"></a>작업 제출

다음 U-SQL 스크립트가 포함된 로컬 텍스트 파일을 만듭니다.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

스크립트를 제출합니다.

```powershell
Submit-AdlJob -AccountName $adla –ScriptPath "<LocalPathToScriptFile>"
```

### <a name="list-jobs"></a>작업 나열

계정에서 모든 작업을 나열합니다. 출력은 현재 실행 중인 작업 및 최근에 완료된 해당 작업을 포함합니다.

```powershell
Get-AdlJob -Account $adla
```

지난 7일 이내에 "joe@contoso.com"이 제출한 실패한 작업을 모두 나열합니다.

```powershell
Get-AdlJob -Account $adla `
    -Submitter "joe@contoso.com" `
    -SubmittedAfter (Get-Date).AddDays(-7) `
    -Result Failed
```

### <a name="get-information-about-a-job"></a>작업 정보 가져오기

특정 작업의 상태를 가져옵니다.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

작업이 완료될 때까지 `Get-AdlAnalyticsJob`을 반복하는 대신 `Wait-AdlJob` cmdlet을 사용하여 작업이 종료될 때까지 기다릴 수 있습니다.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

작업이 종료되면 폴더의 파일을 나열하여 출력 파일이 있는지 확인합니다.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

파일의 존재 여부를 확인합니다.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="cancel-a-job"></a>작업 취소

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

## <a name="uploading-and-downloading"></a>업로드 및 다운로드

파일을 업로드합니다.

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\data.tsv" `
    -Destination "/data_copy.csv" 
```

전체 폴더를 업로드합니다.

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\myData\" `
    -Destination "/myData/" `
    -Recurse
```

파일을 다운로드합니다.

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/data.csv" `
    -Destination "<LocalPath>\data.csv"
```

전체 폴더를 다운로드합니다.

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/" `
    -Destination "<LocalPath>\myData\" `
    -Recurse
```

> [!NOTE]
> 업로드 또는 다운로드 프로세스가 중단될 경우 ``-Resume`` 플래그와 함께 해당 cmdlet을 다시 실행하여 프로세스를 다시 시작할 수 있습니다.

## <a name="manage-catalog-items"></a>카탈로그 항목 관리
U-SQL 카탈로그는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다. 자세한 내용은 [U-SQL 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)을 참조하세요.

### <a name="list-items"></a>항목 나열

U-SQL 데이터베이스를 나열합니다.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Database 
```

데이터베이스 내 테이블을 나열합니다.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master"
```

스키마 내 테이블을 나열합니다.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master.dbo"
```

### <a name="get-information-about-a-catalog-item"></a>카탈로그 항목에 대한 정보 가져오기

U-SQL 데이터베이스의 세부 정보를 가져옵니다.

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

U-SQL 데이터베이스의 테이블에 대한 세부 정보를 가져옵니다.

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"
```

U-SQL 데이터베이스의 존재 여부를 테스트합니다.

```powershell
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-catalog-items"></a>카탈로그 항목 만들기

U-SQL 데이터베이스 내에서 Azure에서 호스트되는 데이터베이스에 대한 자격 증명 개체를 만듭니다. 현재 U-SQL 자격 증명은 PowerShell을 통해 만들 수 있는 유일한 유형의 카탈로그 항목입니다.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>템플릿을 사용하여 Data Lake Analytics 계정 만들기

또한 다음 PowerShell 스크립트를 통해 Azure 리소스 그룹 템플릿을 사용할 수도 있습니다.

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 응용 프로그램 배포](../azure-resource-manager/resource-group-template-deploy.md) 및 [Azure Resource Manager 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.

**예제 템플릿**

다음 텍스트를 `.json` 파일로 저장한 후 이전의 PowerShell 스크립트를 통해 템플릿을 사용합니다. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)을 사용하여 Data Lake Analytics 시작
* [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) |  [Azure portal](data-lake-analytics-manage-use-portal.md) | [CLI](data-lake-analytics-manage-use-cli.md)를 사용하여 Azure Data Lake Analytics 관리 

