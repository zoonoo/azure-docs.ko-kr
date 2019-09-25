---
title: Azure 활동 로그 개요
description: Azure 활동 로그란 무엇이고 Azure 구독 내에서 발생하는 이벤트를 파악하는 데 어떻게 사용할 수 있는지 알아봅니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: ee3a1fef379e2950172dddc389b30e0a363127ae
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262120"
---
# <a name="overview-of-azure-activity-log"></a>Azure 활동 로그 개요

**Azure 활동 로그** 는 azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 합니다. 여기에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. 관리 범주가 구독에 대 한 제어 평면 이벤트를 보고 하기 때문에 활동 로그는 이전에 _감사 로그_ 또는 _작업 로그_로 알려져 있었습니다. 

활동 로그를 사용 하 여 구독의 리소스에 대해 수행 되는 쓰기 작업 (PUT, POST, DELETE)에 대 한 _내용_, 대상 및 _시기_ _를 결정_합니다. 또한 작업 및 기타 관련 속성의 상태도 이해할 수 있습니다. 

활동 로그에는 읽기 (GET) 작업 또는 클래식/RDFE 모델을 사용 하는 리소스에 대 한 작업이 포함 되지 않습니다.

## <a name="comparison-to-resource-logs"></a>리소스 로그와 비교
각 Azure 구독에 대 한 단일 활동 로그가 있습니다. 외부 ("제어 평면")에서 리소스에 대 한 작업에 대 한 데이터를 제공 합니다. 리소스에서 리소스 [로그](resource-logs-overview.md) 를 내보내고 해당 리소스의 작업에 대 한 정보 ("데이터 평면")를 제공 합니다. 리소스 로그를 수집 하는 각 리소스에 대 한 진단 설정을 만들어야 합니다.

![리소스 로그와 비교 되는 활동 로그](media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)


> [!NOTE]
> Azure 활동 로그는 주로 Azure Resource Manager에서 발생하는 활동을 대상으로 합니다. 클래식/RDFE 모델을 사용하여 리소스를 추적하지 않습니다. 일부 클래식 리소스 유형(예: Microsoft.ClassicCompute)에는 Azure Resource Manager의 프록시 리소스 공급자가 있습니다. 이러한 프록시 리소스 공급자를 사용하는 Azure Resource Manager를 통해 클래식 리소스 유형과 상호 작용하면 작업이 활동 로그에 표시됩니다. Azure Resource Manager 프록시 외부의 클래식 리소스 유형과 상호 작용할 경우 사용자 작업은 작업 로그에만 기록됩니다. 작업 로그는 포털의 별도 섹션에서 탐색할 수 있습니다.

## <a name="activity-log-retention"></a>활동 로그 보존
활동 로그 항목은 만든 후에는 시스템에 의해 수정 되거나 삭제 되지 않습니다. 또한 인터페이스에서 또는 프로그래밍 방식으로 변경할 수 없습니다. 활동 로그 이벤트는 90 일 동안 저장 됩니다. 이 데이터를 더 오랜 기간 동안 저장 하려면 [Azure Monitor으로 수집](activity-log-collect.md) 하거나 [저장소 또는 Event Hubs으로 내보내십시오](activity-log-export.md).

## <a name="view-the-activity-log"></a>활동 로그 보기
Azure Portal **모니터** 메뉴의 모든 리소스에 대 한 활동 로그를 봅니다. 해당 리소스의 메뉴에 있는 **활동 로그** 옵션에서 특정 리소스에 대 한 활동 로그를 봅니다. PowerShell, CLI 또는 REST API를 사용 하 여 활동 로그 레코드를 검색할 수도 있습니다.  [Azure 활동 로그 이벤트 보기 및 검색](activity-log-view.md)을 참조 하세요.

![활동 로그 보기](./media/activity-logs-overview/view-activity-log.png)

## <a name="collect-activity-log-in-azure-monitor"></a>활동 로그를 수집 Azure Monitor
Azure Monitor의 Log Analytics 작업 영역에 활동 로그를 수집 하 여 다른 모니터링 데이터로 분석 하 고 데이터를 90 일 보다 오래 유지 합니다. [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그 수집 및 분석](activity-log-collect.md)을 참조 하세요.

![활동 로그 쿼리](./media/activity-logs-overview/query-activity-log.png)

## <a name="export-activity-log"></a>활동 로그 내보내기
활동 로그를 보관 하거나, 타사 서비스 또는 사용자 지정 분석 솔루션에서 수집 하기 위해 이벤트 허브로 스트리밍하려면 활동 로그를 Azure Storage으로 내보냅니다. [Azure 활동 로그 내보내기를](activity-log-export.md)참조 하세요. [**Power BI 콘텐츠 팩**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용 하 여 Power BI에서 활동 로그 이벤트를 분석할 수도 있습니다.

## <a name="alert-on-activity-log"></a>활동 로그에 대 한 경고
활동 로그 [경고](activity-log-alerts.md)를 사용 하 여 활동 로그에 특정 이벤트가 생성 될 때 경고를 만들 수 있습니다. 활동 로그가 Log Analytics 작업 영역에 연결 되어 있는 경우 [로그 쿼리](alerts-log-query.md) 를 사용 하 여 경고를 만들 수도 있지만 쿼리 경고를 기록 하는 데 비용이 듭니다. 활동 로그 경고에 대 한 비용은 없습니다.

## <a name="categories-in-the-activity-log"></a>활동 로그의 범주
활동 로그의 각 이벤트에는 다음 표에 설명 된 특정 범주가 있습니다. 이러한 범주의 스키마에 대한 전체 정보는 [Azure Activity Log 이벤트 스키마](activity-log-schema.md)를 참조하세요. 

| Category | 설명 |
|:---|:---|
| 관리 | 리소스 관리자을 통해 수행 되는 모든 만들기, 업데이트, 삭제 및 동작 작업의 레코드를 포함 합니다. 관리 이벤트의 예로는 _가상 머신 만들기_ 및 _네트워크 보안 그룹 삭제_가 있습니다.<br><br>리소스 관리자를 사용 하 여 사용자 또는 응용 프로그램에서 수행 하는 모든 작업은 특정 리소스 종류에 대 한 작업으로 모델링 됩니다. 작업 유형이 _쓰기_, _삭제_또는 _작업_인 경우 해당 작업의 시작 및 성공 또는 실패의 레코드가 관리 범주에 기록 됩니다. 관리 이벤트에는 구독에서 역할 기반 액세스 제어에 대 한 변경 내용도 포함 됩니다. |
| Service Health | Azure에서 발생 한 모든 서비스 상태 인시던트의 레코드를 포함 합니다. 미국 동부에서 Service Health 이벤트 SQL Azure의 예로는 _가동 중지 시간이 발생_합니다. <br><br>Service Health 이벤트는 6 가지 형태로 제공 됩니다. _작업 필요_, _복구_, _인시던트_, _유지 관리_, _정보_또는 _보안_을 지원 합니다. 이러한 이벤트는 이벤트의 영향을 받는 구독에 리소스가 있는 경우에만 생성 됩니다.
| Resource Health | Azure 리소스에 발생 한 리소스 상태 이벤트의 레코드를 포함 합니다. Resource Health 이벤트의 예는 _가상 컴퓨터 상태를 사용할 수 없음으로 변경 하_는 것입니다.<br><br>Resource Health 이벤트는 다음 네 가지 상태 중 하나를 나타낼 수 있습니다. _사용 가능_, _사용할 수 없음_, _저하_됨 및 _알 수 없음_입니다. 또한 Resource Health 이벤트는 _플랫폼에서 시작_ 되거나 _사용자가 시작한_것으로 분류 될 수 있습니다. |
| 알림 | Azure 경고에 대 한 활성화 레코드를 포함 합니다. 경고 이벤트의 예로는 _myVM의 CPU%가 지난 5 분 동안 80을 초과_했습니다.|
| 자동 크기 조정 | 구독에서 정의한 자동 크기 조정 설정에 따라 자동 크기 조정 엔진의 작업과 관련 된 모든 이벤트의 레코드를 포함 합니다. 자동 크기 조정 이벤트의 예로는 _자동 크기 조정 확장 작업이 실패 했습니다_. |
| 권장 | Azure Advisor의 권장 사항 이벤트를 포함 합니다. |
| 보안 | Azure Security Center에서 생성 된 모든 경고의 레코드를 포함 합니다. 보안 이벤트의 예로 _의심 스러운 이중 확장 파일이 실행_됩니다. |
| 정책 | Azure Policy에서 수행 하는 모든 효과 동작 작업의 레코드를 포함 합니다. 정책 이벤트의 예로 _감사_ 및 _거부_를 들 수가 있습니다. Policy에서 수행하는 모든 작업은 리소스에 대한 작업으로 모델링됩니다. |


## <a name="next-steps"></a>다음 단계

* [Azure 활동 로그를 내보낼 로그 프로필 만들기](activity-log-export.md)
* [Azure 활동 로그를 Event Hubs로 스트림](activity-logs-stream-event-hubs.md)
* [저장소에 Azure 활동 로그 보관](archive-activity-log.md)

