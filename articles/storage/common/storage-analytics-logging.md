---
title: Azure Storage 분석 로깅
description: Azure 저장소에 대한 요청에 대한 세부 정보를 기록하는 방법에 대해 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5b94a97f1286e1273300014e4eef140be412436b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637175"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage 분석 로깅

스토리지 분석은 Storage 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 이 정로를 사용하면 개별 요청을 모니터링하고 스토리지 서비스의 문제를 진단할 수 있습니다. 요청은 최상의 노력을 기준으로 기록됩니다.

 스토리지 분석 로깅은 Storage 계정에 대해 기본적으로 사용하지 않도록 설정됩니다. 이 작업은 [Azure Portal](https://portal.azure.com/)에서 수행할 수 있습니다. 자세한 내용은 [Azure Portal에서 스토리지 계정 모니터링](/azure/storage/storage-monitor-storage-account)을 참조하세요. REST API 또는 클라이언트 라이브러리를 통해 프로그래밍 방식으로 스토리지 분석을 사용하도록 설정할 수도 있습니다. [Blob 서비스 속성 받기,](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) [큐 서비스 속성 받기](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)및 테이블 서비스 속성 [받기](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) 작업을 사용하여 각 서비스에 대한 저장소 분석을 사용하도록 설정합니다.

 서비스 엔드포인트에 대한 요청이 있는 경우에만 로그 항목이 만들어집니다. 예를 들어 저장소 계정에 Blob 끝점에 활동이 있지만 테이블 또는 Queue 끝점에 없는 경우 Blob 서비스와 관련된 로그만 만들어집니다.

> [!NOTE]
>  스토리지 분석 로깅은 현재 Blob, 큐 및 Table Service에서만 사용할 수 있습니다. 그러나 프리미엄 스토리지 계정은 지원되지 않습니다.

## <a name="requests-logged-in-logging"></a>로깅에 로그인한 요청
### <a name="logging-authenticated-requests"></a>인증된 요청 로깅

 다음과 같은 유형의 인증된 요청이 기록됩니다.

- 성공한 요청
- 실패한 요청(시간 제한, 제한, 네트워크, 권한 부여 및 기타 오류)
- 실패한 요청 및 성공한 요청을 포함하는 SAS(공유 액세스 서명) 또는 OAuth를 사용하는 요청
- 분석 데이터에 대한 요청

  로그 만들기/삭제 등 스토리지 분석 자체에서 수행한 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 항목에 나와 있습니다.

### <a name="logging-anonymous-requests"></a>익명 요청 로깅

 다음과 같은 유형의 익명 요청이 기록됩니다.

- 성공한 요청
- 서버 오류
- 클라이언트 및 서버 모두의 시간 초과 오류
- 오류 코드가 304(수정되지 않음)인 실패한 GET 요청

  기타 모든 실패한 익명 요청은 기록되지 않습니다. 기록되는 데이터의 전체 목록은 [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) 및 [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 항목에 나와 있습니다.

## <a name="how-logs-are-stored"></a>로그 저장 방법

모든 로그는 저장소 계정에 대해 Storage Analytics를 사용하도록 설정할 때 자동으로 생성되는 `$logs`라는 컨테이너의 블록 blob에 저장됩니다. `$logs` 컨테이너는 저장소 계정의 `http://<accountname>.blob.core.windows.net/$logs`Blob 네임스페이스에 있습니다. Storage 계정을 사용하도록 설정한 후에는 이 컨테이너를 삭제할 수 없지만 해당 콘텐츠는 삭제할 수 있습니다. 저장소 검색 도구를 사용하여 컨테이너로 직접 이동하는 경우 로깅 데이터가 포함된 모든 Blob이 표시됩니다.

> [!NOTE]
>  `$logs` 컨테이너 목록 작업(예: 컨테이너 목록 작업)이 수행될 때 컨테이너가 표시되지 않습니다. 직접 액세스해야 합니다. 예를 들어 Blob 목록 작업을 사용하여 `$logs` 컨테이너의 Blob에 액세스할 수 있습니다.

요청을 기록할 때 스토리지 분석은 중간 결과를 블록으로 업로드합니다. 그리고 이러한 블록을 정기적으로 커밋하여 Blob로 제공합니다. 저장소 서비스가 로그 작성기를 플러시하는 빈도가 때문에 **로그** 데이터가 $logs 컨테이너의 Blob에 표시되는 데 최대 1시간이 걸릴 수 있습니다. 같은 시간에 생성되는 로그의 경우 중복 레코드가 있을 수 있습니다. **RequestId** 및 **Operation** 번호를 검사하여 레코드 중복 여부를 확인할 수 있습니다.

매시간 여러 파일이 포함된 다량의 로그 데이터가 발생하는 경우 Blob 메타데이터를 사용하여 Blob 메타데이터 필드를 검토함으로써 로그에 포함된 데이터를 확인할 수 있습니다. 또한 로그 파일에 데이터를 기록하는 동안 지연이 있을 수 있으므로 Blob 메타데이터는 Blob 이름보다 Blob 콘텐츠를 보다 정확하게 표시합니다.

대부분의 저장소 검색 도구를 사용하여 Blob의 메타데이터를 볼 수 있습니다. 또한 PowerShell을 사용하거나 프로그래밍 방식으로 이 정보를 읽을 수 있습니다. 다음 PowerShell 조각은 로그 Blob 목록을 이름으로 필터링하여 시간을 지정하고, 메타데이터로 필터링하여 **write** 작업이 포함된 로그만 식별하는 예제입니다.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

프로그래밍 방식으로 Blob 나열에 대한 자세한 내용은 [Blob 리소스 열거](https://msdn.microsoft.com/library/azure/hh452233.aspx) 및 [Blob 리소스에 대한 속성 및 메타데이터 설정 및 검색을](https://msdn.microsoft.com/library/azure/dd179404.aspx)참조하십시오.  

### <a name="log-naming-conventions"></a>로그 명명 규칙

 각 로그는 다음과 같은 형식으로 작성됩니다.

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 다음 표에서는 로그 이름의 각 특성에 대해 설명합니다.

|attribute|Description|
|---------------|-----------------|
|`<service-name>`|스토리지 서비스의 이름입니다. 예: `blob`" `table``queue`|
|`YYYY`|로그의 4자리 숫자 연도입니다. 예: `2011`|
|`MM`|로그의 2자리 숫자 월입니다. 예: `07`|
|`DD`|로그의 2자리 숫자 일입니다. 예: `31`|
|`hh`|24시간 UTC 형식으로 된 로그의 시작 시간을 나타내는 2자리 숫자 시간입니다. 예: `18`|
|`mm`|로그의 시작 분을 나타내는 2자리 숫자입니다. **참고:**  이 값은 현재 버전의 저장소 분석에서 지원되지 않으며 `00`해당 값은 항상 입니다.|
|`<counter>`|1시간 동안 스토리지 서비스에 대해 생성되는 로그 Blob의 수를 나타내는 0부터 시작되는 6자리 카운터입니다. 이 카운터는 `000000`에서 시작합니다. 예: `000001`|

 다음은 위 예를 결합한 완전한 예제 로그 이름입니다.

 `blob/2011/07/31/1800/000001.log`

 다음은 위 로그에 액세스하는 데 사용할 수 있는 예제 URI입니다.

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 스토리지 요청이 기록되면 생성되는 로그 이름과 요청한 작업이 완료된 시간 간의 상관 관계가 지정됩니다. 예를 들어 GetBlob 요청이 2011년 7월 31일 오후 6시 30분에 완료된 경우 로그는 다음 접두사로 기록됩니다.`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>로그 메타데이터

 모든 로그 Blob는 해당 Blob에 포함된 로깅 데이터를 식별하는 데 사용할 수 있는 메타데이터와 함께 저장됩니다. 다음 표에서는 각 메타데이터 특성에 대해 설명합니다.

|attribute|Description|
|---------------|-----------------|
|`LogType`|로그에 읽기, 쓰기 또는 삭제 작업과 관련된 정보가 포함되는지 여부를 설명합니다. 이 값에는 한 가지 형식이 포함될 수도 있고 3개 형식이 모두 조합(쉼표로 구분)되어 포함될 수도 있습니다.<br /><br /> 예 1: `write`<br /><br /> 예 2: `read,write`<br /><br /> 예 3:`read,write,delete`|
|`StartTime`|`YYYY-MM-DDThh:mm:ssZ` 형식을 사용해서 표시되는 로그 항목의 초기 시간입니다. 예: `2011-07-31T18:21:46Z`|
|`EndTime`|`YYYY-MM-DDThh:mm:ssZ` 형식을 사용해서 표시되는 로그 항목의 마지막 시간입니다. 예: `2011-07-31T18:22:09Z`|
|`LogVersion`|로그 형식의 버전입니다.|

 다음 목록에서는 위 예제를 사용한 전체 예제 메타데이터를 보여줍니다.

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>저장소 로깅 사용

Azure 포털, PowerShell 및 저장소 SDK를 사용하여 저장소 로깅을 활성화할 수 있습니다.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Azure 포털을 사용하여 저장소 로깅 사용  

Azure 포털에서 진단 **설정(클래식) 블레이드를** 사용하여 저장소 계정의 **메뉴 블레이드의** **모니터링(클래식)** 섹션에서 액세스할 수 있는 저장소 로깅을 제어합니다.

기록된 데이터에 대해 기록할 저장소 서비스와 보존 기간(일)을 지정할 수 있습니다.  

### <a name="enable-storage-logging-using-powershell"></a>PowerShell을 사용하여 스토리지 로깅 사용  

 로컬 컴퓨터에서 PowerShell을 통해 Azure PowerShell cmdlet **Get-AzureStorageServiceLoggingProperty**를 사용하여 현재 설정을 검색하거나, cmdlet **Set-AzureStorageServiceLoggingProperty**를 사용하여 현재 설정을 변경하도록 저장소 계정의 저장소 로깅을 구성할 수 있습니다.  

 저장소 로깅을 제어하는 cmdlet은 로그에 대한 요청 유형의 쉼표로 구분된 목록이 포함된 문자열인 **LoggingOperations** 매개 변수를 사용합니다. 가능한 세 가지 요청 유형은 **읽기**, **쓰기** 및 **삭제**입니다. 로깅을 해제하려면 **LoggingOperations** 매개 변수에 **none** 값을 사용합니다.  

 다음 명령은 보존이 5일로 설정된 기본 저장소 계정의 Queue 서비스에서 읽기, 쓰기 및 삭제요청을 위해 로깅을 전환합니다.  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 다음 명령은 기본 저장소 계정의 테이블 서비스에 대해 로깅을 해제합니다.  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Azure 구독에서 작동하도록 Azure PowerShell cmdlet을 구성하고 사용할 기본 스토리지 계정을 선택하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)을 참조하세요.  

### <a name="enable-storage-logging-programmatically"></a>프로그래밍 방식으로 스토리지 로깅 사용  

 Azure 포털 또는 Azure PowerShell cmdlet을 사용하여 저장소 로깅을 제어하는 것 외에도 Azure 저장소 API 중 하나를 사용할 수도 있습니다. 예를 들어 .NET 언어를 사용 중인 경우 저장소 클라이언트 라이브러리를 사용할 수 있습니다.  

 **CloudBlobClient**, **CloudQueueClient** 및 **CloudTableClient** 클래스에는 모두 **SetServiceProperties** 및 **SetServicePropertiesAsync**와 같이 **ServiceProperties** 개체를 매개 변수로 사용하는 메서드가 포함되어 있습니다. **ServiceProperties** 개체를 사용하여 저장소 로깅을 구성할 수 있습니다. 예를 들어 다음 C# 조각은 큐 로깅의 기록되는 항목과 보존 기간을 변경하는 방법을 보여 줍니다.  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 .NET 언어를 사용하여 저장소 로깅을 구성하는 자세한 내용은 [저장소 클라이언트 라이브러리 참조](https://msdn.microsoft.com/library/azure/dn261237.aspx)를 참조하십시오.  

 REST API를 사용하여 스토리지 로깅을 구성하는 것에 대한 일반적인 내용은 [저장소 분석 사용 및 구성을](https://msdn.microsoft.com/library/azure/hh360996.aspx)참조하십시오.  

## <a name="download-storage-logging-log-data"></a>스토리지 로깅 로그 데이터 다운로드

 로그 데이터를 보고 분석하려면 관심 있는 로그 데이터가 포함된 Blob을 로컬 컴퓨터에 다운로드해야 합니다. 많은 저장소 검색 도구를 사용하면 저장소 계정에서 Blob을 다운로드할 수 있습니다. 또한 Azure Storage 팀에서 제공한 명령줄 Azure 복사 도구 [AzCopy를](storage-use-azcopy-v10.md) 사용하여 로그 데이터를 다운로드할 수도 있습니다.  
 
>[!NOTE]
> 컨테이너가 `$logs` Event Grid와 통합되지 않으므로 로그 파일이 작성될 때 알림을 받지 않습니다. 

 관심 있는 로그 데이터를 다운로드하고 동일한 로그 데이터를 두 번 이상 다운로드하지 않으려면  

-   로그 데이터가 포함된 Blob에 날짜 및 시간 명명 규칙을 사용하여 분석을 위해 이미 다운로드한 Blob을 추적함으로써 동일한 데이터를 두 번 이상 다시 다운로드하는 것을 방지합니다.  

-   로그 데이터가 포함된 Blob에 메타데이터를 사용하여 Blob이 로그 데이터를 저장하는 특정 기간을 식별함으로써 다운로드해야 하는 정확한 Blob을 식별합니다.  

AzCopy를 시작하려면 [AzCopy로 시작하기](storage-use-azcopy-v10.md) 를 참조하십시오. 

다음 예제에서는 2014년 5월 20일 오전 09시, 오전 10시 및 오전 11시부터 시간 동안 큐 서비스에 대한 로그 데이터를 다운로드하는 방법을 보여 주며 있습니다.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

특정 파일을 다운로드하는 방법에 대한 자세한 내용은 [특정 파일 다운로드를](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)참조하십시오.

로그 데이터를 다운로드하면 파일의 로그 항목을 볼 수 있습니다. 이러한 로그 파일은 Microsoft 메시지 분석기를 포함하여 많은 로그 읽기 도구가 구문 분석할 수 있는 구분된 텍스트 형식을 사용합니다(자세한 내용은 [Microsoft Azure Storage 의 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)가이드 참조). 다양한 도구에는 로그 파일의 콘텐츠를 형식 지정, 필터링, 정렬 및 검색하기 위한 다양한 기능이 포함되어 있습니다. 저장소 로깅 로그 파일 형식 및 콘텐츠에 대한 자세한 내용은 [저장소 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format) 및 저장소 분석 [로깅 작업 및 상태 메시지를](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [스토리지 분석 로그 형식](/rest/api/storageservices/storage-analytics-log-format)
* [스토리지 분석에서 기록한 작업 및 상태 메시지](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [스토리지 분석 지표(클래식)](storage-analytics-metrics.md)
