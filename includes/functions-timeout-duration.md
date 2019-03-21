---
title: 포함 파일
description: 포함 파일
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ffb29fc76313e8870b52cb0a63936da7853ea6ce
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305147"
---
## <a name="timeout"></a>함수 앱 시간 제한 기간 

함수 앱의 제한 시간 기간에 functionTimeout 속성에 의해 정의 되는 [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) 프로젝트 파일입니다. 다음 표에서 런타임 버전을 모두 및 계획 모두에 대 한 분에서 기본 및 최대 값을 보여줍니다.

| 계획 | 런타임 버전 | 기본값 | 최대 |
|------|---------|---------|---------|
| Consumption | 1.x | 5 | 10 |
| Consumption | 2.x | 5 | 10 |
| App Service | 1.x | Unlimited | Unlimited |
| App Service | 2.x | 30 | Unlimited |

> [!NOTE] 
> 함수 앱 제한 시간 설정에 관계 없이 230 초 이며 요청에 응답할 HTTP에서 트리거한 함수를 사용할 수 있는 최대 기간 때문에 이것이 합니다 [기본 Azure Load Balancer의 유휴 제한 시간](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)합니다. 더 긴 처리 시간을 사용 하 여 고려해 야 합니다 [Durable Functions 비동기 패턴](../articles/azure-functions/durable/durable-functions-concepts.md#async-http) 또는 [실제 작업을 연기 하 고 즉각적인 응답을 반환할](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)합니다.
