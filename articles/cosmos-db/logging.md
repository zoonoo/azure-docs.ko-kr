---
title: "Azure Cosmos DB 진단 로깅 | Microsoft Docs"
description: "이 자습서를 사용하여 Azure Cosmos DB 로깅을 시작할 수 있습니다."
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: mimig
ms.openlocfilehash: b8f92953634f9294805521d8b925ed67d121a17d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Azure DB Cosmos DB 진단 로깅

하나 이상의 Azure Cosmos DB 데이터베이스를 사용하기 시작한 후에는 데이터베이스에 액세스하는 방법과 시기를 모니터링할 수 있습니다. Azure Cosmos DB의 진단 로깅을 통해 이 모니터링을 수행할 수 있습니다. 진단 로깅을 사용하도록 설정하면 [Azure Storage](https://azure.microsoft.com/services/storage/)로 로그를 전송하고, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)로 스트리밍하고/또는 [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite)에 포함되는 [Log Analytics](https://azure.microsoft.com/services/log-analytics/)로 내보냅니다.

![Log Analytics를 통해 Storage, Event Hubs 또는 Operations Management Suite로 진단 로깅](./media/logging/azure-cosmos-db-logging-overview.png)

이 자습서를 사용하여 Azure Portal, CLI 또는 PowerShell을 통해 Azure Cosmos DB 로깅을 시작합니다.

## <a name="what-is-logged"></a>로깅되는 내용

* 액세스 권한, 시스템 오류 또는 잘못된 요청으로 인해 실패한 요청을 포함하여 모든 인증된 백엔드 요청(REST API)이 로깅됩니다. 사용자가 시작한 Graph, Cassandra 및 Table API 요청은 현재 사용할 수 없습니다.
* 모든 문서, 컨테이너 및 데이터베이스에 대한 CRUD 작업을 포함하는 데이터베이스 자체에 대한 작업입니다.
* 이러한 키 만들기, 수정 또는 삭제를 포함하는 계정 키에 대한 작업입니다.
* 401 응답이 발생하는 인증되지 않은 요청. 예를 들어 전달자 토큰이 없거나 형식이 잘못되거나 만료되거나 토큰이 잘못된 요청이 해당합니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 리소스가 필요합니다.

* 기존 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너. 이러한 리소스를 만드는 방법에 대한 지침은 [Azure Portal을 사용하여 데이터베이스 계정 만들기](create-sql-api-dotnet.md#create-a-database-account), [CLI 샘플](cli-samples.md) 또는 [PowerShell 샘플](powershell-samples.md)을 참조하세요.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Azure Portal에서 로깅 켜기

1. [Azure Portal](https://portal.azure.com)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **진단 로그**를 클릭한 다음 **진단 로그 켜기**를 클릭합니다.

    ![Azure Portal에서 Azure Cosmos DB에 대한 진단 로깅 설정](./media/logging/turn-on-portal-logging.png)

2. **진단 설정** 페이지에서 다음을 수행합니다. 

    * **이름**. 만들 로그에 대한 이름을 입력합니다.

    * **저장소 계정에 보관**. 이 옵션을 사용하려면 연결할 기존 저장소 계정이 필요합니다. 포털에서 새 저장소 계정을 만들려면 [저장소 계정 만들기](../storage/common/storage-create-storage-account.md)를 참조하고 지침에 따라 Resource Manager, 범용 계정을 만듭니다. 그런 다음 포털의 이 페이지로 돌아가 저장소 계정을 선택합니다. 새로 만든 저장소 계정이 드롭다운 메뉴에 나타나기까지 몇 분 정도 걸릴 수 있습니다.
    * **이벤트 허브로 스트림**. 이 옵션을 사용하려면 연결할 기존 Event Hub 네임스페이스 및 이벤트 허브가 필요합니다. Event Hubs 네임스페이스를 만들려면 [Azure Portal을 사용하여 Event Hubs 네임스페이스 및 이벤트 허브 만들기](../event-hubs/event-hubs-create.md)를 참조하세요. 그런 다음 포털의 이 페이지로 돌아가 Event Hub 네임스페이스 및 정책 이름을 선택합니다.
    * **Log Analytics에 보내기**.     이 옵션을 사용하려면 기존 작업 영역을 사용하거나 포털에서 [새 작업 영역 만들기](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) 단계를 따라 새 Log Analytics 작업 영역을 만듭니다. Log Analytics에서 로그를 보는 방법에 대한 자세한 내용은 [Log Analytics에서 로그 보기](#view-in-loganalytics)를 참조하세요.
    * **DataPlaneRequests 로그**. 이 옵션을 선택하면 SQL, Graph, MongoDB, Cassandra 및 Table API 계정에 대해 Azure Cosmos DB의 기본 배포 플랫폼으로부터 백엔드 요청을 기록합니다. 저장소 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.
    * **MongoRequests 로깅**. 이 옵션을 선택하면 MongoDB API 계정을 서비스하기 위한 Azure Cosmos DB의 프런트 엔드로부터 사용자가 시작한 요청이 기록됩니다.  저장소 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.
    * **메트릭 요청**. [Azure Metrics](../monitoring-and-diagnostics/monitoring-supported-metrics.md)에 자세한 데이터를 저장하려면 이 옵션을 선택합니다. 저장소 계정으로 보관하려는 경우 진단 로그의 보존 기간을 선택할 수 있습니다. 보존 기간이 만료되면 로그가 자동으로 삭제됩니다.

3. **저장**을 클릭합니다.

    “\<작업 영역 이름>의 진단을 업데이트하지 못했습니다. \<subscription id> 구독은 microsoft.insights를 사용하도록 등록되지 않았습니다.” 오류를 수신하는 경우 [Azure 진단 문제 해결](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) 지침을 따라 계정을 등록한 다음 이 절차를 다시 시도합니다.

    나중에 진단 로그를 저장하는 방식을 변경하려면 언제든지 이 페이지로 돌아와 사용자 계정의 진단 로그 설정을 수정합니다.

## <a name="turn-on-logging-using-cli"></a>CLI를 사용하여 로깅 켜기

Azure CLI를 사용하여 메트릭 및 진단 로깅을 사용하도록 설정하려면 다음 명령을 사용합니다.

- Storage 계정에서 진단 로그의 저장소를 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId`는 Azure Cosmos DB 계정의 이름입니다. `storageId`는 로그를 전송할 저장소 계정의 이름입니다.

- 이벤트 허브로 진단 로그의 스트리밍을 사용하도록 설정하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId`는 Azure Cosmos DB 계정의 이름입니다. `serviceBusRuleId`는 다음 형식의 문자열입니다.

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- 진단 로그를 Log Analytics 작업 영역으로 보낼 수 있게 하려면 다음 명령을 사용합니다.

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

이러한 매개 변수를 결합하여 여러 출력 옵션을 활성화할 수 있습니다.

## <a name="turn-on-logging-using-powershell"></a>PowerShell을 사용하여 로깅 켜기

PowerShell을 사용하여 로깅을 켜려면 Azure Powershell 버전 1.0.1 이상이 필요합니다.

Azure PowerShell을 설치하고 Azure 구독에 연결하려면 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.

이미 Azure PowerShell가 설치되어 있고 버전을 알 수 없는 경우, PowerShell 콘솔에서 `(Get-Module azure -ListAvailable).Version`을 입력합니다.  

### <a id="connect"></a>구독에 연결
Azure PowerShell 세션을 시작하고 다음 명령 사용하여 Azure 계정에 로그인합니다.  

```powershell
Login-AzureRmAccount
```

팝업 브라우저 창에 Azure 계정 사용자 이름 및 암호를 입력합니다. Azure PowerShell은 이 계정과 연관된 모든 구독을 가져와서 기본적으로 첫 번째 구독을 사용합니다.

구독이 여러 개인 경우 Azure Key Vault을 만드는 데 사용된 특정된 하나를 지정해야 합니다. 계정에 대한 구독을 보려면 다음을 입력합니다.

```powershell
Get-AzureRmSubscription
```

그런 다음 로깅하려는 Azure Cosmos DB 계정과 연결된 구독을 지정하려면 다음을 입력합니다.

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> 사용자 계정에 여러 구독이 연결된 경우 구독을 지정하는 것이 중요합니다.
>
>

Azure PowerShell 구성에 관한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

### <a id="storage"></a>로그에 대한 새 저장소 계정 만들기
로그에 대해 기존 저장소 계정을 사용할 수 있지만 이 자습서에서는 Azure Cosmos DB 로그 전용 새 저장소 계정을 만듭니다. 편의를 위해 저장소 계정 세부 정보를 **sa**라는 변수에 저장합니다.

추가적인 관리 편이성을 위해 Azure Cosmos DB 데이터베이스를 포함하는 것과 동일한 리소스 그룹을 사용합니다. ContosoResourceGroup, contosocosmosdblogs 및 '미국 중북부'에 대한 값을 사용자 고유의 해당 값으로 바꿉니다.

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> 기존 저장소 계정을 사용하려는 경우 사용자 Azure Cosmos DB 구독과 동일한 구독을 사용하고 클래식 배포 모델보다는 Resource Manager 배포 모델을 사용해야 합니다.
>
>

### <a id="identify"></a>로그를 위한 Azure Cosmos DB 계정 식별
Azure Cosmos DB 계정 이름을 **account**라는 변수로 설정합니다. 여기서 ResourceName은 Azure Cosmos DB 계정의 이름입니다.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>로깅 사용
Azure Cosmos DB에 대한 로깅을 사용하도록 설정하려면 새 저장소 계정, Azure Cosmos DB 계정 및 로그를 설정할 범주에 대한 변수와 함께 Set-AzureRmDiagnosticSetting cmdlet을 사용합니다. 다음 명령을 실행하고 **-Enabled** 플래그를 **$true**로 설정합니다.

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

명령의 출력은 다음과 유사합니다.

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

저장소 계정에 정보를 저장하는 사용자 데이터베이스에 로깅을 사용할 수 있는지 확인합니다.

선택적으로 오래된 로그를 자동으로 삭제하는 로그에 대한 보존 정책을 설정할 수도 있습니다. 예를 들어 **-RetentionEnabled** 플래그를 사용하여 보존 정책을 **$true**로 설정하고 **-RetentionInDays** 매개 변수를 **90**으로 설정하므로 90일보다 오래된 로그는 자동으로 삭제됩니다.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>로그에 액세스
**DataPlaneRequests** 범주에 대한 Azure Cosmos DB 로그는 제공된 저장소 계정의 **insights-logs-data-plane-requests** 컨테이너에 저장됩니다. 

먼저, 컨테이너 이름에 대한 변수를 만듭니다. 이 변수는 연습의 나머지 전체에서 사용됩니다.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

이 컨테이너에 있는 모든 Blob을 나열하려면 다음을 입력합니다.

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

출력은 다음과 유사합니다.

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

이 출력에서 확인할 수 있듯이 Blob은 명명 규칙 `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`을 따릅니다.

날짜 및 시간 값은 UTC를 사용합니다.

여러 리소스에 대한 로그를 수집하는 데 동일한 저장소 계정을 사용할 수 있으므로 Blob 이름에 있는 정규화된 리소스 ID는 필요한 Blob에 액세스하거나 다운로드하는 데 매우 유용합니다. 하지만 그 전에 먼저 모든 Blob을 다운로드하는 방법을 다룹니다.

먼저 Blob을 다운로드할 폴더를 만듭니다. 예: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

그런 다음 모든 Blob 목록을 가져옵니다.  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

'Get-AzureStorageBlobContent'를 통해 이 목록을 파이프하여 Blob을 대상 폴더로 다운로드합니다.

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

이 두 번째 명령을 실행할 때 Blob 이름의 **/** 구분 기호는 대상 폴더 아래에 전체 폴더 구조를 만듭니다. 이 폴더 구조는 Blob을 파일로 다운로드하고 저장하는 데 사용됩니다.

선택적으로 Blob을 다운로드하려면 와일드카드를 사용합니다. 예: 

* 여러 데이터베이스가 있고 CONTOSOCOSMOSDB3이라는 하나의 데이터베이스에 대한 로그를 다운로드하려는 경우:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* 리소스 그룹이 여러 개이고 하나의 리소스 그룹에 대한 로그를 다운로드하려는 경우 `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`을(를) 사용합니다.

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* 2017년 7월의 모든 로그를 다운로드하려는 경우 `-Blob '*/year=2017/m=07/*'`을(를) 사용합니다.

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

또한,

* 데이터베이스 리소스의 진단 설정 상태를 쿼리하려면: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* 데이터베이스 계정 리소스에 대한 **DataPlaneRequests** 범주의 로깅을 사용하지 않도록 설정하려면: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


이러한 각 쿼리에서 반환된 Blob은 아래 코드와 같이 텍스트로 저장되거나 JSON Blob으로 서식이 지정됩니다. 

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

각 JSON Blob의 데이터에 대해 자세히 알아보려면 [Azure Cosmos DB 로그 해석](#interpret)을 참조하세요.

## <a name="managing-your-logs"></a>로그 관리

로그는 Azure Cosmos DB 작업이 수행된 시간부터 2시간 동안 계정에서 사용 가능합니다. 저장소 계정의 로그 관리에 따라 다릅니다.

* 표준 Azure 액세스 제어 메서드를 사용하여 액세스할 수 있는 사용자를 제한하여 로그를 보호합니다.
* 더 이상 저장소 계정에 유지하지 않으려는 로그를 삭제합니다.
* Storage 계정에 보관된 데이터 평면 요청의 보존 기간은 **DataPlaneRequests 로그**가 선택된 경우 포털에서 구성됩니다. 해당 설정을 변경하려면 [Azure Portal에서 로깅 켜기](#turn-on-logging-in-the-azure-portal)를 참조하세요.


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Log Analytics에서 로그 보기

로깅을 사용하도록 설정한 경우 **Log Analytics로 전송** 옵션을 선택하면 컬렉션의 진단 데이터가 2시간 이내에 Log Analytics로 전달됩니다. 로깅을 사용하도록 설정한 직후 Log Analytics를 보면 아무 데이터도 표시되지 않습니다. 2시간을 기다린 후 다시 시도하세요. 

로그를 보기 전에 Log Analytics 작업 영역이 새로운 Log Analytics 쿼리 언어를 사용할 수 있도록 업그레이드되었는지 여부를 확인하는 것이 좋습니다. 확인하려면 [Azure Portal](https://portal.azure.com)을 열고 왼쪽 끝에서 **Log Analytics**를 클릭하고 다음 그림과 같이 작업 영역 이름을 선택합니다. 아래 그림과 같이 **OMS 작업 영역** 페이지가 표시됩니다.

![Azure Portal의 Log Analytics](./media/logging/azure-portal.png)

**OMS 작업 영역** 페이지에 다음 메시지가 표시되는 경우 작업 영역이 새 언어를 사용할 수 있도록 업그레이드되지 않은 것입니다. 새 쿼리 언어로 업그레이드하는 방법에 대한 자세한 내용은 [새 로그 검색으로 Azure Log Analytics 작업 영역 업그레이드](../log-analytics/log-analytics-log-search-upgrade.md)를 참조하세요. 

![Log Analytics 업그레이드 알림](./media/logging/upgrade-notification.png)

Log Analytics에서 진단 데이터를 보려면 다음 그림과 같이 왼쪽 메뉴에서 로그 검색 페이지 또는 페이지의 관리 영역을 엽니다.

![Azure Portal에서 로그 검색 옵션](./media/logging/log-analytics-open-log-search.png)

이제 데이터 수집을 사용하도록 설정한 경우 새 쿼리 언어를 사용하여 다음 로그 검색 예제를 실행하여 최근 10개 로그 `AzureDiagnostics | take 10`을 확인합니다.

![예제 10 로그 검색](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>쿼리

**로그 검색** 상자에 입력할 수 있고 Azure Cosmos DB 컨테이너를 모니터링하는 데 유용한 추가 쿼리의 예를 다음과 같습니다. 이러한 쿼리는 [새 언어](../log-analytics/log-analytics-log-search-upgrade.md)에서 작동합니다. 

각 로그 검색에서 반환된 데이터의 의미를 알아보려면 [Azure Cosmos DB 로그 해석](#interpret)을 참조하세요.

* 지정된 기간 동안 Azure Cosmos DB의 모든 진단 로그.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* 최근 10개의 로깅 이벤트.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* 모든 작업, 작업 유형별로 그룹화

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* 모든 작업, 리소스별로 그룹화

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* 모든 사용자 활동, 리소스별로 그룹화 이 로그는 진단 로그가 아닌 활동 로그입니다.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* 3밀리초보다 오래 소요되는 작업.

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 작업을 실행 중인 에이전트.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* 길게 실행된 작업이 수행된 시기.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

새 로그 검색 언어를 사용하는 방법에 대한 자세한 내용은 [Log Analytics에서 로그 검색 이해](../log-analytics/log-analytics-log-search-new.md)를 참조하세요. 

## <a id="interpret"></a>로그 해석

Azure Storage 및 Log Analytics에 저장된 진단 데이터는 매우 유사한 스키마를 사용합니다. 

다음 표는 각 로그 항목의 내용에 대해 설명합니다.

| Azure Storage 필드 또는 속성 | Log Analytics 속성 | 설명 |
| --- | --- | --- |
| 실시간 | TimeGenerated | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| ResourceId | 리소스 | 로그가 사용하도록 설정된 Azure Cosmos DB 계정입니다.|
| 카테고리 | Category | Azure Cosmos DB 로그의 경우 DataPlaneRequests가 사용 가능한 유일한 값입니다. |
| operationName | OperationName | 작업의 이름입니다. 이 값은 Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed 또는 Upsert 작업 중 하나일 수 있습니다.   |
| properties | 해당 없음 | 이 필드의 내용은 다음 표에 설명되어 있습니다. |
| activityId | activityId_g | 기록된 작업의 고유 GUID입니다. |
| userAgent | userAgent_s | 요청을 수행하는 클라이언트 사용자 에이전트를 지정하는 문자열입니다. 형식은 {사용자 에이전트 이름}/{버전}입니다.|
| resourceType | ResourceType | 액세스한 리소스 유형입니다. 이 값은 Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction 또는 Offer 리소스 종류 중 하나일 수 있습니다. |
| statusCode |statusCode_s | 작업의 응답 상태입니다. |
| requestResourceId | ResourceId | 요청과 관련된 resourceId이며 수행된 작업에 따라 databaseRid, collectionRid 또는 documentRid를 가리킬 수 있습니다.|
| clientIpAddress | clientIpAddress_s | 클라이언트의 IP 주소입니다. |
| requestCharge | requestCharge_s | 작업에서 사용된 RU 수입니다. |
| collectionRid | collectionId_s | 컬렉션의 고유 ID입니다.|
| duration | duration_s | 작업 기간(틱 단위)입니다. |
| requestLength | requestLength_s | 요청 길이(바이트)입니다. |
| responseLength | responseLength_s | 응답 길이(바이트)입니다.|
| resourceTokenUserRid | resourceTokenUserRid_s | 인증에 [리소스 토큰](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)이 사용되고 사용자의 리소스 ID를 가리키는 경우 비어 있지 않습니다. |

## <a name="next-steps"></a>다음 단계

- 로깅 사용뿐 아니라 여러 Azure 서비스에서 지원하는 메트릭 및 로그 카테고리에 대해서도 파악하려면 [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/monitoring-overview-metrics.md) 및 [Azure 진단 로그 개요](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 문서를 읽어보세요.
- Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.
   - [Azure Event Hubs란?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Event Hubs 시작](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- [Azure Storage에서 메트릭 및 진단 로그 다운로드](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs) 참조
- [Log Analytics의 로그 검색 이해](../log-analytics/log-analytics-log-search-new.md) 참조
