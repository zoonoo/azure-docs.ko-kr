---
title: 모니터링 Azure Storage | Microsoft Docs
description: Azure Storage의 성능 및 가용성을 모니터링 하는 방법에 대해 알아봅니다.
author: normesta
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5564634471045838dae3344dc883b6fdc203711e
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82722922"
---
# <a name="monitoring-azure-storage"></a>모니터링 Azure Storage

Azure 리소스를 사용 하는 중요 한 응용 프로그램 및 비즈니스 프로세스를 사용 하는 경우 해당 리소스의 가용성, 성능 및 작업을 모니터링 하려고 합니다. 이 문서에서는 Azure Storage에서 생성 되는 모니터링 데이터 및 Azure Monitor의 기능을 사용 하 여이 데이터에 대 한 경고를 분석 하는 방법을 설명 합니다.

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 공개 미리 보기 상태 이며 모든 공용 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 미리 보기에 등록 하려면 [이 페이지](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)를 참조 하세요.  이 미리 보기에서는 범용 v1 및 범용 v2 저장소 계정에서 blob (Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 프리미엄 저장소 계정에 대 한 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

## <a name="monitor-overview"></a>모니터 개요

각 저장소 리소스에 대 한 Azure Portal의 **개요** 페이지에는 요청 및 시간별 청구 사용량을 포함 하 여 리소스 사용에 대 한 간략 한 보기가 포함 되어 있습니다. 이는 유용한 정보 이지만 모니터링 데이터의 양이 적은 경우에만 사용할 수 있습니다. 이러한 데이터 중 일부는 자동으로 수집 되며, 저장소 리소스를 만드는 즉시 분석에 사용할 수 있습니다. 일부 구성으로 데이터 컬렉션의 추가 형식을 사용할 수 있습니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Storage는 azure에서 전체 stack 모니터링 서비스인 [Azure Monitor](../../azure-monitor/overview.md) 를 사용 하 여 모니터링 데이터를 만들며,이를 통해 다른 클라우드 및 온-프레미스의 리소스 외에도 azure 리소스를 모니터링할 수 있는 완전 한 기능 집합을 제공 합니다. 

다음을 설명 하는 [Azure Monitor을 사용 하 여 Azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md) 문서를 시작 합니다.

- Azure Monitor란?
- 모니터링과 관련 된 비용
- Azure에서 수집 된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석 하 고 경고 하는 Azure의 표준 도구

다음 섹션에서는 Azure Storage에서 수집한 특정 데이터를 설명 하 고 Azure tools를 사용 하 여 데이터 수집을 구성 하 고이 데이터를 분석 하기 위한 예제를 제공 하 여이 문서를 작성 합니다.

## <a name="monitoring-data-from-azure-storage"></a>Azure Storage에서 데이터 모니터링

Azure Storage는 [azure 리소스의 데이터 모니터링](../../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)에 설명 된 다른 azure 리소스와 동일한 종류의 모니터링 데이터를 수집 합니다. Azure Storage에서 만든 로그 및 메트릭에 대 한 자세한 내용은 [Azure Storage 모니터링 데이터 참조](monitor-storage-reference.md) 를 참조 하세요.

Azure Monitor의 메트릭 및 로그는 Azure Resource Manager 저장소 계정만 지원 합니다. Azure Monitor은 클래식 저장소 계정을 지원 하지 않습니다. 클래식 저장소 계정에서 메트릭 또는 로그를 사용 하려면 Azure Resource Manager 저장소 계정으로 마이그레이션해야 합니다. [Azure Resource Manager로 마이그레이션](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview)을 참조하세요.

원하는 경우 클래식 메트릭과 로그를 계속 사용할 수 있습니다. 실제로 클래식 메트릭과 로그는 Azure Monitor의 메트릭과 로그를 사용 하 여 병렬로 사용할 수 있습니다. Azure Storage는 기존 메트릭과 로그에서 서비스를 종료할 때까지 계속 제공 됩니다. 

### <a name="logs-in-azure-monitor-preview"></a>Azure Monitor의 로그 (미리 보기)

서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 저장소 계정에 blob 끝점의 활동이 있지만 테이블 또는 큐 끝점에는 없는 경우 blob service와 관련 된 로그만 생성 됩니다. Azure Storage 로그에는 저장소 서비스에 대 한 성공 및 실패 한 요청에 대 한 자세한 정보가 포함 됩니다. 이 정로를 사용하면 개별 요청을 모니터링하고 스토리지 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

#### <a name="logging-authenticated-requests"></a>인증된 요청 로깅

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- 실패한 요청 및 성공한 요청을 포함하는 SAS(공유 액세스 서명) 또는 OAuth를 사용하는 요청
- 분석 데이터에 대 한 요청 ( **$logs** 컨테이너의 클래식 로그 데이터 및 **$metric** 테이블의 클래스 메트릭 데이터)

로그 생성 또는 삭제와 같은 저장소 서비스 자체에 의해 수행 된 요청은 기록 되지 않습니다. 기록 된 데이터의 전체 목록은 [저장소에 기록 된 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 로그 형식](monitor-storage-reference.md) 항목에 설명 되어 있습니다.

#### <a name="logging-anonymous-requests"></a>익명 요청 로깅

 다음과 같은 유형의 익명 요청이 기록됩니다.

- 성공한 요청
- 서버 오류
- 클라이언트 및 서버 모두의 시간 초과 오류
- 오류 코드가 304(수정되지 않음)인 실패한 GET 요청

기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록 된 데이터의 전체 목록은 [저장소에 기록 된 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [저장소 로그 형식](monitor-storage-reference.md) 항목에 설명 되어 있습니다.

## <a name="configuration"></a>Configuration

플랫폼 메트릭과 활동 로그는 자동으로 수집 되지만 리소스 로그를 수집 하거나 Azure Monitor 외부로 전달 하려면 진단 설정을 만들어야 합니다. Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 자세한 프로세스는 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기를](../../azure-monitor/platform/diagnostic-settings.md) 참조 하세요.

진단 설정을 만들 때 로그를 사용 하도록 설정 하려는 저장소 유형 (blob, 큐, 테이블, 파일)을 선택 해야 합니다. Azure Portal에서 진단 설정을 만드는 경우 목록에서 리소스를 선택할 수 있습니다. PowerShell 또는 Azure CLI를 사용 하는 경우 저장소 형식의 리소스 ID를 사용 해야 합니다. 저장소 계정의 **속성** 페이지를 열어 Azure Portal에서 리소스 ID를 찾을 수 있습니다.

또한 로그를 수집할 작업의 범주를 지정 해야 합니다. Azure Storage에 대 한 범주는 다음 표에 나와 있습니다.

| 범주 | 설명 |
|:---|:---|
| StorageRead | Blob에 대 한 읽기 작업입니다.  |
| StorageWrite | Blob에 대 한 쓰기 작업입니다. |
| StorageDelete | Blob에 대 한 삭제 작업입니다. |

## <a name="analyzing-metric-data"></a>메트릭 데이터 분석

메트릭 탐색기를 사용 하 여 다른 Azure 서비스의 메트릭을 사용 하 여 Azure storage에 대 한 메트릭을 분석할 수 있습니다. **Azure Monitor** 메뉴에서 **메트릭** 을 선택 하 여 메트릭 탐색기를 엽니다. 이 도구 사용에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](../../azure-monitor/platform/metrics-getting-started.md) 을 참조 하세요. 

다음 예에서는 계정 수준에서 **트랜잭션**을 보는 방법을 보여 줍니다.

![Azure Portal에서 메트릭에 액세스 스크린 샷](./media/monitor-storage/access-metrics-portal.png)

차원을 지원하는 메트릭의 경우 원하는 차원 값을 사용하여 메트릭을 필터링할 수 있습니다. 다음 예제에서는 **API 이름** 차원에 대한 값을 선택하여 특정 작업의 계정 수준에서 **트랜잭션**을 보는 방법을 보여줍니다.

![Azure Portal에서 차원을 사용하여 메트릭에 액세스 스크린 샷](./media/monitor-storage/access-metrics-portal-with-dimension.png)

Azure Storage에서 지 원하는 차원의 전체 목록은 [메트릭 차원](monitor-storage-reference.md#metrics-dimensions)을 참조 하세요.

Azure storage에 대 한 모든 메트릭은 다음 네임 스페이스에 있습니다.

- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices

모든 Azure Monitor 지원 메트릭 (Azure Storage 포함)의 목록은 [지원 되는 메트릭 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)을 참조 하세요.


### <a name="access-metrics"></a>메트릭에 액세스

> [!TIP]
> Azure CLI 또는 .NET 예제를 보려면 아래에 해당 하는 탭을 선택 합니다.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

#### <a name="list-the-metric-definition"></a>메트릭 정의를 나열 합니다.

저장소 계정 또는 blob, 파일, 테이블 또는 큐 서비스와 같은 개별 저장소 서비스의 메트릭 정의를 나열할 수 있습니다. [AzMetricDefinition](https://docs.microsoft.com/powershell/module/az.monitor/get-azmetricdefinition?view=azps-3.3.0) cmdlet을 사용 합니다.

이 예제에서는 `<resource-ID>` 자리 표시자를 전체 저장소 계정의 리소스 id 또는 blob, file, table 또는 queue service와 같은 개별 저장소 서비스의 리소스 id로 바꿉니다. 이러한 리소스 Id는 Azure Portal의 저장소 계정 **속성** 페이지에서 찾을 수 있습니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetricDefinition -ResourceId $resourceId
```

#### <a name="read-metric-values"></a>메트릭 값 읽기

저장소 계정 또는 blob, 파일, 테이블 또는 큐 서비스와 같은 개별 저장소 서비스의 계정 수준 메트릭 값을 읽을 수 있습니다. [AzMetric](https://docs.microsoft.com/powershell/module/Az.Monitor/Get-AzMetric?view=azps-3.3.0) cmdlet을 사용 합니다.

```powershell
   $resourceId = "<resource-ID>"
   Get-AzMetric -ResourceId $resourceId -MetricNames "UsedCapacity" -TimeGrain 01:00:00
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

#### <a name="list-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

저장소 계정 또는 blob, 파일, 테이블 또는 큐 서비스와 같은 개별 저장소 서비스의 메트릭 정의를 나열할 수 있습니다. [Az monitor 메트릭 list-정의](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list-definitions) 명령을 사용 합니다.
 
이 예제에서는 `<resource-ID>` 자리 표시자를 전체 저장소 계정의 리소스 id 또는 blob, file, table 또는 queue service와 같은 개별 저장소 서비스의 리소스 id로 바꿉니다. 이러한 리소스 Id는 Azure Portal의 저장소 계정 **속성** 페이지에서 찾을 수 있습니다.

```azurecli-interactive
   az monitor metrics list-definitions --resource <resource-ID>
```

#### <a name="read-account-level-metric-values"></a>계정 수준 메트릭 값을 읽습니다.

저장소 계정 또는 blob, 파일, 테이블 또는 큐 서비스와 같은 개별 저장소 서비스의 메트릭 값을 읽을 수 있습니다. [Az monitor 메트릭 list](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) 명령을 사용 합니다.

```azurecli-interactive
   az monitor metrics list --resource <resource-ID> --metric "UsedCapacity" --interval PT1H
```

### <a name="net"></a>[.NET](#tab/dotnet)

Azure Monitor는 [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) 를 제공 하 여 메트릭 정의와 값을 읽습니다. [샘플 코드](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)는 다른 매개 변수로 SDK를 사용하는 방법을 보여줍니다. 스토리지 메트릭을 사용하려면 `0.18.0-preview` 이상의 버전을 사용해야 합니다.
 
이 예제에서는 `<resource-ID>` 자리 표시자를 전체 저장소 계정의 리소스 id로 바꾸거나 blob, file, table 또는 queue service와 같은 개별 저장소 서비스의 리소스 id로 바꿉니다. 이러한 리소스 Id는 Azure Portal의 저장소 계정 **속성** 페이지에서 찾을 수 있습니다.

변수를 `<subscription-ID>` 구독의 ID로 바꿉니다.  `<tenant-ID>`, `<application-ID>`및 `<AccessKey>`에 대 한 값을 가져오는 방법에 대 한 지침은 [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체 만들기](https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/)를 참조 하세요. 

#### <a name="list-account-level-metric-definition"></a>계정 수준 메트릭 정의 나열

다음 예에서는 계정 수준에서 메트릭 정의를 나열 하는 방법을 보여 줍니다.

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";


        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            // Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

#### <a name="read-account-level-metric-values"></a>계정 수준 메트릭 값을 읽습니다.

다음 예제는 계정 차원에서 `UsedCapacity` 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValue()
    {
        var resourceId = "<resource-ID>";
        var subscriptionId = "<subscription-ID>";
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            // Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

#### <a name="read-multi-dimensional-metric-values"></a>다차원 메트릭 값 읽기

다차원 메트릭에서 특정 차원 값에 대한 메트릭 데이터를 읽으려면 메타 데이터 필터를 정의해야 합니다.

다음 예제는 다차원을 지원하는 메트릭에서 메트릭 데이터를 읽는 방법을 보여줍니다.

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "<subscription-ID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "<tenant-ID>";
        var applicationId = "<application-ID>";
        var accessKey = "<AccessKey>";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

---

## <a name="analyzing-log-data"></a>로그 데이터 분석

리소스 로그는 저장소 계정의 blob, 이벤트 데이터 또는 로그 분석 쿼리를 통해 액세스할 수 있습니다.

이러한 로그에 표시 되는 필드에 대 한 자세한 참조는 [모니터링 데이터 참조 Azure Storage](monitor-storage-reference.md) 를 참조 하세요.

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 공개 미리 보기 상태 이며 모든 공용 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 미리 보기에 등록 하려면 [이 페이지](https://www.microsoft.com)를 참조 하세요.  이 미리 보기에서는 범용 v1 및 범용 v2 저장소 계정에서 blob (Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 프리미엄 저장소 계정에 대 한 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

### <a name="access-logs-in-a-storage-account"></a>저장소 계정에서 로그 액세스

로그는 대상 저장소 계정의 컨테이너에 저장 된 blob으로 표시 됩니다. 데이터는 줄로 구분 된 JSON 페이로드로 단일 blob 내에 수집 되 고 저장 됩니다. blob의 이름은 다음 명명 규칙을 따릅니다.

`https://<destination-storage-account>.blob.core.windows.net/insights-logs-<storage-operation>/resourceId=/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<source-storage-account>/blobServices/default/y=<year>/m=<month>/d=<day>/h=<hour>/m=<minute>/PT1H.json`

예를 들면 다음과 같습니다.

`https://mylogstorageaccount.blob.core.windows.net/insights-logs-storagewrite/resourceId=/subscriptions/`<br>`208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/blobServices/default/y=2019/m=07/d=30/h=23/m=12/PT1H.json`

### <a name="access-logs-in-event-hub"></a>이벤트 허브에서 로그 액세스

이벤트 허브에 전송 된 로그는 파일로 저장 되지 않지만 이벤트 허브에서 로그 정보를 받았는지 확인할 수 있습니다. Azure Portal에서 이벤트 허브로 이동한 다음 **들어오는 메시지** 수가 0 보다 큰지 확인 합니다. 

![감사 로그](media/monitor-storage/event-hub-log.png)

보안 정보 및 이벤트 관리 및 모니터링 도구를 사용 하 여 이벤트 허브로 전송 되는 로그 데이터에 액세스 하 고 해당 데이터를 읽을 수 있습니다. 자세한 내용은 [내 이벤트 허브로 전송 되는 모니터링 데이터를 사용 하 여 수행할 수 있는 작업](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)을 참조 하세요.

### <a name="access-logs-in-log-analytics-workspace"></a>Log Analytics 작업 영역에서 로그 액세스

Azure Monitor 로그 쿼리를 사용 하 여 Log Analytics 작업 영역으로 전송 된 로그에 액세스할 수 있습니다.

[Azure Monitor에서 Log Analytics 시작](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)을 참조 하세요.

데이터는 다음 테이블에 저장 됩니다.

| 테이블 | 설명 |
|:---|:---|
|StorageBlobLogs | Blob 저장소의 작업을 설명 하는 로그입니다. |
|StorageFileLogs | 파일 공유의 작업을 설명 하는 로그입니다. |
|StorageQueueLogs | 큐의 작업을 설명 하는 로그입니다.|
|StorageTableLogs| 테이블의 동작을 설명 하는 로그입니다.|

### <a name="azure-storage-log-analytics-queries-in-azure-monitor"></a>Log Analytics 쿼리를 Azure Storage Azure Monitor

다음은 Azure Storage 계정을 모니터링 하는 데 도움이 되는 **로그 검색** 검색 표시줄에 입력할 수 있는 몇 가지 쿼리입니다. 이러한 쿼리는 [새 언어](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)에서 작동합니다.

Azure Storage 계정을 모니터링 하는 데 사용할 수 있는 쿼리는 다음과 같습니다.

* 지난 3 일 동안 가장 자주 발생 하는 오류 10 개를 나열 하려면

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by StatusText
    | top 10 by count_ desc
    ```
* 지난 3 일 동안 오류가 발생 하는 상위 10 개 작업을 나열 합니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText !contains "Success"
    | summarize count() by OperationName
    | top 10 by count_ desc
    ```
* 지난 3 일 동안 가장 긴 종단 간 대기 시간으로 상위 10 개 작업을 나열 합니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | top 10 by DurationMs desc
    | project TimeGenerated, OperationName, DurationMs, ServerLatencyMs, ClientLatencyMs = DurationMs - ServerLatencyMs
    ```
* 지난 3 일 동안 서버 쪽 제한 오류를 발생 시키는 모든 작업을 나열 합니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and StatusText contains "ServerBusy"
    | project TimeGenerated, OperationName, StatusCode, StatusText
    ```
* 지난 3 일 동안 익명 액세스 권한이 있는 모든 요청을 나열 합니다.

    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d) and AuthenticationType == "Anonymous"
    | project TimeGenerated, OperationName, AuthenticationType, Uri
    ```
* 지난 3 일 동안 사용 된 작업의 원형 차트를 만들려면
    ```Kusto
    StorageBlobLogs
    | where TimeGenerated > ago(3d)
    | summarize count() by OperationName
    | sort by count_ desc 
    | render piechart
    ```
## <a name="faq"></a>FAQ

**Azure Storage가 Managed Disks 또는 Unmanaged Disks에 대한 메트릭을 지원하나요?**

예. Azure Compute는 디스크에서 메트릭을 지원합니다. 자세한 내용은 [문서](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Storage에서 만든 로그 및 메트릭에 대 한 참조는 [모니터링 데이터 참조를 Azure Storage](monitor-storage-reference.md) .
- Azure 리소스 모니터링에 대 한 자세한 내용은 [Azure Monitor를 사용 하 여 azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md) 을 참조 하세요.
- [Azure Storage 메트릭 마이그레이션](./storage-metrics-migration.md)

