---
title: 토픽 및 이벤트 구독 모니터링 - Azure Event Grid IoT Edge | Microsoft Docs
description: 토픽 및 이벤트 구독 모니터링
ms.date: 05/10/2021
ms.topic: article
ms.subservice: iot-edge
ms.openlocfilehash: 02fa0daa54d7b5a079ee1d8dff5a104ca23cc56e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367800"
---
# <a name="monitor-topics-and-event-subscriptions"></a>토픽 및 이벤트 구독 모니터링

Edge의 Event Grid는 [Prometheus 표시 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)으로 토픽 및 이벤트 구독에 대한 여러 메트릭을 표시합니다. 이 문서에서는 사용 가능한 메트릭 및 메트릭을 사용하도록 설정하는 방법을 설명합니다.

## <a name="enable-metrics"></a>메트릭 사용

컨테이너 만들기 옵션에서 `metrics__reporterType` 환경 변수를 `prometheus`로 설정하여 메트릭을 내보내도록 모듈을 구성합니다.

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

메트릭은 http 모듈의 `5888/metrics` 및 https `4438/metrics`에서 사용할 수 있습니다. 예를 들어 http의 경우 `http://<modulename>:5888/metrics?api-version=2019-01-01-preview`입니다. 이 시점에서 메트릭 모듈은 엔드포인트를 폴링하여 이 [예제 아키텍처](https://github.com/veyalla/ehm)에서와 같이 메트릭을 수집할 수 있습니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

토픽 및 이벤트 구독은 모두 메트릭을 내보낸 후 이벤트 배달 및 모듈 성능에 대한 인사이트를 제공합니다.

### <a name="topic-metrics"></a>토픽 메트릭

| 메트릭 | Description |
| ------ | ----------- |
| EventsReceived | 토픽에 게시된 이벤트 수
| UnmatchedEvents | 이벤트 구독과 일치하지 않고 삭제되는 토픽에 게시된 이벤트 수
| SuccessRequests | 토픽에서 수신한 인바운드 게시 요청 수
| SystemErrorRequests | 내부 시스템 오류로 인해 실패한 인바운드 게시 요청 수
| UserErrorRequests | 잘못된 형식의 JSON과 같은 사용자 오류로 인해 실패한 인바운드 게시 요청 수
| SuccessRequestLatencyMs | 게시 요청 응답 대기 시간(밀리초)


### <a name="event-subscription-metrics"></a>이벤트 구독 메트릭

| 메트릭 | Description |
| ------ | ----------- |
| DeliverySuccessCounts | 구성된 엔드포인트에 성공적으로 배달된 이벤트 수
| DeliveryFailureCounts | 구성된 엔드포인트에 배달되지 못한 이벤트 수
| DeliverySuccessLatencyMs | 성공적으로 배달된 이벤트의 대기 시간(밀리초)
| DeliveryFailureLatencyMs | 배달되지 못한 이벤트의 대기 시간(밀리초)
| SystemDelayForFirstAttemptMs | 첫 번째 배달 시도 전 이벤트의 시스템 연기 시간(밀리초)
| DeliveryAttemptsCount | 이벤트 배달 시도 횟수(성공 및 실패)
| ExpiredCounts | 구성된 엔드포인트로 배달되지 않은 만료 이벤트 수