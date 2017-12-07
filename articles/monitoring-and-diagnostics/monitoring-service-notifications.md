---
title: "서비스 상태 알림 정의 | Microsoft Docs"
description: "서비스 상태 알림을 통해 Microsoft Azure에서 게시한 서비스 상태 메시지를 볼 수 있습니다."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: efdd42d244710b27fc33154b708cfbe40312e3b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="service-health-notifications"></a>서비스 상태 알림
## <a name="overview"></a>개요

이 문서에서는 Azure Portal을 사용하여 서비스 상태 알림을 확인하는 방법을 보여 줍니다.

서비스 상태 알림을 통해 구독 중인 리소스에 영향을 줄 수 있는 Azure 팀에서 게시한 서비스 상태 메시지를 볼 수 있습니다. 이러한 알림은 활동 로그 이벤트의 하위 클래스이며 활동 로그에서도 찾을 수 있습니다. 서비스 상태 알림은 클래스에 따라 정보 제공용이거나 실행 가능할 수 있습니다.

5가지의 서비스 상태 알림 클래스가 있습니다.  

- **작업 필요:** 때때로 Azure가 계정에서 비정상적인 일이 발생한 것을 알아챌 수 있습니다. Azure가 이를 해결하는 작업을 수행해야 할 수 있습니다. Azure는 Azure 엔지니어링 또는 지원에 문의하는 방법에 대한 자세한 설명과 취해야 할 조치에 대해 설명하는 알림을 보내드립니다.  
- **복구 지원:** 이벤트가 발생했으며 엔지니어가 여전히 영향이 있음을 확인했습니다. Azure 엔지니어링에서 서비스를 정상상태로 복원하기 위해 사용자와 직접 작업해야 합니다.  
- **인시던트:** 서비스에 영향을 미치는 이벤트가 현재 구독에 있는 하나 이상의 리소스에 영향을 줍니다.  
- **유지 관리:** 이 알림은 구독 중인 하나 이상의 리소스에 영향을 줄 수 있는 계획된 유지 관리 활동을 알려 주는 알림입니다.  
- **정보:** 간혹 리소스 활용도를 높이는 데 도움이 될 수 있는 잠재적인 최적화 정보를 사용자에게 알리는 알림을 Azure가 보낼 수 있습니다.  
- **보안:** Azure에서 실행 중인 솔루션에 대한 긴급한 보안 관련 정보입니다.

각 서비스 상태 알림은 범위 및 리소스에 대한 영향에 대한 세부 정보를 포함합니다. 세부 정보는 다음과 같습니다.

속성 이름 | 설명
-------- | -----------
channels | “Admin”, “Operation” 값 중 하나입니다.
CorrelationId | 일반적으로 문자열 형식의 GUID입니다. 동일한 uber 작업에 속하는 이벤트는 일반적으로 동일한 correlationId를 공유합니다.
eventDataId | 이벤트의 고유 식별자입니다.
eventName | 이벤트의 제목입니다.
최소 수준 | 이벤트의 수준입니다. 다음 값 중 하나: “Critical”, “Error”, “Warning”, “Informational” 및 “Verbose”
resourceProviderName | 영향을 받는 리소스의 리소스 공급자 이름입니다.
resourceType| 영향을 받는 리소스의 리소스 형식입니다.
subStatus | 일반적으로 해당 REST 호출의 HTTP 상태 코드이지만 다음과 같이 하위 상태를 설명하는 다른 문자열을 포함할 수 있습니다. 예를 들어 이러한 일반적인 값은 다음과 같습니다. OK(HTTP 상태 코드: 200), Created(HTTP 상태 코드: 201), Accepted(HTTP 상태 코드: 202), No Content(HTTP 상태 코드: 204), Bad Request(HTTP 상태 코드: 400), Not Found(HTTP 상태 코드: 404), Conflict(HTTP 상태 코드: 409), Internal Server Error(HTTP 상태 코드: 500), Service Unavailable(HTTP 상태 코드:503), Gateway Timeout(HTTP 상태 코드: 504).
eventTimestamp | 이벤트에 해당하는 요청을 처리한 Azure 서비스에 의해 이벤트가 생성된 타임스탬프입니다.
submissionTimestamp |   이벤트를 쿼리할 수 있게 되는 타임스탬프입니다.
subscriptionId | 이 이벤트가 로깅된 Azure 구독입니다.
status | 작업의 상태를 설명하는 문자열. 일반적인 값: Started, In Progress, Succeeded, Failed, Active, Resolved.
operationName | 작업의 이름입니다.
카테고리 | "ServiceHealth"
resourceId | 영향을 받는 리소스의 리소스 ID입니다.
Properties.title | 이 통신에 대한 지역화된 제목입니다. 기본 언어는 영어입니다.
Properties.communication | HTML 태그와 통신에 대한 지역화된 세부 정보입니다. 기본값은 영어입니다.
Properties.incidentType | 가능한 값: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | 이 이벤트가 연결된 인시던트를 식별합니다. 인시던트와 관련된 이벤트를 상호 연결할 때 사용합니다.
Properties.impactedServices | 인시던트에 의해 영향을 받는 서비스 및 지역을 설명하는 이스케이프된 JSON Blob입니다. 각각 ServiceName과 ImpactedRegions 목록을 포함하는 서비스 목록으로, 각 ImpactedRegions에는 RegionName이 포함됩니다.
Properties.defaultLanguageTitle | 통신은 영어로 이루어집니다.
Properties.defaultLanguageContent | 영어로 통신은 html 태그 또는 일반 텍스트로 수행됩니다.
Properties.stage | AssistedRecovery, ActionRequired, Information, Incident, Security에 대해 가능한 값: Active, Resolved. Maintenance에 대해 가능한 값: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete
Properties.communicationId | 이 이벤트가 연결된 통신입니다.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Azure Portal에서 서비스 상태 알림 보기
1.  [포털](https://portal.azure.com)에서 **모니터** 서비스로 이동합니다.

    ![모니터](./media/monitoring-service-notifications/home-monitor.png)
2.  **모니터** 옵션을 클릭하여 모니터 환경을 엽니다. Azure Monitor는 모든 모니터링 설정과 데이터를 하나의 통합 보기로 모읍니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.  이제 **경고** 섹션을 클릭합니다.

    ![모니터](./media/monitoring-service-notifications/service-health-summary.png)
4. **+활동 로그 경고 추가** 작업을 클릭하여 알림을 구성하면 향후 서비스 알림을 전달받을 수 있습니다. 서비스 알림에 대한 경고 구성의 자세한 내용은 [활동 로그 경고 및 알림 서비스 알림 페이지](monitoring-activity-log-alerts-on-service-notifications.md)를 확인합니다.

## <a name="next-steps"></a>다음 단계:
[서비스 상태 알림이 게시될 때마다 경고 알림](monitoring-activity-log-alerts-on-service-notifications.md)을 수신합니다.  
[활동 로그 경고에 대한 자세한 내용](monitoring-activity-log-alerts.md)
