---
title: Azure IoT Hub MQTT 5 API 참조(미리 보기)
description: IoT Hub의 MQTT 5 API 참조에 대한 자세한 정보
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96603565"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT Hub 데이터 평면 MQTT 5 API 참조

이 문서에서는 IoT Hub 데이터 평면 API의 버전 2.0(api-version: `2020-10-01-preview`)에서 사용할 수 있는 작업을 정의합니다.

## <a name="operations"></a>작업

### <a name="get-twin"></a>트윈 가져오기

트윈 상태 가져오기

#### <a name="request"></a>요청

**토픽 이름:** `$iothub/twin/get`

**속성**: 없음

**페이로드**: 비어 있음

#### <a name="success-response"></a>성공 응답

**속성**: 없음

**페이로드**: 트윈

#### <a name="alternative-responses"></a>대체 응답

| 상태 | 이름 | Description |
| :----- | :--- | :---------- |
| 0100 |  잘못된 요청 | 작업 메시지의 형식이 잘못되어 처리할 수 없음 |
| 0101 |  권한 없음 | 클라이언트가 이 작업을 수행할 권한이 없음 |
| 0102 |  허용되지 않음 | 작업이 허용되지 않음 |
| 0501 |  정체됨 | 요청 속도가 SKU당 너무 높음 |
| 0502 |  할당량이 초과됨 | 현재 SKU당 일일 할당량을 초과함 |
| 0601 |  서버 오류 | 내부 서버 오류 |
| 0602 |  제한 시간 | 작업이 완료되기 전에 시간 초과됨 |
| 0603 |  서버 작업 중 | 서버 작업 중 |

#### <a name="pseudo-code-sample"></a>의사 코드 샘플

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>패치 트윈이 보고됨

패치 트윈 보고됨 상태

#### <a name="request"></a>요청

**토픽 이름:** `$iothub/twin/patch/reported`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| if-version | u64 | 아니요 |  |

**페이로드**: TwinState

#### <a name="success-response"></a>성공 응답

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 버전 | u64 | 예 | 패치가 적용된 후의 보고된 상태 버전 |

**페이로드**: 비어 있음

#### <a name="alternative-responses"></a>대체 응답

| 상태 | 이름 | Description |
| :----- | :--- | :---------- |
| 0104 |  사전 조건 실패 | 사전 조건이 충족되지 않아 요청이 취소됨 |
| 0100 |  잘못된 요청 | 작업 메시지의 형식이 잘못되어 처리할 수 없음 |
| 0101 |  권한 없음 | 클라이언트가 이 작업을 수행할 권한이 없음 |
| 0102 |  허용되지 않음 | 작업이 허용되지 않음 |
| 0501 |  정체됨 | 요청 속도가 SKU당 너무 높음 |
| 0502 |  할당량이 초과됨 | 현재 SKU당 일일 할당량을 초과함 |
| 0601 |  서버 오류 | 내부 서버 오류 |
| 0602 |  제한 시간 | 작업이 완료되기 전에 시간 초과됨 |
| 0603 |  서버 작업 중 | 서버 작업 중 |

#### <a name="pseudo-code-sample"></a>의사 코드 샘플

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>명령 수신

수신 및 처리 명령

#### <a name="message"></a>메시지

**토픽 이름:** `$iothub/commands`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| sequence-no | u64 | 예 | 메시지의 시퀀스 번호 |
| enqueued-time | time | 예 | 메시지가 시스템에 입력된 시간의 타임스탬프 |
| delivery-count | u32 | 예 | 메시지 배달이 시도된 횟수 |
| creation-time | time | 아니요 | 메시지를 만든 시간의 타임스탬프(보낸 사람이 제공) |
| message-id | 문자열 | 아니요 | 메시지 ID(보낸 사람이 제공) |
| user-id | 문자열 | 아니요 | 사용자 ID(보낸 사람이 제공) |
| correlation-id | 문자열 | 아니요 | 상관 관계 ID(보낸 사람이 제공) |
| 콘텐츠 유형 | 문자열 | 아니요 | 페이로드의 콘텐츠 형식 결정 |
| content-encoding | 문자열 | 아니요 | 페이로드의 콘텐츠 인코딩 결정 |

**페이로드**: 임의의 바이트 시퀀스

#### <a name="success-acknowledgment"></a>성공 승인

클라이언트에 의해 명령 처리가 수락되었음을 나타냄

**속성**: 없음

**페이로드**: 비어 있음

#### <a name="alternative-acknowledgments"></a>대체 승인

| 이유 코드 | 상태 | 이름 | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | 중단 | 지금 명령이 처리되지 않고 앞으로 다시 배달되어야 함을 나타냄 |
| 131 | 0100 | 거부 | 명령이 클라이언트에 의해 거부되었고 다시 시도하면 안 됨을 나타냄 |

#### <a name="pseudo-code-sample"></a>의사 코드 샘플

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>직접 메서드 수신

직접 메서드 호출 수신 및 처리

#### <a name="request"></a>요청

**토픽 이름:** `$iothub/methods/{name}`

**속성**: 없음

**페이로드**: 임의의 바이트 시퀀스

#### <a name="success-response"></a>성공 응답

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| response-code | u32 | 예 |  |

**페이로드**: 임의의 바이트 시퀀스

#### <a name="alternative-responses"></a>대체 응답

| 상태 | 이름 | Description |
| :----- | :--- | :---------- |
| 06A0 |  Unavailable | 이 연결을 통해 클라이언트에 연결할 수 없음을 나타냄 |

#### <a name="pseudo-code-sample"></a>의사 코드 샘플

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>트윈에 필요한 상태 변경 내용 수신

트윈에 필요한 상태로 업데이트 수신

#### <a name="message"></a>메시지

**토픽 이름:** `$iothub/twin/patch/desired`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 버전 | u64 | 예 | 이 업데이트와 일치하는 원하는 상태의 버전 |

**페이로드**: TwinState

#### <a name="pseudo-code-sample"></a>의사 코드 샘플

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>원격 분석 전송

원격 분석 채널에 메시지 게시 - 기본적으로 EventHubs 또는 라우팅 구성을 통해 다른 엔드포인트.

#### <a name="message"></a>메시지

**토픽 이름:** `$iothub/telemetry`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 콘텐츠 유형 | 문자열 | 아니요 | 게시된 메시지에서 `content-type` 시스템 속성으로 변환 |
| content-encoding | 문자열 | 아니요 | 게시된 메시지에서 `content-encoding` 시스템 속성으로 변환 |
| message-id | 문자열 | 아니요 | 게시된 메시지에서 `message-id` 시스템 속성으로 변환 |
| user-id | 문자열 | 아니요 | 게시된 메시지에서 `user-id` 시스템 속성으로 변환 |
| correlation-id | 문자열 | 아니요 | 게시된 메시지에서 `correlation-id` 시스템 속성으로 변환 |
| creation-time | time | 아니요 | 게시된 메시지에서 `iothub-creation-time-utc` 속성으로 변환 |

**페이로드**: 임의의 바이트 시퀀스

#### <a name="success-acknowledgment"></a>성공 승인

메시지가 원격 분석 채널에 게시됨

**속성**: 없음

**페이로드**: 비어 있음

#### <a name="alternative-acknowledgments"></a>대체 승인

| 이유 코드 | 상태 | 이름 | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | 잘못된 요청 | 작업 메시지의 형식이 잘못되어 처리할 수 없음 |
| 135 | 0101 | 권한 없음 | 클라이언트가 이 작업을 수행할 권한이 없음 |
| 131 | 0102 | 허용되지 않음 | 작업이 허용되지 않음 |
| 131 | 0601 | 서버 오류 | 내부 서버 오류 |
| 151 | 0501 | 정체됨 | 요청 속도가 SKU당 너무 높음 |
| 151 | 0502 | 할당량이 초과됨 | 현재 SKU당 일일 할당량을 초과함 |
| 131 | 0602 | 제한 시간 | 작업이 완료되기 전에 시간 초과됨 |
| 131 | 0603 | 서버 작업 중 | 서버 작업 중 |

#### <a name="pseudo-code-sample"></a>의사 코드 샘플

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>응답

### <a name="bad-request"></a>잘못된 요청

작업 메시지의 형식이 잘못되어 처리할 수 없음

**이유 코드:** `131`

**상태:** `0100`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="conflict"></a>충돌

작업이 다른 진행 중 작업과 충돌

**이유 코드:** `131`

**상태:** `0103`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | 문자열 | 아니요 | 오류에 대한 추가 진단과의 상관 관계에 대해 추적 ID |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="not-allowed"></a>허용되지 않음

작업이 허용되지 않음

**이유 코드:** `131`

**상태:** `0102`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="not-authorized"></a>권한 없음

클라이언트가 이 작업을 수행할 권한이 없음

**이유 코드:** `135`

**상태:** `0101`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | 문자열 | 아니요 | 오류에 대한 추가 진단과의 상관 관계에 대해 추적 ID |

**페이로드**: 비어 있음

### <a name="not-found"></a>찾을 수 없음

요청된 리소스가 없음

**이유 코드:** `131`

**상태:** `0504`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="not-modified"></a>수정 안 됨

제공된 사전 조건에 따라 리소스가 수정되지 않음

**이유 코드:** `0`

**상태:** `0001`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="precondition-failed"></a>사전 조건 실패

사전 조건이 충족되지 않아 요청이 취소됨

**이유 코드:** `131`

**상태:** `0104`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="quota-exceeded"></a>할당량이 초과됨

현재 SKU당 일일 할당량을 초과함

**이유 코드:** `151`

**상태:** `0502`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="resource-exhausted"></a>리소스가 소진됨

리소스에 작업을 완료할 수 있는 용량이 없음

**이유 코드:** `131`

**상태:** `0503`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="server-busy"></a>서버 작업 중

서버 작업 중

**이유 코드:** `131`

**상태:** `0603`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | 문자열 | 아니요 | 오류에 대한 추가 진단과의 상관 관계에 대해 추적 ID |

**페이로드**: 비어 있음

### <a name="server-error"></a>서버 오류

내부 서버 오류

**이유 코드:** `131`

**상태:** `0601`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | 문자열 | 아니요 | 오류에 대한 추가 진단과의 상관 관계에 대해 추적 ID |

**페이로드**: 비어 있음

### <a name="target-failed"></a>대상 실패

대상이 응답했지만 응답이 잘못되었거나 잘못된 형식임

**이유 코드:** `131`

**상태:** `06A2`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="target-timeout"></a>대상 시간 제한

대상이 요청을 완료할 때까지 기다리는 동안 시간 초과됨

**이유 코드:** `131`

**상태:** `06A1`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | 문자열 | 아니요 | 오류에 대한 추가 진단과의 상관 관계에 대해 추적 ID |
| reason | 문자열 | 아니요 | 메시지에서 구체적으로 잘못된 점에 대한 정보 포함 |

**페이로드**: 비어 있음

### <a name="target-unavailable"></a>대상 사용할 수 없음

요청을 완료하기 위해 대상에 연결 불가

**이유 코드:** `131`

**상태:** `06A0`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="throttled"></a>정체됨

요청 속도가 SKU당 너무 높음

**이유 코드:** `151`

**상태:** `0501`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="timeout"></a>제한 시간

작업이 완료되기 전에 시간 초과됨

**이유 코드:** `131`

**상태:** `0602`

**속성**:

| 이름 | 유형 | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| trace-id | 문자열 | 아니요 | 오류에 대한 추가 진단과의 상관 관계에 대해 추적 ID |

**페이로드**: 비어 있음

