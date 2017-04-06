---
title: "Azure IoT Hub 직접 메서드 이해 | Microsoft Docs"
description: "개발자 가이드 - 직접 메서드를 사용하여 서비스 앱의 장치에서 코드 호출"
services: iot-hub
documentationcenter: .net
author: nberdy
manager: timlt
editor: 
ms.assetid: 9f0535f1-02e6-467a-9fc4-c0950702102d
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2017
ms.author: nberdy
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: ce7860e291c71e825561caf3ee7eafe904101799
ms.lasthandoff: 03/10/2017


---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>IoT Hub의 직접 메서드 호출 및 이해
## <a name="overview"></a>개요
IoT Hub를 사용하면 클라우드의 장치에서 직접 메서드를 호출할 수 있습니다. 직접 메서드는 사용자가 지정한 시간 제한을 초과하는 즉시 성공하거나 실패한다는 점에서 HTTP 호출과 비슷한 디바이스와의 요청-응답 상호 작용을 나타냅니다. 즉각적인 작업 과정이 장치의 응답 가능성 여부에 따라 달라지는 시나리오에 유용합니다. 예를 들어 장치가 오프라인일 때 장치에 SMS 깨우기(wake-up)를 보내는 경우가 여기에 해당됩니다.(SMS가 메서드 호출보다 비용이 높아지고 있습니다.)

각 장치 메서드는 단일 장치를 대상으로 합니다. [jobs][lnk-devguide-jobs]는 여러 장치에서 직접 메서드를 호출하고 연결되지 않은 장치에 대한 메서드 호출을 예약하는 방법을 제공합니다.

IoT Hub에 **서비스 연결** 권한만 있다면 누구든 장치에서 메서드를 호출할 수 있습니다.

### <a name="when-to-use"></a>사용하는 경우
직접 메서드는 요청-응답 패턴을 따르며, 결과를 즉각적으로 확인해야 하는 통신, 대개는 팬을 작동하는 것과 같이 장치의 대화식 제어가 필요한 통신입니다.

desired 속성, 직접 메서드 또는 클라우드-장치 메시지 사용에 대해 궁금한 점이 있으면 [클라우드-장치 통신 지침][lnk-c2d-guidance]을 참조하세요.

## <a name="method-lifecycle"></a>메서드 수명 주기
직접 메서드는 장치에서 구현되며, 제대로 인스턴스화하기 위해 메서드 페이로드에&0;개 이상의 입력이 필요할 수 있습니다. 직접 메서드는 서비스 지향 URI를 통해 호출합니다(`{iot hub}/twins/{device id}/methods/`). 장치는 장치별 MQTT 토픽을 통해 직접 메서드를 수신합니다(`$iothub/methods/POST/{method name}/`). 향후 추가적인 장치 쪽 네트워킹 프로토콜에서 직접 메서드를 지원할 예정입니다.

> [!NOTE]
> 장치에서 직접 메서드를 호출할 때 속성 이름과 값은 US-ASCII로 출력 가능한 영숫자만 포함할 수 있으며 다음 집합은 제외됩니다. ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 
> 

직접 메서드는 동기식이며 제한 시간(기본값: 30초, 최대 3600초 설정 가능)이 지나면 성공하거나 실패합니다. 직접 메서드는 장치가 온라인 상태에서 명령을 수신하는 경우에만 작동하기(예: 휴대폰에서 조명 켜기)를 바라는 대화형 시나리오에서 유용합니다. 이러한 시나리오에서는 클라우드 서비스가 결과에 최대한 빨리 대응할 수 있도록 즉각적인 성공이나 실패를 보려고 합니다. 장치는 메서드의 결과로 메시지 본문을 반환할 수 있지만 메서드가 반드시 그렇게 해야 하는 것은 아닙니다. 메서드 호출의 순서 지정 또는 동시성 의미 체계에 대한 보장은 없습니다.

직접 메서드는 클라우드 쪽에서는 HTTP 전용, 장치 쪽에서는 MQTT 전용입니다.

메서드 요청 및 응답에 대한 페이로드는 최대 8KB의 JSON 문서입니다.

## <a name="reference-topics"></a>참조 항목:
다음 참조 항목에서는 직접 메서드 사용에 대한 자세한 정보를 제공합니다.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>백 엔드 앱에서 직접 메서드 호출
### <a name="method-invocation"></a>메서드 호출
장치의 직접 메서드 호출은 다음을 포함하는 HTTP 호출입니다.

* 장치별 *URI*(`{iot hub}/twins/{device id}/methods/`)
* POST *메서드*
* *헤더* - 권한 부여, 요청 ID, 콘텐츠 형식, 콘텐츠 인코딩을 포함합니다.
* 다음과 같은 형식의 투명한 JSON *본문*:

```
{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

시간 제한은 초 단위입니다. 시간 제한이 설정되지 않으면 기본값이 30초로 설정됩니다.

### <a name="response"></a>응답
백 엔드 앱은 다음을 포함하는 응답을 수신합니다.

* *HTTP 상태 코드* - 현재 연결되지 않은 장치에 대한 404 오류를 비롯한 IoT Hub에서 오는 오류에 사용됩니다.
* *헤더* - ETag, 요청 ID, 콘텐츠 형식, 콘텐츠 인코딩을 포함합니다.
* 다음과 같은 형식의 JSON *본문*:

```
{
    "status" : 201,
    "payload" : {...}
}
```

   `status`와 `body`는 모두 장치에 의해 제공되며 장치 자체의 상태 코드 및/또는 설명으로 응답하는 데 사용됩니다.

## <a name="handle-a-direct-method-on-a-device"></a>장치에서 직접 메서드 처리
### <a name="method-invocation"></a>메서드 호출
장치는 MQTT 토픽으로 직접 메서드 요청을 수신합니다. `$iothub/methods/POST/{method name}/?$rid={request id}`

장치가 수신하는 본문은 다음과 같은 형식입니다.

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

메서드 요청은 QoS 0입니다.

### <a name="response"></a>응답
장치는 `$iothub/methods/res/{status}/?$rid={request id}`에 응답을 보내는데 여기서:

* `status` 속성은 장치가 제공하는 메서드 실행 상태입니다.
* `$rid` 속성은 IoT Hub로부터 수신한 메서드 호출의 요청 ID입니다.

본문은 장치에 의해 설정되며 모든 상태가 될 수 있습니다.

## <a name="additional-reference-material"></a>추가 참조 자료
이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 끝점][lnk-endpoints] - 각 IoT Hub에서 런타임 및 관리 작업에 대해 공개하는 다양한 끝점에 대해 설명합니다.
* [제한 및 할당량][lnk-quotas] - IoT Hub 서비스에 적용되는 할당량과 서비스를 사용할 때 예상되는 제한 동작에 대해 설명합니다.
* [Azure IoT 장치 및 서비스 SDK][lnk-sdks] - IoT Hub와 상호 작용하는 장치 및 서비스 앱 모두를 개발할 때 사용하는 다양한 언어 SDK를 나열합니다.
* [장치 쌍 및 작업을 위한 IoT Hub 쿼리 언어][lnk-query] - IoT Hub에서 장치 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 IoT Hub 쿼리 언어에 대해 설명합니다.
* [IoT Hub MQTT 지원][lnk-devguide-mqtt] - MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계
직접 메서드를 사용하는 방법에 대해 알아봤으니 다음 IoT Hub 개발자 가이드 항목을 살펴보세요.

* [여러 장치에서 jobs 예약][lnk-devguide-jobs]

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 살펴보세요.

* [직접 메서드 사용][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md

