---
title: Azure IoT Hub MQTT 5 지원 (미리 보기)
description: IoT Hub의 MQTT 5 지원에 대 한 자세한 정보
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603552"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>IoT Hub MQTT 5 지원 개요 (미리 보기)

**버전:** 2.0 **api-버전:** 2020-10-01-미리 보기

이 문서는 MQTT 버전 5.0 프로토콜을 통해 IoT Hub 데이터 평면 API를 정의 합니다. 이 API의 전체 정의에 대 한 [Api 참조](iot-hub-mqtt-5-reference.md) 를 참조 하세요.

## <a name="prerequisites"></a>전제 조건

- 새 IoT hub에서 [미리 보기 모드를 사용 하도록 설정](iot-hub-preview-mode.md) 하 여 mqtt 5를 시도 합니다.
- [Mqtt 5 사양](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) 에 대 한 사전 지식이 필요 합니다.

## <a name="level-of-support-and-limitations"></a>지원 수준 및 제한 사항

MQTT 5에 대 한 IoT Hub 지원은 미리 보기 상태 이며 다음과 같은 방식으로 제한 됩니다 ( `CONNACK` 별도로 명시 하지 않는 한 속성을 통해 클라이언트에 전달 됨).

- 공식 [Azure IoT Hub 장치 SDK](iot-hub-devguide-sdks.md) 는 아직 지원 되지 않습니다.
- 구독 식별자는 지원 되지 않습니다.
- 공유 구독은 지원 되지 않습니다.
- `RETAIN`는 지원되지 않습니다.
- `Maximum QoS`이(가) `1`인 경우
- `Maximum Packet Size` 는 `256 KiB` (작업당 추가 제한 사항이 적용 됨)입니다.
- 할당 된 클라이언트 Id는 지원 되지 않습니다.
- `Keep Alive` 는 `19 min` (선거의 check의 최대 지연 시간)로 제한 됩니다 `28.5 min` .
- `Topic Alias Maximum`이(가) `10`인 경우
- `Response Information` 지원 되지 않음 `CONNACK` `Response Information` `CONNECT` 에 속성이 포함 된 경우에도 속성을 반환 하지 않습니다 `Request Response Information` .
- `Receive Maximum` ( `PUBLISH` 클라이언트-서버 방향)에서 허용 되는 승인 되지 않은 처리 되지 않은 패킷의 최대 수 `QoS: 1` 는 `16` 입니다.
- 단일 클라이언트는 구독을 초과 하지 않을 수 있습니다 `50` .
  제한에 도달 하면 `SUBACK` `0x97` 구독에 대해 (할당량 초과) 이유 코드가 반환 됩니다.

## <a name="connection-lifecycle"></a>연결 수명 주기

### <a name="connection"></a>연결

이 API를 사용 하 여 IoT Hub에 클라이언트를 연결 하려면 MQTT 5 사양에 따라 연결을 설정 합니다.
클라이언트 `CONNECT` 는 성공한 TLS 핸드셰이크 후 30 초 이내에 패킷을 전송 해야 합니다. 그렇지 않으면 서버에서 연결을 닫습니다.
패킷의 예는 `CONNECT` 다음과 같습니다.

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

- `Authentication Method` 속성이 필요 하며 사용 되는 인증 방법을 식별 합니다. 인증 방법에 대 한 자세한 내용은 [인증](#authentication)을 참조 하세요.
- `Authentication Data` 속성 처리는에 따라 다릅니다 `Authentication Method` . `Authentication Method`가로 설정 된 경우 `SAS` `Authentication Data` 는 필수 이며 유효한 서명을 포함 해야 합니다. 인증 데이터에 대 한 자세한 내용은 [인증](#authentication)을 참조 하세요.
- `api-version` 이 사양에 적용 하려면 속성이 필수 이며이 사양의 헤더에 제공 된 API 버전 값으로 설정 되어야 합니다.
- `host` 속성은 테 넌 트의 호스트 이름을 정의 합니다. TLS 핸드셰이크 중에 클라이언트 Hello 레코드에 SNI 확장이 표시 되지 않은 경우 필요 합니다.
- `sas-at` 연결 시간을 정의 합니다.
- `sas-expiry` 제공 된 SAS의 만료 시간을 정의 합니다.
- `client-agent` 필요에 따라 연결을 만드는 클라이언트에 대 한 정보를 전달 합니다.

> [!NOTE]
> `Authentication Method` 대문자 이름이 지정 된 사양의 모든 기타 속성은 MQTT 5의 첫 번째 클래스 속성입니다. MQTT 5 사양에 자세히 설명 되어 있습니다. `api-version` 대시 케이스의 기타 속성은 IoT Hub API에 특정 한 사용자 속성입니다.

IoT Hub `CONNACK` 는 인증으로 완료 되 고 연결을 지원 하기 위해 데이터를 인출 하면 패킷으로 응답 합니다. 연결이 성공적으로 설정 된 경우는 `CONNACK` 다음과 같습니다.

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

이러한 `CONNACK` 패킷 속성은 [mqtt 5 사양을](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080)따릅니다. IoT Hub 기능을 반영 합니다.

### <a name="authentication"></a>인증

`Authentication Method`클라이언트의 속성은 `CONNECT` 이 연결에 대해 사용 하는 인증의 종류를 정의 합니다.

- `SAS`-공유 액세스 서명은 `CONNECT` 의 속성에 제공 됩니다. `Authentication Data`
- `X509` -클라이언트가 클라이언트 인증서 인증에 의존 합니다.

 인증 방법이 IoT Hub의 클라이언트 구성 방법과 일치 하지 않으면 인증에 실패 합니다.

> [!NOTE]
> 이 API `Authentication Method` 를 사용 하려면 패킷에 속성을 설정 해야 `CONNECT` 합니다. `Authentication Method`속성이 제공 되지 않으면 응답으로 연결이 실패 `Bad Request` 합니다.

이전 API 버전에서 사용 되는 사용자 이름/암호 인증은 지원 되지 않습니다.

#### <a name="sas"></a>SAS

SAS 기반 인증을 사용 하는 경우 클라이언트는 연결 컨텍스트의 서명을 제공 해야 합니다. 이는 MQTT 연결의 신뢰성을 증명 합니다. 서명은 IoT Hub의 클라이언트 구성에서 두 인증 키 중 하나를 기반으로 하거나 [공유 액세스 정책의](iot-hub-devguide-security.md)두 공유 액세스 키 중 하나를 기반으로 해야 합니다.

서명할 문자열은 다음과 같이 구성 되어야 합니다.

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` 는 SNI 확장 (TLS 핸드셰이크 중 클라이언트 Hello 레코드에서 클라이언트에서 표시) 또는 `host` 패킷의 사용자 속성에서 파생 됩니다 `CONNECT` .
- `Client Id` 는 패킷의 클라이언트 식별자입니다 `CONNECT` .
- `sas-policy` -있는 경우 인증에 사용 되는 IoT Hub 액세스 정책을 정의 합니다. 이는 패킷에서 사용자 속성으로 인코딩됩니다 `CONNECT` . 선택 사항: 생략 하면 장치 레지스트리의 인증 설정이 대신 사용 됩니다.
- `sas-at` -있는 경우 현재 연결 시간을 지정 합니다. 이 클래스는 패킷에서 형식의 사용자 속성으로 인코딩됩니다 `time` `CONNECT` .
- `sas-expiry` 인증에 대 한 만료 시간을 정의 합니다. `time`패킷에 입력 된 사용자 속성 `CONNECT` 입니다. 이 속성은 필수입니다.

선택적 매개 변수의 경우 생략 하는 경우 서명 하는 문자열에서 빈 문자열을 대신 사용 해야 합니다.

HMAC-SHA256은 장치의 대칭 키 중 하나를 기반으로 문자열을 해시 하는 데 사용 됩니다. 해시 값은 속성 값으로 설정 됩니다 `Authentication Data` .

#### <a name="x509"></a>X509

`Authentication Method`속성이로 설정 된 경우 `X509` IoT Hub는 제공 된 클라이언트 인증서에 따라 연결을 인증 합니다.

#### <a name="reauthentication"></a>재인증

SAS 기반 인증을 사용 하는 경우 수명이 짧은 인증 토큰을 사용 하는 것이 좋습니다. 연결을 인증 된 상태로 유지 하 고 만료로 인해 연결 끊기를 방지 하려면 클라이언트는 `AUTH` `Reason Code: 0x19` (재인증)로 패킷을 전송 하 여 다시 인증 해야 합니다.

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

규칙:

- `Authentication Method` 초기 인증에 사용 되는 것과 같아야 합니다.
- 공유 액세스 정책을 기반으로 하는 SAS를 사용 하 여 연결이 원래 인증 된 경우 재인증에 사용 되는 서명은 동일한 정책을 기반으로 해야 합니다.

재인증이 성공 하면 IoT Hub `AUTH` `Reason Code: 0x00` (성공)로 패킷을 보냅니다. 그렇지 않으면 IoT Hub는 `DISCONNECT` `Reason Code: 0x87` (권한 없음)로 패킷을 전송 하 고 연결을 닫습니다.

### <a name="disconnection"></a>끊어진

다음과 같은 이유로 서버에서 클라이언트 연결을 끊을 수 있습니다.

- 클라이언트는 부정 승인 (또는 응답)을 직접 사용 하 여에 응답할 수 없는 방식으로 오동작 됩니다.
- 서버에서 연결 상태를 최신으로 유지 하는 데 실패 합니다.
- 동일한 id의 클라이언트가 연결 되었습니다.

서버는 MQTT 5.0 사양에 정의 된 이유 코드와의 연결을 끊을 수 있습니다. 주목할 만한 광고:

- `135` (권한 없음) 재인증에 실패 하는 경우 현재 SAS 토큰이 만료 되거나 장치의 자격 증명이 변경 되는 경우
- `142` 동일한 클라이언트 id를 사용 하는 새 연결이 열린 경우에는 세션을 통해 수행 됩니다.
- `159` (연결 비율이 초과 됨) IoT hub에 대 한 연결 빈도가 다음을 초과 하는 경우  
- `131` (구현 관련 오류)는이 API에 정의 된 모든 사용자 지정 오류에 사용 됩니다. `status` 및 `reason` 속성은 연결 끊기 원인에 대 한 추가 정보를 전달 하는 데 사용 됩니다 (자세한 내용은 [응답](#response) 참조).

## <a name="operations"></a>작업

이 API의 모든 기능은 작업으로 표현 됩니다. 다음은 원격 분석 작업 전송의 예입니다.

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

이 API에 대 한 전체 작업 사양은 [Api 참조](iot-hub-mqtt-5-reference.md)를 참조 하세요.

> [!NOTE]
> 이 사양의 모든 샘플은 클라이언트의 관점에서 표시 됩니다. 서명 `->` 이란 클라이언트 전송 패킷을 수신 하는 것을 의미 `<-` 합니다.

### <a name="message-topics-and-subscriptions"></a>메시지 항목 및 구독

이 API에서 작업의 메시지에 사용 되는 항목은로 시작 `$iothub/` 합니다.
MQTT broker 의미 체계가 이러한 작업에 적용 되지 않습니다. 자세한 내용은 "[로 \$ 시작 ](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246)하는 항목"을 참조 하세요.
`$iothub/`이 API에 정의 되지 않은로 시작 하는 항목은 지원 되지 않습니다.

- 정의 되지 않은 토픽으로 메시지를 보내면 `Not Found` 응답이 발생 합니다 (아래 세부 정보는 [응답](#response) 참조).
- 정의 되지 않은 토픽을 구독 하면이 `SUBACK` 포함 `Reason Code: 0x8F` 됩니다 (토픽 필터 유효 하지 않음).

토픽 이름 및 속성 이름은 대/소문자를 구분 하므로 정확히 일치 해야 합니다. 예를 들어는 `$iothub/telemetry/` 가 이지만은 지원 되지 않습니다 `$iothub/telemetry` .

> [!NOTE]
> 의 구독에서 와일드 카드는 `$iothub/..` 지원 되지 않습니다. 즉, 클라이언트는 또는을 구독할 수 `$iothub/+` 없습니다 `$iothub/#` . 이 작업을 수행 하려고 하면 `SUBACK` `Reason Code: 0xA2` (와일드 카드 구독이 지원 되지 않음)이 발생 합니다. `+`항목 이름에 포함 된 작업의 경우에는 경로 매개 변수 대신 단일 세그먼트 와일드 카드 ()만 지원 됩니다.

### <a name="interaction-types"></a>상호 작용 형식

이 API의 모든 작업은 다음 두 가지 상호 작용 형식 중 하나를 기반으로 합니다.

- 선택적 승인 (MessageAck)이 포함 된 메시지
- Request-Response (ReqRep)

작업은 방향에 따라서도 달라 집니다 (exchange의 초기 메시지 방향에 따라 결정 됨).

- 클라이언트-서버 (c2s)
- 서버-클라이언트 (s2c)

예를 들어, 원격 분석 보내기는 "승인 포함 메시지" 유형의 클라이언트-서버 작업 이지만 직접 메서드 처리는 Request-Response 유형의 서버 간 작업입니다.

#### <a name="message-acknowledgement-interactions"></a>메시지 승인 상호 작용

선택적 승인 (MessageAck) 상호 작용이 있는 메시지는 `PUBLISH` MQTT의 및 패킷 교환으로 표현 됩니다 `PUBACK` . 승인은 선택 사항이 며 발신자는를 사용 하 여 패킷을 전송 하 여 요청 하지 않도록 선택할 수 있습니다 `PUBLISH` `QoS: 0` .

> [!NOTE]
> `PUBACK`클라이언트에서 선언 된 대로 패킷의 속성을 잘라내야 하는 경우 `Maximum Packet Size` IoT Hub는 지정 된 제한에 맞게 사용자 속성 수를 유지 합니다. 처음에 나열 된 사용자 속성은 나중에 나열 된 것 보다 더 높은 전송할 가능성이 있습니다. `Reason String` 속성의 우선 순위가 가장 높습니다.

##### <a name="example-of-simple-messageack-interaction"></a>간단한 MessageAck 상호 작용의 예

메시지:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

승인 (성공):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Request-Response 상호 작용

ReqRep (Request-Response) 상호 작용에서 요청 및 응답은 모두 `PUBLISH` 로 패킷을 변환 `QoS: 0` 합니다.

`Correlation Data` 속성은 둘 다에서 설정 해야 하며 응답 패킷을 요청 패킷과 일치 시키는 데 사용 됩니다.

이 API는 `$iothub/responses` 모든 ReqRep 작업에 대해 단일 응답 항목을 사용 합니다. 클라이언트-서버 작업에 대 한이 항목에 대 한 구독/구독 취소는 필요 하지 않습니다. 서버는 모든 클라이언트를 구독 하는 것으로 간주 합니다.

##### <a name="example-of-simple-reqrep-interaction"></a>간단한 ReqRep 상호 작용 예제

요청:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

응답 (성공):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep 상호 작용 `PUBLISH` `QoS: 1` 은 요청 또는 응답 메시지로를 사용 하는 패킷을 지원 하지 않습니다. 요청 `PUBLISH` 을 보내면 `Bad Request` 응답이 반환 됩니다.

속성에서 지원 되 `Correlation Data` 는 최대 길이는 16 바이트입니다. `Correlation Data` `PUBLISH` 패킷의 속성이 16 바이트 보다 긴 값으로 설정 된 경우 IoT Hub가 `DISCONNECT` 결과와 함께 전송 되 `Bad Request` 고 연결을 닫습니다. 이 동작은이 API 내에서 교환 되는 패킷에만 적용 됩니다.

> [!NOTE]
> 상관 관계 데이터는 임의의 바이트 시퀀스입니다. 예를 들어, UTF-8 문자열이 보장 되지 않습니다.
>
> ReqRep는 응답에 미리 정의 된 항목을 사용 합니다. 요청 패킷의 응답 항목 속성 `PUBLISH` (보낸 사람에 의해 설정 된 경우)은 무시 됩니다.

IoT Hub 모든 클라이언트-서버 ReqRep 작업에 대 한 응답 항목에 자동으로 클라이언트를 구독 합니다. 클라이언트가 응답 항목에서 명시적으로 구독을 취소 하는 경우에도 구독을 자동으로은 IoT Hub. 서버-클라이언트 ReqRep 상호 작용의 경우 장치를 구독 하는 데 여전히 필요 합니다.

### <a name="message-properties"></a>메시지 속성

작업 속성 (시스템 또는 사용자 정의)은 MQTT 5에서 패킷 속성으로 표현 됩니다.

사용자 속성 이름은 대/소문자를 구분 하므로 정의 된 대로 정확 하 게 입력 해야 합니다. 예를 들어는 `Trace-ID` 가 이지만은 지원 되지 않습니다 `trace-id` .

사양의 외부에 사용자 속성이 있고 접두사가 없는 요청 `@` 에는 오류가 발생 합니다.

시스템 속성은 첫 번째 클래스 속성 (예: `Content Type` ) 또는 사용자 속성으로 인코딩됩니다. 사양은 지원 되는 시스템 속성의 포괄적인 목록을 제공 합니다.
모든 첫 번째 클래스 속성은 사양에 명시적으로 지정 된 경우를 제외 하 고는 무시 됩니다.

사용자 정의 속성이 허용 되는 경우 해당 이름은 형식 뒤에와 야 합니다 `@{property name}` . 사용자 정의 속성은 유효한 UTF-8 문자열 값만 지원 합니다. 예를 들어 `MyProperty1` 값이 인 속성은 `15` name 및 value를 사용 하 여 사용자 속성으로 인코딩해야 합니다 `@MyProperty` `15` .

IoT Hub 사용자 속성을 인식 하지 못하는 경우 오류로 간주 되 고 IoT Hub `PUBACK` `Reason Code: 0x83` (구현 관련 오류) 및 `status: 0100` (잘못 된 요청)으로 응답 합니다. 승인이 요청 되지 않은 경우 (QoS: 0) `DISCONNECT` 동일한 오류가 있는 패킷이 다시 전송 되 고 연결이 종료 됩니다.

이 API는 다음과 같은 데이터 형식을 정의 합니다 `string` .

- `time`: 이후의 시간 (밀리초) `1970-01-01T00:00:00.000Z` 입니다. 예를 들어 `1600987195320` ,의 경우 `2020-09-24T22:39:55.320Z`
- `u32`: 부호 없는 32 비트 정수 숫자,
- `u64`: 부호 없는 64 비트 정수 숫자,
- `i32`: 부호 있는 32 비트 정수 숫자입니다.

### <a name="response"></a>응답

상호 작용 `Success` 으로 인해,, `Bad Request` `Not Found` 및 다른 결과가 발생할 수 있습니다.
결과는 사용자 속성 별로 구분 됩니다 `status` . `Reason Code``PUBACK`패킷 (MessageAck 상호 작용의 경우)은 `status` 가능한 경우 의미에서 일치 합니다.

> [!NOTE]
> 클라이언트가 연결 패킷에를 지정 하는 경우 `Request Problem Information: 0` `PUBACK` 속성을 포함 하 여 mqtt 5 사양을 따르도록 사용자 속성이 패킷에 전송 되지 않습니다 `status` . 이 경우 클라이언트는 여전히를 사용 하 여 `Reason Code` 긍정 또는 부정적 인지 여부를 확인할 수 있습니다. 

모든 상호 작용에는 기본 (또는 성공)이 있습니다. `Reason Code` `0` 및 `status` 속성은 "not set"입니다. 그렇지 않은 경우:

- MessageAck 상호 작용의 경우은 `PUBACK` `Reason Code` 0X0 (성공)이 아닌를 가져옵니다. `status` 결과를 보다 명확 하 게 나타내기 위해 속성이 있을 수 있습니다.
- ReqRep 상호 작용의 경우 응답은 `PUBLISH` `status` 속성 집합을 가져옵니다.
- 를 사용 하 여 MessageAck 상호 작용에 응답할 수 있는 방법이 없기 때문에 `QoS: 0` `DISCONNECT` 응답 정보를 사용 하는 대신 패킷이 전달 된 후 연결이 끊어집니다.

예제:

잘못 된 요청 (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

권한 없음 (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

권한 없음 (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

필요한 경우 IoT Hub에서 다음 사용자 속성을 설정 합니다.

- `status` -작업의 상태에 대 한 확장 코드를 IoT Hub 합니다. 이 코드를 사용 하 여 결과를 구분할 수 있습니다.
- `trace-id` – 작업의 추적 ID입니다. IoT Hub는 내부 조사에 사용할 수 있는 작업과 관련 된 추가 진단을 유지할 수 있습니다.
- `reason` -작업이 속성으로 표시 된 상태에서 종료 되는 이유에 대 한 추가 정보를 제공 하는 사람이 읽을 수 있는 메시지 `status` 입니다.

> [!NOTE]
> 클라이언트가 `Maximum Packet Size` 연결 패킷에서 매우 작은 값으로 속성을 설정 하는 경우 일부 사용자 속성은 맞지 않고 패킷에 나타나지 않습니다.
> 
> `reason` 는 사용자 에게만 사용 되며 클라이언트 논리에서 사용 하면 안 됩니다. 이 API를 사용 하면 경고 또는 버전 변경을 하지 않고 언제 든 지 메시지를 변경할 수 있습니다.
>
> 클라이언트 `RequestProblemInformation: 0` 에서 연결 패킷을 보내면 사용자 속성이 [mqtt 5 사양의](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053)승인에 포함 되지 않습니다.

#### <a name="status-code"></a>상태 코드

`status` 속성은 작업에 대 한 상태 코드를 전달 합니다. 컴퓨터 읽기 효율성에 최적화 되어 있습니다.
과 같은 문자열에서 16 진수로 인코딩된 2 바이트 부호 없는 정수로 구성 됩니다 `0501` .
코드 구조 (비트 맵):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

첫 번째 바이트가 플래그에 사용 됩니다.

- 비트 0과 1은 결과 유형을 표시 합니다.
  - `00` -성공
  - `01` -클라이언트 오류
  - `10` -서버 오류
- 비트 2: `1` 오류를 다시 시도할 수 있음을 나타냅니다.
- bits 3 ~ 7은 예약 되어 있으며로 설정 되어야 합니다. `0`

두 번째 바이트는 실제 고유 응답 코드를 포함 합니다. 플래그가 다른 오류 코드는 같은 두 번째 바이트 값을 가질 수 있습니다. 예를 들어,,, `0001` `0101` 와 같은 `0201` 의미를 `0301` 갖는 오류 코드가 있을 수 있습니다.

예를 들어 `Too Many Requests` 는 클라이언트 이며, 자체 코드가 인 다시 시도 가능한 오류입니다 `1` . 해당 값은 `0000 0101 0000 0001` 또는 `0x0501` 입니다.

클라이언트는 형식 비트를 사용 하 여 작업이 성공적으로 완료 되었는지 여부를 식별할 수 있습니다. 클라이언트는 재시도 가능 비트를 사용 하 여 작업을 다시 시도 하는 것이 적절 한지 여부를 결정할 수도 있습니다.

## <a name="recommendations"></a>권장 사항

### <a name="session-management"></a>세션 관리

`CONNACK` 패킷은 `Session Present` 서버에서 이전에 만든 세션을 복원 했는지 여부를 나타내는 속성을 전달 합니다. 이 속성을 사용 하 여 구독을 이전에 수행 했기 때문에 항목을 구독 하거나 구독을 건너뛸지 여부를 파악할 수 있습니다.

을 사용 하려면 `Session Present` 클라이언트에서 구독을 추적 해야 합니다 (즉, 전송 된 `SUBSCRIBE` 패킷과 `SUBACK` 성공한 이유 코드와 함께 수신). 또는 단일 exchange의 모든 항목을 구독 해야 합니다 `SUBSCRIBE` / `SUBACK` . 그렇지 않고 클라이언트에서 두 개의 `SUBSCRIBE` 패킷을 전송 하 고 그 중 하나만 서버에서 성공적으로 처리 되는 경우 서버 `Session Present: 1` 는 `CONNACK` 클라이언트의 구독 일부만 수락 하는 동안에서 통신 합니다.

이전 버전의 클라이언트가 모든 항목을 구독 하지 못하는 경우를 방지 하려면 클라이언트 동작이 변경 될 때 (예: 펌웨어 업데이트의 일부로) 무조건 구독 하는 것이 좋습니다. 또한 허용 되는 최대 구독 수를 사용 하 여 오래 된 구독이 남아 있지 않도록 하려면 더 이상 사용 하지 않는 구독을 명시적으로 구독 취소 합니다.

### <a name="batching"></a>일괄 처리

메시지 일괄 처리를 전송 하는 특별 한 형식은 없습니다. Tls 및 네트워킹에서 리소스를 많이 사용 하는 작업의 오버 헤드를 줄이려면 `PUBLISH` `PUBACK` `SUBSCRIBE` 기본 tls/TCP 스택으로 전달 하기 전에 패킷 (,, 등)을 함께 연결 합니다. 또한 클라이언트는 "batch" 내에서 토픽 별칭을 보다 쉽게 만들 수 있습니다.

- 연결에 대 한 첫 번째 패킷에 전체 항목 이름을 넣고 `PUBLISH` 토픽 별칭을 연결 합니다.
- 동일한 항목에 대 한 다음 패킷을 빈 항목 이름 및 토픽 별칭 속성으로 둡니다.

## <a name="migration"></a>마이그레이션

이 섹션에서는 [이전 MQTT api](iot-hub-mqtt-support.md)와 비교 하 여 api의 변경 내용을 나열 합니다.

- 전송 프로토콜은 MQTT 5입니다. 이전에-MQTT 3.1.1.
- SAS 인증에 대 한 컨텍스트 정보는 `CONNECT` 서명과 함께 인코딩 되는 대신 패킷에 직접 포함 됩니다.
- 인증 방법을 사용 하 여 사용 된 인증 방법을 표시 합니다.
- 공유 액세스 서명은 인증 데이터 속성에 저장 됩니다. 이전에 암호 필드를 사용 했습니다.
- 작업에 대 한 항목은 다음과 같습니다.
  - 원격 분석: `$iothub/telemetry` 대신 `devices/{Client Id}/messages/events` ,
  - 명령: `$iothub/commands` 대신 `devices/{Client Id}/messages/devicebound` ,
  - 패치 쌍이 보고 됨: `$iothub/twin/patch/reported` 대신 `$iothub/twin/PATCH/properties/reported`
  - 변경 된 쌍의 필요한 상태 변경: `$iothub/twin/patch/desired` 대신 `$iothub/twin/PATCH/properties/desired` .
- 클라이언트-서버 요청-응답 작업의 응답 항목이 필요 하지 않습니다.
- 사용자 속성은 토픽 이름 세그먼트의 인코딩 속성 대신 사용 됩니다.
- 속성 이름은 특수 접두사가 있는 약어 대신 "대시-사례" 명명 규칙의 철자입니다. 이제 사용자 정의 속성에 접두사가 필요 합니다. 예를 들어는 `$.mid` 이제이가 됩니다 `message-id` `myProperty1` `@myProperty1` .
- 상관 관계 데이터 속성은 `$rid` 항목에서 인코딩된 속성이 아닌 요청-응답 작업에 대 한 요청 및 응답 메시지를 상호 연결 하는 데 사용 됩니다.
- `iothub-connection-auth-method` 속성이 더 이상 원격 분석 이벤트에 기록 되지 않습니다.
- C2D 명령은 장치에서 구독이 없을 때 제거 되지 않습니다. 장치가 구독 되거나 만료 될 때까지 큐에 대기 상태를 유지 합니다.

## <a name="examples"></a>예제

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

대체 승인 (제한 됨):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Get 쌍의 상태를 보냅니다.

요청:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

응답 (성공):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

응답 (허용 되지 않음):

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

### <a name="handle-direct-method-call"></a>직접 메서드 호출을 처리 합니다.

요청:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

응답 (성공):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` 설정 되지 않음-성공 응답입니다.

장치를 사용할 수 없음 응답:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>QoS 0을 사용 하는 동안 오류가 발생 했습니다. 1 부

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

### <a name="error-while-using-qos-0-part-2"></a>QoS 0을 사용 하는 동안 오류 발생, 2 부

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

- MQTT 5 미리 보기 API 참조를 검토 하려면 [IoT Hub 데이터 평면 MQTT 5 api 참조](iot-hub-mqtt-5-reference.md)를 참조 하세요.
- C # 샘플을 따르려면 [GitHub 샘플 리포지토리](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp)를 참조 하세요.