---
title: Azure Storage 모니터링 데이터 참조 | Microsoft Docs
description: Azure Storage의 모니터링 데이터를 위한 로그 및 메트릭 참조.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 481406b02d7d864dd16ac42918ae1aa2dea0b145
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195218"
---
# <a name="azure-storage-monitoring-data-reference"></a>Azure Storage 모니터링 데이터 참조

Azure Storage의 모니터링 데이터를 수집하고 분석하는 방법은 [Azure Storage 모니터링](monitor-storage.md)을 참조하세요.

## <a name="metrics"></a>메트릭

다음 표에는 Azure Storage에 대해 수집된 플랫폼 메트릭이 나와 있습니다. 

### <a name="capacity-metrics"></a>용량 메트릭

용량 메트릭 값은 매 1시간마다 Azure Monitor에 전송됩니다. 값은 매일 새로 고쳐집니다. 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 용량 메트릭에 대해 지원되는 시간 조직은 1시간(PT1H)입니다.

Azure Storage는 Azure Monitor에서 다음과 같은 용량 메트릭을 제공합니다.

#### <a name="account-level"></a>계정 수준

| 메트릭 | Description |
| ------------------- | ----------------- |
| UsedCapacity | 스토리지 계정에서 사용한 스토리지 양입니다. 표준 스토리지 계정의 경우 이는 Blob, 테이블, 파일 및 큐에서 사용한 용량의 합계입니다. Premium Storage 계정 및 Blob Storage 계정의 경우 BlobCapacity와 같습니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

#### <a name="blob-storage"></a>Blob Storage

| 메트릭 | Description |
| ------------------- | ----------------- |
| BlobCapacity | 스토리지 계정에 사용한 Blob Storage의 총계입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 <br/> 차원: **BlobType** 및 **BlobTier**([정의](#metrics-dimensions)) |
| BlobCount    | 스토리지 계정에 저장된 Blob 개체 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 <br/> 차원: **BlobType** 및 **BlobTier**([정의](#metrics-dimensions)) |
| ContainerCount    | 스토리지 계정의 컨테이너 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| IndexCapacity     | ADLS Gen2 계층적 인덱스에 사용한 스토리지 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

#### <a name="table-storage"></a>Table Storage

| 메트릭 | Description |
| ------------------- | ----------------- |
| TableCapacity | 스토리지 계정에 사용한 Table Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| TableCount   | 스토리지 계정의 테이블 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| TableEntityCount | 스토리지 계정의 테이블 엔터티 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

#### <a name="queue-storage"></a>Queue Storage

| 메트릭 | Description |
| ------------------- | ----------------- |
| QueueCapacity | 스토리지 계정에 사용한 Queue Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| QueueCount   | 스토리지 계정의 큐 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| QueueMessageCount | 스토리지 계정의 만료되지 않은 큐 메시지 수입니다. <br/><br/>단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

#### <a name="file-storage"></a>File Storage

| 메트릭 | Description |
| ------------------- | ----------------- |
| FileCapacity | 스토리지 계정에 사용한 File Storage 양입니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileCount   | 스토리지 계정의 파일 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |
| FileShareCount | 스토리지 계정의 파일 공유 수입니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 평균 <br/> 값 예제: 1024 |

### <a name="transaction-metrics"></a>트랜잭션 메트릭

트랜잭션 메트릭은 스토리지 계정에 대한 모든 요청이 있을 때 Azure Storage에서 Azure Monitor로 내보내집니다. 스토리지 계정에 작업이 없는 경우 해당 기간에 트랜잭션 메트릭에도 데이터가 없습니다. 모든 트랜잭션 메트릭은 계정 수준과 서비스 수준에서 모두 사용할 수 있습니다(Blob Storage, Table Storage, Azure 파일 및 Queue Storage). 시간 조직은 메트릭 값이 표시되는 시간 간격을 정의합니다. 모든 트랜잭션 메트릭에 대해 지원되는 시간 조직은 PT1H 및 PT1M입니다.

Azure Storage는 Azure Monitor에서 다음과 같은 트랜잭션 메트릭을 제공합니다.

| 메트릭 | Description |
| ------------------- | ----------------- |
| 트랜잭션 | 스토리지 서비스 또는 지정된 API 작업에 대해 제기된 요청 수입니다. 이 수는 성공 및 실패 요청뿐만 아니라 오류를 발생시킨 요청도 포함합니다. <br/><br/> 단위: 개수 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: ResponseType, GeoType, ApiName, Authentication([정의](#metrics-dimensions))<br/> 값 예제: 1024 |
| 수신 | 수신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 수신뿐만 아니라 Azure 내의 수신도 포함합니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| 송신 | 송신 데이터 양입니다. 이 수는 외부 클라이언트에서 Azure Storage로 송신뿐만 아니라 Azure 내의 송신도 포함합니다. 따라서 이 수는 청구 가능한 송신을 반영하지 않습니다. <br/><br/> 단위: 바이트 <br/> 집계 유형: 합계 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| SuccessServerLatency | Azure Storage에서 성공적인 요청을 처리하는 데 사용한 평균 시간입니다. 이 값은 SuccessE2ELatency에 지정된 네트워크 대기 시간을 포함하지 않습니다. <br/><br/> 단위: 밀리초 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| SuccessE2ELatency | 스토리지 서비스 또는 지정된 API 작업에 대해 제기된 성공적인 요청의 평균 엔드투엔드 대기 시간입니다. 이 값은 Azure Storage 내에서 요청을 읽고 응답을 보내고 응답 확인을 수신하는 데 필요한 처리 시간을 포함합니다. <br/><br/> 단위: 밀리초 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 1024 |
| 가용성 | 스토리지 서비스 또는 지정된 API 작업에 대한 가용성 백분율입니다. 가용성은 총 청구 가능 요청 값을 적용 가능한 요청 수(예기치 않은 오류를 발생시킨 요청 포함)로 나누어서 계산합니다. 모든 예기치 않은 오류는 스토리지 서비스 또는 지정된 API 작업에 대한 가용성을 감소시킵니다. <br/><br/> 단위: 백분율 <br/> 집계 유형: 평균 <br/> 적용 가능한 차원: GeoType, ApiName, Authentication([정의](#metrics-dimensions)) <br/> 값 예제: 99.99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>메트릭 차원

Azure Storage는 Azure Monitor의 메트릭에 대해 다음과 같은 차원을 지원합니다.

| 차원 이름 | Description |
| ------------------- | ----------------- |
| **BlobType** | Blob 메트릭용 Blob 형식만. 지원되는 값은 **BlockBlob**, **PageBlob** 및 **Azure Data Lake Storage**입니다. 추가 Blob는 BlockBlob에 포함됩니다. |
| **BlobTier** | Azure Storage는 가장 비용 효율적인 방식으로 Blob 개체 데이터를 저장할 수 있도록 여러 액세스 계층을 제공합니다. [Azure Storage Blob 계층](../blobs/storage-blob-storage-tiers.md)을 참조하세요. 지원되는 값은 다음과 같습니다. <br/> <li>**핫**: 핫 액세스 계층</li> <li>**쿨**: 쿨 액세스 계층</li> <li>**보관**: 보관 액세스 계층</li> <li>**프리미엄**: 블록 Blob 프리미엄 계층</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: 프리미엄 페이지 Blob 계층 유형</li> <li>**표준**: 표준 페이지 Blob 계층 유형</li> <li>**계층 없음**: 범용 v1 스토리지 계정 계층 유형</li> |
| **GeoType** | 기본 또는 보조 클러스터에서 전송되는 트랜잭션입니다. 사용 가능한 값에는 **기본**과 **보조**가 있습니다. 이는 보조 테넌트에서 개체를 읽을 때 RA-GRS(Read Access Geo Redundant Storage)에 적용됩니다. |
| **ResponseType** | 트랜잭션 응답 형식입니다. 사용 가능한 값은 다음을 포함합니다. <br/><br/> <li>**ServerOtherError**: 설명한 것을 제외한 다른 모든 서버 쪽 오류입니다. </li> <li>**ServerBusyError**: HTTP 503 상태 코드를 반환한 인증된 요청입니다. </li> <li>**ServerTimeoutError**: HTTP 500 상태 코드를 반환한 시간 초과된 인증된 요청입니다. 서버 오류로 인해 시간 제한이 발생하였습니다. </li> <li>**AuthorizationError**: 무단 데이터 액세스 또는 인증 실패로 인해 실패한 인증된 요청입니다. </li> <li>**NetworkError**: 네트워크 오류로 인해 실패한 인증된 요청입니다. 가장 일반적으로 시간 제한이 만료하기 전에 클라이언트가 연결을 너무 일찍 닫은 경우에 발생합니다. </li><li>**ClientAccountBandwidthThrottlingError**: 요청이 [스토리지 계정 확장성 제한](scalability-targets-standard-account.md)을 초과하여 대역폭이 제한되었습니다.</li><li>**ClientAccountRequestThrottlingError**: 요청이 [스토리지 계정 확장성 제한](scalability-targets-standard-account.md)을 초과하여 요청 속도가 제한되었습니다.<li>**ClientThrottlingError**: 기타 클라이언트 쪽 제한 오류입니다. ClientAccountBandwidthThrottlingError 및 ClientAccountRequestThrottlingError는 제외됩니다.</li> <li>**ClientTimeoutError**: HTTP 500 상태 코드를 반환한 시간 초과된 인증된 요청입니다. 클라이언트의 네트워크 시간 제한 또는 요청 시간 제한이 스토리지 서비스에서 예상한 것보다 낮은 값으로 설정된 경우 이는 예상된 시간 제한입니다. 그렇지 않은 경우 이는 ServerTimeoutError로 보고됩니다. </li> <li>**ClientOtherError**: 설명한 것을 제외한 다른 모든 클라이언트 쪽 오류입니다. </li> <li>**성공**: 성공한 요청입니다.</li> <li> **SuccessWithThrottling**: 첫 번째 시도에서 SMB 클라이언트가 제한되었지만 재시도 후에 성공하여 요청이 성공했습니다.</li> |
| **ApiName** | 작업 이름입니다. 예를 들면 다음과 같습니다. <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> 모든 작업 이름은 [문서](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)를 참조하세요. |
| **인증** | 트랜잭션에 사용되는 인증 형식입니다. 사용 가능한 값은 다음을 포함합니다. <br/> <li>**AccountKey**: 트랜잭션이 스토리지 계정 키를 사용하여 인증됩니다.</li> <li>**SAS**: 트랜잭션이 공유 액세스 서명을 사용하여 인증됩니다.</li> <li>**OAuth**: 트랜잭션이 OAuth 액세스 토큰을 사용하여 인증됩니다.</li> <li>**Anonymous**: 트랜잭션이 익명으로 요청됩니다. 실행 전 요청은 포함되지 않습니다.</li> <li>**AnonymousPreflight**: 트랜잭션이 실행 전 요청입니다.</li> |

메트릭 지원 차원의 경우 해당 메트릭 값을 보려면 차원 값을 지정해야 합니다. 예를 들어 성공적인 응답에 대한 **트랜잭션** 값을 조사하는 경우 **성공**을 포함한 **ResponseType** 차원을 필터링해야 합니다. 또는 블록 Blob에 대한 **BlobCount** 값을 조사하는 경우 **BlockBlob**을 포함한 **BlobType** 차원을 필터링해야 합니다.

## <a name="resource-logs-preview"></a>리소스 로그(미리 보기)

> [!NOTE]
> Azure Monitor의 Azure Storage 로그는 현재 공개 미리 보기이며 모든 퍼블릭 클라우드 지역에서 미리 보기 테스트에 사용할 수 있습니다. 미리 보기에 등록하려면 [이 페이지](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)를 참조하세요.  이 미리 보기에서는 BLOB(Azure Data Lake Storage Gen2 포함), 파일, 큐, 테이블, 범용 v1의 프리미엄 스토리지 계정, 범용 v2 스토리지 계정에 로그를 사용할 수 있습니다. 클래식 스토리지 계정은 지원되지 않습니다.

다음 표에는 Azure Monitor Logs 또는 Azure Storage에서 수집되는 경우 Azure Storage 리소스 로그의 속성이 나와 있습니다. 속성은 작업, 서비스, 작업을 수행하는 데 사용된 인증 유형을 설명합니다.

### <a name="fields-that-describe-the-operation"></a>작업을 설명하는 필드

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| 속성 | Description |
|:--- |:---|
|**time** | 스토리지에서 요청을 수신한 UTC(협정 세계시) 시간입니다. 예: `2018/11/08 21:09:36.6900118`|
|**resourceId** | 스토리지 계정의 리소스 ID입니다. 예: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | 요청된 작업의 범주입니다. 예: `StorageRead`, `StorageWrite` 또는 `StorageDelete`.|
|**operationName** | 수행된 REST 작업의 유형입니다. <br> 전체 작업 목록은 [스토리지 분석 기록 작업 및 상태 메시지 토픽](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)을 참조하세요. |
|**operationVersion** | 요청 시 지정된 스토리지 서비스 버전입니다. 이는 **x-ms-version** 헤더의 값과 같습니다. 예: `2017-04-17`|
|**schemaVersion** | 로그의 스키마 버전입니다. 예: `1.0`|
|**statusCode** | 요청의 HTTP 상태 코드입니다. 요청이 중단되면 이 값이 `Unknown`으로 설정될 수 있습니다. <br> 예: `206` |
|**statusText** | 요청된 작업의 상태입니다.  전체 상태 메시지 목록은 [스토리지 분석 기록 작업 및 상태 메시지 토픽](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)을 참조하세요. 버전 2017-04-17 이상에서는 상태 메시지 `ClientOtherError`가 사용되지 않습니다. 그 대신 이 필드가 오류 코드를 포함합니다. 예: `SASSuccess`  |
|**durationMs** | 요청된 작업을 수행하는 데 걸린 총 시간으로 밀리초 단위입니다. 여기에는 수신 요청을 읽는 시간과 요청자에게 응답을 보내는 시간이 포함됩니다. 예: `12`|
|**callerIpAddress** | 요청자의 IP 주소(포트 번호 포함)입니다. 예: `192.100.0.102:4362` |
|**correlationId** | 여러 리소스에서 로그를 상호 연결하는 데 사용되는 ID입니다. 예: `b99ba45e-a01e-0042-4ea6-772bbb000000` |
|**location** | 스토리지 계정의 위치입니다. 예: `North Europe` |
|**protocol**|작업에 사용되는 프로토콜입니다. 예: `HTTP`, `HTTPS`, `SMB` 또는 `NFS`|
| **uri** | 요청된 URI(Uniform Resource Identifier)입니다. 예: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10` |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>작업이 인증된 방식을 설명하는 필드

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| 속성 | Description |
|:--- |:---|
|**identity / type** | 요청을 수행하는 데 사용된 인증 형식입니다. 예: `OAuth`, `SAS Key`, `Account Key` 또는 `Anonymous` |
|**identity / tokenHash**|이 필드는 내부 전용으로 예약되어 있습니다. |
|**authorization / action** | 요청에 할당된 작업입니다. 예: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**authorization / roleAssignmentId** | 역할 할당 ID입니다. 예: `4e2521b7-13be-4363-aeda-111111111111`|
|**authorization / roleDefinitionId** | 역할 정의 ID입니다. 예: `ba92f5b4-2d11-453d-a403-111111111111"`|
|**principals / id** | 보안 주체의 ID입니다. 예: `a4711f3a-254f-4cfb-8a2d-111111111111`|
|**principals / type** | 보안 주체의 유형입니다. 예: `ServicePrincipal` |
|**requester / appID** | 요청자로 사용되는 OAuth(Open Authorization) 애플리케이션 ID입니다. <br> 예: `d3f7d5fe-e64a-4e4e-871d-333333333333`|
|**requester / audience** | 요청의 OAuth 대상 그룹입니다. 예: `https://storage.azure.com` |
|**requester / objectId** | 요청자의 OAuth 개체 ID입니다. Kerberos 인증의 경우 Kerberos로 인증된 사용자의 개체 식별자를 나타냅니다. 예: `0e0bf547-55e5-465c-91b7-2873712b249c` |
|**requester / tenantId** | ID의 OAuth 테넌트 ID입니다. 예: `72f988bf-86f1-41af-91ab-222222222222`|
|**requester / tokenIssuer** | OAuth 토큰 발급자입니다. 예: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`|
|**requester / upn** | 요청자의 UPN(사용자 계정 이름)입니다. 예: `someone@contoso.com` |
|**requester / userName** | 이 필드는 내부 전용으로 예약되어 있습니다.|

### <a name="fields-that-describe-the-service"></a>서비스를 설명하는 필드

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| 속성 | Description |
|:--- |:---|
|**accountName** | 스토리지 계정 이름입니다. 예: `mystorageaccount`  |
|**requestUrl** | 요청된 URL입니다. 예: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`|
|**userAgentHeader** | **User-Agent 헤더** 값으로, 따옴표로 묶여 있습니다. 예: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`|
|**referrerHeader** | **Referrer** 헤더 값입니다. 예: `http://contoso.com/about.html`|
|**clientRequestId** | 요청의 **x-ms-client-request-id** 헤더 값입니다. 예: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6` |
|**etag** | 반환된 개체의 ETag 식별자로, 따옴표로 묶여 있습니다. 예: `0x8D101F7E4B662C4`  |
|**serverLatencyMs** | 요청된 작업을 수행하는 데 걸린 총 시간으로 밀리초 단위입니다. 이 값은 네트워크 대기 시간(수신 요청을 읽는 시간과 요청자에게 응답을 보내는 시간)은 포함하지 않습니다. 예: `22` |
|**serviceType** | 이 요청과 연관된 서비스입니다. 예: `blob`, `table`, `files` 또는 `queue`. |
|**operationCount** | 요청에 관련된 각 로깅된 작업의 수입니다. 이 수는 인덱스 `0`에서 시작합니다. 일부 요청에는 둘 이상의 작업이 필요합니다(예: Blob 복사 요청). 대부분의 요청에서는 하나의 작업만 수행합니다. 예: `1` |
|**requestHeaderSize** | 요청 헤더의 크기로, 바이트 단위입니다. 예: `578` <br>요청이 실패할 경우 이 값은 비어 있을 수 있습니다. |
|**requestBodySize** | 스토리지 서비스에서 읽은 요청 패킷의 크기로, 바이트 단위입니다. <br> 예: `0` <br>요청이 실패할 경우 이 값은 비어 있을 수 있습니다.  |
|**responseHeaderSize** | 응답 헤더의 크기로, 바이트 단위입니다. 예: `216` <br>요청이 실패할 경우 이 값은 비어 있을 수 있습니다.  |
|**responseBodySize** | 스토리지 서비스가 쓴 응답 패킷의 크기로, 바이트 단위입니다. 요청이 실패할 경우 이 값은 비어 있을 수 있습니다. 예: `216`  |
|**requestMd5** | 요청의 **Content-MD5** 헤더 또는 **x-ms-content-md5** 헤더의 값입니다. 이 필드에 지정된 MD5 해시 값은 요청의 콘텐츠를 나타냅니다. 예: `788815fd0198be0d275ad329cafd1830` <br>이 필드는 비어 있을 수 있습니다.  |
|**serverMd5** | 스토리지 서비스에 의해 계산된 MD5 해시 값입니다. 예: `3228b3cf1069a5489b298446321f8521` <br>이 필드는 비어 있을 수 있습니다.  |
|**lastModifiedTime** | 반환된 개체의 LMT(마지막 수정 시간)입니다.  예: `Tuesday, 09-Aug-11 21:13:26 GMT` <br>여러 개체를 반환할 수 있는 작업의 경우 이 필드가 비어 있습니다. |
|**conditionsUsed** | 조건을 나타내는 키-값 쌍의 세미콜론으로 구분된 목록입니다. 조건은 다음 중 하나일 수 있습니다. <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> 예: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT` |
|**contentLengthHeader** | 스토리지 서비스로 전송된 요청의 Content-Length 헤더 값입니다. 요청이 성공할 경우 이 값은 requestBodySize와 같습니다. 요청에 실패할 경우 이 값은 requestBodySize와 다르거나 비어 있을 수 있습니다. |
|**tlsVersion** | 요청 연결에 사용되는 TLS 버전입니다. 예: `TLS 1.2` |
|**smbTreeConnectID** | 트리 연결 시간에 설정된 SMB(서버 메시지 블록) **treeConnectId**입니다. 예: `0x3` |
|**smbPersistentHandleID** | 네트워크 다시 연결 시에도 존속되는 SMB2 CREATE 요청의 영구 핸들 ID입니다.  [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1에서 **SMB2_FILEID.Persistent**로 참조됩니다. 예: `0x6003f` |
|**smbVolatileHandleID** | 네트워크 다시 연결 시에 재활용되는 SMB2 CREATE 요청의 휘발성 핸들 ID입니다.  [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1에서 **SMB2_FILEID.Volatile**로 참조됩니다. 예: `0xFFFFFFFF00000065` |
|**smbMessageID** | 연결에 상대적인 **MessageId**입니다. 예: `0x3b165` |
|**smbCreditsConsumed** | 요청에서 사용하는 수신 또는 송신으로, 64k 단위입니다. 예: `0x3` |
|**smbCommandDetail** | 일반적인 요청 유형이 아닌 이 특정 요청에 대한 자세한 정보입니다. 예: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | 파일 또는 디렉터리와 연결된 **FileId**입니다.  NTFS FileId와 대략적으로 유사합니다. 예: `0x9223442405598953` |
|**smbSessionID** | 세션 설정 시점에 설정된 SMB2 **SessionId**입니다. 예: `0x8530280128000049` |
|**smbCommandMajor  uint32** | **SMB2_HEADER.Command**의 값입니다. 현재 이 값은 0과 18 사이의 숫자입니다(0과 18 포함). 예: `0x6` |
|**smbCommandMinor** | 해당하는 경우 **SmbCommandMajor**의 서브클래스입니다. 예: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>참고 항목

- Azure Storage 모니터링에 대한 설명은 [Azure Storage 모니터링](monitor-storage.md)을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../../azure-monitor/insights/monitor-azure-resource.md)을 참조하세요.