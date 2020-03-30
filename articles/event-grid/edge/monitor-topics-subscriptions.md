---
title: 토픽 및 이벤트 구독 모니터링 - Azure 이벤트 그리드 IoT 에지 | 마이크로 소프트 문서
description: 토픽 및 이벤트 구독 모니터링
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086665"
---
# <a name="monitor-topics-and-event-subscriptions"></a>토픽 및 이벤트 구독 모니터링

Edge의 이벤트 그리드는 [프로메테우스 박람회 형식의](https://prometheus.io/docs/instrumenting/exposition_formats/)토픽 및 이벤트 구독에 대한 여러 메트릭을 노출합니다. 이 문서에서는 사용 가능한 메트릭과 이를 사용하도록 설정하는 방법에 대해 설명합니다.

## <a name="enable-metrics"></a>메트릭 사용

`metrics__reporterType` 환경 변수를 컨테이너 만들기 옵션에 `prometheus` 설정하여 메트릭을 내보도록 모듈을 구성합니다.

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

메트릭은 http 및 `5888/metrics` `4438/metrics` https에 대한 모듈에서 사용할 수 있습니다. 예를 들어 `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` http의 경우 이 시점에서 메트릭 모듈은 끝점을 폴링하여 이 예제 [아키텍처와](https://github.com/veyalla/ehm)같이 메트릭을 수집할 수 있습니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

토픽과 이벤트 구독 모두 메트릭을 방출하여 이벤트 전달 및 모듈 성능에 대한 통찰력을 제공합니다.

### <a name="topic-metrics"></a>주제 메트릭

| 메트릭 | 설명 |
| ------ | ----------- |
| 접수된 이벤트 | 토픽에 게시된 이벤트 수
| 일치하지 않는 이벤트 | 이벤트 구독과 일치하지 않고 삭제된 토픽에 게시된 이벤트 수
| 성공 요청 | 토픽에서 수신한 인바운드 게시 요청 수
| 시스템 오류 요청 | 내부 시스템 오류로 인해 인바운드 게시 요청 수가 실패했습니다.
| 사용자 오류 요청 | 잘못된 JSON과 같은 사용자 오류로 인해 인바운드 게시 요청의 수가 실패했습니다.
| 성공요청지연 | 요청 응답 대기 시간을 밀리초 단위로 게시


### <a name="event-subscription-metrics"></a>이벤트 구독 메트릭

| 메트릭 | 설명 |
| ------ | ----------- |
| 납품 성공 카운트 | 구성된 끝점에 성공적으로 전달된 이벤트 수
| 배달 실패 카운트 | 구성된 끝점에 배달되지 못한 이벤트 수
| 납품성공지연 | 이벤트 대기 시간이 밀리초 단위로 성공적으로 전달되었습니다.
| 배달 실패지연 | 이벤트 배달 실패의 대기 시간(밀리초)
| 시스템딜레이지포퍼스트시도 | 밀리초 단위로 첫 번째 배달을 시도하기 전에 이벤트의 시스템 지연
| 배달 시도 카운트 | 이벤트 배달 시도 수 - 성공 및 실패
| 만료된 카운트 | 만료되어 구성된 끝점에 배달되지 않은 이벤트 수