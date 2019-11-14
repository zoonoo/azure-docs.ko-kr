---
title: Azure Blob Storage (미리 보기)에서 피드 변경 | Microsoft Docs
description: Azure Blob Storage의 변경 피드 로그 및 사용 방법에 대해 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: c4669809f1efa1f69081da17bf5ccbeddc39a716
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077140"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Azure Blob Storage의 변경 피드 지원 (미리 보기)

변경 피드의 목적은 저장소 계정의 blob 및 blob 메타 데이터에 발생 하는 모든 변경 내용에 대 한 트랜잭션 로그를 제공 하는 것입니다. 변경 피드는 이러한 변경 내용에 대 한 **순서**, **보장**, **내구성**, 변경 **불가능**, **읽기 전용** 로그를 제공 합니다. 클라이언트 응용 프로그램은 스트리밍 또는 일괄 처리 모드에서 언제 든 지 이러한 로그를 읽을 수 있습니다. 변경 피드를 사용 하면 Blob Storage 계정에서 발생 하는 변경 이벤트를 저렴 한 비용으로 처리 하는 효율적이 고 확장 가능한 솔루션을 빌드할 수 있습니다.

> [!NOTE]
> 변경 피드는 공개 미리 보기 상태 이며 **westcentralus** 및 **westus2** 지역에서 사용할 수 있습니다. 이 문서의 [조건](#conditions) 섹션을 참조 하세요. 미리 보기에 등록 하려면이 문서의 [구독 등록](#register) 단원을 참조 하세요.

변경 피드는 저장소 계정의 특수 컨테이너에 [blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 으로 표준 [blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 비용으로 저장 됩니다. 요구 사항에 따라 이러한 파일의 보존 기간을 제어할 수 있습니다 (현재 릴리스의 [조건](#conditions) 참조). 변경 이벤트는 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양의 레코드로 변경 피드에 추가 됩니다. 인라인 스키마를 사용 하 여 풍부한 데이터 구조를 제공 하는 간단 하 고 빠른 이진 형식입니다. 이 형식은 Hadoop 에코시스템, Stream Analytics 및 Azure Data Factory에서 널리 사용됩니다.

이러한 로그는 증분 방식으로 또는 전체에서 처리할 수 있습니다. 원하는 수의 클라이언트 응용 프로그램은 변경 피드를 독립적으로 개별적으로 읽을 수 있습니다. [Apache 드릴](https://drill.apache.org/docs/querying-avro-files/) 또는 [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) 와 같은 분석 응용 프로그램은 로그를 Avro 파일로 직접 사용할 수 있으며,이를 통해 높은 대역폭을 사용 하 여 사용자 지정 응용 프로그램을 작성 하지 않고도 로그를 처리할 수 있습니다.

변경 피드 지원은 변경 된 개체를 기반으로 데이터를 처리 하는 시나리오에 적합 합니다. 예를 들어 응용 프로그램에서 다음을 수행할 수 있습니다.

  - 보조 인덱스를 업데이트 하 고, 캐시, 검색 엔진 또는 기타 콘텐츠 관리 시나리오와 동기화 합니다.
  
  - 스트리밍 방식 또는 일괄 처리 모드에서 개체에 발생 하는 변경 내용을 기반으로 비즈니스 분석 정보 및 메트릭을 추출 합니다.
  
  - 엔터프라이즈 데이터 관리에 대 한 보안, 규정 준수 또는 인텔리전스를 위해 일정 기간 동안 개체에 대 한 변경 내용을 저장, 감사 및 분석 합니다.

  - 재해 관리 또는 규정 준수를 위해 계정에서 개체 상태를 백업, 미러링 또는 복제 하는 솔루션을 빌드합니다.

  - 변경 이벤트에 반응 하거나 만들어지거나 변경 된 개체를 기반으로 실행을 예약 하는 연결 된 응용 프로그램 파이프라인을 빌드합니다.

> [!NOTE]
> [Blob Storage 이벤트](storage-blob-event-overview.md) 는 Azure Functions 또는 응용 프로그램이 Blob에 발생 하는 변경 내용에 반응할 수 있도록 하는 실시간 일회성 이벤트를 제공 합니다. 변경 피드는 변경 내용에 대 한 지속적이 고 순서가 지정 된 로그 모델을 제공 합니다. 변경 피드의 변경 내용은 몇 분 후에 변경 피드에서 사용할 수 있도록 설정 됩니다. 응용 프로그램이이 보다 훨씬 더 빠르게 이벤트에 반응 해야 하는 경우 [Blob Storage 이벤트](storage-blob-event-overview.md) 를 대신 사용 하는 것이 좋습니다. Blob Storage 이벤트를 사용 하면 Azure Functions 또는 응용 프로그램에서 개별 이벤트를 실시간으로 대응할 수 있습니다.

## <a name="enable-and-disable-the-change-feed"></a>변경 피드 사용 및 사용 안 함

변경 캡처를 시작 하려면 저장소 계정에서 변경 피드를 사용 하도록 설정 해야 합니다. 변경 피드를 사용 하지 않도록 설정 하 여 변경 캡처를 중지 합니다. 포털 또는 Powershell에서 Azure Resource Manager 템플릿을 사용 하 여 변경 내용을 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

변경 피드를 사용 하도록 설정 하는 경우 기억해 야 할 몇 가지 사항은 다음과 같습니다.

- **$Blobchangefeed** 컨테이너에 저장 된 각 저장소 계정에는 blob 서비스에 대 한 변경 피드가 하나 뿐입니다.

- 변경 내용은 blob 서비스 수준 에서만 캡처됩니다.

- 변경 피드는 계정에서 발생 하는 모든 사용 가능한 이벤트의 *모든* 변경 내용을 캡처합니다. 클라이언트 응용 프로그램은 필요에 따라 이벤트 유형을 필터링 할 수 있습니다. 현재 릴리스의 [조건을](#conditions) 참조 하세요.

- GPv2 및 Blob 저장소 계정만 변경 피드를 사용 하도록 설정할 수 있습니다. GPv1 저장소 계정, Premium BlockBlobStorage 계정 및 계층적 네임 스페이스 사용 계정은 현재 지원 되지 않습니다.

### <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 템플릿을 배포 하려면 다음을 수행 합니다.

1. Azure Portal에서 **리소스 만들기**를 선택 합니다.

2. **Marketplace 검색**에서 **템플릿 배포**를 입력하고 **ENTER**를 누릅니다.

3. **템플릿 배포**를 선택 하 고 **만들기**를 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드**를 선택 합니다.

4. 템플릿 편집기에서 다음 json을 붙여넣습니다. `<accountName>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. **저장** 단추를 선택 하 고 계정의 리소스 그룹을 지정한 다음 **구매** 단추를 선택 하 여 템플릿을 배포 하 고 변경 피드를 사용 하도록 설정 합니다.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 템플릿을 배포 하려면 다음을 수행 합니다.

1. 최신 PowershellGet을 설치 합니다.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Powershell 콘솔을 닫았다가 다시 엽니다.

3. **Az. Storage** preview 모듈을 설치 합니다.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. `Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

   ```powershell
   Connect-AzAccount
   ```

5. 저장소 계정에 대 한 변경 피드를 사용 하도록 설정 합니다.

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

---

## <a name="understand-change-feed-organization"></a>변경 피드 구성 이해

<a id="segment-index"></a>

### <a name="segments"></a>세그먼트

변경 피드는 **매시간** *세그먼트로* 구성 되지만 몇 분 마다 추가 및 업데이트 되는 변경 내용에 대 한 로그입니다. 이러한 세그먼트는 해당 시간에 발생 하는 blob 변경 이벤트가 있는 경우에만 생성 됩니다. 이렇게 하면 클라이언트 응용 프로그램에서 전체 로그를 검색할 필요 없이 특정 시간 범위 내에 발생 하는 변경 내용을 사용할 수 있습니다. 자세히 알아보려면 [사양](#specifications)을 참조 하세요.

변경 피드의 사용 가능한 시간별 세그먼트는 해당 세그먼트에 대 한 변경 피드 파일의 경로를 지정 하는 매니페스트 파일에 설명 되어 있습니다. `$blobchangefeed/idx/segments/` 가상 디렉터리 목록은 이러한 세그먼트를 시간별로 정렬 하 여 보여 줍니다. 세그먼트의 경로는 세그먼트가 나타내는 시간별 시간 범위의 시작을 설명 합니다. 이 목록을 사용 하 여 관심 있는 로그 세그먼트를 필터링 할 수 있습니다.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 변경 피드를 사용 하도록 설정 하면 `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` 자동으로 생성 됩니다. 이 파일은 무시 해도 됩니다. 항상 빈 초기화 파일입니다. 

세그먼트 매니페스트 파일 (`meta.json`)은 `chunkFilePaths` 속성에서 해당 세그먼트에 대 한 변경 피드 파일의 경로를 표시 합니다. 다음은 세그먼트 매니페스트 파일의 예입니다.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> 저장소 계정의 컨테이너를 나열 하는 경우 계정에서 변경 피드 기능을 사용 하도록 설정한 후에만 `$blobchangefeed` 컨테이너가 표시 됩니다. 컨테이너를 확인 하기 전에 변경 피드를 사용 하도록 설정한 후 몇 분 정도 기다려야 합니다.

<a id="log-files"></a>

### <a name="change-event-records"></a>이벤트 레코드 변경

변경 피드 파일에는 일련의 변경 이벤트 레코드가 포함 되어 있습니다. 각 변경 이벤트 레코드는 개별 blob에 대 한 변경 내용 하나에 해당 합니다. 레코드는 [Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양을 사용 하 여 serialize 되 고 파일에 기록 됩니다. Avro 파일 형식 사양을 사용 하 여 레코드를 읽을 수 있습니다. 해당 형식으로 파일을 처리 하는 데 사용할 수 있는 여러 라이브러리가 있습니다.

변경 피드 파일은 `$blobchangefeed/log/` 가상 디렉터리에 [추가 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs)으로 저장 됩니다. 각 경로 아래에 있는 첫 번째 변경 피드 파일은 파일 이름에 `00000` 합니다 (예: `00000.avro`). 해당 경로에 추가 되는 각 후속 로그 파일의 이름은 1 씩 증가 합니다 (예: `00001.avro`).

다음은 Json으로 변환 된 변경 피드 파일의 이벤트 레코드 변경 예입니다.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

각 속성에 대 한 설명은 [Blob Storage에 대 한 Azure Event Grid 이벤트 스키마](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)를 참조 하세요.

> [!NOTE]
> 세그먼트에 대 한 변경 피드 파일은 세그먼트를 만든 후 즉시 표시 되지 않습니다. 지연 시간은 변경 피드의 몇 분 이내에 변경 피드의 게시 대기 시간에 대 한 일반적인 간격 내에 있습니다.

## <a name="consume-the-change-feed"></a>변경 피드 사용

변경 피드는 몇 가지 메타 데이터와 로그 파일을 생성 합니다. 이러한 파일은 저장소 계정의 **$blobchangefeed** 컨테이너에 있습니다. 

> [!NOTE]
> 현재 릴리스에서 **$blobchangefeed** 컨테이너는 Azure Storage 탐색기 나 Azure Portal에 표시 되지 않습니다. 현재 ListContainers API를 호출할 때 $blobchangefeed 컨테이너를 볼 수 없지만 컨테이너에서 직접 Listcontainers API를 호출 하 여 blob을 볼 수 있습니다.

클라이언트 응용 프로그램은 변경 피드 프로세서 SDK와 함께 제공 되는 blob 변경 피드 프로세서 라이브러리를 사용 하 여 변경 피드를 사용할 수 있습니다. 

[Azure Blob Storage에서 변경 피드 로그 처리를](storage-blob-change-feed-how-to.md)참조 하세요.

<a id="specifications"></a>

## <a name="specifications"></a>사양

- 변경 이벤트 레코드는 변경 피드에만 추가 됩니다. 이러한 레코드가 추가 되 면 변경할 수 없으며 레코드 위치는 안정적입니다. 클라이언트 응용 프로그램은 변경 피드의 읽기 위치에서 자신의 검사점을 유지 관리할 수 있습니다.

- 변경 이벤트 레코드는 변경 된 몇 분의 순서에 따라 추가 됩니다. 클라이언트 응용 프로그램은 스트리밍 액세스를 위해 추가 되거나 대량으로 추가 될 때 레코드를 사용 하도록 선택할 수 있습니다.

- 변경 이벤트 레코드는 **blob 당**수정 순서로 정렬 됩니다. Blob 간의 변경 순서는 Azure Blob Storage에서 정의 되지 않습니다. 이전 세그먼트의 모든 변경 내용은 후속 세그먼트에서 변경 되기 전에 발생 합니다.

- 변경 이벤트 레코드는 [Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양을 사용 하 여 로그 파일로 직렬화 됩니다.

- `eventType` 값이 `Control` 인 이벤트 레코드를 내부 시스템 레코드에 변경 하 고 계정의 개체에 대 한 변경 내용을 반영 하지 않습니다. 이러한 레코드는 무시 해도 됩니다.

- `storageDiagnonstics` 속성 모음의 값은 응용 프로그램에서 사용 하도록 설계 되지 않은 내부용 으로만 사용 됩니다. 응용 프로그램에는 해당 데이터에 대 한 계약 종속성이 없어야 합니다. 이러한 속성은 무시 해도 됩니다.

- 세그먼트가 나타내는 시간은 15 분 범위의 **근사치** 입니다. 따라서 지정 된 시간 내에 모든 레코드의 소비를 보장 하려면 연속 된 이전 및 다음 시간 세그먼트를 사용 합니다.

- 각 세그먼트의 `chunkFilePaths`수는 다를 수 있습니다. 이는 로그 스트림의 내부 분할으로 인해 게시 처리량을 관리 하기 때문입니다. 각 `chunkFilePath`의 로그 파일은 상호 배타적인 blob을 포함 하도록 보장 되며, 반복 하는 동안 blob 당 수정 순서를 위반 하지 않고 병렬로 사용 하 고 처리할 수 있습니다.

- 세그먼트가 `Publishing` 상태로 시작 됩니다. 세그먼트에 대 한 레코드를 추가 하는 작업이 완료 되 면 `Finalized`됩니다. `$blobchangefeed/meta/Segments.json` 파일의 `LastConsumable` 속성 날짜 이후 날짜가 지정 된 모든 세그먼트의 로그 파일은 응용 프로그램에서 사용 하지 않아야 합니다. `$blobchangefeed/meta/Segments.json` 파일의 `LastConsumable`속성 예는 다음과 같습니다.

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>구독 등록 (미리 보기)

변경 피드는 공개 미리 보기 상태 이므로 기능을 사용 하려면 구독을 등록 해야 합니다.

### <a name="register-by-using-powershell"></a>PowerShell을 사용 하 여 등록

PowerShell 콘솔에서 다음 명령을 실행 합니다.

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Azure CLI를 사용 하 여 등록

Azure Cloud Shell에서 다음 명령을 실행 합니다.

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>조건 및 알려진 문제 (미리 보기)

이 섹션에서는 변경 피드의 현재 공개 미리 보기의 알려진 문제 및 조건을 설명 합니다.
- 미리 보기의 경우 westcentralus 또는 westus2 지역에서 저장소 계정에 대 한 변경 피드를 사용 하도록 설정 하기 전에 먼저 [구독을 등록](#register) 해야 합니다. 
- 변경 피드는 만들기, 업데이트, 삭제 및 복사 작업만 캡처합니다. 메타 데이터 업데이트는 현재 미리 보기로 캡처되지 않습니다.
- 단일 변경에 대 한 이벤트 레코드 변경 내용은 변경 피드에서 두 번 이상 나타날 수 있습니다.
- 변경 피드 로그 파일의 수명은 시간 기반 보존 정책을 설정 하 여 관리할 수 없습니다.
- 로그 파일의 `url` 속성은 항상 비어 있습니다.
- Segment. json 파일의 `LastConsumable` 속성에는 변경 피드가 마무리 하는 첫 번째 세그먼트가 나열 되지 않습니다. 이 문제는 첫 번째 세그먼트가 완료 된 후에만 발생 합니다. 첫 번째 시간 이후의 모든 후속 세그먼트는 `LastConsumable` 속성에서 정확 하 게 캡처됩니다.

## <a name="next-steps"></a>다음 단계

- .NET 클라이언트 응용 프로그램을 사용 하 여 변경 피드를 읽는 방법의 예제를 참조 하세요. [Azure Blob Storage에서 변경 피드 로그 처리를](storage-blob-change-feed-how-to.md)참조 하세요.
- 실시간으로 이벤트에 반응 하는 방법에 대해 알아봅니다. [Blob Storage 이벤트에 대 한 응답을](storage-blob-event-overview.md) 참조 하세요.
