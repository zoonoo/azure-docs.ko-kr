---
title: Azure IoT Hub 메시지 라우팅 이해하기 | Microsoft Docs
description: 개발자 가이드 - 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 보내는 방법입니다. 원격 분석 데이터 및 비원격 분석 데이터를 전송하는 방법에 대한 정보가 포함됩니다.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370460"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub 메시지 라우팅을 사용하여 다양한 엔드포인트에 디바이스-클라우드 메시지 보내기

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

메시지 라우팅을 사용하면 확장 가능하고 신뢰할 수 있는 자동화된 방식으로 디바이스에서 클라우드 서비스로 메시지를 보낼 수 있습니다. 메시지 라우팅은 다음 용도로 사용할 수 있습니다. 

* **디바이스 원격 분석 메시지는 물론 이벤트** 즉, 디바이스 수명 주기 이벤트 및 디바이스 쌍 변경 이벤트를 기본 제공 엔드포인트 및 사용자 지정 엔드포인트로 보냅니다. [엔드포인트 라우팅](#routing-endpoints)에 대해 알아보세요.

* 다양한 쿼리를 적용하여 **다양한 엔드포인트로 데이터를 라우팅하기 전에 데이터를 필터링**합니다. 메시지 라우팅을 사용하면 메시지 속성 및 메시지 본문뿐만 아니라 디바이스 쌍 태그 및 디바이스 쌍 속성에 대해서도 쿼리할 수 있습니다. [메시지 라우팅에 쿼리](iot-hub-devguide-routing-query-syntax.md)를 사용하는 방법에 대해 자세히 알아보세요.

IoT Hub는 메시지 라우팅을 작동하기 위해 이러한 서비스 엔드포인트에 대한 쓰기 액세스가 필요합니다. Azure Portal을 통해 엔드포인트를 구성하는 경우 필요한 권한이 추가됩니다. 예상된 처리량을 지원하도록 서비스를 구성해야 합니다. 예를 들어 이벤트 허브를 사용자 지정 끝점으로 사용하는 경우 IoT Hub 메시지 라우팅을 통해 보낼 이벤트의 수신을 처리할 수 있도록 해당 이벤트 허브의 **처리량 단위를** 구성해야 합니다. 마찬가지로 서비스 버스 큐를 끝점으로 사용하는 경우 소비자가 송신할 때까지 큐가 수집된 모든 데이터를 보유할 수 있도록 **최대 크기를** 구성해야 합니다. IoT 솔루션을 처음 구성하는 경우 추가 엔드포인트를 모니터링하고 실제 부하에 맞게 조정해야 할 수 있습니다.

IoT Hub는 프로토콜 전체에서의 상호 운용성을 위해 모든 디바이스-클라우드 메시징에 대해 [일반적인 형식](iot-hub-devguide-messages-construct.md)을 정의합니다. 메시지가 동일한 엔드포인트를 가리키는 여러 경로와 일치하는 경우 IoT Hub는 메시지를 해당 엔드포인트에 한 번만 전달합니다. 따라서 Service Bus 큐 또는 항목에 대해 중복 제거를 구성할 필요가 없습니다. 분할된 큐에서 파티션 선호도는 메시지 순서를 보장합니다. [메시지 라우팅을 구성](tutorial-routing.md)하는 방법을 알아보려면 이 자습서를 사용하세요.

## <a name="routing-endpoints"></a>라우팅 엔드포인트

IoT Hub에는 Event Hubs와 호환되는 기본 제공 엔드포인트(**메시지/이벤트**)가 있습니다. 구독의 다른 서비스를 IoT Hub에 연결하여 메시지를 라우팅할 [사용자 지정 엔드포인트](iot-hub-devguide-endpoints.md#custom-endpoints)를 만들 수 있습니다. 

각 메시지는 라우팅 쿼리가 일치하는 모든 끝점으로 라우팅됩니다. 즉, 메시지를 여러 끝점으로 라우팅할 수 있습니다.

IoT Hub는 현재 다음과 같은 서비스를 사용자 지정 엔드포인트로 지원합니다.

### <a name="built-in-endpoint"></a>기본 제공 엔드포인트

표준 [Event Hubs 통합 및 SDK](iot-hub-devguide-messages-read-builtin.md)를 사용하여 기본 제공 엔드포인트(**메시지/이벤트**)에서 디바이스-클라우드 메시지를 수신할 수 있습니다. 배선이 만들어지면 해당 끝점으로 경로가 만들어지지 않는 한 데이터가 기본 제공 끝점으로 흐르는 것을 중지합니다.

### <a name="azure-storage"></a>Azure Storage

IoT Hub는 Azure [Blob 저장소](../storage/blobs/storage-blobs-introduction.md) 및 [Azure 데이터 레이크 저장소 Gen2(ADLS](../storage/blobs/data-lake-storage-introduction.md) Gen2) 계정으로 메시지를 라우팅할 수 있는 두 가지 스토리지 서비스가 있습니다. Azure Data Lake 저장소 계정은 [계층적 네임스페이스-blob](../storage/blobs/data-lake-storage-namespace.md)저장소 위에 빌드된 저장소 계정입니다. 이 두 가지 모두 저장소에 Blob을 사용합니다.

IoT Hub는 [아파치 아브로](https://avro.apache.org/) 형식과 JSON 형식으로 Azure 저장소에 데이터를 쓰는 것을 지원합니다. 기본값은 AVRO입니다. 인코딩 형식은 Blob 저장소 끝점이 구성된 경우에만 설정할 수 있습니다. 기존 끝점에 대해 형식을 편집할 수 없습니다. JSON 인코딩을 사용하는 경우 메시지 [시스템 속성에서](iot-hub-devguide-routing-query-syntax.md#system-properties)contentType을 **응용 프로그램/json으로** 설정하고 contentEncoding을 **UTF-8로** 설정해야 합니다. 이 두 값은 대/소문자를 구분하지 않습니다. 콘텐츠 인코딩이 설정되지 않은 경우 IoT Hub는 기본 64 인코딩 된 형식으로 메시지를 작성합니다. IoT Hub 만들기 또는 REST API 업데이트, 특히 [라우팅저장소컨테이너속성,](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)Azure 포털, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)또는 [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0)을 사용하여 인코딩 형식을 선택할 수 있습니다. 다음 다이어그램에서는 Azure 포털에서 인코딩 형식을 선택하는 방법을 보여 주며 있습니다.

![Blob 스토리지 엔드포인트 인코딩](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub는 일괄 처리가 특정 크기에 도달하거나 일정 시간이 경과할 때마다 메시지를 일괄 처리하고 데이터를 저장소에 기록합니다. IoT Hub는 기본적으로 다음 파일 명명 규칙을 따릅니다. 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

원하는 파일 명명 규칙을 사용할 수 있지만, 나열된 토큰은 모두 사용해야 합니다. 쓸 데이터가 없으면 IoT Hub가 빈 Blob을 작성합니다.

모든 Blob 또는 파일을 파티션을 가정하지 않고 읽을 수 있도록 Blob 또는 파일을 나열한 다음 반복하는 것이 좋습니다. 파티션 범위는 [Microsoft 시작 장애 조치(failover)](iot-hub-ha-dr.md#microsoft-initiated-failover) 또는 IoT Hub [수동 장애 조치(failover)](iot-hub-ha-dr.md#manual-failover) 중에 변경할 수 있습니다. [Blob 목록 API를](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 사용하여 파일 목록에 대한 Blob 목록을 열거하거나 [ADLS Gen2 API 를 나열할](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) 수 있습니다. 다음 샘플을 안내로 참조하십시오.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

> [!NOTE]
> 저장소 계정에 IoT Hub의 연결을 제한하는 방화벽 구성이 있는 경우 [Microsoft 신뢰할 수 있는 타사 예외를](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) 사용하는 것이 좋습니다(관리되는 서비스 ID가 있는 IoT 허브의 일부 지역에서 사용 가능).

Azure Data Lake Gen2 호환 저장소 계정을 만들려면 다음 이미지와 같이 **고급** 탭의 *계층 적 네임스페이스* 필드에서 새 V2 저장소 계정을 만들고 *사용하도록 선택합니다.*

![Azure 날짜 호수 Gen2 저장소 선택](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus 큐 및 Service Bus 토픽

IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색**이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 엔드포인트가 **연결할 수 없음**으로 표시됩니다.

> [!NOTE]
> 서비스 버스 리소스에 IoT Hub의 연결을 제한하는 방화벽 구성이 있는 경우 [Microsoft 신뢰할 수 있는 타사 예외(관리되는](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) 서비스 ID가 있는 IoT 허브의 일부 지역에서 사용 가능)를 사용하는 것이 좋습니다.


### <a name="event-hubs"></a>Event Hubs

기본 제공 Event Hubs 호환 엔드포인트 외에, Event Hubs 유형의 사용자 지정 엔드포인트로 데이터를 라우팅할 수도 있습니다. 

> [!NOTE]
> 이벤트 허브 리소스에 IoT Hub의 연결을 제한하는 방화벽 구성이 있는 경우 [Microsoft 신뢰할 수 있는 타사 예외(관리되는](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) 서비스 ID가 있는 IoT 허브의 일부 지역에서 사용 가능)를 사용하는 것이 좋습니다.


## <a name="reading-data-that-has-been-routed"></a>라우팅된 데이터 읽기

이 [자습서](tutorial-routing.md)에 따라 경로를 구성할 수 있습니다.

엔드포인트에서 메시지를 읽는 방법을 알아보려면 다음 자습서를 사용하세요.

* [기본 제공 엔드포인트](quickstart-send-telemetry-node.md)에서 읽기

* [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)에서 읽기

* 이벤트 [허브에서](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) 읽기

* 서비스 [버스 대기열에서](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) 읽기

* [Service Bus 토픽](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)에서 읽기


## <a name="fallback-route"></a>대체(fallback) 경로

대체(fallback) 경로는 기존 경로에서 쿼리 조건을 충족하지 않는 모든 메시지를 [Event Hubs](/azure/event-hubs/)와 호환되는 기본 제공 Event Hubs(**messages/events**)로 보냅니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 경로가 만들어지면 해당 끝점에 대한 경로가 만들어지지 않는 한 데이터가 기본 제공 끝점으로 흐르는 것을 중지합니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로를 사용할 수 있는 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정해야 합니다.

Azure 포털 >메시지 라우팅 블레이드에서 대체 경로를 활성화/비활성화할 수 있습니다. [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties)에 Azure Resource Manager를 사용하여 대체(fallback) 경로에 대해 사용자 지정 엔드포인트를 사용할 수도 있습니다.

## <a name="non-telemetry-events"></a>비-원격 분석 이벤트

메시지 라우팅은 장치 원격 분석 외에도 장치 쌍 변경 이벤트, 장치 수명 주기 이벤트 및 디지털 쌍 변경 이벤트(공개 미리 보기)를 보낼 수 있습니다. 예를 들어, **디바이스 쌍 변경 이벤트**로 설정된 데이터 원본으로 경로가 생성되면, IoT Hub는 디바이스 쌍의 변경 사항을 포함하는 엔드포인트로 메시지를 전송합니다. 마찬가지로 데이터 원본을 **장치 수명 주기 이벤트로**설정한 경로로 만든 경우 IoT Hub는 장치가 삭제되었는지 또는 만들었는지 여부를 나타내는 메시지를 보냅니다. 마지막으로, 개발자는 [IoT 플러그 앤 플레이 공개 미리 보기](../iot-pnp/overview-iot-plug-and-play.md)의 일부로 디지털 쌍 **변경 IoT Hub 이벤트** 로 설정된 데이터 원본이 있는 경로를 만들고 디지털 쌍 [속성](../iot-pnp/iot-plug-and-play-glossary.md)이 설정 되거나 변경 될 때마다 디지털 쌍으로 메시지를 보낼 수 있습니다. [digital twin](../iot-pnp/iot-plug-and-play-glossary.md)이 대체 되거나 기본 장치 쌍에 대해 변경 이벤트가 발생하는 경우

[또한 IoT Hub는 Azure Event Grid와 통합하여](iot-hub-event-grid.md) 장치 이벤트를 게시하여 이러한 이벤트를 기반으로 워크플로의 실시간 통합 및 자동화를 지원합니다. [메시지 라우팅과 Event Grid 간의 주요 차이점](iot-hub-event-grid-routing-comparison.md)을 확인하고 내 시나리오에 무엇이 가장 적합한지 알아보세요.

## <a name="testing-routes"></a>경로 테스트

새 경로를 만들거나 기존 경로를 편집하는 경우에는 샘플 메시지로 경로 쿼리를 테스트해야 합니다. 개별 경로를 테스트하거나 모든 경로를 한 번에 테스트할 수 있으며 테스트 중에는 엔드포인트에 메시지가 라우팅되지 않습니다. Azure 포털, Azure 리소스 관리자, Azure PowerShell 및 Azure CLI를 테스트에 사용할 수 있습니다. 결과는 샘플 메시지가 쿼리와 일치하는지, 메시지가 쿼리와 일치하지 않았는지 또는 샘플 메시지 또는 쿼리 구문이 올바르지 않아 테스트를 실행할 수 없는지 식별하는 데 도움이 됩니다. 자세히 알아보려면 [경로 테스트](/rest/api/iothub/iothubresource/testroute) 및 [모든 경로 테스트](/rest/api/iothub/iothubresource/testallroutes)를 참조하세요.

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>주문 보증 은 적어도 한 번 배송

IoT Hub 메시지 라우팅은 최종 지점으로 메시지를 주문하고 적어도 한 번 배달하는 것을 보장합니다. 즉, 중복 된 메시지가 있을 수 있으며 원래 메시지 순서를 준수 하는 일련의 메시지를 다시 전송할 수 있습니다. 예를 들어 원래 메시지 순서가 [1,2,3,4]인 경우 [1,2,1,2,3,1,2,3,4]와 같은 메시지 시퀀스를 받을 수 있습니다. 주문 보증은 메시지를 받는 경우 [1]이 항상 [2,3,4]가 뒤따릅니다는 것입니다.

메시지 중복을 처리하는 경우 일반적으로 장치 또는 모듈인 원본 지점에서 메시지의 응용 프로그램 속성에 고유 식별자를 스탬프하는 것이 좋습니다. 메시지를 사용하는 서비스는 이 식별자를 사용하여 중복 메시지를 처리할 수 있습니다.

## <a name="latency"></a>대기 시간

기본 제공 엔드포인트를 사용하여 디바이스-클라우드 간 원격 분석 메시지를 라우팅할 경우 첫 번째 경로를 만든 후 엔드투엔드 대기 시간이 약간 증가합니다.

대부분의 경우 평균 대기 시간 증가는 500ms 미만입니다. **라우팅: 메시지/이벤트에 대한 메시지 대기 시간** 또는 **d2c.endpoints.latency.builtIn.events** IoT Hub 메트릭을 사용하여 대기 시간을 모니터링할 수 있습니다. 첫 번째 경로 이후에 다른 경로를 만들거나 삭제해도 엔드투엔드 대기 시간에는 영향을 주지 않습니다.

## <a name="monitoring-and-troubleshooting"></a>모니터링 및 문제 해결

IoT Hub는 라우팅 및 끝점과 관련된 몇 가지 메트릭을 제공하여 허브 및 보낸 메시지의 상태를 개관합니다. 여러 메트릭의 정보를 결합하여 문제의 근본 원인을 식별할 수 있습니다. 예를 들어 메트릭 **라우팅 사용: 원격 분석 메시지가 삭제되거나** **d2c.telemetry.egress.d.d.d가 라우트** 및 대체 경로의 쿼리와 일치하지 않을 때 삭제된 메시지 수를 식별합니다. [IoT Hub 메트릭](iot-hub-metrics.md)에는 기본적으로 IoT Hub에 대해 활성화된 모든 메트릭이 나열됩니다.

REST API [Endpoint Health 가져오기](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)를 사용 하 여 Endpoint의 [Health 상태](iot-hub-devguide-endpoints.md#custom-endpoints) 를 가져올 수 있습니다. 메시지 대기 시간 라우팅과 관련된 [IoT Hub 메트릭을](iot-hub-metrics.md) 사용하여 엔드포인트 상태 확인 또는 비정상일 때 오류를 식별하고 디버깅하는 것이 좋습니다. 예를 들어 끝점 유형 이벤트 허브의 경우 **d2c.endpoints.latency.eventHubs를**모니터링할 수 있습니다. IoT Hub가 결국 일관된 상태 상태를 설정하면 비정상 끝점의 상태가 정상으로 업데이트됩니다.

Azure Monitor [진단 설정에서](../iot-hub/iot-hub-monitor-resource-health.md) **경로** 진단 로그를 사용하여 IoT Hub에서 인식한 라우팅 쿼리 및 끝점 상태를 평가하는 동안 발생하는 오류를 추적할 수 있습니다(예: 끝점이 죽은 경우). 이러한 진단 로그는 사용자 지정 처리를 위해 Azure Monitor 로그, 이벤트 허브 또는 Azure 저장소로 전송될 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 메시지 경로를 만드는 방법에 대한 자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md)를 참조하세요.

* [디바이스-클라우드 메시지를 보내는 방법](quickstart-send-telemetry-node.md)

* 디바이스-클라우드 메시지를 보내는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.
