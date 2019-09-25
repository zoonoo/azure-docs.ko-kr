---
title: Azure의 로그 및 메트릭 | Microsoft Docs
description: Azure 리소스 작업에 대 한 풍부 하 고 빈번한 데이터를 제공 하는 Azure의 진단 로그에 대 한 개요입니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262363"
---
# <a name="logs-and-metrics-in-azure"></a>Azure의 로그 및 메트릭
[로그](data-platform-logs.md) 및 [메트릭이](data-platform-metrics.md) 다릅니다.

Azure의 응용 프로그램 및 서비스 모니터링은 [azure 데이터 플랫폼](data-platform.md)에 저장 된로 분리할 수 있습니다. 

로그 및 메트릭은 두 범주로 구분할 수 있습니다.

- 플랫폼과 같은 구성 없이 자동으로 생성 되는 플랫폼 로그 및 메트릭 



| 계층 | 플랫폼 로그 | 플랫폼 메트릭 | 사용자 지정 로그 | 사용자 지정 메트릭 |
|:---|:---|:---|:---|:---|
| 애플리케이션  | | | | |
| 게스트 OS     | 하트비트 |  | 진단 확장<br>Log Analytics 에이전트 | 진단 확장 |
| 리소스     | [리소스 로그](resource-logs-overview.md)<br>(각 서비스에만 해당) | [리소스 메트릭](metrics-supported.md)<br>(각 서비스에만 해당) | | [사용자 지정 메트릭](metrics-custom-overview.md) |
| 구독 | [활동 로그](activity-logs-overview.md) | | | |
| 테넌트       | 

## <a name="next-steps"></a>다음 단계

* [**Event Hubs**로 리소스 진단 로그 스트림](resource-logs-stream-event-hubs.md)
* [Azure Monitor REST API를 사용하여 리소스 진단 설정 변경](https://docs.microsoft.com/rest/api/monitor/)
* [Azure Storage에서 Azure Monitor를 사용하여 로그 분석](collect-azure-metrics-logs.md)
