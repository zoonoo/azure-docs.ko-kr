---
title: 구성 - Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge의 Event Grid 구성입니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 632227579fd021a0d2ce1d0b1bb0b8a8288c5f47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171672"
---
# <a name="event-grid-configuration"></a>Event Grid 구성

Event Grid는 환경마다 수정할 수 있는 많은 구성을 제공합니다. 다음 섹션은 모든 사용 가능한 옵션과 해당 기본값에 대한 참조를 제공합니다.

## <a name="tls-configuration"></a>TLS 구성

클라이언트 인증에 대한 일반적인 내용은 [보안 및 인증](security-authentication.md)을 참조하세요. 사용 예제는 [이 문서](configure-api-protocol.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Event Grid 모듈의 TLS 정책입니다. 기본값은 HTTPS만입니다.
|`inbound__serverAuth__serverCert__source`| Event Grid 모듈에서 TLS 구성에 사용하는 서버 인증서의 원본입니다. 기본값은 IoT Edge입니다.

## <a name="incoming-client-authentication"></a>수신 클라이언트 인증

클라이언트 인증에 대한 일반적인 내용은 [보안 및 인증](security-authentication.md)을 참조하세요. 예제는 [이 문서](configure-client-auth.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 인증서 기반 클라이언트 인증을 설정/해제합니다. 기본값은 true입니다.
|`inbound__clientAuth__clientCert__source`| 클라이언트 인증서의 유효성을 검사하는 데 사용할 원본입니다. 기본값은 IoT Edge입니다.
|`inbound__clientAuth__clientCert__allowUnknownCA`| 자체 서명된 클라이언트 인증서를 허용하는 정책입니다. 기본값은 true입니다.
|`inbound__clientAuth__sasKeys__enabled`| SAS 키 기반 클라이언트 인증을 설정/해제합니다. 기본값은 해제입니다.
|`inbound__clientAuth__sasKeys__key1`| 수신 요청의 유효성을 검사할 값 중 하나입니다.
|`inbound__clientAuth__sasKeys__key2`| 수신 요청의 유효성을 검사할 선택적인 두 번째 값입니다.

## <a name="outgoing-client-authentication"></a>발신 클라이언트 인증
클라이언트 인증에 대한 일반적인 내용은 [보안 및 인증](security-authentication.md)을 참조하세요. 예제는 [이 문서](configure-identity-auth.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 발신 요청에 대한 ID 인증서 연결을 설정/해제합니다. 기본값은 true입니다.
|`outbound__clientAuth__clientCert__source`| Event Grid 모듈의 발신 인증서를 검색하는 원본입니다. 기본값은 IoT Edge입니다.

## <a name="webhook-event-handlers"></a>웹후크 이벤트 처리기

클라이언트 인증에 대한 일반적인 내용은 [보안 및 인증](security-authentication.md)을 참조하세요. 예제는 [이 문서](configure-webhook-subscriber-auth.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| HTTPS 구독자만 허용할지 여부를 제어하는 정책입니다. 기본값은 true(HTTPS만)입니다.
|`outbound__webhook__skipServerCertValidation`| 구독자 인증서의 유효성을 검사할지 여부를 제어하는 플래그입니다. 기본값은 true입니다.
|`outbound__webhook__allowUnknownCA`| 구독자가 자체 서명된 인증서를 제공할 수 있는지 여부를 제어하는 정책입니다. 기본값은 true입니다. 

## <a name="delivery-and-retry"></a>배달 및 다시 시도

이 기능에 대한 일반적인 내용은 [전송 및 다시 시도](delivery-retry.md)를 참조하세요.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 이벤트 전송 작업의 최대 시도 횟수입니다. 기본값은 30입니다.
| `broker__defaultEventTimeToLiveInSeconds` | 이벤트가 전송되지 않는 경우 이벤트를 삭제하려면 경과되어야 하는 초 단위의 TTL(Time to Live)입니다. 기본값은 **7200** 초입니다.

## <a name="output-batching"></a>출력 일괄 처리

이 기능에 대한 일반적인 내용은 [전송 및 출력 일괄 처리](delivery-output-batching.md)를 참조하세요.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` 노브에 허용되는 최대값입니다. 기본값은 `1_058_576`여야 합니다.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 노브에 허용되는 최대값입니다. 기본값은 `50`여야 합니다.
| `broker__defaultMaxBatchSizeInBytes` | `MaxEventsPerBatch`만 지정된 경우의 최대 전송 요청 크기입니다. 기본값은 `1_058_576`여야 합니다.
| `broker__defaultMaxEventsPerBatch` | `MaxBatchSizeInBytes`만 지정된 경우 일괄 처리에 추가할 최대 이벤트 수입니다. 기본값은 `10`여야 합니다.

## <a name="metrics"></a>메트릭

IoT Edge의 Event Grid에서 메트릭을 사용하는 방법에 대한 자세한 내용은 [토픽 및 구독 모니터링](monitor-topics-subscriptions.md)을 참조하세요.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `metrics__reporterType` | 메트릭 엔드포인트의 보고자 유형입니다. 기본값은 `none`이며 메트릭을 사용하지 않도록 설정합니다. `prometheus`로 설정하면 Prometheus 표시 형식으로 메트릭을 사용하도록 설정합니다.