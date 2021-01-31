---
title: Azure 스토리지 분석 로그 사용 및 관리 (클래식) | Microsoft Docs
description: Azure 스토리지 분석를 사용 하 여 Azure에서 저장소 계정을 모니터링 하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bc6632b55ba8fd90317a8b5046a3e84d863bf0ef
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221721"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Azure 스토리지 분석 로그 사용 및 관리 (클래식)

[Azure 스토리지 분석](storage-analytics.md) 는 blob, 큐 및 테이블에 대 한 로그를 제공 합니다. [Azure Portal](https://portal.azure.com) 를 사용 하 여 계정에 대 한 로그를 기록 하도록 구성할 수 있습니다. 이 문서에서는 로그를 사용 하도록 설정 하 고 관리 하는 방법을 보여 줍니다. 메트릭을 사용 하도록 설정 하는 방법을 알아보려면 [Azure 스토리지 분석 메트릭 사용 및 관리 (클래식)](storage-monitor-storage-account.md)를 참조 하세요.  Azure Portal 모니터링 데이터를 검사 하 고 저장 하는 데 관련 된 비용이 있습니다. 자세한 정보는 [스토리지 분석](storage-analytics.md)을 참조하세요.

> [!NOTE]
> 스토리지 분석 로그 대신 Azure Monitor에서 Azure Storage 로그를 사용 하는 것이 좋습니다. Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 이 미리 보기는 blob (Azure Data Lake Storage Gen2 포함), 파일, 큐 및 테이블에 대 한 로그를 사용 하도록 설정 합니다. 자세히 알아보려면 다음 문서를 참조 하세요.
>
> - [모니터링 Azure Blob Storage](../blobs/monitor-blob-storage.md)
> - [모니터링 Azure Files](../files/storage-files-monitoring.md)
> - [Azure Queue Storage 모니터링](../queues/monitor-queue-storage.md)
> - [Azure 테이블 저장소 모니터링](../tables/monitor-table-storage.md)

스토리지 분석 및 기타 도구를 사용하여 Azure Storage 관련 문제를 식별, 진단 및 해결하는 방법에 대한 자세한 지침은 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

<a id="configure-logging"></a>

## <a name="enable-logs"></a>로그 사용 설정

Azure Storage에서 Blob, 테이블 및 큐 서비스에 대한 읽기, 쓰기 및 삭제 요청에 대한 진단 로그를 저장하도록 지시할 수 있습니다. 설정한 데이터 보존 정책도 이러한 로그에 적용됩니다.

> [!NOTE]
> Azure Files 현재 스토리지 분석 메트릭을 지원 하지만 스토리지 분석 로깅을 지원 하지 않습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 **Storage 계정**, Storage 계정 이름을 차례로 선택하여 Storage 계정 블레이드를 엽니다.

2. 메뉴 블레이드의 **모니터링 (클래식)** 섹션에서 **진단 설정 (클래식)** 을 선택 합니다.

    ![Azure Portal에서 모니터링 아래의 진단 메뉴 항목](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. **상태** 가 **사용**(On)으로 설정되어 있는지 확인하고, 로깅을 사용하도록 설정하려는 **서비스** 를 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![Azure Portal에서 로깅 구성](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. **데이터 삭제** 확인란이 선택 되어 있는지 확인 합니다.  그런 다음 슬라이더 컨트롤을 확인란 아래로 이동 하거나 slider 컨트롤 옆의 텍스트 상자에 표시 되는 값을 직접 수정 하 여 로그 데이터를 보존할 일 수를 설정 합니다. 새 스토리지 계정의 기본값은 7일입니다. 보존 정책을 설정하지 않으려면 0을 입력합니다. 보존 정책이 없으면 로그 데이터를 삭제 해야 합니다.

   > [!WARNING]
   > 로그는 계정에 데이터로 저장 됩니다. 로그 데이터를 시간에 따라 누적 하 여 저장소 비용을 늘릴 수 있습니다. 짧은 시간 동안만 로그 데이터가 필요한 경우 데이터 보존 정책을 수정 하 여 비용을 줄일 수 있습니다. 부실 로그 데이터 (보존 정책 보다 오래 된 데이터)는 시스템에 의해 삭제 됩니다. 계정에 대 한 로그 데이터를 보존할 기간에 따라 보존 정책을 설정 하는 것이 좋습니다. 자세한 내용은 [스토리지 메트릭에 대한 청구](storage-analytics-metrics.md#billing-on-storage-metrics)를 참조하세요.

4. **저장** 을 클릭합니다.

   진단 로그는 스토리지 계정의 이름이 *$logs* 인 Blob 컨테이너에 저장됩니다. [Microsoft Azure Storage 탐색기](https://storageexplorer.com)와 같은 저장소 탐색기를 사용 하거나 저장소 클라이언트 라이브러리 또는 PowerShell을 사용 하 여 프로그래밍 방식으로 로그 데이터를 볼 수 있습니다.

   $logs 컨테이너 액세스에 대한 자세한 내용은 [스토리지 분석 로깅](storage-analytics-logging.md)을 참조하세요.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell 명령 창을 엽니다.

2. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

3. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 설정 합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

5. 사용하려는 스토리지 계정을 정의하는 스토리지 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다. 

6. **AzStorageServiceLoggingProperty** 를 사용 하 여 현재 로그 설정을 변경 합니다. Storage 로깅을 제어하는 cmdlet은 로그에 대한 요청 유형의 쉼표로 구분된 목록이 포함된 문자열인 **LoggingOperations** 매개 변수를 사용합니다. 가능한 세 가지 요청 유형은 **읽기**, **쓰기** 및 **삭제** 입니다. 로깅을 해제하려면 **LoggingOperations** 매개 변수에 **none** 값을 사용합니다.  

   다음 명령은 기본 스토리지 계정의 큐 서비스의 읽기, 쓰기 및 삭제 요청에 대해 보존 기간이 5일로 설정된 로깅을 설정합니다.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > 로그는 계정에 데이터로 저장 됩니다. 로그 데이터를 시간에 따라 누적 하 여 저장소 비용을 늘릴 수 있습니다. 짧은 시간 동안만 로그 데이터가 필요한 경우 데이터 보존 정책을 수정 하 여 비용을 줄일 수 있습니다. 부실 로그 데이터 (보존 정책 보다 오래 된 데이터)는 시스템에 의해 삭제 됩니다. 계정에 대 한 로그 데이터를 보존할 기간에 따라 보존 정책을 설정 하는 것이 좋습니다. 자세한 내용은 [스토리지 메트릭에 대한 청구](storage-analytics-metrics.md#billing-on-storage-metrics)를 참조하세요.
   
   다음 명령은 기본 스토리지 계정의 테이블 서비스에 대해 로깅을 해제합니다.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>로그 데이터 보존 기간 수정

로그 데이터를 시간에 따라 누적 하 여 저장소 비용을 늘릴 수 있습니다. 짧은 시간 동안만 로그 데이터가 필요한 경우 로그 데이터 보존 기간을 수정 하 여 비용을 줄일 수 있습니다. 예를 들어 3 일 동안만 로그가 필요한 경우 로그 데이터 보존 기간을 값으로 설정 `3` 합니다. 그러면 3 일 후에 계정에서 로그가 자동으로 삭제 됩니다. 이 섹션에서는 현재 로그 데이터 보존 기간을 확인 하 고 원하는 경우 해당 기간을 업데이트 하는 방법을 보여 줍니다.

> [!NOTE]
> 이러한 단계는 **계층 구조 네임 스페이스** 설정이 사용 하도록 설정 되지 않은 계정에만 적용 됩니다. 계정에서 해당 설정을 사용 하도록 설정한 경우에는 보존 기간 (일) 설정이 아직 지원 되지 않습니다. 대신 Azure Storage 탐색기, REST 또는 SDK와 같은 지원 되는 도구를 사용 하 여 수동으로 로그를 삭제 해야 합니다. 저장소 계정에서 이러한 로그를 찾으려면 [로그를 저장 하는 방법](storage-analytics-logging.md#how-logs-are-stored)을 참조 하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에서 **Storage 계정**, Storage 계정 이름을 차례로 선택하여 Storage 계정 블레이드를 엽니다.
2. 메뉴 블레이드의 **모니터링 (클래식)** 섹션에서 **진단 설정 (클래식)** 을 선택 합니다.

    ![Azure Portal 모니터링의 진단 메뉴 항목](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. **데이터 삭제** 확인란이 선택 되어 있는지 확인 합니다.  그런 다음 슬라이더 컨트롤을 확인란 아래로 이동 하거나 slider 컨트롤 옆의 텍스트 상자에 표시 되는 값을 직접 수정 하 여 로그 데이터를 보존할 일 수를 설정 합니다.

   > [!div class="mx-imgBorder"]
   > ![Azure Portal에서 보존 기간을 수정 합니다.](./media/manage-storage-analytics-logs/modify-retention-period.png)

   새 저장소 계정의 기본 일 수는 7 일입니다. 보존 정책을 설정하지 않으려면 0을 입력합니다. 보존 정책이 없는 경우 언제든 모니터링 데이터를 삭제할 수 있습니다.
   
4. **저장** 을 클릭합니다.

   진단 로그는 스토리지 계정의 이름이 *$logs* 인 Blob 컨테이너에 저장됩니다. [Microsoft Azure Storage 탐색기](https://storageexplorer.com)와 같은 저장소 탐색기를 사용 하거나 저장소 클라이언트 라이브러리 또는 PowerShell을 사용 하 여 프로그래밍 방식으로 로그 데이터를 볼 수 있습니다.

   $logs 컨테이너 액세스에 대한 자세한 내용은 [스토리지 분석 로깅](storage-analytics-logging.md)을 참조하세요.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell 명령 창을 엽니다.

2. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

3. Id가 둘 이상의 구독과 연결 된 경우 활성 구독을 설정 합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

5. 저장소 계정을 정의 하는 저장소 계정 컨텍스트를 가져옵니다.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   * `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다. 

6. [AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageserviceloggingproperty) 를 사용 하 여 현재 로그 보존 정책을 볼 수 있습니다. 다음 예에서는 blob 및 queue storage 서비스에 대 한 보존 기간을 콘솔에 출력 합니다.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   콘솔 출력에서 보존 기간은 열 머리글 아래에 나타납니다 `RetentionDays` .

   > [!div class="mx-imgBorder"]
   > ![PowerShell 출력의 보존 정책](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. [AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageserviceloggingproperty) 를 사용 하 여 보존 기간을 변경 합니다. 다음 예에서는 보존 기간을 4 일로 변경 합니다.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.  

### <a name="net-v12"></a>[.NET v12](#tab/dotnet)

다음 예에서는 blob 및 queue storage 서비스에 대 한 보존 기간을 콘솔에 출력 합니다.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

다음 예에서는 보존 기간을 4 일로 변경 합니다. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

다음 예에서는 blob 및 queue storage 서비스에 대 한 보존 기간을 콘솔에 출력 합니다.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

다음 예에서는 blob 및 queue storage 서비스에 대 한 로그의 보존 기간을 4 일로 변경 합니다. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>로그 데이터를 삭제 하 고 있는지 확인 합니다.

저장소 계정 컨테이너의 내용을 확인 하 여 로그가 삭제 되는지 확인할 수 있습니다 `$logs` . 다음 이미지는 컨테이너에 있는 폴더의 내용을 보여 줍니다 `$logs` . 폴더는 1 월 2021에 해당 하며 각 폴더에는 1 일의 로그가 포함 되어 있습니다. 오늘 날짜가 1 월 29 2021이 고 보존 정책이 1 일로 설정 된 경우이 폴더에는 1 일에 대 한 로그만 포함 되어야 합니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal의 로그 폴더 목록](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>로그 데이터 보기

 로그 데이터를 보고 분석하려면 관심 있는 로그 데이터가 포함된 Blob을 로컬 컴퓨터에 다운로드해야 합니다. 많은 스토리지 검색 도구를 사용하여 스토리지 계정에서 blob을 다운로드할 수 있습니다. 또한 Azure Storage 팀에서 제공한 명령줄 Azure 복사 도구([AzCopy](storage-use-azcopy-v10.md))를 사용하여 로그 데이터를 다운로드할 수 있습니다.  
 
>[!NOTE]
> `$logs` 컨테이너는 Event Grid와 통합되지 않으므로 로그 파일이 기록될 때 알림을 수신하지 않습니다. 

 관심 있는 로그 데이터를 다운로드하고 동일한 로그 데이터를 두 번 이상 다운로드하지 않으려면  

-   로그 데이터가 포함된 Blob에 날짜 및 시간 명명 규칙을 사용하여 분석을 위해 이미 다운로드한 Blob을 추적함으로써 동일한 데이터를 두 번 이상 다시 다운로드하는 것을 방지합니다.  

-   로그 데이터가 포함된 Blob에 메타데이터를 사용하여 Blob이 로그 데이터를 저장하는 특정 기간을 식별함으로써 다운로드해야 하는 정확한 Blob을 식별합니다.  

AzCopy를 시작하려면 [AzCopy 시작](storage-use-azcopy-v10.md)을 참조하세요. 

다음 예에서는 2014년 5월 20일 오전 9시, 오전 10시 및 오전 11시에 시작하는 시간 동안 큐 서비스에 대한 로그 데이터를 다운로드하는 방법을 보여 줍니다.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

특정 파일을 다운로드 하는 방법에 대 한 자세한 내용은 [AzCopy v10를 사용 하 여 Azure blob storage에서 Blob 다운로드](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조 하세요.

로그 데이터를 다운로드하면 파일의 로그 항목을 볼 수 있습니다. 이러한 로그 파일은 많은 로그 읽기 도구가 구문 분석할 수 있는 분리 된 텍스트 형식을 사용 합니다 (자세한 내용은 [모니터링, 진단 및 문제 Microsoft Azure Storage 해결](storage-monitoring-diagnosing-troubleshooting.md)가이드 참조). 다양한 도구에는 로그 파일의 콘텐츠를 형식 지정, 필터링, 정렬 및 검색하기 위한 다양한 기능이 포함되어 있습니다. 스토리지 로깅 로그 파일 형식 및 콘텐츠에 대한 자세한 내용은 [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 및 [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 스토리지 분석에 대 한 자세한 내용은 스토리지 분석 [스토리지 분석](storage-analytics.md) 를 참조 하세요.
* [스토리지 분석 메트릭을 구성](storage-monitor-storage-account.md)합니다.
* .NET 언어를 사용하여 Storage 로깅을 구성하는 방법에 대한 자세한 내용은 [Storage 클라이언트 라이브러리 참조](/previous-versions/azure/dn261237(v=azure.100))를 참조하세요. 
* REST API를 사용하여 Storage 로깅을 구성하는 방법에 대한 일반적인 내용은 [스토리지 분석 설정 및 구성](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics)을 참조하세요.
* 스토리지 분석 로그의 형식에 대해 자세히 알아보세요. [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format)을 참조 하세요.