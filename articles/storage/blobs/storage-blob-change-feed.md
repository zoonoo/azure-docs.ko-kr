---
title: Azure Blob 저장소의 피드 변경(미리 보기) | 마이크로 소프트 문서
description: Azure Blob 저장소에서 변경 피드 로그에 대해 알아보고 로그를 사용하는 방법에 대해 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: ac111b06d578a0e9af8581ef2e8caeccfc4a291e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536890"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Azure Blob 저장소에서 피드 지원 변경(미리 보기)

변경 피드의 목적은 저장소 계정의 Blob 및 Blob 메타데이터에 발생하는 모든 변경 내용의 트랜잭션 로그를 제공하는 것입니다. 변경 피드는 이러한 변경 내용의 **주문,** **보증,** **내구성,** **불변성,** **읽기 전용** 로그를 제공합니다. 클라이언트 응용 프로그램은 스트리밍 또는 일괄 처리 모드에서 언제든지 이러한 로그를 읽을 수 있습니다. 변경 피드를 사용하면 저렴한 비용으로 Blob Storage 계정에서 발생하는 변경 이벤트를 처리하는 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

변경 피드는 표준 [Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/) 비용으로 저장소 계정의 특수 컨테이너에 [Blob으로](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 저장됩니다. 요구 사항에 따라 이러한 파일의 보존 기간을 제어할 수 있습니다(현재 릴리스의 [조건](#conditions) 참조). 변경 이벤트는 [아파치 Avro](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양의 레코드로 변경 피드에 추가됩니다: 인라인 스키마를 사용하여 풍부한 데이터 구조를 제공하는 컴팩트하고 빠른 이진 형식입니다. 이 형식은 Hadoop 에코시스템, Stream Analytics 및 Azure Data Factory에서 널리 사용됩니다.

이러한 로그를 비동기적, 증분 또는 전체로 처리할 수 있습니다. 임의의 수의 클라이언트 응용 프로그램은 변경 피드를 병렬로 자신의 속도로 독립적으로 읽을 수 있습니다. [아파치 드릴이나](https://drill.apache.org/docs/querying-avro-files/) [아파치 스파크와](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) 같은 분석 애플리케이션은 로그를 Avro 파일로 직접 사용할 수 있으므로 사용자 지정 응용 프로그램을 작성하지 않고도 저렴한 비용으로 고대역폭으로 처리할 수 있습니다.

변경 피드 지원은 변경된 개체를 기반으로 데이터를 처리하는 시나리오에 적합합니다. 예를 들어 응용 프로그램은 다음을 수행할 수 있습니다.

  - 보조 인덱스를 업데이트하고 캐시, 검색 엔진 또는 기타 콘텐츠 관리 시나리오와 동기화합니다.
  
  - 스트리밍 방식 또는 일괄 모드에서 개체에 발생하는 변경 사항을 기반으로 비즈니스 분석 인사이트 및 메트릭을 추출합니다.
  
  - 엔터프라이즈 데이터 관리를 위한 보안, 규정 준수 또는 인텔리전스를 위해 개체에 대한 변경 사항을 저장, 감사 및 분석할 수 있습니다.

  - 재해 관리 또는 규정 준수를 위해 계정에서 개체 상태를 백업, 미러 또는 복제하는 솔루션을 빌드합니다.

  - 변경 이벤트에 반응하는 연결된 응용 프로그램 파이프라인을 빌드하거나 생성하거나 변경된 개체를 기반으로 실행을 예약합니다.

> [!NOTE]
> 변경 피드는 Blob에 발생하는 변경 내용의 지속적, 순서가 지정된 로그 모델을 제공합니다. 변경 내용이 작성되고 변경 후 몇 분 이내에 변경 피드 로그에 사용할 수 있습니다. 응용 프로그램이 이보다 훨씬 빠르게 이벤트에 반응해야 하는 경우 [Blob Storage 이벤트를](storage-blob-event-overview.md) 대신 사용하는 것이 좋습니다. [Blob 저장소 이벤트는](storage-blob-event-overview.md) Azure Function 또는 응용 프로그램이 Blob에 발생하는 변경 사항에 신속하게 대응할 수 있도록 실시간 일회성 이벤트를 제공합니다. 

## <a name="enable-and-disable-the-change-feed"></a>변경 피드 사용 및 비활성화

변경 내용을 캡처하고 기록하기 시작하려면 저장소 계정의 변경 피드를 사용하도록 설정해야 합니다. 변경 피드를 사용하지 않도록 설정하여 변경 내용 캡처를 중지합니다. 포털 또는 Powershell에서 Azure 리소스 관리자 템플릿을 사용하여 변경 내용을 활성화하고 비활성화할 수 있습니다.

변경 피드를 사용하도록 설정할 때 염두에 두어야 할 몇 가지 사항은 다음과 같습니다.

- 각 저장소 계정에 는 Blob 서비스에 대한 변경 피드가 하나뿐이며 **$blobchangefeed** 컨테이너에 저장됩니다.

- 변경 내용 만들기, 업데이트 및 삭제는 Blob 서비스 수준에서만 캡처됩니다.

- 변경 피드는 계정에서 발생하는 사용 가능한 모든 이벤트에 대한 *모든* 변경 내용을 캡처합니다. 클라이언트 응용 프로그램은 필요에 따라 이벤트 유형을 필터링할 수 있습니다. (현재 릴리스의 [조건을](#conditions) 참조하십시오.)

- GPv2 및 Blob 저장소 계정만 피드 변경을 사용하도록 설정할 수 있습니다. 프리미엄 BlockBlobStorage 계정 및 계층 적 네임 스페이스 사용 계정 현재 지원 되지 않습니다. GPv1 저장소 계정은 지원되지 않지만 가동 중지 시간 없이 GPv2로 업그레이드할 수 있으며 자세한 내용은 [GPv2 저장소 계정으로 업그레이드를](../common/storage-account-upgrade.md) 참조하십시오.

> [!IMPORTANT]
> 변경 피드는 공개 미리 보기로 제공되며 **서권** 및 **westus2** 지역에서 사용할 수 있습니다. 이 문서의 [조건](#conditions) 섹션을 참조하십시오. 미리 보기에 등록하려면 이 문서의 [구독 등록](#register) 섹션을 참조하십시오. 저장소 계정에서 변경 피드를 사용하도록 설정하려면 먼저 구독을 등록해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 저장소 계정에 대한 변경 피드를 사용하도록 설정합니다.

1. Azure [포털에서](https://portal.azure.com/)저장소 계정을 선택합니다. 

2. **Blob Service**에서 **데이터 보호** 옵션으로 이동합니다.

3. **Blob 변경 피드에서** **활성화를** 클릭합니다.

4. **저장** 버튼을 선택하여 데이터 보호 설정을 확인합니다.

![](media/storage-blob-soft-delete/storage-blob-soft-delete-portal-configuration.png)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 변경 피드를 활성화합니다.

1. 최신 PowershellGet을 설치합니다.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. 닫은 다음 Powershell 콘솔을 다시 엽니다.

3. **Az.Storage** 미리 보기 모듈을 설치합니다.

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. `Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

   ```powershell
   Connect-AzAccount
   ```

5. 저장소 계정의 변경 피드를 사용하도록 설정합니다.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[템플릿](#tab/template)
Azure 리소스 관리자 템플릿을 사용하여 Azure 포털을 통해 기존 저장소 계정에 대한 변경 피드를 사용하도록 설정합니다.

1. Azure 포털에서 **리소스 만들기를 선택합니다.**

2. **Marketplace 검색**에서 **템플릿 배포**를 입력하고 **ENTER**를 누릅니다.

3. **[사용자 지정 템플릿 배포를](https://portal.azure.com/#create/Microsoft.Template)** 선택한 다음 **편집기에서 사용자 고유의 템플릿 빌드를 선택합니다.**

4. 템플릿 편집기에서 다음 json에 붙여 넣습니다. `<accountName>` 자리 표시자를 스토리지 계정 이름으로 바꿉니다.

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
    
5. **저장** 단추를 선택하고 계정의 리소스 그룹을 지정한 다음 **구매** 단추를 선택하여 템플릿을 배포하고 변경 피드를 사용하도록 설정합니다.

---

## <a name="consume-the-change-feed"></a>변경 피드 사용

변경 피드는 여러 메타데이터 및 로그 파일을 생성합니다. 이러한 파일은 저장소 계정의 **$blobchangefeed** 컨테이너에 있습니다. 

> [!NOTE]
> 현재 릴리스에서는 **$blobchangefeed** 컨테이너가 Azure 저장소 탐색기 또는 Azure 포털에 표시되지 않습니다. 현재 ListContainers API를 호출할 때 $blobchangefeed 컨테이너를 볼 수 없지만 컨테이너에서 ListBlobs API를 직접 호출하여 Blob을 볼 수 있습니다.

클라이언트 응용 프로그램은 변경 피드 프로세서 SDK와 함께 제공되는 Blob 변경 피드 프로세서 라이브러리를 사용하여 변경 피드를 사용할 수 있습니다. 

[Azure Blob 저장소에서 프로세스 변경 피드 로그를](storage-blob-change-feed-how-to.md)참조하십시오.

## <a name="understand-change-feed-organization"></a>변경 피드 조직 이해

<a id="segment-index"></a>

### <a name="segments"></a>세그먼트

변경 피드는 **시간별** *세그먼트로* 구성되지만 몇 분마다 추가되고 업데이트되는 변경 내용로 구성됩니다. 이러한 세그먼트는 해당 시간에 발생하는 Blob 변경 이벤트가 있는 경우에만 만들어집니다. 이렇게 하면 클라이언트 응용 프로그램에서 전체 로그를 검색할 필요 없이 특정 시간 범위 내에서 발생하는 변경 내용을 사용할 수 있습니다. 자세한 내용은 사양 [을](#specifications)참조하십시오.

변경 피드의 사용 가능한 시간별 세그먼트는 해당 세그먼트의 변경 피드 파일에 대한 경로를 지정하는 매니페스트 파일에 설명되어 있습니다. `$blobchangefeed/idx/segments/` 가상 디렉터리 목록에는 시간별로 정렬된 세그먼트가 표시됩니다. 세그먼트의 경로는 세그먼트가 나타내는 시간별 시간 범위의 시작을 설명합니다. 이 목록을 사용하여 관심 있는 로그 세그먼트를 필터링할 수 있습니다.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 변경 `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` 피드를 사용하도록 설정하면 자동으로 생성됩니다. 이 파일을 무시해도 됩니다. 항상 비어 있는 초기화 파일입니다. 

세그먼트 매니페스트`meta.json`파일 () 속성에서 해당 세그먼트에 대 `chunkFilePaths` 한 변경 피드 파일의 경로를 표시 합니다. 세그먼트 매니페스트 파일의 예는 다음과 같습니다.

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
> 컨테이너는 `$blobchangefeed` 계정에서 변경 피드 기능을 활성화한 후에만 나타납니다. 컨테이너에 Blob을 나열하려면 변경 피드를 사용하도록 설정한 후 몇 분 후에 기다려야 합니다. 

<a id="log-files"></a>

### <a name="change-event-records"></a>이벤트 레코드 변경

변경 피드 파일에는 일련의 변경 이벤트 레코드가 포함되어 있습니다. 각 변경 이벤트 레코드는 개별 Blob에 대한 하나의 변경에 해당합니다. 기록은 직렬화되고 [아파치 아브로](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양을 사용하여 파일에 기록됩니다. 레코드는 Avro 파일 형식 사양을 사용하여 읽을 수 있습니다. 해당 형식으로 파일을 처리하는 데 사용할 수 있는 여러 라이브러리가 있습니다.

변경 피드 파일은 `$blobchangefeed/log/` 가상 디렉터리에 [Blob을 부속하기 로 저장됩니다.](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) 각 경로 아래의 첫 번째 `00000` 변경 피드 파일은 `00000.avro`파일 이름(예:)에 있습니다. 해당 경로에 추가된 각 후속 로그 파일의 이름은 1씩 증가합니다(예: `00001.avro`).

다음은 Json으로 변환된 변경 피드 파일의 변경 이벤트 레코드의 예입니다.

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

각 속성에 대한 설명은 [Blob Storage에 대한 Azure 이벤트 그리드 이벤트 스키마를](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)참조하십시오.

> [!NOTE]
> 세그먼트에 대한 변경 피드 파일은 세그먼트를 만든 후 즉시 나타나지 않습니다. 지연 기간은 변경 후 몇 분 내에 있는 변경 피드의 게시 대기 시간 의 정상적인 간격 내에 있습니다.

<a id="specifications"></a>

## <a name="specifications"></a>사양

- 변경 이벤트 레코드는 변경 피드에만 추가됩니다. 이러한 레코드가 추가되면 변경할 수 없으며 레코드 위치가 안정됩니다. 클라이언트 응용 프로그램은 변경 피드의 읽기 위치에 대한 자체 검사점(검사점)을 유지할 수 있습니다.

- 변경 이벤트 레코드는 변경 후 몇 분 이내에 추가됩니다. 클라이언트 응용 프로그램은 스트리밍 액세스에 추가되거나 다른 시간에 대량으로 레코드를 사용하도록 선택할 수 있습니다.

- 변경 이벤트 레코드는 **Blob당**수정 순서에 따라 정렬됩니다. Azure Blob 저장소에서 Blob에 대한 변경 순서는 정의되지 않습니다. 이전 세그먼트의 모든 변경 사항은 후속 세그먼트의 변경 전에 있습니다.

- 변경 이벤트 레코드는 [아파치 Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) 형식 사양을 사용하여 로그 파일에 직렬화됩니다.

- 값이 내부 시스템 `eventType` `Control` 레코드이며 계정의 개체에 대한 변경 사항을 반영하지 않는 이벤트 레코드를 변경합니다. 이러한 레코드를 안전하게 무시할 수 있습니다.

- 속성 가방의 `storageDiagnonstics` 값은 내부 용도로만 사용되며 응용 프로그램에서 사용하도록 설계되지 않았습니다. 응용 프로그램은 해당 데이터에 대한 계약상 종속성이 없어야 합니다. 이러한 속성을 무시해도 됩니다.

- 세그먼트로 표시되는 시간은 **경계가** 15분인 근사치입니다. 따라서 지정된 시간 내에 모든 레코드를 소비하려면 이전 및 다음 시간 세그먼트를 연속해서 사용하십시오.

- 각 세그먼트는 게시 처리량을 관리하기 위해 로그 스트림의 `chunkFilePaths` 내부 분할로 인해 다른 번호를 가질 수 있습니다. 각 `chunkFilePath` 로그 파일에는 상호 배타적인 Blob이 포함되어 있는지 보장되며 반복 중에 Blob당 수정 순서를 위반하지 않고 병렬로 사용 및 처리할 수 있습니다.

- 세그먼트는 `Publishing` 상태에서 시작합니다. 세그먼트에 대한 레코드의 부가 가 완료되면. `Finalized` `$blobchangefeed/meta/Segments.json` 파일의 `LastConsumable` 속성 날짜 이후에 날짜가 지정된 세그먼트에 파일을 기록하면 응용 프로그램에서 사용하지 않아야 합니다. 파일의 `LastConsumable`속성 예는 다음과 `$blobchangefeed/meta/Segments.json` 같습니다.

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

## <a name="register-your-subscription-preview"></a>구독 등록(미리 보기)

변경 피드는 공개 미리 보기에서만 사용되므로 이 기능을 사용하려면 구독을 등록해야 합니다.

### <a name="register-by-using-powershell"></a>PowerShell을 사용하여 등록

PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```
   
### <a name="register-by-using-azure-cli"></a>Azure CLI를 사용하여 등록

Azure 클라우드 셸에서 다음 명령을 실행합니다.

```azurecli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>조건 및 알려진 문제(미리 보기)

이 섹션에서는 변경 피드의 현재 공개 미리 보기에서 알려진 문제 및 조건에 대해 설명합니다. 
- 미리 보기의 경우 먼저 [구독을 등록해야](#register) 서중부 또는 westus2 지역에서 저장소 계정에 대한 변경 피드를 사용하도록 설정할 수 있습니다. 
- 변경 피드는 만들기, 업데이트, 삭제 및 복사 작업만 캡처합니다. 메타데이터 업데이트는 현재 미리 보기에서 캡처되지 않습니다.
- 단일 변경에 대한 변경 이벤트 레코드는 변경 피드에 두 번 이상 나타날 수 있습니다.
- 아직 시간 기반 보존 정책을 설정 하 여 변경 피드 로그 파일의 수명을 관리할 수 없습니다 및 Blob을 삭제할 수 없습니다. 
- 로그 파일의 속성은 `url` 현재 항상 비어 있습니다.
- segments.json 파일의 속성에는 `LastConsumable` 변경 피드가 종료하는 첫 번째 세그먼트가 나열되지 않습니다. 이 문제는 첫 번째 세그먼트가 완료된 후에만 발생합니다. 첫 번째 시간 이후의 모든 후속 `LastConsumable` 세그먼트는 속성에 정확하게 캡처됩니다.
- 현재 $blobchangefeed **컨테이너를** 볼 수 없습니다 ListContainers API를 호출 하 고 컨테이너 Azure 포털 또는 저장소 탐색기에서 나타나지 않습니다.
- 이전에 [계정 장애 조치(failover)를](../common/storage-disaster-recovery-guidance.md) 시작한 저장소 계정에로고파일이 나타나지 않는 문제가 있을 수 있습니다. 향후 계정 장애 조치(failover)는 미리 보기 중에 로그 파일에도 영향을 미칠 수 있습니다.

## <a name="faq"></a>FAQ

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>변경 피드와 스토리지 분석 로깅의 차이점은 무엇입니까?
애널리틱스 로그는 모든 작업에서 성공 및 실패한 요청으로 모든 읽기, 쓰기, 목록 및 삭제 작업의 레코드를 포함합니다. 애널리틱스 로그는 최선의 노력이며 주문이 보장되지 않습니다.

변경 피드는 Blob 생성, 수정 및 삭제와 같은 성공적인 돌연변이 또는 계정 변경에 대한 트랜잭션 로그를 제공하는 솔루션입니다. 변경 피드는 Blob당 성공적인 변경 순서로 모든 이벤트를 기록하고 표시하도록 보장하므로 방대한 양의 읽기 작업 또는 실패한 요청에서 노이즈를 필터링할 필요가 없습니다. 변경 피드는 특정 보증이 필요한 응용 프로그램 개발을 위해 근본적으로 설계되고 최적화되어 있습니다.

### <a name="should-i-use-change-feed-or-storage-events"></a>피드 또는 저장소 변경 이벤트를 사용해야 합니까?
변경 피드 및 [Blob 저장소 이벤트로](storage-blob-event-overview.md) 두 기능을 모두 활용할 수 있습니다 동일한 배달 안정성 보장과 동일한 정보를 제공 하며 주요 차이점은 이벤트 레코드의 대기 시간, 정렬 및 저장입니다. 변경 피드는 변경 후 몇 분 이내에 레코드를 로그에 게시하고 Blob당 변경 작업 순서를 보장합니다. 저장소 이벤트는 실시간으로 푸시되며 정렬되지 않을 수 있습니다. 변경 피드 이벤트는 자신의 정의된 보존이 있는 읽기 전용 안정 로그로 저장소 계정 내에 보관되며, 저장소 이벤트는 명시적으로 저장하지 않는 한 이벤트 처리기에서 일시적으로 사용할 수 있습니다. 변경 피드를 사용하면 Blob API 또는 SDK를 사용하여 여러 응용 프로그램에서 자신의 편의에 따라 로그를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- .NET 클라이언트 응용 프로그램을 사용하여 변경 피드를 읽는 방법에 대한 예제를 참조하세요. [Azure Blob 저장소에서 프로세스 변경 피드 로그를](storage-blob-change-feed-how-to.md)참조하십시오.
- 이벤트에 실시간으로 반응하는 방법에 대해 알아봅니다. [Blob 저장소 이벤트에 대한 반응](storage-blob-event-overview.md) 보기
- 모든 요청에 대해 성공 및 실패한 작업에 대한 자세한 로깅 정보에 대해 자세히 알아봅니다. [Azure 저장소 분석 로깅](../common/storage-analytics-logging.md) 참조
