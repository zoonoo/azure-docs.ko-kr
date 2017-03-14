---
title: "Azure IoT Hub 메시징 이해 | Microsoft 문서"
description: "개발자 가이드 - IoT Hub를 사용한 장치-클라우드 및 클라우드-장치 메시징 메시지 형식 및 지원되는 통신 프로토콜에 대한 정보가 포함됩니다."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2fb6f4d8330eb62e01af318277bc0e90aee039e0
ms.openlocfilehash: d3c4d1a91615957764552a985e0dfeba7c10a927
ms.lasthandoff: 03/01/2017


---
# <a name="send-and-receive-messages-with-iot-hub"></a>IoT Hub를 통해 메시지 보내고 받기
## <a name="overview"></a>개요
IoT Hub는 장치와의 통신을 위해 다음과 같은 메시징 기본 형식을 제공합니다.

* [장치-클라우드][lnk-d2c] 장치에서 백 엔드 앱 방향.
* [클라우드-장치][lnk-c2d] 백 엔드 앱(*서비스* 또는 *클라우드*)에서.

IoT Hub 메시징 기능의 핵심 속성은 메시지의 안정성 및 내구성입니다. 이 속성을 사용하여 장치 쪽에서 일시적인 연결 및 클라우드 쪽에서 이벤트 처리에 급증한 부하를 복원합니다. IoT Hub는 장치-클라우드 및 클라우드-장치 메시징 모두에 대해 *한 번 이상* 전달 보증을 구현합니다.

IoT Hub는 여러 [장치 지향 프로토콜][lnk-protocols](예: MQTT, AMQP 및 HTTP)을 지원합니다. 프로토콜을 통해 원활한 상호 운용성을 지원하기 위해 IoT Hub는 모든 장치 지향 프로토콜에서 지원하는 [일반적인 메시지 형식][lnk-message-format]을 정의합니다.

IoT Hub는 허브에서 수신한 장치-클라우드 메시지를 백 엔드 앱에서 읽을 수 있도록 기본 제공 [Event Hubs 호환 끝점][lnk-compatible-endpoint]을 공개합니다. 또한 구독의 다른 서비스를 허브에 연결하여 IoT Hub에 사용자 지정 끝점을 만들 수도 있습니다.

### <a name="when-to-use"></a>사용하는 경우
장치 앱에서 시계열 원격 분석 및 경고를 보내려면 장치-클라우드 메시지를 사용하고 장치 앱에 단방향 알림을 보내려면 클라우드-장치 메시지를 사용합니다.

reported 속성, 장치-클라우드 메시지 또는 파일 업로드 사용에 대해 궁금한 점이 있으면 [장치-클라우드 통신 지침][lnk-d2c-guidance]을 참조하세요.
desired 속성, 직접 메서드 또는 클라우드-장치 메시지 사용에 대해 궁금한 점이 있으면 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.

IoT Hub와 Event Hubs 서비스 비교는 [IoT Hub 및 Event Hubs의 비교][lnk-compare]를 참조하세요.

## <a name="device-to-cloud-messages"></a>장치-클라우드 메시지
장치 지향 끝점(**/devices/{deviceId}/messages/events**)을 통해 장치-클라우드 메시지를 보냅니다. 그런 다음 라우팅 규칙은 메시지를 IoT Hub의 서비스 지향 끝점 중 하나로 라우팅합니다. 라우팅 규칙은 허브를 통과하는 장치-클라우드 메시지의 속성을 사용하여 허브를 라우팅할 위치를 결정합니다. 기본적으로 메시지는 [Event Hubs][lnk-event-hubs]와 호환되는 기본 제공 서비스 연결 끝점(messages/events)으로 라우팅됩니다. 따라서 Standard [Event Hubs 통합 및 SDK][lnk-compatible-endpoint]를 사용하여 장치-클라우드 메시지를 받을 수 있습니다.

IoT Hub는 스트리밍 메시징 패턴을 사용하여 장치-클라우드 메시징을 구현합니다. IoT Hub의 장치 - 클라우드 메시지는 여러 독자가 읽을 수 있는 서비스를 통과하는 많은 양의 이벤트가 있다는 점에서 [Service Bus][lnk-servicebus] *메시지*보다는 [Event Hubs][lnk-event-hubs] *이벤트*에 가깝습니다.

이 구현에는 다음과 같은 의미가 있습니다.

* Event Hubs 이벤트와 마찬가지로 장치-클라우드 메시지는 영구적이며 IoT Hub의 기본 **messages/events** 끝점에 최대&7;일 동안 보관됩니다.
* Event Hubs 이벤트와 마찬가지로 장치-클라우드 메시지는 최대 256KB까지 가능하며 보내기를 최적화하기 위해 일괄 처리로 그룹화할 수 있습니다. 배치는 최대 256KB가 될 수 있습니다.

그러나 IoT Hub 장치-클라우드 메시징 및 이벤트 허브 간에 몇 가지 중요한 차이가 있습니다.

* [IoT Hub에 대한 액세스 제어][lnk-devguide-security] 섹션에서 설명한 것처럼 IoT Hub는 장치 단위 인증 및 액세스 제어를 허용합니다.
* IoT Hub를 사용하면 최대 10개의 사용자 지정 끝점을 만들 수 있습니다. 메시지는 IoT Hub에 구성된 경로에 따라 끝점으로 전달됩니다.
* Event Hubs가 네임스페이스 당 5000개의 AMQP 연결로 제한되는 반면 IoT Hub는 동시에 연결된 장치를 수백만 개까지 사용할 수 있습니다([할당량 및 제한][lnk-quotas] 참조).
* IoT Hub는 **PartitionKey**를 사용하는 임의의 분할을 허용하지 않습니다. 장치-클라우드 메시지는 원래 **deviceId**와 관련하여 분할됩니다.
* IoT Hub를 확장하는 것은 이벤트 허브의 크기를 조정하는 것과 약간 다릅니다. 자세한 내용은 [IoT Hub 크기 조정][lnk-guidance-scale]을 참조하십시오.

장치-클라우드 메시징을 사용하는 방법에 대한 세부 정보는 [Azure IoT SDK][lnk-sdks]를 참조하세요.

메시지 라우팅을 설정하는 방법에 대한 자세한 내용은 [라우팅 규칙](#routing-rules)을 참조하세요.

> [!NOTE]
> HTTP를 사용하여 장치-클라우드 메시지를 보낼 때 속성 이름과 값에는 ASCII 영숫자 문자와 ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``만 포함할 수 있습니다.
> 
> 

### <a name="non-telemetry-traffic"></a>비-원격 분석 트래픽
때때로 원격 분석 데이터 요소 외에도 장치는 응용 프로그램 비즈니스 논리 계층에서 별도로 실행하고 처리해야 하는 메시지와 요청을 보냅니다. 예를 들어 백 엔드에서 특정 작업을 트리거해야 하는 중요한 경고가 있습니다. 이러한 유형의 메시지를 처리 전용 끝점에 보내도록 라우팅 규칙을 쉽게 작성할 수 있습니다.

이런 종류의 메시지를 처리하는 최선의 방법에 대한 정보는 [자습서: IoT Hub 장치-클라우드 메시지를 처리하는 방법][lnk-d2c-tutorial] 자습서를 참조하세요.

### <a name="routing-rules"></a>라우팅 규칙

IoT Hub를 사용하면 메시지 속성에 기반하여 메시지를 IoT Hub 끝점으로 라우팅할 수 있습니다. 라우팅 규칙을 사용하면 메시지를 처리하거나 추가 코드를 작성하기 위해 추가 서비스를 수행하지 않고서 필요한 곳에 메시지를 보낼 수 있습니다. 구성하는 각 라우팅 규칙에는 다음과 같은 속성이 있습니다.

* **이름** - 규칙을 식별하는 고유한 이름입니다.
* **원본** - 처리할 데이터 스트림의 원본입니다. 예를 들어 장치 원격 분석이 있습니다.
* **조건** - 메시지 속성에 대해 실행되고 끝점과 일치하는지 여부를 확인하는 데 사용되는 라우팅 규칙에 대한 쿼리 식입니다. 경로 조건 구성에 대한 자세한 내용은 [참조 - 장치 쌍 및 작업에 대한 쿼리 언어][lnk-devguide-query-language]를 참조하세요.
* **끝점** - IoT Hub에서 조건과 일치하는 메시지를 보내는 끝점의 이름입니다. 끝점은 IoT Hub와 동일한 지역에 있어야 합니다. 그렇지 않으면 지역 간 쓰기 요금이 부과될 수 있습니다.

하나의 메시지가 여러 라우팅 규칙의 조건과 일치할 수 있습니다.이 경우 IoT Hub는 일치된 각 규칙과 연결된 끝점으로 메시지를 배달합니다. 또한 IoT Hub는 메시지 배달을 자동으로 중복 제거하므로 메시지가 모두 동일한 대상을 가진 여러 규칙과 일치하면 해당 대상에 한 번만 기록됩니다.

IoT Hub에 사용자 지정 끝점을 만드는 방법에 대한 자세한 내용은 [IoT Hub 끝점][lnk-devguide-endpoints]을 참조하세요.

### <a name="built-in-endpoint-messagesevents"></a>기본 제공 끝점: messages/events

IoT Hub는 다음 속성을 노출하여 기본 제공 Event Hub와 호환되는 메시징 끝점 **messages/events**를 제어할 수 있게 합니다.

* **분할 개수**. 이 속성은 생성 시 설정하여 장치-클라우드 이벤트 수집에 대한 [파티션][lnk-event-hub-partitions] 수를 정의합니다.
* **보존 시간**. 이 속성은 IoT Hub에서 메시지를 보존할 일 수를 지정합니다. 기본값은&1;일이지만&7;일로 늘릴 수 있습니다.

또한 IoT Hub를 사용하면 기본 제공 장치-클라우드 수신 끝점에서 소비자 그룹을 관리할 수 있습니다.

기본적으로 메시지 라우팅 규칙과 명시적으로 일치하지 않는 모든 메시지는 기본 제공 끝점에 기록됩니다. 이 대체 경로를 비활성화하면 메시지 라우팅 규칙과 명시적으로 일치하지 않는 메시지는 삭제됩니다.

[IoT Hub 리소스 공급자 REST API][lnk-resource-provider-apis]를 통해 프로그래밍 방식으로 또는 [Azure Portal][lnk-management-portal]을 사용하여 보존 시간을 수정할 수 있습니다.

### <a name="anti-spoofing-properties"></a>스푸핑 방지 속성
장치-클라우드 메시지에서 스푸핑된 장치를 피하려면 IoT Hub는 다음 속성을 사용하여 모든 메시지를 보여줍니다.

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

처음 두 가지는 [장치 ID 속성][lnk-device-properties]에 따라 원래 장치의 **deviceId** 및 **generationId**를 포함합니다.

**ConnectionAuthMethod** 속성은 다음 속성을 가진 JSON으로 직렬화된 개체를 포함합니다.

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>클라우드-장치 메시지
서비스 지향 끝점(**/messages/devicebound**)을 통해 클라우드-장치 메시지를 보냅니다. 장치는 장치별 끝점(**/devices/{deviceId}/messages/devicebound**)을 통해 메시지를 수신합니다.

각 클라우드-장치 메시지는 **to** 속성을 **/devices/{deviceId}/messages/devicebound**로 설정하여 단일 장치를 대상화합니다.

> [!IMPORTANT]
> 각 장치 큐는 클라우드-장치 메시지를 최대 50개까지 보유합니다. 동일한 장치에 더 많은 메시지를 보내려고 하면 오류가 발생합니다.
> 
> [!NOTE]
> 클라우드-장치 메시지를 보낼 때 속성 이름과 값에는 ASCII 영숫자 문자와 ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``만 포함할 수 있습니다.
> 
> 

### <a name="message-lifecycle"></a>메시지 수명 주기
메시지 전달을 한 번 이상 보장하기 위해 IoT Hub는 장치 별 큐에 클라우드-장치 메시지를 유지합니다. IoT Hub가 큐에서 메시지를 제거하기 위해 장치가 *완료* 를 명시적으로 인정해야 합니다. 연결 및 장치 오류로부터 복구를 보장합니다.

다음 다이어그램은 클라우드-장치 메시지에 대한 수명 주기 상태 Graph를 보여줍니다.

![클라우드-장치 메시지 수명 주기][img-lifecycle]

서비스가 보내는 메시지는 *큐에 넣음*상태로 간주됩니다. 장치가 메시지를 *수신*하려고 하면 IoT Hub는 메시지를 *잠그고* 상태를 **숨김**으로 설정하여 동일한 장치에 있는 다른 스레드가 다른 메시지 수신을 시작하도록 허용합니다. 장치 스레드가 메시지의 처리를 완료하면 IoT Hub에 메시지를 *완료* 했다고 알립니다.

또한 장치는 다음을 수행할 수 있습니다.

* 메시지 *거부*. 이 경우 IoT Hub는 메시지를 **Deadlettered** 상태로 설정합니다. 참고: MQTT로 연결하는 장치는 클라우드-장치 메시지를 거부할 수 없습니다.
* 메시지 *중단*. 이 경우 IoT Hub는 상태를 **큐에 넣음**으로 설정하여 메시지를 큐에 다시 넣습니다.

스레드는 IoT Hub에 알리지 않고 메시지를 처리하는 데 실패할 수 있습니다. 이 경우 *표시 또는 잠금 시간 초과* 후에 메시지는 **숨김** 상태에서 **큐에 넣음** 상태로 자동 전환됩니다. 이 시간 제한의 기본값은&1;분입니다.

메시지는 IoT Hub의 **최대 배달 횟수** 속성에 지정된 최대 지정된 횟수만큼 **큐에 넣음** 및 **숨김** 상태 간에 전환될 수 있습니다. 해당 전환 횟수 후에 IoT Hub는 메시지의 상태를 **Deadlettered**로 설정합니다. 마찬가지로 IoT Hub는 만료 시간 후에 메시지의 상태를 **Deadlettered**로 설정합니다. 관련 설명은 [TTL(Time to Live)][lnk-ttl]을 참조하세요.

클라우드-장치 메시지에 대한 자습서는 [자습서: IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d-tutorial]을 참조하세요. 다양한 Azure IoT SDK가 클라우드-장치 기능을 어떻게 노출하는지에 대한 참조 항목은 [Azure IoT SDK][lnk-sdks]를 참조하세요.

> [!NOTE]
> 일반적으로 클라우드-장치 메시지는 메시지의 손실이 응용 프로그램 논리에 영향을 줄 때마다 완료합니다. 예를 들어, 메시지 콘텐츠가 로컬 저장소에 성공적으로 유지되거나 작업이 성공적으로 실행되었을 수 있습니다. 또한 메시지가 임시 정보를 전달하고 있으므로 손실되더라도 응용 프로그램의 기능에 영향을 주지 않을 수도 있습니다. 경우에 따라 장기간 실행되는 작업의 경우 로컬 저장소에 작업 설명을 보관한 후 클라우드-장치 메시지를 완료할 수 있습니다. 그런 다음 작업이 진행되는 다양한 단계에서 하나 이상의 장치-클라우드 메시지를 사용하여 솔루션 백 엔드에 알림을 제공할 수 있습니다.
> 
> 

### <a name="message-expiration-time-to-live"></a>메시지 만료(TTL(Time To Live))
모든 클라우드-장치 메시지에는 만료 시간이 있습니다. 이 시간은 서비스에 의해 설정되거나(**ExpiryTimeUtc** 속성) IoT Hub 속성처럼 기본 *TTL(Time To Live)* 을 사용하여 IoT Hub에 의해 설정됩니다. [클라우드-장치 구성 옵션][lnk-c2d-configuration]을 참조하세요.

> [!NOTE]
> 메시지 만료를 활용하여 연결되지 않은 장치에 메시지 보내기를 방지하는 일반적인 방법은 TTL(Time to Live) 값을 짧게 설정하는 것입니다. 이 방법은 장치 연결 상태를 유지 관리하는 것과 동일한 결과를 내면서 더 효율적입니다. 메시지 승인을 요청하면 IoT Hub는 메시지를 수신할 수 있는 장치가 무엇인지 온라인 상태이거나 장애가 발생한 장치가 무엇인지를 알려줍니다.
> 
> 

### <a name="message-feedback"></a>메시지 피드백
클라우드-장치 메시지를 보낼 때 서비스는 해당 메시지의 최종 상태에 대한 메시지 단위 피드백을 전달하도록 요청할 수 있습니다.

* **Ack** 속성을 **positive**로 설정하면 클라우드-장치 메시지가 **Completed** 상태가 되는 경우에만 IoT Hub가 피드백 메시지를 생성합니다.
* **Ack** 속성을 **negative**로 설정하면 클라우드-장치 메시지가 **Deadlettered** 상태가 되는 경우에만 IoT Hub가 피드백 메시지를 생성합니다.
* **Ack** 속성을 **full**로 설정하면 IoT Hub가 두 가지 경우에 모두 피드백 메시지를 생성합니다.

> [!NOTE]
> **Ack**가 **full**이고 피드백 메시지를 수신하지 못한 경우 피드백 메시지가 만료되었음을 의미합니다. 서비스는 원본 메시지에서 발생한 상황을 알지 못합니다. 실제로 서비스는 만료되기 전에 피드백을 처리할 수 있는지 확인해야 합니다. 오류가 발생한 경우 서비스를 다시 가동하는 데 충분한 시간을 허용하기 위해 최대 만료 시간이&2;일로 지정되어 있습니다.
> 
> 

[끝점][lnk-endpoints]에 설명된 대로 IoT Hub는 서비스 지향 끝점(**/messages/servicebound/feedback**)을 통해 피드백을 메시지로 전달합니다. 피드백 수신을 위한 의미 체계는 클라우드-장치 메시지의 경우와 같으며 동일한 [메시지 수명 주기][lnk-lifecycle]를 갖습니다. 가능한 경우 메시지 피드백은 다음 형식으로 단일 메시지에서 일괄 처리됩니다.

| 속성 | 설명 |
| --- | --- |
| EnqueuedTime |메시지를 만든 시간을 나타내는 타임스탬프입니다. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

본문은 각각 다음과 같은 속성이 있는 레코드의 JSON으로 직렬화된 배열입니다.

| 속성 | 설명 |
| --- | --- |
| EnqueuedTimeUtc |메시지의 결과가 발생하는 경우를 나타내는 타임스탬프입니다. 예를 들어 완료된 장치 또는 만료된 메시지입니다. |
| OriginalMessageId |**MessageId** 입니다. |
| StatusCode |필수 정수입니다. IoT Hub에 의해 생성된 피드백 메시지에서 사용됩니다. <br/> 0 = 성공 <br/> 1 = 메시지 만료됨 <br/> 2 = 최대 배달 횟수 초과됨 <br/> &3; = 메시지 거부 |
| 설명 |**StatusCode**에 대한 문자열 값입니다. |
| deviceId |**DeviceId** 입니다. |
| DeviceGenerationId |**DeviceGenerationId** 입니다. |

> [!IMPORTANT]
> 서비스는 원본 메시지와 해당 피드백을 상호 연결하기 위해 클라우드-장치 메시지에 대한 **MessageId** 를 지정해야 합니다.
> 
> 

다음 예제에서는 피드백 메시지의 본문을 보여 줍니다.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>클라우드-장치 구성 옵션
각 IoT Hub는 클라우드-장치 메시징에 다음 구성 옵션을 노출합니다.

| 속성 | 설명 | 범위 및 기본값 |
| --- | --- | --- |
| defaultTtlAsIso8601 |클라우드-장치 메시지에 대한 기본 TTL |최대 2D(최소 1 분)까지 ISO_8601 간격입니다. 기본값은 1시간입니다. |
| maxDeliveryCount |클라우드-장치 장치 단위 큐에 대한 최대 전달 수입니다. |1에서 100까지 입니다. 기본값은 10입니다. |
| feedback.ttlAsIso8601 |서비스 바인딩 피드백 메시지에 대한 보존 기간입니다. |최대 2D(최소 1 분)까지 ISO_8601 간격입니다. 기본값은 1시간입니다. |
| feedback.maxDeliveryCount |피드백 큐에 대한 최대 전달 수입니다. |1에서 100까지 입니다. 기본값은 100입니다. |

자세한 내용은 [IoT Hub 만들기][lnk-portal]를 참조하세요.

## <a name="read-device-to-cloud-messages"></a>장치-클라우드 메시지 읽기
IoT Hub는 허브에서 수신한 장치-클라우드 메시지를 읽도록 백 엔드 서비스의 기본 제공 끝점인 **messages/events**를 공개합니다. 이 끝점은 Event Hub와 호환되기 때문에 Event Hubs 서비스가 메시지 읽기에 대해 지원하는 모든 메커니즘을 사용할 수 있습니다.

IoT Hub에서 사용자 지정 끝점을 만들 수도 있습니다. IoT Hub는 현재 사용자 지정 끝점으로 Event Hubs, Service Bus 큐 및 Service Bus 토픽을 지원합니다. 이러한 서비스에서 읽는 방법에 대한 자세한 내용은 [Event Hubs][lnk-getstarted-eh]에서 읽기, [Service Bus 큐][lnk-getstarted-queue]에서 읽기, [Service Bus 토픽][lnk-getstarted-topic]에서 읽기를 참조하세요.

### <a name="reading-from-the-built-in-endpoint"></a>기본 제공 끝점에서 읽기

[.NET용 Azure Service Bus SDK][lnk-servicebus-sdk] 또는 [Event Hubs - 이벤트 프로세서 호스트][lnk-eventprocessorhost]를 사용하는 경우 적절한 권한으로 모든 IoT Hub 연결 문자열을 사용할 수 있습니다. **이벤트/메시지** 를 이벤트 허브 이름으로 사용합니다.

IoT Hub를 인식하지 않는 SDK(또는 제품 통합)를 사용하는 경우 [Azure Portal][lnk-management-portal]의 IoT Hub 설정에서 Event Hub 호환 끝점 및 Event Hub 호환 이름을 검색해야 합니다.

1. IoT Hub 블레이드에서 **끝점**을 클릭합니다.
2. **기본 제공 끝점** 섹션에서 **이벤트**를 클릭합니다. 블레이드에서는 **Event Hub 호환 끝점**, **Event Hub 호환 이름**, **파티션**, **보존 시간** 및 **소비자 그룹** 값을 포함하고 있습니다.
   
    ![장치-클라우드 설정][img-eventhubcompatible]

> [!NOTE]
> IoT Hub SDK에는 **끝점** 블레이드에서 보여 주듯이 IoT Hub 끝점 이름으로 **messages/events**가 필요합니다.
>
>

> [!NOTE]
> 사용 중인 SDK에 **호스트 이름** 또는 **네임스페이스** 값이 필요한 경우 **Event Hub 호환 끝점**에서 스키마를 제거합니다. 예를 들어 Event Hubs 호환 끝점이 **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**인 경우 **호스트 이름**은 **iothub-ns-myiothub-1234.servicebus.windows.net**이 되고 **네임스페이스**는 **iothub-ns-myiothub-1234**가 됩니다.
> 
>

지정된 Event Hubs에 연결할 수 있는 **ServiceConnect** 권한이 있는 공유 액세스 정책을 사용할 수 있습니다.

이전의 정보를 사용하여 이벤트 허브 연결 문자열을 작성해야 하는 경우 다음과 같은 패턴을 사용합니다.

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

다음은 IoT Hub를 노출하는 이벤트 허브와 호환 가능한 끝점으로 사용할 수 있는 SDK 및 통합의 목록입니다.

* [Java 이벤트 허브 클라이언트](https://github.com/hdinsight/eventhubs-client)
* [Apache Storm spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). GitHub의 [spout 원본](https://github.com/apache/storm/tree/master/external/storm-eventhubs) 을 볼 수 있습니다.
* [Apache Spark 통합](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>참조 항목:
다음 참조 항목에서는 IoT Hub와 메시지 교환에 대한 자세한 정보를 제공합니다.

## <a name="message-format"></a>메시지 형식
IoT Hub 메시지는 다음을 구성합니다.

* *시스템 속성*집합. IoT Hub가 해석하거나 설정하는 속성입니다. 이 집합은 미리 결정됩니다.
* *응용 프로그램 속성*집합. 메시지 본문을 역직렬화할 필요 없이 응용 프로그램이 정의하고 액세스할 수 있는 문자열 속성 사전입니다. IoT Hub는 절대로 이러한 속성을 수정하지 않습니다.
* 불투명한 이진 본문.

다양한 프로토콜에서 메시지가 인코딩되는 방식에 대한 자세한 내용은 [Azure IoT SDK][lnk-sdks]를 참조하세요.

다음 테이블에는 IoT Hub 메시지의 시스템 속성 집합이 나열되어 있습니다.

| 속성 | 설명 |
| --- | --- |
| MessageId |사용자가 설정할 수 있는 메시지에 대한 식별자는 요청-회신 패턴에 사용됩니다. 형식: ASCII 7 비트 영숫자 문자 + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`의 대/소문자 구분 문자열(최대 128자 길이)입니다. |
| 시퀀스 번호 |숫자(장치 큐 별로 고유함)는 IoT Hub에서 각 클라우드-장치 메시지에 할당됩니다. |
| 받는 사람
 |[클라우드-장치][lnk-c2d] 메시지에 지정된 대상입니다. |
| ExpiryTimeUtc |메시지 만료 날짜 및 시간입니다. |
| EnqueuedTime |IoT Hub에서 메시지를 수신한 날짜 및 시간입니다. |
| CorrelationId |일반적으로 요청-응답 패턴으로 요청의 MessageId가 포함된 응답 메시지의 String 속성입니다. |
| UserId |메시지의 원본을 지정하는 데 사용되는 ID입니다. 메시지가 IoT Hub에서 생성되면 `{iot hub name}`로 설정합니다. |
| Ack |피드백 메시지 생성기입니다. 이 속성은 장치에서 소비하는 메시지의 결과로 피드백 메시지를 생성하는 IoT Hub를 요청하기 위해 클라우드-장치 메시지에서 사용됩니다. 가능한 값: **none**(기본값): 피드백 메시지가 생성되지 않습니다. **positive**: 메시지가 완료된 경우 피드백 메시지를 받습니다. **negative**: 장치에서 완료되지 않고 메시지가 만료(또는 최대 전달 횟수에 도달)되면 피드백 메시지를 받습니다. **full**: positive 및 negative 모두입니다. 자세한 내용은 [메시지 피드백][lnk-feedback]을 참조하세요. |
| ConnectionDeviceId |IoT Hub에서 장치-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 장치의 **deviceId** 를 포함합니다. |
| ConnectionDeviceGenerationId |IoT Hub에서 장치-클라우드 메시지에 설정하는 ID입니다. 메시지를 보낸 장치의 **generationId**([장치 ID 속성][lnk-device-properties]당)를 포함합니다. |
| ConnectionAuthMethod |IoT Hub에서 장치-클라우드 메시지에 설정하는 인증 방법입니다. 이 속성에는 메시지를 보내는 장치를 인증하는 데 사용되는 인증 방법에 대한 정보가 포함됩니다. 자세한 내용은 [장치-클라우드 스푸핑 방지][lnk-antispoofing]를 참조하세요. |

## <a name="message-size"></a>메시지 크기

IoT Hub는 실제 페이로드만을 고려하여 프로토콜 진단 방식으로 메시지 크기를 측정합니다. 크기(바이트 단위)는 다음의 합계로 계산됩니다.

* 본문 크기(바이트 단위) 더하기
* 모든 메시지 시스템 속성 값의 크기(바이트 단위) 더하기
* 모든 사용자 속성 이름 및 값의 크기(바이트 단위)

속성 이름과 값은 ASCII 문자로 제한되므로 문자열의 길이는 바이트 단위의 크기와 같습니다.

## <a name="communication-protocols"></a>통신 프로토콜
IoT Hub는 장치측 통신에 대해 [MQTT][lnk-mqtt], WebSocket을 통한 MQTT, [AMQP][lnk-amqp], WebSocket을 통한 AMQP 및 HTTP 프로토콜을 지원합니다. 다음 표에는 선택한 프로토콜에 대한 고급 권장 지침이 나와 있습니다.

| 프로토콜 | 이 프로토콜을 선택해야 하는 경우 |
| --- | --- |
| MQTT <br> WebSocket을 통한 MQTT |동일한 TLS 연결을 통해 여러 장치(각각 장치별 자격 증명 보유)에 연결할 필요가 없는 모든 장치에서 사용합니다. |
| AMQP <br> WebSocket을 통한 AMQP |장치 간에 연결 멀티플렉싱을 활용하기 위해 필드 및 클라우드 게이트웨이에서 사용합니다. |
| HTTP |다른 프로토콜을 지원할 수 없는 장치에 사용됩니다. |

장치 측 통신용 프로토콜을 선택할 때는 다음 사항을 고려해야 합니다.

* **클라우드-장치 패턴**. HTTP에는 서버 푸시를 구현하는 효율적인 방법이 없습니다. 이와 같이 HTTP를 사용하는 경우 장치는 클라우드-장치 메시지에 IoT Hub를 폴링합니다. 이 방법은 장치와 IoT Hub 모두에 비효율적입니다. 현재 HTTP 지침에 따르면 각 장치는 25분 이상 간격으로 메시지를 폴링해야 합니다. 반면에 MQTT 및 AMQP는 클라우드-장치 메시지를 받을 때 서버 푸시를 지원합니다. 따라서 IoT Hub의 메시지가 장치에 즉시 푸시될 수 있습니다. 전달 대기 시간이 중요한 경우 MQTT 또는 AMQP는 가장 사용하기 적합한 프로토콜입니다. 드물게 연결되는 장치의 경우 HTTP도 작동합니다.
* **현장 게이트웨이**. MQTT 및 HTTP를 사용하는 경우 동일한 TLS 연결을 사용하여 여러 장치(각각 장치별 자격 증명 보유)를 연결할 수 없습니다. 따라서 [필드 게이트웨이 시나리오][lnk-azure-gateway-guidance]의 경우 필드 게이트웨이에 연결된 각 장치에 대해 필드 게이트웨이와 IoT Hub 간에 하나의 TLS 연결이 필요하므로 이러한 프로토콜이 최적의 방법은 아닙니다.
* **낮은 리소스 장치**. MQTT 및 HTTP 라이브러리는 AMQP 라이브러리보다 적은 공간을 차지합니다. 이와 같이 장치에 리소스가 제한되어 있으면(예: 1MB RAM보다 작음) 이러한 프로토콜은 사용할 수 있는 유일한 프로토콜 구현일 수도 있습니다.
* **네트워크 통과**. 표준 AMQP 프로토콜은 포트 5671을 사용하는 반면 MQTT는 비 HTTP 프로토콜에 접근할 수 있는 네트워크에서 문제를 일으킬 수 있는 포트 8883을 수신 대기합니다. WebSocket을 통한 MQTT, WebSocket을 통한 AMQP 및 HTTP는 이 시나리오에서 사용할 수 있습니다.
* **페이로드 크기**. MQTT 및 AMQP는 바이너리 프로토콜로, HTTP보다 더 많이 압축된 페이로드를 발생합니다.

> [!NOTE]
> HTTP를 사용하는 경우 각 장치는 25분 이상마다 클라우드-장치에 대해 폴링합니다. 그렇지만 개발하는 동안에는 25분 보다 좀 더 자주 폴링을 하도록 할 수도 있습니다.
> 
> 

## <a name="port-numbers"></a>포트 번호
장치는 다양한 프로토콜을 사용하여 Azure에서 IoT Hub와 통신할 수 있습니다. 일반적으로 프로토콜은 솔루션의 특정 요구 사항에 따라 선택됩니다. 다음 표에는 특정 프로토콜을 사용할 수 있는 장치에 대해 열려 있어야 하는 아웃바운드 포트가 나와 있습니다.

| 프로토콜 | 포트 |
| --- | --- |
| MQTT |8883 |
| WebSocket을 통한 MQTT |443 |
| AMQP |5671 |
| Websocket 통한 AMQP |443 |
| HTTP |443 |
| LWM2M(장치 관리) |5684 |

Azure 지역에 IoT Hub를 만들면 IoT Hub는 해당 IoT Hub의 수명 동안 동일한 IP 주소를 유지합니다. 그러나 서비스 품질을 유지하기 위해 Microsoft가 IoT Hub를 다른 배율 단위로 이동하는 경우에는 새 IP 주소로 할당됩니다.

## <a name="notes-on-mqtt-support"></a>MQTT 지원에 대한 참고 사항
IoT Hub는 다음과 같은 제한 사항과 특정 동작으로 MQTT v3.1.1 프로토콜을 구현합니다.

* **QoS 2는 지원되지 않습니다**. 장치 앱이 **QoS 2**의 메시지를 게시하는 경우, IoT Hub는 네트워크 연결을 닫습니다. 장치 앱이 **QoS 2**의 토픽을 구독하는 경우, IoT Hub는 **SUBACK** 패킷에서 최대 QoS level 1을 부여합니다.
* **보존 메시지는 지속되지 않습니다**. 장치 앱이 RETAIN 플래그가 1로 설정된 메시지를 게시하는 경우, IoT Hub는 **x-opt-retain** 응용 프로그램 속성을 메시지에 추가합니다. 이 경우에 IoT Hub는 보관 메시지를 유지하지 않고 백 엔드 앱에 전달합니다.

자세한 내용은 [IoT Hub MQTT 지원][lnk-devguide-mqtt]을 참조하세요.

최종 고려 사항으로 IoT Hub와 직접 인터페이스하는 고성능 사용자 지정 프로토콜 게이트웨이를 배포할 수 있도록 [Azure IoT 프로토콜 게이트웨이][lnk-azure-protocol-gateway]를 검토해야 합니다. Azure IoT 프로토콜 게이트웨이를 사용하면 brownfield MQTT 배포를 수용하는 장치 프로토콜 또는 기타 사용자 지정 프로토콜을 사용자 지정할 수 있습니다. 그렇지만 이 방법을 사용하는 경우 사용자 지정 프로토콜 게이트웨이를 실행하고 작동해야 합니다.

## <a name="additional-reference-material"></a>추가 참조 자료
이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-endpoints] - 각 IoT Hub에서 런타임 및 관리 작업에 대해 공개하는 다양한 끝점에 대해 설명합니다.
* [제한 및 할당량][lnk-quotas] - IoT Hub 서비스에 적용되는 할당량과 서비스를 사용할 때 예상되는 제한 동작에 대해 설명합니다.
* [Azure IoT 장치 및 서비스 SDK][lnk-sdks] - IoT Hub와 상호 작용하는 장치 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.
* [장치 쌍 및 작업을 위한 IoT Hub 쿼리 언어][lnk-query] - IoT Hub에서 장치 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 IoT Hub 쿼리 언어에 대해 설명합니다.
* [IoT Hub MQTT 지원][lnk-devguide-mqtt] - MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계
IoT Hub를 통해 메시지를 보내고 받는 방법에 대해 알아봤으니 다음과 같은 IoT Hub 개발자 가이드 항목을 살펴보세요.

* [장치에서 파일 업로드][lnk-devguide-upload]
* [IoT Hub에서 장치 ID 관리][lnk-devguide-identities]
* [IoT Hub에 대한 액세스 제어][lnk-devguide-security]
* [장치 쌍을 사용하여 상태 및 구성 동기화][lnk-devguide-device-twins]
* [장치에서 직접 메서드 호출][lnk-devguide-directmethods]
* [여러 장치에서 jobs 예약][lnk-devguide-jobs]

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 살펴보세요.

* [Azure IoT Hub 시작][lnk-getstarted-tutorial]
* [IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d-tutorial]
* [IoT Hub 장치-클라우드 메시지를 처리하는 방법][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-what-is-event-hubs.md#partitions
