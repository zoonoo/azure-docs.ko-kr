---
title: 구성-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid 구성
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846478"
---
# <a name="event-grid-configuration"></a>Event Grid 구성

Event Grid는 환경 마다 수정할 수 있는 다양 한 구성을 제공 합니다. 다음 섹션에서는 사용 가능한 모든 옵션과 해당 기본값에 대 한 참조를 제공 합니다.

## <a name="tls-configuration"></a>TLS 구성

일반적으로 클라이언트 인증에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조 하세요. 사용 예제는 [이 문서](configure-api-protocol.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Event Grid 모듈의 TLS 정책입니다. 기본값은 HTTPS 전용입니다.
|`inbound__serverAuth__serverCert__source`| Event Grid 모듈이 해당 TLS 구성에 사용 하는 서버 인증서의 원본입니다. 기본값은 IoT Edge입니다.

## <a name="incoming-client-authentication"></a>들어오는 클라이언트 인증

일반적으로 클라이언트 인증에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조 하세요. 예제는 [이 문서](configure-client-auth.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 인증서 기반 클라이언트 인증을 설정/해제 합니다. 기본값은 true입니다.
|`inbound__clientAuth__clientCert__source`| 클라이언트 인증서의 유효성을 검사 하기 위한 원본입니다. 기본값은 IoT Edge입니다.
|`inbound__clientAuth__clientCert__allowUnknownCA`| 자체 서명 된 클라이언트 인증서를 허용 하는 정책입니다. 기본값은 true입니다.
|`inbound__clientAuth__sasKeys__enabled`| SAS 키 기반 클라이언트 인증을 설정/해제 합니다. 기본값은 off입니다.
|`inbound__clientAuth__sasKeys__key1`| 들어오는 요청의 유효성을 검사 하는 값 중 하나입니다.
|`inbound__clientAuth__sasKeys__key2`| 들어오는 요청의 유효성을 검사 하는 선택적 두 번째 값입니다.

## <a name="outgoing-client-authentication"></a>나가는 클라이언트 인증
일반적으로 클라이언트 인증에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조 하세요. 예제는 [이 문서](configure-identity-auth.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 보내는 요청에 대 한 id 인증서 연결을 설정/해제 합니다. 기본값은 true입니다.
|`outbound__clientAuth__clientCert__source`| Event Grid 모듈의 나가는 인증서를 검색 하기 위한 원본입니다. 기본값은 IoT Edge입니다.

## <a name="webhook-event-handlers"></a>Webhook 이벤트 처리기

일반적으로 클라이언트 인증에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조 하세요. 예제는 [이 문서](configure-webhook-subscriber-auth.md)에서 찾을 수 있습니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| HTTPS 구독자만 허용할지 여부를 제어 하는 정책입니다. 기본값은 true (HTTPS만)입니다.
|`outbound__webhook__skipServerCertValidation`| 구독자 인증서의 유효성을 검사할지 여부를 제어 하는 플래그입니다. 기본값은 true입니다.
|`outbound__webhook__allowUnknownCA`| 구독자가 자체 서명 된 인증서를 제공할 수 있는지 여부를 제어 하는 정책입니다. 기본값은 true입니다. 

## <a name="delivery-and-retry"></a>배달 및 다시 시도

이 기능에 대 한 일반적인 정보는 [배달 및 다시 시도](delivery-retry.md)를 참조 하세요.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 이벤트를 전달할 최대 시도 횟수입니다. 기본값은 30입니다.
| `broker__defaultEventTimeToLiveInSeconds` | 배달 되지 않은 경우 이벤트를 삭제 하는 시간 (초)의 TTL (time to live)입니다. 기본값은 **7200** 초입니다.

## <a name="output-batching"></a>출력 일괄 처리

이 기능에 대 한 일반적인 정보는 [배달 및 출력 일괄 처리](delivery-output-batching.md)를 참조 하세요.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | `ApproxBatchSizeInBytes` 노브에 허용 되는 최대값입니다. 기본값은 `1_058_576`입니다.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | `MaxEventsPerBatch` 노브에 허용 되는 최대값입니다. 기본값은 `50`입니다.
| `broker__defaultMaxBatchSizeInBytes` | `MaxEventsPerBatch`만 지정 된 경우 최대 배달 요청 크기입니다. 기본값은 `1_058_576`입니다.
| `broker__defaultMaxEventsPerBatch` | `MaxBatchSizeInBytes`만 지정 된 경우 일괄 처리에 추가할 최대 이벤트 수입니다. 기본값은 `10`입니다.

## <a name="metrics"></a>메트릭

IoT Edge에서 Event Grid 하는 메트릭을 사용 하는 방법에 대 한 자세한 내용은 [항목 및 구독 모니터링](monitor-topics-subscriptions.md) 을 참조 하세요.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `metrics__reporterType` | 메트릭 enpoint에 대 한 보고자 유형입니다. 기본값은 `none` 이며 메트릭을 사용 하지 않습니다. `prometheus`로 설정 하면 프로메테우스 표시 형식으로 메트릭을 사용할 수 있습니다.