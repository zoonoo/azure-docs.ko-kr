---
title: 구성 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: IoT 에지의 이벤트 그리드 구성.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846478"
---
# <a name="event-grid-configuration"></a>이벤트 그리드 구성

Event Grid는 환경별 수정할 수 있는 많은 구성을 제공합니다. 다음 섹션은 사용 가능한 모든 옵션 및 기본값에 대한 참조입니다.

## <a name="tls-configuration"></a>TLS 구성

일반적으로 클라이언트 인증에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md)참조하십시오. 사용의 예는 [이 문서에서](configure-api-protocol.md)찾을 수 있습니다.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| 이벤트 그리드 모듈의 TLS 정책입니다. 기본값은 HTTPS만 입니다.
|`inbound__serverAuth__serverCert__source`| TLS 구성에 이벤트 그리드 모듈에서 사용하는 서버 인증서의 소스입니다. 기본값은 IoT Edge입니다.

## <a name="incoming-client-authentication"></a>들어오는 클라이언트 인증

일반적으로 클라이언트 인증에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md)참조하십시오. 예제는 [이 문서에서](configure-client-auth.md)찾을 수 있습니다.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| 인증서 기반 클라이언트 인증을 켜고 끄려면 기본값은 true입니다.
|`inbound__clientAuth__clientCert__source`| 클라이언트 인증서유효성 검사용 원본입니다. 기본값은 IoT Edge입니다.
|`inbound__clientAuth__clientCert__allowUnknownCA`| 자체 서명된 클라이언트 인증서를 허용하는 정책입니다. 기본값은 true입니다.
|`inbound__clientAuth__sasKeys__enabled`| SAS 키 기반 클라이언트 인증을 켜고 끕니다. 기본값이 꺼져 있습니다.
|`inbound__clientAuth__sasKeys__key1`| 들어오는 요청의 유효성을 검사하는 값 중 하나입니다.
|`inbound__clientAuth__sasKeys__key2`| 들어오는 요청의 유효성을 검사하는 선택적 두 번째 값입니다.

## <a name="outgoing-client-authentication"></a>나가는 클라이언트 인증
일반적으로 클라이언트 인증에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md)참조하십시오. 예제는 [이 문서에서](configure-identity-auth.md)찾을 수 있습니다.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| 나가는 요청에 대한 ID 인증서를 첨부하는 켜기/끄기. 기본값은 true입니다.
|`outbound__clientAuth__clientCert__source`| 이벤트 그리드 모듈의 나가는 인증서를 검색하기 위한 소스입니다. 기본값은 IoT Edge입니다.

## <a name="webhook-event-handlers"></a>웹후크 이벤트 처리기

일반적으로 클라이언트 인증에 대한 자세한 내용은 [보안 및 인증을](security-authentication.md)참조하십시오. 예제는 [이 문서에서](configure-webhook-subscriber-auth.md)찾을 수 있습니다.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| HTTPS 구독자만 허용되는지 여부를 제어하는 정책입니다. 기본값은 true입니다(HTTPS만).
|`outbound__webhook__skipServerCertValidation`| 구독자의 인증서의 유효성을 검사할지 여부를 제어하기 위해 플래그를 표시합니다. 기본값은 true입니다.
|`outbound__webhook__allowUnknownCA`| 구독자가 자체 서명된 인증서를 표시할 수 있는지 여부를 제어하는 정책입니다. 기본값은 true입니다. 

## <a name="delivery-and-retry"></a>배달 및 다시 시도

이 기능에 대해 일반적으로 자세히 알아보려면 [배달 및 다시 시도를](delivery-retry.md)참조하십시오.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 이벤트를 전달하는 최대 시도 횟수입니다. 기본값은 30입니다.
| `broker__defaultEventTimeToLiveInSeconds` | TTL(라이브 시간)을 몇 초 만에 완료한 후 이벤트가 전달되지 않으면 삭제됩니다. 기본값은 **7200초입니다.**

## <a name="output-batching"></a>출력 일괄 처리

이 기능에 대해 일반적으로 자세히 알아보려면 [배달 및 출력 일괄 처리를](delivery-output-batching.md)참조하십시오.

| 속성 이름 | 설명 |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | 손잡이에 `ApproxBatchSizeInBytes` 허용되는 최대값입니다. 기본값은 `1_058_576`입니다.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | 손잡이에 `MaxEventsPerBatch` 허용되는 최대값입니다. 기본값은 `50`입니다.
| `broker__defaultMaxBatchSizeInBytes` | 지정된 경우에만 최대 `MaxEventsPerBatch` 배달 요청 크기입니다. 기본값은 `1_058_576`입니다.
| `broker__defaultMaxEventsPerBatch` | 지정한 경우에만 일괄 처리에 추가할 `MaxBatchSizeInBytes` 최대 이벤트 수입니다. 기본값은 `10`입니다.

## <a name="metrics"></a>메트릭

IoT Edge에서 이벤트 그리드를 사용하여 메트릭을 사용하는 방법에 대해 알아보려면 [항목 및 구독 모니터를 참조하세요.](monitor-topics-subscriptions.md)

| 속성 이름 | 설명 |
| ---------------- | ------------ |
| `metrics__reporterType` | 메트릭 enpoint에 대한 리포터 유형입니다. 기본값은 `none` 메트릭을 사용하지 않도록 설정합니다. 프로메테우스 박람회 형식의 메트릭을 `prometheus` 활성화하도록 설정합니다.