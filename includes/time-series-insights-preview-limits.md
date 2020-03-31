---
title: 포함 파일
description: 포함 파일
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123166"
---
### <a name="general-availability-and-preview-comparison"></a>일반 공급 및 미리 보기 비교

다음 표에는 Azure Time Series Insights 일반 가용성(GA) 및 미리 보기 인스턴스 간의 몇 가지 주요 차이점이 요약되어 있습니다.

| | GA | 미리 보기 |
| --- | --- | ---|
| 일류 시민 | 이벤트 중심 | 시간 시리즈 중심 |
| 의미론적 추론 | 하위 수준(참조 데이터) | 고급 (모델) |
| 데이터 컨텍스트화 | 비장치 수준 | 장치 및 비장치 수준 |
| 컴퓨팅 로직 스토리지 | 예 | 모델의 형식 변수 부분에 저장됨 |
| 스토리지 및 액세스 제어 | 예 | 모델을 통해 사용 가능 |
| 집계/샘플링 | 예 | 이벤트 가중치 및 시간 가중치 |
| 신호 재구성 | 예 | 보간 |
| 파생 된 시간계의 생산 | 예 | 예, 병합 및 조인 |
| 언어 유연성 | 컴포지블 이블 | 컴포지스 블 |
| 식 언어 | 줄바스트 | 시계열 표현식(줄바스트 문자열, 값, 식 및 함수) |

### <a name="property-limits"></a>속성 제한

Time Series Insights 속성 한도가 GA의 최대 한도인 800에서 1,000으로 증가했습니다. 제공된 이벤트 속성에는 [타임시리즈 인사이트 미리 보기 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)에서 볼 수 있는 해당 JSON, CSV 및 차트 열이 있습니다.

| SKU | 최대 속성 |
| --- | --- |
| 페이그 미리보기 | 1,000개의 속성(열) |
| GA S1 | 600개의 속성(열) |
| GA S2 | 800개 속성(열) |

### <a name="event-sources"></a>이벤트 원본

인스턴스당 최대 2개의 이벤트 소스가 지원됩니다. 

* [이벤트 허브 소스를 추가하는](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)방법에 대해 알아봅니다.
* [IoT 허브 소스를 구성합니다.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

기본적으로 [미리 보기 환경은](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) 환경당 초당 최대 **1MB바이트(MB/s)의**침투 속도를 지원합니다. 고객은 필요한 경우 미리 보기 환경을 최대 **16MB/s** 처리량까지 확장할 수 있습니다. 파티션당 제한은 **0.5MB/s입니다.** 

### <a name="api-limits"></a>API 한도

TIME Series Insights 미리 보기에 대한 REST API 제한은 [REST API 참조 설명서에](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)지정되어 있습니다.
