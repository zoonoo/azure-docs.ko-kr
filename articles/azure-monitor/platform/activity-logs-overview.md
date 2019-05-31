---
title: Azure 활동 로그 개요
description: Azure 활동 로그란 무엇이고 Azure 구독 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/19/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 34857108cf7f0580c380ffbd4bbcedb5cd5a807a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245017"
---
# <a name="overview-of-azure-activity-log"></a>Azure 활동 로그 개요

합니다 **Azure 활동 로그** Azure 동안 발생 한 구독 수준 이벤트에 대 한 정보를 제공 합니다. 여기에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. 이전에 활동 로그 라고 했습니다 _감사 로그_ 또는 _Operational 로그_가 관리 범주를 구독에 대 한 제어 평면 이벤트를 보고 합니다. 

활동 로그를 확인 하는 데는 _무엇_, _하는_, 및 _때_ 한 모든 쓰기 작업 (PUT, POST, DELETE) 구독의 리소스에서 수행 합니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 

활동 로그에는 읽기 (GET) 작업 또는 Classic/RDFE 모델을 사용 하는 리소스에 대 한 작업이 포함 되지 않습니다.

## <a name="comparison-to-diagnostic-logs"></a>진단 로그에 대 한 비교
각 Azure 구독에 대해 단일 활동 로그를 있습니다. 외부에서 리소스에 대 한 작업에 대 한 데이터를 제공 ("제어 평면"). [진단 로그](diagnostic-logs-overview.md) 는 리소스에서 내보내며 하 고 해당 리소스 ("데이터 평면")의 작업에 대 한 정보를 제공 합니다. 각 리소스에 대 한 진단 설정을 활성화 해야 합니다.

![진단 로그를 비교 하는 활동 로그](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. Ugresources Classic/RDFE 모델을 사용 하는 추적 하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 포털의 별도 섹션에서 탐색할 수 있습니다.

## <a name="activity-log-retention"></a>활동 로그 보관
활동 로그 이벤트는 90 일 동안 저장 됩니다. 더 긴 기간에 대 한이 데이터를 저장할 [Azure Monitor에서 수집](activity-log-collect.md) 또는 [storage 또는 Event Hubs로 내보내기](activity-log-export.md)합니다.

## <a name="view-the-activity-log"></a>활동 로그를 보려면
모든 리소스에 대 한 활동 로그를 보려면 합니다 **모니터** Azure portal의 메뉴. 특정 리소스에 대 한 활동 로그를 보려면 합니다 **활동 로그** 해당 리소스 메뉴의 옵션입니다. 또한 PowerShell, CLI 또는 REST API를 사용 하 여 활동 로그 레코드를 검색할 수 있습니다.  참조 [뷰와 검색 Azure 활동 로그 이벤트](activity-log-view.md)합니다.

![활동 로그 보기](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>Azure Monitor의 활동 로그를 수집 합니다.
기타 모니터링 데이터를 사용 하 여 분석을 90 일 이상에 대 한 데이터를 보존 하려면 Azure Monitor에서 Log Analytics 작업 영역으로 활동 로그를 수집 합니다. 참조 [Azure Monitor에서 Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](activity-log-collect.md)합니다.

![활동 로그 쿼리](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>활동 로그 내보내기
보관에 대 한 Azure Storage에 활동 로그 내보내기 또는 타사 서비스 또는 사용자 지정 분석 솔루션으로 수집 하기 위해 이벤트 허브로 스트리밍하십시오. 참조 [Azure 활동 로그 내보내기](activity-log-export.md)합니다. Power BI를 사용 하 여 활동 로그 이벤트를 분석할 수도 있습니다는 [ **Power BI 콘텐츠 팩**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)합니다.

## <a name="alert-on-activity-log"></a>활동 로그에 대 한 경고
사용 하 여 활동 로그에서 특정 이벤트를 만들 때 경고를 만들 수는 [활동 로그 경고](activity-log-alerts.md)합니다. 사용 하 여 경고를 만들 수도 있습니다는 [로그 쿼리](alerts-log-query.md) 경우 활동 로그는 Log Analytics 작업 영역에 연결 되어 있지만 기록할 비용이 발생 경고를 쿼리 합니다. 활동 로그 경고에 대 한 비용은 없습니다.

## <a name="categories-in-the-activity-log"></a>활동 로그의 범주
활동 로그의 각 이벤트는 다음 표에 설명 된 특정 범주를 포함 합니다. 이러한 범주의 스키마에 대한 전체 정보는 [Azure Activity Log 이벤트 스키마](activity-log-schema.md)를 참조하세요. 

| Category | 설명 |
|:---|:---|
| 관리 | 모든 레코드가 포함 만들기, 업데이트, 삭제 및 동작 작업 Resource Manager를 통해 수행 합니다. 관리 이벤트의 예로 _가상 머신 만들기_ 하 고 _네트워크 보안 그룹 삭제_합니다.<br><br>사용자 또는 Resource Manager를 사용 하 여 응용 프로그램에서 수행한 모든 작업은 특정 리소스 형식에 대 한 작업으로 모델링 됩니다. 작업 형식이 _작성할_를 _삭제_, 또는 _동작_, 레코드의 시작 및 성공 또는 실패 작업의 관리 범주에 기록 됩니다. 관리 이벤트 구독에 역할 기반 액세스 제어 변경 내용을 포함 합니다. |
| 서비스 상태 | Azure에서 발생 한 모든 서비스 상태 인시던트 레코드를 포함 합니다. 서비스 상태 이벤트의 예로 _미국 동부에 있는 SQL Azure는 가동 중지 시간 발생_합니다. <br><br>Service Health 이벤트 5 종류에 제공 합니다. _작업 필요_, _복구 지원_를 _인시던트_를 _유지 관리_를 _정보_, 또는  _보안_합니다. 이러한 이벤트는 이벤트에 의해 영향을 받을 구독에는 리소스가 있는 경우에 생성 됩니다.
| 리소스 상태 | Azure 리소스에 발생 한 모든 리소스 상태 이벤트의 레코드를 포함 합니다. Resource Health 이벤트의 예로 _가상 컴퓨터 상태 변경에 사용할 수 없게_합니다.<br><br>Resource Health 이벤트는 네 가지 상태 중 하나를 나타낼 수 있습니다. _사용 가능한_, _사용할 수 없게_를 _저하_, 및 _알 수 없는_합니다. 또한으로 Resource Health 이벤트를 분류할 수 있습니다 _플랫폼에서 시작_ 하거나 _사용자가 시작한_합니다. |
| 경고 | Azure 경고 활성화의 레코드가 포함 되어 있습니다. 경고 이벤트의 예로 _지난 5 분 동안 myVM의 CPU 비율이 80 되었습니다_합니다.|
| 자동 크기 조정 | 구독에서 정의한 자동 크기 조정 설정을 기준으로 자동 크기 조정 엔진 작업 관련 이벤트의 레코드를 포함 합니다. 자동 크기 조정 이벤트의 예로 _자동 크기 조정 강화 동작이 실패_합니다. |
| 권장 사항 | Azure Advisor의 권장 이벤트가 포함 되어 있습니다. |
| 보안 | Azure Security Center에서 생성 된 모든 경고의 기록을 포함 합니다. 보안 이벤트의 예로 _의심 스러운 이중 확장 파일이 실행_합니다. |
| 정책 | Azure Policy를 수행한 모든 적용 작업의 레코드를 포함 합니다. 정책 이벤트의 예로 _감사_ 하 고 _거부_합니다. Policy에서 수행하는 모든 작업은 리소스에 대한 작업으로 모델링됩니다. |


## <a name="next-steps"></a>다음 단계

* [Azure 활동 로그를 내보내려면 로그 프로필 만들기](activity-log-export.md)
* [Azure 활동 로그를 Event Hubs로 스트림](activity-logs-stream-event-hubs.md)
* [저장소에 Azure 활동 로그 보관](archive-activity-log.md)

