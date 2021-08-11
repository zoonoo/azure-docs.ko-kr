---
title: Azure IoT Hub MQTT 5 지원(미리 보기)
description: IoT Hub의 MQTT 5 지원에 대한 자세한 정보
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fbe51d51da55d6708bfae96d0fec15eec9896602
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109484850"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub MQTT 5 지원 개요(미리 보기)

**버전:** 2.0 **api-version:** 2020-10-01-preview

이 문서에서는 MQTT 버전 5.0 프로토콜을 통해 IoT Hub 데이터 평면 API를 정의합니다. 이 API의 전체 정의는 [API 참조](iot-hub-mqtt-5-reference.md)를 확인하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 새 IoT 허브에서 MQTT 5를 사용해 볼 수 있도록 [미리 보기 모드를 설정](iot-hub-preview-mode.md)합니다.
- [MQTT 5 사양](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html)에 대한 사전 지식이 필요합니다.

## <a name="level-of-support-and-limitations"></a>지원 수준 및 제한 사항

MQTT 5에 대한 IoT Hub 지원은 현재 미리 보기로 제공되며 다음과 같은 제한이 있습니다(별도로 명시하지 않는 한 `CONNACK` 속성을 통해 클라이언트에 전달됨).

- 공식 [Azure IoT Hub 디바이스 SDK](iot-hub-devguide-sdks.md)는 아직 지원되지 않습니다.
- 구독 식별자는 지원되지 않습니다.
- 공유 구독은 지원되지 않습니다.
- `RETAIN`는 지원되지 않습니다.
- `Maximum QoS`은 `1`입니다.
- `Maximum Packet Size`는 `256 KiB`입니다(작업별 추가 제한 적용).
- 할당된 클라이언트 ID는 지원되지 않습니다.
- `Keep Alive`는 `19 min`으로 제한됩니다(활동성 검사의 최대 지연 시간 - `28.5 min`).
- `Topic Alias Maximum`은 `10`입니다.
- `Response Information`은 지원되지 않습니다. `CONNECT`에 `Request Response Information` 속성이 포함된 경우에도 `CONNACK`가 `Response Information` 속성을 반환하지 않습니다.
- `Receive Maximum`(클라이언트-서버 방향에서 허용되는 `QoS: 1`인 미승인 미처리 `PUBLISH` 패킷의 최대 수)는 `16`입니다.
- 단일 클라이언트의 구독 수는 `50`개를 초과할 수 없습니다.
  이 제한에 도달하면 `SUBACK`은 구독에 대한 `0x97`(할당량 초과) 사유 코드를 반환합니다.

## <a name="connection-lifecycle"></a>연결 수명 주기

### <a name="connection"></a>연결

이 API를 사용하여 클라이언트를 IoT Hub 연결하려면 MQTT 5 사양에 따라 연결을 설정합니다.
TLS 핸드셰이크가 성공한 후 30초 이내에 클라이언트에서 `CONNECT` 패킷을 보내야 합니다. 그렇지 않으면 서버에서 연결을 닫습니다.
다음은 `CONNECT` 패킷의 예입니다.

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- `Authentication Method` 속성은 필수이며 사용되는 인증 방법을 식별합니다. 인증 방법에 대한 자세한 내용은 [인증](#authentication)을 참조하세요.
- `Authentication Data` 속성 처리는 `Authentication Method`에 따라 달라집니다. `Authentication Method`가 `SAS`로 설정된 경우 `Authentication Data`는 필수이며 유효한 서명을 포함해야 합니다. 인증 데이터에 대한 자세한 내용은 [인증](#authentication)을 참조하세요.
- `api-version` 속성은 필수이며 이 사양을 적용하려면 이 사양의 헤더에 제공된 API 버전 값으로 설정해야 합니다.
- `host` 속성은 테넌트의 호스트 이름을 정의합니다. TLS 핸드셰이크 중에 클라이언트 Hello 레코드에 SNI 확장이 제공되지 않는 한 이 속성은 필수입니다.
- `sas-at`는 연결 시간을 정의합니다.
- `sas-expiry`는 제공된 SAS의 만료 시간을 정의합니다.
- `client-agent`는 필요에 따라 연결을 만드는 클라이언트에 대한 정보를 전달합니다.

> [!NOTE]
> `Authentication Method` 및 사양 전체에서 대문자 이름을 가진 기타 속성은 MQTT 5의 첫 번째 클래스 속성이며 MQTT 5 사양에 대한 세부 정보에서 설명합니다. `api-version` 및 대시 케이스의 기타 속성은 IoT Hub API에 적용되는 사용자 속성입니다.

IoT Hub는 인증을 완료하고 연결을 지원하기 위한 데이터를 가져온 후 `CONNACK` 패킷으로 응답합니다. 연결이 성공적으로 설정되면 `CONNACK`는 다음과 같습니다.

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

이러한 `CONNACK` 패킷 속성은 [MQTT 5 사양](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)을 따르며 IoT Hub 기능을 반영합니다.

### <a name="authentication"></a>인증

`CONNECT` 클라이언트의 `Authentication Method` 속성은 이 연결에 사용되는 인증 종류를 정의합니다.

- `SAS` - 공유 액세스 서명은 `CONNECT`의 `Authentication Data` 속성에 제공됩니다.
- `X509` - 클라이언트에서 클라이언트 인증서 인증을 사용합니다.

 인증 방법이 IoT Hub 클라이언트에 구성된 방법과 일치하지 않으면 인증이 실패합니다.

> [!NOTE]
> 이 API를 사용하려면 `CONNECT` 패킷에서 `Authentication Method` 속성을 설정해야 합니다. `Authentication Method` 속성을 제공하지 않으면 `Bad Request` 응답과 함께 연결이 실패합니다.

이전 버전의 API에서 사용된 사용자 이름/암호 인증은 지원되지 않습니다.

#### <a name="sas"></a>SAS

SAS 기반 인증을 사용하는 경우 클라이언트에서 연결 컨텍스트의 서명을 제공해야 합니다. 이 방법으로 MQTT 연결의 신뢰성을 증명합니다. 서명은 IoT Hub 클라이언트 구성의 두 가지 인증 키 또는 [공유 액세스 정책](iot-hub-dev-guide-sas.md)의 두 가지 공유 액세스 키 중 하나를 기반으로 해야 합니다.

서명할 문자열은 다음과 같이 구성되어야 합니다.

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name`은 SNI 확장(TLS 핸드셰이크 중에 클라이언트 Hello 레코드의 클라이언트에서 제공) 또는 `CONNECT` 패킷의 `host` 사용자 속성에서 파생됩니다.
- `Client Id`는 `CONNECT` 패킷의 클라이언트 식별자입니다.
- `sas-policy` - 있는 경우 인증에 사용되는 IoT Hub 액세스 정책을 정의합니다. `CONNECT` 패킷에서 사용자 속성으로 인코딩됩니다. 선택 사항: 이 속성을 생략하면 디바이스 레지스트리의 인증 설정이 대신 사용됩니다.
- `sas-at` - 있는 경우 연결 시간 - 현재 시간을 지정합니다. `CONNECT` 패킷에서 `time` 형식의 사용자 속성으로 인코딩됩니다.
- `sas-expiry`는 인증의 만료 시간을 정의합니다. `CONNECT` 패킷의 `time` 형식 사용자 속성입니다. 이 속성은 필수입니다.

선택적 매개 변수의 경우 생략하면 서명할 문자열에 빈 문자열을 대신 사용해야 합니다.

HMAC-SHA256은 디바이스의 대칭 키 중 하나를 기반으로 문자열을 해시하는 데 사용됩니다. 그런 다음, 해시 값이 `Authentication Data` 속성 값으로 설정됩니다.

#### <a name="x509"></a>X509

`Authentication Method` 속성이 `X509`로 설정되면 IoT Hub는 제공된 클라이언트 인증서를 기반으로 연결을 인증합니다.

#### <a name="reauthentication"></a>재인증

SAS 기반 인증을 사용하는 경우 수명이 짧은 인증 토큰을 사용하는 것이 좋습니다. 연결을 인증된 상태로 유지하고 만료로 인한 연결 끊김을 방지하려면 클라이언트에서 다음과 같이 `Reason Code: 0x19`(재인증)로 `AUTH` 패킷을 전송하여 재인증해야 합니다.

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

규칙:

- `Authentication Method`는 초기 인증에 사용되는 방법과 같아야 합니다.
- 공유 액세스 정책을 기반으로 하는 SAS를 사용하여 초기 연결이 인증된 경우 재인증에 사용되는 서명은 동일한 정책을 기반으로 해야 합니다.

재인증이 성공하면 IoT Hub는 `Reason Code: 0x00`(성공)으로 `AUTH` 패킷을 보냅니다. 그렇지 않으면 IoT Hub는 `Reason Code: 0x87`(권한 없음)로 `DISCONNECT` 패킷을 전송하고 연결을 닫습니다.

### <a name="disconnection"></a>연결 끊기

다음과 같은 이유로 서버에서 클라이언트 연결을 끊을 수 있습니다.

- 클라이언트가 부정 승인(또는 응답)을 통해 직접 응답할 수 없는 방식으로 오동작합니다.
- 서버에서 연결 상태를 최신으로 유지할 수 없습니다.
- 같은 ID를 사용하는 클라이언트가 연결되었습니다.

MQTT 5.0 사양에 정의된 사유 코드로 인해 서버에서 연결을 끊을 수 있습니다. 다음은 주요 사유 코드입니다.

- 재인증이 실패하고 현재 SAS 토큰이 만료되거나 디바이스의 자격 증명이 변경되는 경우 - `135`(권한 없음)
- 동일한 클라이언트 ID를 사용하는 새 연결이 열린 경우 - `142`(세션 인계)
- IoT 허브의 연결 속도를 초과하는 경우 - `159`(연결 속도 초과)  
- 이 API에 정의된 모든 사용자 지정 오류 - `131`(구현 관련 오류) `status` 및 `reason` 속성은 연결이 끊어진 원인에 대한 추가 정보를 전달하는 데 사용됩니다(자세한 내용은 [응답](#response) 참조).

## <a name="operations"></a>작업

이 API의 모든 기능은 작업으로 표현됩니다. 다음은 원격 분석 데이터 보내기 작업의 예입니다.

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

이 API의 전체 작업 사양은 [API 참조](iot-hub-mqtt-5-reference.md)를 확인하세요.

> [!NOTE]
> 이 사양의 모든 샘플은 클라이언트 관점에서 표시됩니다. `->` 기호는 클라이언트에서 패킷을 보낸다는 뜻이고, `<-` 기호는 클라이언트에서 패킷을 받는다는 뜻입니다.

### <a name="message-topics-and-subscriptions"></a>메시지 토픽 및 구독

이 API의 작업 메시지에 사용되는 토픽은 `$iothub/`로 시작합니다.
MQTT broker 의미 체계는 이러한 작업에 적용되지 않습니다(자세한 내용은 [\$으로 시작하는 토픽](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246) 참조).
이 API에 정의되지 않은 `$iothub/`로 시작하는 토픽은 지원되지 않습니다.

- 정의되지 않은 토픽에 메시지를 보내면 `Not Found` 응답이 반환됩니다(자세한 내용은 아래 [응답](#response) 참조).
- 정의되지 않은 토픽을 구독하면 `SUBACK` 및 `Reason Code: 0x8F`(토픽 필터가 잘못됨)가 반환됩니다.

토픽 이름과 속성 이름은 대/소문자를 구분하며 정확히 일치해야 합니다. 예를 들어 `$iothub/telemetry/`는 지원되지 않지만 `$iothub/telemetry`는 지원됩니다.

> [!NOTE]
> `$iothub/..` 아래의 구독에 있는 와일드카드는 지원되지 않습니다. 즉, 클라이언트는 `$iothub/+` 또는 `$iothub/#`를 구독할 수 없습니다. 구독하려고 시도하면 `SUBACK` 및 `Reason Code: 0xA2`(와일드카드 구독이 지원되지 않음)가 반환됩니다. 경로 매개 변수가 있는 작업의 토픽 이름에는 경로 매개 변수 대신 단일 세그먼트 와일드카드(`+`)만 지원됩니다.

### <a name="interaction-types"></a>상호 작용 유형

이 API의 모든 작업은 다음과 같은 두 가지 유형의 상호 작용 중 하나를 기반으로 합니다.

- 선택적 승인이 포함된 메시지(MessageAck)
- 요청-응답(ReqRep)

방향에 따라서도 작업이 달라집니다(교환 시 초기 메시지의 방향에 따라 결정).

- 클라이언트-서버(c2s)
- 서버-클라이언트(s2c)

예를 들어 원격 분석 데이터 보내기는 "승인이 포함된 메시지" 유형의 클라이언트-서버 작업이지만, 직접 메서드 처리는 요청-응답 유형의 서버-클라이언트 작업입니다.

#### <a name="message-acknowledgement-interactions"></a>메시지-승인 상호 작용

선택적 승인(MessageAck) 상호 작용이 포함된 메시지는 MQTT에서 `PUBLISH` 및 `PUBACK` 패킷의 교환으로 표현됩니다. 승인은 선택 사항이며 보낸 사람은 `QoS: 0`인 `PUBLISH` 패킷을 전송하여 승인을 요청하지 않도록 선택할 수 있습니다.

> [!NOTE]
> 클라이언트에서 선언한 `Maximum Packet Size` 때문에 `PUBACK` 패킷의 속성을 잘라내야 하는 경우 IoT Hub는 지정된 제한 내에서 최대한 많은 사용자 속성을 유지합니다. 처음에 나열된 사용자 속성은 나중에 나열된 사용자 속성보다 전송될 가능성이 있습니다. `Reason String` 속성의 우선 순위가 가장 낮습니다.

##### <a name="example-of-simple-messageack-interaction"></a>간단한 MessageAck 상호 작용의 예

메시지:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

승인(성공):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>요청-응답 상호 작용

요청-응답(ReqRep) 상호 작용에서는 요청과 응답 모두 `QoS: 0`인 `PUBLISH` 패킷으로 변환됩니다.

`Correlation Data` 속성은 요청과 응답 모두에서 설정해야 하며 응답 패킷을 요청 패킷과 매칭하는 데 사용됩니다.

이 API는 모든 ReqRep 작업에 단일 응답 토픽 `$iothub/responses`를 사용합니다. 클라이언트-서버 작업에 대한 이 토픽을 구독/구독 취소할 필요가 없습니다. 서버는 모든 클라이언트를 구독하는 것으로 간주합니다.

##### <a name="example-of-simple-reqrep-interaction"></a>간단한 ReqRep 상호 작용의 예

요청:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

응답(성공):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep 상호 작용은 `QoS: 1`인 `PUBLISH` 패킷을 요청 또는 응답 메시지로 지원하지 않습니다. 요청 `PUBLISH`를 보내면 `Bad Request` 응답이 반환됩니다.

`Correlation Data` 속성에서 지원되는 최대 길이는 16바이트입니다. `PUBLISH` 패킷의 `Correlation Data` 속성이 16바이트 보다 긴 값으로 설정되면 IoT Hub는 `Bad Request` 결과와 함께 `DISCONNECT`를 전송하고 연결을 닫습니다. 이 동작은 이 API 내에서 교환되는 패킷에만 적용됩니다.

> [!NOTE]
> 상관 관계 데이터는 임의의 바이트 시퀀스이므로 반드시 UTF-8 문자열이라는 보장이 없습니다.
>
> ReqRep는 미리 정의된 토픽을 응답에 사용합니다. 요청 `PUBLISH` 패킷의 응답 토픽 속성(보낸 사람이 설정한 경우)은 무시됩니다.

IoT Hub는 자동으로 클라이언트를 구독하여 모든 클라이언트-서버 ReqRep 작업에 대한 토픽에 응답합니다. 클라이언트에서 응답 항목을 명시적으로 구독 취소하더라도 IoT Hub가 자동으로 구독을 복구합니다. 서버-클라이언트 ReqRep 상호 작용의 경우 여전히 디바이스에서 구독해야 합니다.

### <a name="message-properties"></a>메시지 속성

작업 속성(시스템 또는 사용자가 정의)은 MQTT 5에서 패킷 속성으로 표현됩니다.

사용자 속성 이름은 대/소문자를 구분하므로 정의된 대로 정확하게 입력해야 합니다. 예를 들어 `Trace-ID`는 지원되지 않지만 `trace-id`는 지원됩니다.

요청의 사용자 속성이 사양을 벗어나고 `@` 접두사가 없으면 오류가 발생합니다.

시스템 속성은 첫 번째 클래스 속성(예: `Content Type`) 또는 사용자 속성으로 인코딩됩니다. 사양은 지원되는 시스템 속성의 전체 목록을 제공합니다.
모든 첫 번째 클래스 속성은 사양에 해당 지원이 명시된 경우를 제외하고 무시됩니다.

사용자 정의 속성이 허용되는 경우 해당 이름은 `@{property name}` 형식이어야 합니다. 사용자 정의 속성은 유효한 UTF-8 문자열 값만 지원합니다. 예를 들어 값이 `15`인 `MyProperty1` 속성은 이름이 `@MyProperty`이고 값이 `15`인 사용자 속성으로 인코딩해야 합니다.

IoT Hub가 사용자 속성을 인식하지 못하는 경우 오류로 간주되고 IoT Hub는 `Reason Code: 0x83`(구현 관련 오류) 및 `status: 0100`(잘못된 요청)인 `PUBACK`으로 응답합니다. 승인이 요청되지 않은 경우(QoS: 0) 동일한 오류가 있는 `DISCONNECT` 패킷이 다시 전송되고 연결이 종료됩니다.

이 API는 `string` 외에도 다음과 같은 데이터 형식을 정의합니다.

- `time`: `1970-01-01T00:00:00.000Z` 이후에 경과한 시간(밀리초). 예: `2020-09-24T22:39:55.320Z`는 `1600987195320`
- `u32`: 부호 없는 32비트 정수
- `u64`: 부호 없는 64비트 정수
- `i32`: 부호 있는 32비트 정수

### <a name="response"></a>응답

상호 작용은 `Success`, `Bad Request`, `Not Found` 등의 다양한 결과를 초래할 수 있습니다.
결과는 사용자 속성 `status`를 통해 서로 구별됩니다. `PUBACK` 패킷의 `Reason Code`(MessageAck 상호 작용의 경우)는 가능한 경우 그 의미가 `status`와 일치합니다.

> [!NOTE]
> 클라이언트가 CONNECT 패킷에서 `Request Problem Information: 0`을 지정하는 경우 MQTT 5 사양을 준수하기 위해 `status` 속성을 포함한 그 어떤 사용자 속성도 `PUBACK` 패킷에 전송되지 않습니다. 이 경우 클라이언트는 여전히 `Reason Code`를 사용하여 승인이 긍정인지 부정인지 확인할 수 있습니다. 

모든 상호 작용에는 기본값(또는 성공)이 있습니다. 기본적으로 `Reason Code`는 `0`이고 `status` 속성은 "설정되지 않음"입니다. 그렇지 않은 경우:

- MessageAck 상호 작용의 경우 `PUBACK`은 0x0(성공) 이외의 `Reason Code`를 갖습니다. `status` 속성을 사용하여 결과를 더 명확하게 나타낼 수 있습니다.
- ReqRep 상호 작용의 경우 응답 `PUBLISH`는 `status` 속성 세트를 갖습니다.
- `QoS: 0`을 사용하여 직접 MessageAck 상호 작용에 응답할 방법이 없으므로 `DISCONNECT` 패킷이 응답 정보와 함께 전송된 후 연결이 끊어집니다.

예:

잘못된 요청(MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

권한 없음(MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

권한 없음(ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

필요한 경우 IoT Hub는 다음과 같은 사용자 속성을 설정합니다.

- `status` - 작업 상태에 대한 IoT Hub 확장 코드입니다. 이 코드를 사용하여 결과를 구분할 수 있습니다.
- `trace-id` – 작업의 추적 ID입니다. IoT Hub에서는 내부 조사에 사용할 수 있는 작업에 대한 추가 진단을 유지할 수 있습니다.
- `reason` - 사람이 읽을 수 있는 메시지이며 작업이 `status` 속성으로 표시된 상태로 종료된 이유에 대한 추가 정보를 제공합니다.

> [!NOTE]
> 클라이언트가 CONNECT 패킷에서 `Maximum Packet Size` 속성을 매우 작은 값으로 설정하는 경우 일부 사용자 속성이 맞지 않아 패킷에 표시되지 않을 수 있습니다.
> 
> `reason`은 사람에게만 사용되며 클라이언트 논리에 사용하면 안 됩니다. 이 API를 사용하면 언제든지 경고 또는 버전 변경 없이 메시지를 변경할 수 있습니다.
>
> 클라이언트에서 연결 패킷에 `RequestProblemInformation: 0`을 보내면 사용자 속성이 [MQTT 5 사양](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)에 따라 승인에 포함되지 않습니다.

#### <a name="status-code"></a>상태 코드

`status` 속성은 작업에 대한 상태 코드를 전달합니다. 머신 읽기 효율성에 최적화되어 있습니다.
이 속성은 문자열에서 16진수로 인코딩된 부호 없는 2바이트 정수로 구성됩니다(예: `0501`).
코드 구조(비트 맵):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

첫 번째 바이트는 플래그에 사용됩니다.

- 비트 0과 1은 결과의 종류를 나타냅니다.
  - `00` - 성공
  - `01` - 클라이언트 오류
  - `10` - 서버 오류
- 비트 2: `1`은 오류를 다시 시도할 수 있음을 나타냅니다.
- 비트 3~7은 예약되어 있으며 `0`으로 설정해야 합니다.

두 번째 바이트는 고유한 실제 응답 코드를 포함합니다. 플래그가 서로 다른 오류 코드의 두 번째 바이트 값이 같아도 됩니다. 예를 들어 고유한 의미를 갖는 `0001`, `0101`, `0201`, `0301` 오류 코드가 있을 수 있습니다.

예를 들어 `Too Many Requests`는 자체 코드가 `1`인 다시 시도할 수 있는 클라이언트 오류입니다. 값은 `0000 0101 0000 0001` 또는 `0x0501`입니다.

클라이언트는 형식 비트를 사용하여 작업이 성공적으로 완료되었는지 여부를 식별할 수 있습니다. 또한 클라이언트는 재시도 가능 비트를 사용하여 작업을 다시 시도할 수 있는지 여부를 결정할 수 있습니다.

## <a name="recommendations"></a>권장 사항

### <a name="session-management"></a>세션 관리

`CONNACK` 패킷은 서버에서 이전에 만든 세션을 복원했는지 여부를 나타내는 `Session Present` 속성을 전달합니다. 이 속성을 사용하여 토픽을 구독할 것인지 또는 구독이 이전에 완료되었으므로 구독을 건너뛸 것인지 파악할 수 있습니다.

`Session Present`를 사용하려면 클라이언트가 이전에 만든(즉, `SUBSCRIBE` 패킷을 보내고 성공 사유 코드와 함께 `SUBACK`을 수신한) 구독을 추적하거나, 단일 `SUBSCRIBE`/`SUBACK` 교환의 모든 토픽을 구독해야 합니다. 그렇지 않으면 클라이언트가 두 개의 `SUBSCRIBE` 패킷을 보냈는데 그 중 하나만 서버에서 성공적으로 처리되는 경우 서버가 `CONNACK`에 `Session Present: 1`을 전달하고 클라이언트의 구독 중 일부만 수락됩니다.

이전 버전의 클라이언트가 일부 토픽을 구독하지 않은 상황을 방지하려면 클라이언트 동작이 변경될 때(예: 펌웨어를 업데이트하는 과정에서) 무조건 구독하는 것이 좋습니다. 또한 부실 구독이 남아 있지 않도록(허용되는 최대 구독 수에서 계산) 더 이상 사용되지 않는 구독을 명시적으로 취소합니다.

### <a name="batching"></a>일괄 처리

메시지 일괄 처리를 보내는 특별한 형식은 없습니다. TLS 및 네트워킹에서 리소스 집약적인 작업의 오버헤드를 줄이려면 패킷(`PUBLISH`, `PUBACK`, `SUBSCRIBE` 등)을 함께 번들링한 후 기본 TLS/TCP 스택에 전달합니다. 또한 클라이언트는 "일괄 처리" 내에서 토픽 별칭을 더 쉽게 만들 수 있습니다.

- 연결의 첫 번째 `PUBLISH` 패킷에 전체 토픽 이름을 배치하고 토픽 별칭을 연결합니다.
- 빈 토픽 이름 및 토픽 별칭 속성을 가진 동일한 토픽에 대해 다음 패킷을 배치합니다.

## <a name="migration"></a>마이그레이션

이 섹션에는 [이전 MQTT API](iot-hub-mqtt-support.md)와 다른 API 변경 내용이 나열되어 있습니다.

- 전송 프로토콜은 MQTT 5입니다. 이전에는 MQTT 3.1.1였습니다.
- SAS 인증에 대한 컨텍스트 정보는 서명과 함께 인코딩되는 대신 `CONNECT` 패킷에 직접 포함됩니다.
- 인증 방법을 사용하여 사용된 인증 방법을 표시합니다.
- 공유 액세스 서명은 인증 데이터 속성에 저장됩니다. 이전에는 암호 필드를 사용했습니다.
- 작업에 대한 토픽이 다릅니다.
  - 원격 분석: `devices/{Client Id}/messages/events` 대신 `$iothub/telemetry`
  - 명령: `devices/{Client Id}/messages/devicebound` 대신 `$iothub/commands`
  - 보고되는 패치 트윈: `$iothub/twin/PATCH/properties/reported` 대신 `$iothub/twin/patch/reported`
  - 변경된 Twin Desired State 알림: `$iothub/twin/PATCH/properties/desired` 대신 `$iothub/twin/patch/desired`
- 클라이언트-서버 요청-응답 작업의 응답 토픽을 구독할 필요가 없습니다.
- 토픽 이름 세그먼트에 인코딩 속성 대신 사용자 속성이 사용됩니다.
- 속성 이름은 특수 접두사가 있는 약어 대신 "대시 케이스" 명명 규칙을 따릅니다. 그 대신 이제는 사용자 정의 속성에 접두사가 필요합니다. 예를 들어 `$.mid`는 이제 `message-id`가 되고, `myProperty1`은 `@myProperty1`이 됩니다.
- 상관 관계 데이터 속성은 토픽에서 인코딩된 `$rid` 속성 대신 요청-응답 작업에 대한 요청 메시지와 응답 메시지를 간의 상관 관계를 지정하는 데 사용됩니다.
- `iothub-connection-auth-method` 속성은 더 이상 원격 분석 이벤트에서 스탬핑되지 않습니다.
- C2D 명령은 디바이스에 구독이 없을 때 제거되지 않습니다. 디바이스가 구독하거나 만료될 때까지 큐에 대기 상태를 유지합니다.

## <a name="examples"></a>예

### <a name="send-telemetry"></a>원격 분석 전송

메시지:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

확인:

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

대체 승인(제한됨):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>트윈 상태 가져오기 보내기

요청:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

응답(성공):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

응답(허용되지 않음):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>직접 메서드 호출 처리

요청:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

응답(성공):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status`가 설정되지 않았습니다. 이것은 성공 응답입니다.

디바이스를 사용할 수 없음 응답:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>QoS 0을 사용하는 동안 오류 발생, 파트 1

요청:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

응답:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>QoS 0을 사용하는 동안 오류 발생, 파트 2

요청:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

응답:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>다음 단계

- MQTT 5 미리 보기 API 참조를 검토하려면 [IoT Hub 데이터 평면 MQTT 5 API 참조](iot-hub-mqtt-5-reference.md)를 확인합니다.
- C# 샘플을 따라 하려면 [GitHub 샘플 리포지토리](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)를 참조합니다.