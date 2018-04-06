---
title: '데이터 연결: 이벤트 스트림의 데이터 스트림 입력 | Microsoft Docs'
description: "'inputs'이라는 Stream Analytics에 대해 데이터 연결을 설정하는 방법을 알아보세요. 입력에는 이벤트의 데이터 스트림과 참조 데이터가 포함되어 있습니다."
keywords: 데이터 스트림, 데이터 연결, 이벤트 스트림
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: 0ddc3187e9fc0664838dd07f781f4d7e2e4a7fe0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>데이터 연결: 이벤트에서 Stream Analytics으로의 데이터 스트림 입력에 대해 알아보기
Stream Analytics 작업에 대한 데이터 연결은 데이터 원본의 이벤트 스트림이며 작업의 *입력*으로 참조됩니다. Stream Analytics는 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) 및 [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)를 비롯한 Azure 데이터 스트림 원본과 높은 수준으로 통합됩니다. 이러한 입력 원본은 분석 작업과 동일한 Azure 구독 또는 다른 구독에서 가져올 수 있습니다.

## <a name="data-input-types-data-stream-and-reference-data"></a>데이터 입력 유형: 데이터 스트림 및 참조 데이터
데이터가 해당 데이터 원본에 푸시되면 Stream Analytics 작업에서 사용되고 실시간으로 처리됩니다. 입력은 데이터 스트림 입력과 참조 데이터 입력의 두 가지 형식으로 나뉩니다.

### <a name="data-stream-inputs"></a>데이터 스트림 입력
데이터 스트림은 시간이 지남에 따라 생성되는 이벤트의 제한 없는 시퀀스입니다. Stream Analytics 작업은 하나 이상의 데이터 스트림 입력을 포함해야 합니다. Event Hubs, IoT Hub 및 Blob Storage는 데이터 스트림 입력 원본으로 지원됩니다. 이벤트 허브는 여러 장치 및 서비스에서 이벤트 스트림을 수집하는 데 사용됩니다. 이러한 스트림은 소셜 미디어 활동 피드, 주식 거래 정보 또는 센서 데이터를 포함할 수 있습니다. IoT Hub는 IoT(사물 인터넷) 시나리오에서 연결된 장치로부터 데이터를 수집하도록 최적화됩니다.  Blob Storage를 로그 파일과 같은 스트림으로 대량 데이터 수집을 위한 입력 원본으로 사용할 수 있습니다.  

### <a name="reference-data"></a>참조 데이터
Stream Analytics은 *참조 데이터*라는 입력도 지원합니다. 고정적이거나 천천히 변하는 보조 데이터입니다. 일반적으로 상관 관계 및 조회를 수행하는 데 사용됩니다. 예를 들어 정적 값을 조회하기 위해 SQL 조인을 수행하기는 하지만 데이터 스트림 입력의 데이터를 참조 데이터의 데이터로 조인할 수 있습니다. Azure Blob 저장소는 현재 유일하게 지원되는 참조 데이터용 입력 소스입니다. 참조 데이터 원본 Blob은 크기가 100MB로 제한됩니다.

참조 데이터 입력을 만드는 방법을 알아보려면 [참조 데이터 사용](stream-analytics-use-reference-data.md)을 참조하세요.  

## <a name="compression"></a>압축

Azure Stream Analytics는 모든 데이터 스트림 입력 원본(Event Hub, IoT Hub 및 Blob Storage)에서 압축을 지원합니다. 이 기능은 Azure Portal의 **새 입력** 블레이드에 새 드롭다운 옵션을 추가하므로 선택적으로 데이터 스트림을 압축하도록 선택할 수 있습니다. 현재 지원되는 참조 형식은 None, GZip 및 Deflate 압축입니다. 참조 데이터에는 압축이 지원되지 않습니다.

Avro serialization에서는 압축 형식을 지정할 필요가 없습니다. 입력 Avro 데이터가 압축되는 경우 투명하게 처리됩니다. 

## <a name="create-data-stream-input-from-event-hubs"></a>Event Hubs에서 데이터 스트림 입력 만들기

Azure Event Hubs는 확장성 있는 게시-구독 이벤트 수집기를 제공합니다. 이벤트 허브는 초당 수백만 개의 이벤트를 수집할 수 있으므로 연결된 장치와 응용 프로그램이 생성하는 대량의 데이터를 처리하고 분석할 수 있습니다. Event Hubs 및 Stream Analytics를 함께 사용하면 실시간 분석을 위한 종단 간 솔루션이 제공됩니다. Event Hubs를 통해 Azure에 실시간으로 이벤트가 제공되며 Stream Analytics 작업에서 이러한 이벤트를 실시간으로 처리합니다. 예를 들어 Event Hubs에 웹 클릭, 센서 판독값 또는 온라인 로그 이벤트를 보낼 수 있습니다. 그런 다음 실시간 필터링, 집계 및 상관 관계에 대한 입력 데이터 스트림으로 Event Hubs를 사용하도록 Stream Analytics 작업을 만들 수 있습니다.

Stream Analytics의 Event Hubs에서 오는 이벤트의 기본 타임스탬프가 `EventEnqueuedUtcTime`인 이벤트 허브에 이벤트가 도착한 타임스탬프입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

### <a name="consumer-groups"></a>소비자 그룹
각 Stream Analytics 이벤트 허브 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 셀프 조인이 포함되거나 입력이 여러 개인 경우 둘 이상의 판독기 다운스트림으로 일부 입력을 읽을 수 있습니다. 이러한 상황은 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 파티션당 소비자 그룹마다 5개 판독기의 Event Hubs 한도 초과를 방지하려면 각 Stream Analytics 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다. 또한 이벤트 허브당 20개의 소비자 그룹으로 제한됩니다. 자세한 내용은 [Event Hubs 프로그래밍 가이드](../event-hubs/event-hubs-programming-guide.md)를 참조하세요.

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>데이터 스트림 입력으로 이벤트 허브 구성
다음 표에서는 입력으로 이벤트 허브를 구성할 때 Azure Portal의 **새 입력** 블레이드에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** |이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| **서비스 버스 네임스페이스** |Azure Service Bus 네임스페이스는 메시징 엔터티 집합에 대한 컨테이너입니다. 새 이벤트 허브를 만들 때 Service Bus 네임스페이스도 만듭니다. |
| **이벤트 허브 이름** |입력으로 사용할 이벤트 허브의 이름입니다. |
| **이벤트 허브 정책 이름** |이벤트 허브에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| **이벤트 허브 소비자 그룹**(선택 사항) |이벤트 허브에서 데이터를 수집하는 데 사용할 소비자 그룹입니다. 소비자 그룹이 지정되지 않으면 Stream Analytics 작업에서 기본 소비자 그룹을 사용합니다. 각 Stream Analytics 작업마다 고유한 소비자 그룹을 사용하는 것이 좋습니다. |
| **이벤트 직렬화 형식** |들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다. |
| **Encoding** | 현재 유일하게 지원되는 인코딩 형식은 UTF-8입니다. |
| **압축**(선택 사항) | 들어오는 데이터 스트림의 압축 유형(None, GZip 또는 Deflate)입니다. |

데이터를 이벤트 허브에서 가져온 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **EventProcessedUtcTime** |이벤트가 Stream Analytics으로 처리되는 날짜 및 시간입니다. |
| **EventEnqueuedUtcTime** |Event Hubs에서 이벤트를 받은 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 이러한 필드를 사용하여 다음 예제와 같은 쿼리를 작성할 수 있습니다.

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Event Hub를 IoT Hub Route의 끝점으로 사용하는 경우에는 [GetMetadataPropertyValue 함수](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx)를 사용하여 IoT Hub 메타데이터에 액세스할 수 있습니다.
> 

## <a name="create-data-stream-input-from-iot-hub"></a>IoT Hub에서 데이터 스트림 입력 만들기
Azure Iot 허브는 IoT 시나리오에 최적화된, 확장성이 뛰어난 게시-구독 이벤트 처리기입니다.

Stream Analytics의 IoT Hub에서 오는 이벤트의 기본 타임스탬프가 `EventEnqueuedUtcTime`인 IoT Hub에 이벤트가 도착한 타임스탬프입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

> [!NOTE]
> `DeviceClient` 속성으로 전송된 메시지만 처리할 수 있습니다.
> 
> 

### <a name="consumer-groups"></a>소비자 그룹
각 Stream Analytics IoT Hub 입력마다 고유한 소비자 그룹이 있도록 구성해야 합니다. 작업에 셀프 조인이 포함되거나 입력이 여러 개인 경우 둘 이상의 판독기 다운스트림으로 일부 입력을 읽을 수 있습니다. 이러한 상황은 단일 소비자 그룹의 판독기 수에 영향을 줍니다. 파티션당 소비자 그룹마다 5개 판독기의 Azure IoT Hub 한도 초과를 방지하려면 각 Stream Analytics 작업에 대한 소비자 그룹을 지정하는 것이 좋습니다.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>데이터 스트림 입력으로 IoT Hub 구성
다음 표에서는 입력으로 IoT Hub를 구성할 때 Azure Portal의 **새 입력** 블레이드에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** |이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다.|
| **IoT Hub** |입력으로 사용할 IoT Hub의 이름입니다. |
| **끝점** |IoT Hub에 대 한 끝점입니다.|
| **공유 액세스 정책 이름** |IoT Hub에 대한 액세스를 제공하는 공유 액세스 정책입니다. 각 공유 액세스 정책에는 이름, 사용자가 설정한 사용 권한 및 액세스 키가 있습니다. |
| **공유 액세스 정책 키** |IoT Hub에 대한 액세스를 인증하는 데 사용되는 공유 액세스 키입니다. |
| **소비자 그룹**(선택 사항) |IoT Hub에서 데이터를 수집하는 소비자 그룹입니다. 소비자 그룹이 지정되지 않으면 Stream Analytics 작업에서 기본 소비자 그룹을 사용합니다. 각 Stream Analytics 작업마다 서로 다른 소비자 그룹을 사용하는 것이 좋습니다. |
| **이벤트 직렬화 형식** |들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다. |
| **Encoding** |현재 유일하게 지원되는 인코딩 형식은 UTF-8입니다. |
| **압축**(선택 사항) | 들어오는 데이터 스트림의 압축 유형(None, GZip 또는 Deflate)입니다. |

데이터를 IoT Hub에서 가져온 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **EventProcessedUtcTime** |이벤트가 처리되는 날짜 및 시간입니다. |
| **EventEnqueuedUtcTime** |IoT Hub에서 이벤트를 받은 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |
| **IoTHub.MessageId** | IoT Hub에서 양방향 통신을 상호 연결하는 데 사용되는 ID입니다. |
| **IoTHub.CorrelationId** |IoT Hub에서 메시지 응답 및 피드백에 사용되는 ID입니다. |
| **IoTHub.ConnectionDeviceId** |이 메시지를 보내는 데 사용된 인증 ID입니다. 이 값은 IoT Hub에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| **IoTHub.ConnectionDeviceGenerationId** |이 메시지를 보내는 데 사용된 인증된 장치의 생성 ID입니다. 이 값은 IoT Hub에서 서비스 바운드 메시지에 자동 삽입됩니다. |
| **IoTHub.EnqueuedTime** |IoT Hub에서 메시지가 수신된 시간입니다. |
| **IoTHub.StreamId** |보낸 사람 장치에 의해 추가된 사용자 지정 이벤트 속성입니다. |


## <a name="create-data-stream-input-from-blob-storage"></a>Blob Storage에서 데이터 스트림 입력 만들기
클라우드에 저장할 구조화되지 않은 대량 데이터가 있는 시나리오에서 Azure Blob Storage는 비용 효과적이고 확장성 있는 솔루션을 제공합니다. 일반적으로 Blob Storage의 데이터는 미사용 데이터로 간주됩니다. 그러나 Stream Analytics에서 데이터 스트림으로 처리할 수 있습니다. Stream Analytics에서 Blob Storage 입력에 대한 일반적인 시나리오는 로그 처리입니다. 이 시나리오에서는 시스템에서 원격 분석 데이터가 캡처되고 유의미한 데이터를 추출하기 위해 구문 분석 및 처리되어야 합니다.

Stream Analytics에서 Blob Storage 이벤트의 기본 타임 스탬프는 Blob이 마지막으로 수정된 타임스탬프로 `BlobLastModifiedUtcTime`입니다. 이벤트 페이로드에서 타임스탬프를 사용하여 스트림으로 데이터를 처리하려면 [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx) 키워드를 사용해야 합니다.

CSV 형식의 입력은 데이터 집합에 대한 필드를 정의하는 헤더 행이 *필요*합니다. 또한 모든 헤더 행 필드는 고유해야 합니다.

> [!NOTE]
> Stream Analytics에서는 기존 blob 파일에 콘텐츠를 추가할 수 없습니다. Stream Analytics에서는 각 파일을 한 번만 보며 작업에서 데이터를 읽은 후 파일에서 발생한 모든 변경 내용은 처리되지 않습니다. Blob 파일에 대한 모든 데이터를 한 번에 업로드한 후 다른 새 Blob 파일에 최신 이벤트를 추가하는 것이 좋습니다.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>데이터 스트림 입력으로 Blob Storage 구성

다음 표에서는 입력으로 Blob Storage를 구성할 때 Azure Portal의 **새 입력** 블레이드에서 각 속성을 설명합니다.

| 자산 | 설명 |
| --- | --- |
| **입력 별칭** | 이 입력을 참조하도록 작업 쿼리에서 사용할 친숙한 이름입니다. |
| **Storage 계정** | Blob 파일이 위치한 저장소 계정의 이름입니다. |
| **Storage 계정 키** | 저장소 계정과 연결된 비밀 키입니다. |
| **컨테이너** | Blob 입력에 대한 컨테이너입니다. 컨테이너는 Microsoft Azure Blob service에 저장된 Blob에 대한 논리적 그룹화를 제공합니다. Azure Blob Storage 서비스에 Blob을 업로드하는 경우 해당 Blob에 대한 컨테이너를 지정해야 합니다. |
| **경로 패턴**(선택 사항) | 지정된 컨테이너 내에서 Blob을 찾는 데 사용되는 파일 경로입니다. 경로 내에서 세 변수(`{date}`, `{time}`, `{partition}`)의 인스턴스 중 하나 이상을 지정할 수도 있습니다.<br/><br/>예 1: `cluster1/logs/{date}/{time}/{partition}`<br/><br/>예 2: `cluster1/logs/{date}`<br/><br/>`*` 문자는 경로 접두사에 대해 허용된 값이 아닙니다. 유효한 <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">Azure Blob 문자</a>만 허용됩니다. |
| **날짜 형식**(선택 사항) | 경로에서 날짜 변수를 사용하는 경우 파일이 구성된 날짜 형식입니다. 예제: `YYYY/MM/DD` |
| **시간 형식**(선택 사항) |  경로에서 시간 변수를 사용하는 경우 파일이 구성된 시간 형식입니다. 현재 지원되는 유일한 값은 `HH`입니다. |
| **이벤트 직렬화 형식** | 들어오는 데이터 스트림의 serialization 형식(JSON, CSV 또는 Avro)입니다. |
| **Encoding** | CSV 및 JSON의 경우 UTF-8이 현재 지원되는 유일한 인코딩 형식입니다. |
| **압축**(선택 사항) | 들어오는 데이터 스트림의 압축 유형(None, GZip 또는 Deflate)입니다. |

데이터를 Blob Storage 원본에서 가져온 경우 Stream Analytics 쿼리에서 다음 메타데이터 필드에 액세스할 수 있습니다.

| 자산 | 설명 |
| --- | --- |
| **BlobName** |이 이벤트가 발생한 입력 Blob의 이름입니다. |
| **EventProcessedUtcTime** |이벤트가 Stream Analytics으로 처리되는 날짜 및 시간입니다. |
| **BlobLastModifiedUtcTime** |Blob이 마지막으로 수정된 날짜 및 시간입니다. |
| **PartitionId** |입력 어댑터의 0부터 시작하는 파티션 ID입니다. |

예를 들어 이러한 필드를 사용하여 다음 예제와 같은 쿼리를 작성할 수 있습니다.

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>도움말 보기
추가 지원이 필요한 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Stream Analytics 작업을 위한 Azure의 데이터 연결 옵션에 대해 알아보았습니다. Stream Analytics에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
