---
title: 포함 파일
description: 포함 파일
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 05/03/2021
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 1d9ec2d05374412c17493b47f3bb71524f8cf93f
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110630438"
---
| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| 메트릭 경고 수(클래식) |구독당 100개 활성 경고 규칙 | 지원 요청 |
| 메트릭 경고 |구독당 5,000개 활성 경고 규칙(Azure 퍼블릭, Azure 중국 21Vianet 및 Azure Government 클라우드 기준) 이 제한에 도달하면 [동일한 유형의 다중 리소스 경고](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)를 사용할 수 있는지 살펴보세요.<br/>경고 규칙당 5000개의 메트릭 시계열. | 지원 요청 |
| 활동 로그 경고 | 구독당 100개 활성 경고 규칙(늘릴 수 없음) | 기본값과 동일 |
| 로그 경고 | 구독당 1000개 활성 경고 규칙 리소스당 1000개 활성 경고 규칙 | 지원 요청 |
| 경고 규칙 및 작업 규칙 설명 길이| 로그 검색 경고 4096자<br/>다른 모든 경고 2048자 | 기본값과 동일 |

### <a name="alerts-api"></a>경고 API
Azure Monitor 경고에는 과도한 수의 호출을 하는 사용자로부터 보호하기 위한 몇 가지 제한 한도가 있습니다. 이러한 동작은 잠재적으로 시스템 백 엔드 리소스에 과부하를 일으키고 서비스 응답성을 저하시킬 수 있습니다. 다음 제한은 중단으로부터 고객을 보호하고 일관된 서비스 수준을 보장하기 위해 마련되었습니다. 사용자 제한은 극단적인 사용 시나리오에만 영향을 주도록 설계되었으며 일반적인 사용과 관련이 없어야 합니다.

| 리소스 | 기본 제한 | 최대 제한 |
| --- | --- | --- |
| GET alertsSummary | 구독당 50개 호출(분당) | 기본값과 동일 | 
|   GET 경고(경고 ID를 지정하지 않음) | 구독당 100개 호출(분당) | 기본값과 동일 | 
|   기타 모든 호출 | 구독당 1000개 호출(분당) | 기본값과 동일 | 

