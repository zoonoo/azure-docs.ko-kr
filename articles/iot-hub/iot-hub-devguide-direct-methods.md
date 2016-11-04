<properties
 pageTitle="개발자 가이드 - 직접 메서드 | Microsoft Azure"
 description="Azure IoT Hub 개발자 가이드 - 직접 메서드를 사용하여 장치에서 코드 호출"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>


# <a name="invoke-a-direct-method-on-a-device-preview"></a>장치에 직접 메서드 호출(미리 보기)

## <a name="overview"></a>개요

IoT Hub는 클라우드의 장치에서 메서드를 호출할 수 있는 기능을 제공합니다. 메서드는 장치와의 request-reply 상호 작용을 나타내며, 사용자가 호출의 상태를 알 수 있도록 즉시(사용자가 지정한 제한 시간이 지난 후에) 성공하거나 실패한다는 점에서 HTTP 호출과 유사합니다. 즉각적인 작업 과정이 장치의 응답 가능성 여부에 따라 달라지는 시나리오에 유용합니다. 예를 들어 장치가 오프라인일 때 장치에 SMS 깨우기(wake-up)를 보내는 경우가 여기에 해당됩니다.(SMS가 메서드 호출보다 비용이 높아지고 있습니다.)

메서드를 장치에 대한 직접적인 원격 프로시저 호출이라고 생각할 수 있습니다. 장치에 구현되어 있는 메서드만 클라우드에서 호출될 수 있습니다. 클라우드가 메서드가 정의되어 있지 않은 장치에서 해당 메서드를 호출하려고 하면 메서드 호출은 실패합니다.

각 장치 메서드는 단일 장치를 대상으로 합니다. [작업][lnk-devguide-jobs]은 여러 장치에서 메서드를 호출하는 방법과 연결되지 않은 장치에 대한 메서드 호출을 큐에 넣는 방법을 제공합니다.

IoT Hub에 **서비스 연결** 권한만 있다면 누구든 장치에서 메서드를 호출할 수 있습니다.

### <a name="when-to-use"></a>사용하는 경우

장치 메서드는 클라우드 백 엔드가 장치에 정보를 전달하도록 해준다 점에서 [클라우드-장치 메시지][lnk-devguide-messages]와 유사하지만 근본적인 방식은 다릅니다. 개념적으로 메서드는 동기식이며 지속성이 없는 반면 클라우드-장치 메시지는 비동기식이며 최대 48시간의 지속성을 갖습니다.

메서드는 요청-응답 패턴을 따르며 지속성이 없습니다. 지속성 부족은 장치에 명령을 실행할 때 두 가지 즉각적인 장점을 제공합니다.

- **메서드 실행에 대한 즉각적인 피드백** 즉, 요청과 응답 사이의 상관 관계를 관리할 필요가 없습니다.
- **높은 처리량** 즉, IoT Hub가 지속성을 제공하지 않기 때문에 작업이 보다 빠르게 수행될 수 있습니다. IoT Hub는 클라우드-장치 메시지보다 단위당 더 많은 메서드 호출을 허용합니다.

클라우드-장치 메시지는 반드시 장치에 명령을 보낸다기 보다는 응답을 하거나 할 수 없는 장치에 약간의 정보를 전달하여 장치가 한가한 시간에 정보를 받도록 해주는 클라우드 서비스를 나타냅니다. 클라우드-장치 메시지는 시간 제한이 긴 반면(최대 48시간) 메서드는 훨씬 더 빨리 만료됩니다.

장치 메서드는 장치에 대한 예약된 명령 호출을 위해 장치 및 작업에 대한 즉각적인 명령 호출에 사용합니다.

## <a name="method-lifecycle"></a>메서드 수명 주기

메서드는 장치에서 구현되며 제대로 인스턴스화하기 위해서 메서드 페이로드에 0개 이상의 입력이 필요할 수 있습니다. 직접 메서드는 서비스 지향 URI를 통해 호출합니다(`{iot hub}/twins/{device id}/methods/`). 장치는 장치별 MQTT 토픽을 통해 직접 메서드를 수신합니다(`$iothub/methods/POST/{method name}/`). 향후에는 추가적인 장치 측 네트워킹 프로토콜에서 메서드를 지원할 예정입니다.

> [AZURE.NOTE] 장치에서 직접 메서드를 호출할 때 속성 이름과 값은 US-ASCII로 출력 가능한 영숫자만 포함할 수 있으며 다음 집합은 제외됩니다. ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``

메서드는 동기식이며 시간 제한(기본값: 30초, 최대 3600초까지 설정 가능)이 지나면 성공하거나 실패합니다. 메서드는 장치가 온라인 상태에서 명령을 수신하는 경우에만 작동하기를 바라는(예: 휴대폰에서 조명 켜기) 대화형 시나리오에 유용합니다. 이러한 시나리오에서는 클라우드 서비스가 결과에 최대한 빨리 대응할 수 있도록 즉각적인 성공이나 실패를 보려고 합니다. 장치는 메서드의 결과로 메시지 본문을 반환할 수 있지만 메서드가 반드시 그렇게 해야 하는 것은 아닙니다. 메서드 호출의 순서 지정 또는 동시성 의미 체계에 대한 보장은 없습니다.

장치 메서드 호출은 클라우드 측에서는 HTTP 전용, 장치 측에서는 MQTT 전용입니다.

## <a name="reference"></a>참조

### <a name="servicefacing"></a>서비스 지향

#### <a name="method-invocation"></a>메서드 호출

장치의 직접 메서드 호출은 다음을 포함하는 HTTP 호출입니다.

- 장치별 *URI*(`{iot hub}/twins/{device id}/methods/`)
- POST *메서드*
- *헤더* - 권한 부여, 요청 ID, 콘텐츠 형식, 콘텐츠 인코딩을 포함합니다.
- 다음과 같은 형식의 투명한 JSON *본문*:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  시간 제한은 초 단위입니다. 시간 제한이 설정되지 않으면 기본값이 30초로 설정됩니다.
  
#### <a name="response"></a>응답

백 엔드는 다음을 포함하는 응답을 수신합니다.

- *HTTP 상태 코드* - 현재 연결되지 않은 장치에 대한 404 오류를 비롯한 IoT Hub에서 오는 오류에 사용됩니다.
- *헤더* - ETag, 요청 ID, 콘텐츠 형식, 콘텐츠 인코딩을 포함합니다.
- 다음과 같은 형식의 JSON *본문*:

```
{
    "status" : "OK",
    "payload" : {...}
}
```
  
   `status`와 `body`는 모두 장치에 의해 제공되며 장치 자체의 상태 코드 및/또는 설명으로 응답하는 데 사용됩니다.

### <a name="devicefacing"></a>장치 지향

#### <a name="method-invocation"></a>메서드 호출

장치는 MQTT 토픽으로 직접 메서드 요청을 수신합니다. `$iothub/methods/POST/{method name}/?$rid={request id}`

장치가 수신하는 본문은 다음과 같은 형식입니다.

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

메서드 요청은 QoS 0입니다.

#### <a name="response"></a>응답

장치는 `$iothub/methods/res/{status}/?$rid={request id}`에 응답을 보내는데 여기서:

 - `status` 속성은 장치가 제공하는 메서드 실행 상태입니다.
 - `$rid` 속성은 IoT Hub로부터 수신한 메서드 호출의 요청 ID입니다.

본문은 장치에 의해 설정되며 모든 상태가 될 수 있습니다.

### <a name="additional-reference-material"></a>추가 참조 자료

개발자 가이드의 다른 참조 자료:

- [IoT Hub 끝점][lnk-endpoints]에서는 각 IoT Hub가 런타임 및 관리 작업에 노출하는 다양한 끝점을 설명합니다.
- [제한 및 할당량][lnk-quotas]에서는 IoT Hub 서비스에 적용되는 할당량과 서비스를 언제 사용할지 예상하는 제한 동작을 설명합니다.
- [IoT Hub 장치 및 서비스 SDK][lnk-sdks]에는 IoT Hub와 상호 작용하는 장치 및 서비스 응용 프로그램을 개발할 때 사용할 수 있는 다양한 언어 SDK가 나열되어 있습니다.
- [쌍, 메서드 및 작업용 쿼리 언어][lnk-query]에는 장치 쌍, 메서드 및 작업을 IoT Hub에서 검색하는 데 사용할 수 있는 쿼리 언어가 설명되어 있습니다.
- [IoT Hub MQTT 지원][lnk-devguide-mqtt]에는 MQTT 프로토콜에 대한 IoT Hub 지원에 대하여 자세한 정보가 제공됩니다.

## <a name="next-steps"></a>다음 단계

직접 메서드를 사용하는 방법에 대해 알아봤으니 다음과 같은 개발자 가이드 항목을 살펴보세요.

- [여러 장치에서 작업 예약][lnk-devguide-jobs]

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 살펴보세요.

- [클라우드-장치 메서드 사용][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


