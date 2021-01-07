---
title: Azure IoT Hub MQTT 5 API 참조 (미리 보기)
description: IoT Hub의 MQTT 5 API 참조에 대해 알아봅니다.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603565"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>IoT Hub 데이터 평면 MQTT 5 API 참조

이 문서에서는 `2020-10-01-preview` IoT Hub 데이터 평면 api의 버전 2.0 (api-version:)에서 사용할 수 있는 작업을 정의 합니다.

## <a name="operations"></a>작업

### <a name="get-twin"></a>쌍 가져오기

쌍 상태 가져오기

#### <a name="request"></a>요청

**항목 이름:**`$iothub/twin/get`

**속성**: 없음

**페이로드**: 비어 있음

#### <a name="success-response"></a>성공 응답

**속성**: 없음

**페이로드**: 쌍

#### <a name="alternative-responses"></a>대체 응답

| 상태 | Name | Description |
| :----- | :--- | :---------- |
| 0100 |  잘못된 요청 | 작업 메시지의 형식이 잘못 되어 처리할 수 없습니다. |
| 0101 |  권한 없음 | 클라이언트에 작업을 수행할 수 있는 권한이 없습니다. |
| 0102 |  허용되지 않음 | 작업을 수행할 수 없습니다. |
| 0501 |  정체됨 | SKU 당 요청 비율이 너무 높습니다. |
| 0502 |  할당량 초과 | 현재 SKU 당 일일 할당량을 초과 했습니다. |
| 0601 |  서버 오류 | 내부 서버 오류 |
| 0602 |  제한 시간 | 작업이 완료 되기 전에 시간 초과 되었습니다. |
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

### <a name="patch-twin-reported"></a>패치 쌍이 보고 됨

패치 쌍의 보고 된 상태

#### <a name="request"></a>요청

**항목 이름:**`$iothub/twin/patch/reported`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| -version | u64 | no |  |

**페이로드**: TwinState

#### <a name="success-response"></a>성공 응답

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 버전 | u64 | 예 | 패치가 적용 된 후의 보고 된 상태 버전 |

**페이로드**: 비어 있음

#### <a name="alternative-responses"></a>대체 응답

| 상태 | Name | Description |
| :----- | :--- | :---------- |
| 0104 |  사전 조건 실패 | 사전 조건이 충족 되지 않아 요청이 취소 되었습니다. |
| 0100 |  잘못된 요청 | 작업 메시지의 형식이 잘못 되어 처리할 수 없습니다. |
| 0101 |  권한 없음 | 클라이언트에 작업을 수행할 수 있는 권한이 없습니다. |
| 0102 |  허용되지 않음 | 작업을 수행할 수 없습니다. |
| 0501 |  정체됨 | SKU 당 요청 비율이 너무 높습니다. |
| 0502 |  할당량 초과 | 현재 SKU 당 일일 할당량을 초과 했습니다. |
| 0601 |  서버 오류 | 내부 서버 오류 |
| 0602 |  제한 시간 | 작업이 완료 되기 전에 시간 초과 되었습니다. |
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

### <a name="receive-commands"></a>수신 명령

수신 및 처리 명령

#### <a name="message"></a>메시지

**항목 이름:**`$iothub/commands`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 시퀀스-아니요 | u64 | 예 | 메시지의 시퀀스 번호 |
| 큐에 넣은 시간 | time | 예 | 메시지가 시스템에 입력 된 시간에 대 한 타임 스탬프입니다. |
| delivery-count | u32 | 예 | 메시지 배달이 시도 된 횟수 |
| creation-time | time | no | 메시지를 만든 시간에 대 한 타임 스탬프 (보낸 사람에 의해 제공 됨) |
| message-id | 문자열 | no | 메시지 id (보낸 사람에 의해 제공 됨) |
| user-id | 문자열 | no | 사용자 id (보낸 사람에 의해 제공 됨) |
| correlation-id | 문자열 | no | 상관 관계 id (보낸 사람에 의해 제공 됨) |
| 콘텐츠 유형 | 문자열 | no | 페이로드의 콘텐츠 형식을 결정 합니다. |
| content-encoding | 문자열 | no | 페이로드의 콘텐츠 인코딩을 결정 합니다. |

**페이로드**: 임의의 바이트 시퀀스

#### <a name="success-acknowledgment"></a>성공 승인

클라이언트에서 명령을 처리할 때 명령을 수락 했음을 나타냅니다.

**속성**: 없음

**페이로드**: 비어 있음

#### <a name="alternative-acknowledgments"></a>대체 승인

| 이유 코드 | 상태 | Name | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | 중단 | 지금은 명령이 처리 되지 않으며 앞으로 다시 배달 되어야 함을 나타냅니다. |
| 131 | 0100 | 거부 | 명령이 클라이언트에 의해 거부 되었음을 나타내며 다시 시도 하면 안 됩니다. |

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

### <a name="receive-direct-methods"></a>직접 메서드 받기

직접 메서드 호출 수신 및 처리

#### <a name="request"></a>요청

**항목 이름:**`$iothub/methods/{name}`

**속성**: 없음

**페이로드**: 임의의 바이트 시퀀스

#### <a name="success-response"></a>성공 응답

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 응답-코드 | u32 | 예 |  |

**페이로드**: 임의의 바이트 시퀀스

#### <a name="alternative-responses"></a>대체 응답

| 상태 | Name | Description |
| :----- | :--- | :---------- |
| 06A0 |  Unavailable | 이 연결을 통해 클라이언트에 연결할 수 없음을 나타냅니다. |

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

### <a name="receive-twin-desired-state-changes"></a>수신 쌍 Desired 상태 변경 내용

쌍의 원하는 상태로 업데이트 받기

#### <a name="message"></a>메시지

**항목 이름:**`$iothub/twin/patch/desired`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 버전 | u64 | 예 | 이 업데이트와 일치 하는 필요한 상태의 버전 |

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

### <a name="send-telemetry"></a>원격 분석 보내기

원격 분석 채널에 메시지 게시-EventHubs 또는 라우팅 구성을 통해 다른 끝점.

#### <a name="message"></a>메시지

**항목 이름:**`$iothub/telemetry`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 콘텐츠 유형 | 문자열 | no | 게시 된 `content-type` 메시지에서 시스템 속성으로 변환 합니다. |
| content-encoding | 문자열 | no | 게시 된 `content-encoding` 메시지에서 시스템 속성으로 변환 합니다. |
| message-id | 문자열 | no | 게시 된 `message-id` 메시지에서 시스템 속성으로 변환 합니다. |
| user-id | 문자열 | no | 게시 된 `user-id` 메시지에서 시스템 속성으로 변환 합니다. |
| correlation-id | 문자열 | no | 게시 된 `correlation-id` 메시지에서 시스템 속성으로 변환 합니다. |
| creation-time | time | no | 게시 된 `iothub-creation-time-utc` 메시지에 대 한 속성을로 변환 합니다. |

**페이로드**: 임의의 바이트 시퀀스

#### <a name="success-acknowledgment"></a>성공 승인

원격 분석 채널에 메시지를 게시 했습니다.

**속성**: 없음

**페이로드**: 비어 있음

#### <a name="alternative-acknowledgments"></a>대체 승인

| 이유 코드 | 상태 | Name | Description |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | 잘못된 요청 | 작업 메시지의 형식이 잘못 되어 처리할 수 없습니다. |
| 135 | 0101 | 권한 없음 | 클라이언트에 작업을 수행할 수 있는 권한이 없습니다. |
| 131 | 0102 | 허용되지 않음 | 작업을 수행할 수 없습니다. |
| 131 | 0601 | 서버 오류 | 내부 서버 오류 |
| 151 | 0501 | 정체됨 | SKU 당 요청 비율이 너무 높습니다. |
| 151 | 0502 | 할당량 초과 | 현재 SKU 당 일일 할당량을 초과 했습니다. |
| 131 | 0602 | 제한 시간 | 작업이 완료 되기 전에 시간 초과 되었습니다. |
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

작업 메시지의 형식이 잘못 되어 처리할 수 없습니다.

**이유 코드:**`131`

**상태:**`0100`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="conflict"></a>충돌

작업이 진행 중인 다른 작업과 충돌 합니다.

**이유 코드:**`131`

**상태:**`0103`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 추적 id | 문자열 | no | 오류에 대 한 추가 진단과의 상관 관계에 대 한 추적 ID |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="not-allowed"></a>허용되지 않음

작업을 수행할 수 없습니다.

**이유 코드:**`131`

**상태:**`0102`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="not-authorized"></a>권한 없음

클라이언트에 작업을 수행할 수 있는 권한이 없습니다.

**이유 코드:**`135`

**상태:**`0101`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 추적 id | 문자열 | no | 오류에 대 한 추가 진단과의 상관 관계에 대 한 추적 ID |

**페이로드**: 비어 있음

### <a name="not-found"></a>찾을 수 없음

요청 된 리소스가 없습니다.

**이유 코드:**`131`

**상태:**`0504`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="not-modified"></a>수정 안 됨

제공 된 전제 조건에 따라 리소스가 수정 되지 않았습니다.

**이유 코드:**`0`

**상태:**`0001`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="precondition-failed"></a>사전 조건 실패

사전 조건이 충족 되지 않아 요청이 취소 되었습니다.

**이유 코드:**`131`

**상태:**`0104`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="quota-exceeded"></a>할당량 초과

현재 SKU 당 일일 할당량을 초과 했습니다.

**이유 코드:**`151`

**상태:**`0502`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="resource-exhausted"></a>리소스 부족

리소스에 작업을 완료할 수 있는 용량이 없습니다.

**이유 코드:**`131`

**상태:**`0503`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="server-busy"></a>서버 작업 중

서버 작업 중

**이유 코드:**`131`

**상태:**`0603`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 추적 id | 문자열 | no | 오류에 대 한 추가 진단과의 상관 관계에 대 한 추적 ID |

**페이로드**: 비어 있음

### <a name="server-error"></a>서버 오류

내부 서버 오류

**이유 코드:**`131`

**상태:**`0601`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 추적 id | 문자열 | no | 오류에 대 한 추가 진단과의 상관 관계에 대 한 추적 ID |

**페이로드**: 비어 있음

### <a name="target-failed"></a>대상 실패

대상이 응답 했지만 응답이 잘못 되었거나 형식이 잘못 되었습니다.

**이유 코드:**`131`

**상태:**`06A2`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="target-timeout"></a>대상 시간 제한

대상이 요청을 완료할 때까지 기다리는 동안 제한 시간이 초과 되었습니다.

**이유 코드:**`131`

**상태:**`06A1`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 추적 id | 문자열 | no | 오류에 대 한 추가 진단과의 상관 관계에 대 한 추적 ID |
| reason | 문자열 | no | 메시지에 대해 잘못 된 정보에 대 한 정보를 포함 합니다. |

**페이로드**: 비어 있음

### <a name="target-unavailable"></a>대상 사용할 수 없음

대상에 요청을 완료할 수 없습니다.

**이유 코드:**`131`

**상태:**`06A0`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="throttled"></a>정체됨

SKU 당 요청 비율이 너무 높습니다.

**이유 코드:**`151`

**상태:**`0501`

**속성**: 없음

**페이로드**: 비어 있음

### <a name="timeout"></a>제한 시간

작업이 완료 되기 전에 시간 초과 되었습니다.

**이유 코드:**`131`

**상태:**`0602`

**속성**:

| Name | Type | 필수 | Description |
| :--- | :--- | :------- | :---------- |
| 추적 id | 문자열 | no | 오류에 대 한 추가 진단과의 상관 관계에 대 한 추적 ID |

**페이로드**: 비어 있음

