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
ms.openlocfilehash: eca2d3359614875e5bff0c9bb67f006f0a8cdba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198329"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>Function app timeout duration 

함수 앱의 시간 시간 시간 시간은 `functionTimeout` [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) 프로젝트 파일의 속성에 의해 정의됩니다. 다음 표에서는 계획과 다른 런타임 버전모두에 대한 기본값과 최대값을 분(분)으로 보여 주며,

| 계획 | 런타임 버전 | 기본값 | 최대 |
|------|---------|---------|---------|
| Consumption | 1.x | 5 | 10 |
| Consumption | 2.x | 5 | 10 |
| Consumption | 3.x | 5 | 10 |
| Premium | 1.x | 30 | 제한 없음 |
| Premium | 2.x | 30 | 제한 없음 |
| Premium | 3.x | 30 | 제한 없음 |
| App Service | 1.x | 제한 없음 | 제한 없음 |
| App Service | 2.x | 30 | 제한 없음 |
| App Service | 3.x | 30 | 제한 없음 |

> [!NOTE] 
> 함수 앱 시간 시간 설정에 관계없이 230초는 HTTP 트리거 함수가 요청에 응답하는 데 걸리는 최대 시간입니다. 이는 [Azure 로드 밸러저](../articles/app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds)의 기본 유휴 시간 설정 때문입니다. 처리 시간이 길어지면 [내구함수 비동기 패턴을](../articles/azure-functions/durable/durable-functions-overview.md#async-http) 사용하거나 [실제 작업을 연기하고 즉각적인 응답을 반환하는](../articles/azure-functions/functions-best-practices.md#avoid-long-running-functions)것이 좋습니다.
