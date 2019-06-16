---
title: Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리
description: 이 문서에서는 Azure PowerShell을 사용하여 Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 4273828c9c2bdb75fcbc1de45da55c5a03dd615f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156413"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

이 문서에서는 Azure PowerShell을 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Data Lake Analytics와 함께 PowerShell을 사용하려면 다음과 같은 정보를 수집합니다. 

* **구독 ID**: Data Lake Analytics 계정을 포함하는 Azure 구독의 ID입니다.
* **리소스 그룹**: Data Lake Analytics 계정을 포함하는 Azure 리소스 그룹의 이름입니다.
* **Data Lake Analytics 계정 이름**: Data Lake Analytics 계정의 이름입니다.
* **기본 Data Lake Store 계정 이름**: 모든 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 있습니다.
* **Location**: Data Lake Analytics 계정의 위치입니다(예: “미국 동부 2” 또는 다른 지원되는 위치).

이 자습서의 PowerShell 코드 조각은 이러한 변수를 사용하여 이 정보를 저장합니다.

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Azure에 로그인

### <a name="log-in-using-interactive-user-authentication"></a>대화형 사용자 인증을 사용하여 로그인

구독 ID를 사용하거나 구독 이름으로 로그인

```powershell
# Using subscription id
Connect-AzAccount -SubscriptionId $subId

# Using subscription name
Connect-AzAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>인증 컨텍스트 저장

`Connect-AzAccount` cmdlet은 항상 자격 증명을 묻는 메시지를 표시합니다. 다음 cmdlet을 사용하면 메시지가 표시되지 않게 할 수 있습니다.

```powershell
# Save login session information
Save-AzAccounts -Path D:\profile.json  

# Load login session information
Select-AzAccounts -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>SPI(Service Principle Identity)를 사용하여 로그인

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>계정 관리


### <a name="list-accounts"></a>계정 나열

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>계정 만들기

모든 Data Lake Analytics 계정에는 로그를 저장하는 데 사용할 기본 Data Lake Store 계정이 필요합니다. 기존 계정을 다시 사용하거나 새 계정을 만들 수 있습니다. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>계정 정보 가져오기

계정에 대한 세부 정보를 가져옵니다.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>계정이 있는지 확인

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>데이터 원본 관리
Azure Data Lake Analytics는 현재 다음 데이터 원본을 지원합니다.

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

모든 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 있습니다. 기본 Data Lake Store 계정은 작업 메타데이터 및 작업 감사 로그를 저장하는 데 사용됩니다. 

### <a name="find-the-default-data-lake-store-account"></a>기본 데이터 레이크 저장소 계정 찾기

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

`IsDefault` 속성으로 데이터 원본 목록을 필터링하여 기본 Data Lake Store 계정을 찾을 수 있습니다.

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>데이터 원본 추가

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>데이터 원본 나열

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL 작업 제출

### <a name="submit-a-string-as-a-u-sql-job"></a>문자열을 U-SQL 작업으로 제출

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-job"></a>파일을 U-SQL 작업으로 제출

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>작업 나열

출력은 현재 실행 중인 작업 및 최근에 완료된 해당 작업을 포함합니다.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>상위 N개 작업 나열

기본적으로 작업 목록은 제출 시간을 기준으로 정렬됩니다. 따라서 최근에 제출한 작업이 먼저 표시됩니다. ADLA 계정은 기본적으로 작업을 180일 동안 기억하지만, Ge-AdlJob cmdlet은 기본적으로 처음 500개 작업만 반환합니다. 특정 수의 작업을 나열하려면 -Top 매개 변수를 사용합니다.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>작업 상태별 작업 나열

`-State` 매개 변수 사용. 이러한 값을 결합할 수 있습니다.

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

### <a name="list-jobs-by-job-result"></a>작업 결과별 작업 나열

`-Result` 매개 변수를 사용하여 종료된 작업이 성공적으로 완료되었는지 여부를 검색합니다. 다음 값을 포함합니다.

* Cancelled
* 실패
* 없음
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>작업 제출자별 작업 나열

`-Submitter` 매개 변수를 사용하면 작업을 제출한 사람을 식별할 수 있습니다.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>제출 시간별 작업 나열

`-SubmittedAfter`는 시간 범위로 필터링하는 데 유용합니다.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>작업 상태 가져오기

특정 작업의 상태를 가져옵니다.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>작업 취소

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>작업이 완료될 때까지 대기

작업이 완료될 때까지 `Get-AdlAnalyticsJob`을 반복하는 대신 `Wait-AdlJob` cmdlet을 사용하여 작업이 종료될 때까지 기다릴 수 있습니다.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>작업 기록 분석

Azure PowerShell을 사용하여 Data Lake 분석에서 실행된 작업의 기록을 분석하는 것은 강력한 기술입니다. 사용량 및 비용에 대해 이해하는 데 사용할 수 있습니다. [작업 기록 분석 샘플 리포지토리](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)를 확인하여 자세히 알아볼 수 있습니다.  

## <a name="list-job-pipelines-and-recurrences"></a>작업 파이프라인 및 되풀이 나열

`Get-AdlJobPipeline` cmdlet을 사용하여 이전에 제출한 작업의 파이프라인 정보를 확인합니다.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

`Get-AdlJobRecurrence` cmdlet을 사용하여 이전에 제출한 작업의 되풀이 정보를 확인합니다.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>계산 정책 관리

### <a name="list-existing-compute-policies"></a>기존 계산 정책 나열

`Get-AdlAnalyticsComputePolicy` cmdlet은 Data Lake Analytics 계정에 대한 계산 정책에 대한 정보를 검색합니다.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>계산 정책 만들기

`New-AdlAnalyticsComputePolicy` cmdlet은 Data Lake Analytics 계정에 대한 새 계산 정책을 만듭니다. 이 예제에서는 지정된 사용자에게 제공되는 최대 AU를 50으로 설정하고, 최소 작업 우선 순위를 250으로 설정합니다.

```powershell
$userObjectId = (Get-AzAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>파일 관리

### <a name="check-for-the-existence-of-a-file"></a>파일의 존재 여부를 확인합니다.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>업로드 및 다운로드

파일을 업로드합니다.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

전체 폴더를 재귀적으로 업로드합니다.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

파일을 다운로드합니다.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

전체 폴더를 재귀적으로 다운로드합니다.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> 업로드 또는 다운로드 프로세스가 중단될 경우 ``-Resume`` 플래그와 함께 해당 cmdlet을 다시 실행하여 프로세스를 다시 시작할 수 있습니다.

## <a name="manage-the-u-sql-catalog"></a>U-SQL 카탈로그 관리

U-SQL 카탈로그는 U-SQL 스크립트에서 공유할 수 있도록 데이터 및 코드를 구성하는 데 사용됩니다. 카탈로그를 사용하면 가능한 가장 높은 성능으로 Azure 데이터 레이크의 데이터를 사용할 수 있습니다. 자세한 내용은 [U-SQL 카탈로그 사용](data-lake-analytics-use-u-sql-catalog.md)을 참조하세요.

### <a name="list-items-in-the-u-sql-catalog"></a>U-SQL 카탈로그의 항목 나열

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>모든 어셈블리 U-SQL 카탈로그 나열

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>카탈로그 항목에 대한 세부 정보 가져오기

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>카탈로그에 자격 증명 저장

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

## <a name="manage-firewall-rules"></a>방화벽 규칙 관리

### <a name="list-firewall-rules"></a>방화벽 규칙 나열

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>방화벽 규칙 추가

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>방화벽 규칙 수정

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>방화벽 규칙 제거

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Azure IP 주소 허용

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Azure 작업

### <a name="get-error-details"></a>오류 세부 정보 가져오기

```powershell
Resolve-AzError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Windows 컴퓨터에서 관리자로 실행 중인지 확인

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>TenantID 찾기

구독 이름에서

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

구독 ID에서:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

"contoso.com"과 같은 도메인 주소에서

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>모든 구독 및 테넌트 ID 나열

```powershell
$subs = Get-AzSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>템플릿을 사용하여 Data Lake Analytics 계정 만들기

다음 샘플을 사용하여 Azure 리소스 그룹 템플릿을 사용할 수 있습니다. [템플릿을 사용하여 Data Lake Analytics 계정 만들기](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>다음 단계
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [Azure CLI](data-lake-analytics-get-started-cli.md)를 사용하여 Data Lake Analytics 시작
* [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md)를 사용하여 Azure Data Lake Analytics 관리 
