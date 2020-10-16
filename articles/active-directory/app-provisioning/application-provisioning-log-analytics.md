---
title: 프로 비전이 Azure Active Directory의 Azure Monitor 로그와 통합 되는 방식을 이해 합니다.
description: 프로 비전이 Azure Active Directory의 Azure Monitor 로그와 통합 되는 방식을 이해 합니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: d3fd553e6903951de64c301df94ea9f2fc3eb471
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132094"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>프로 비전이 Azure Monitor 로그와 통합 되는 방식 이해

프로 비전은 Azure Monitor 로그 및 Log Analytics와 통합 됩니다. Azure 모니터링을 사용 하면 대시보드 라고도 하는 통합 문서 만들기, 30 일간의 프로 비전 로그 저장, 사용자 지정 쿼리 및 경고 만들기 등의 작업을 수행할 수 있습니다. 이 문서에서는 프로 비전 로그가 Azure Monitor 로그와 통합 되는 방법을 설명 합니다. 프로 비전 로그가 일반적으로 작동 하는 방식에 대 한 자세한 내용은 [로그 프로 비전](../reports-monitoring/concept-provisioning-logs.md)을 참조 하세요.

## <a name="enabling-provisioning-logs"></a>프로 비전 로그 사용

Azure 모니터링 및 Log Analytics에 대해 이미 잘 알고 있어야 합니다. 그렇지 않은 경우 계속 해 서 자세히 알아보고 응용 프로그램 프로 비전 로그에 대해 자세히 알아보세요. Azure 모니터링에 대해 자세히 알아보려면 [Azure Monitor 개요](../../azure-monitor/overview.md)를 참조 하세요. Azure Monitor 로그 및 Log Analytics에 대해 자세히 알아보려면 Azure Monitor의 [로그 쿼리 개요](../../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

Azure 모니터링을 구성한 후에는 응용 프로그램 프로 비전에 대해 로그를 사용 하도록 설정할 수 있습니다. 옵션은 **진단 설정** 페이지에 있습니다.

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> 작업 영역을 최근에 프로 비전 한 경우 로그를 보내기 전에 약간의 시간이 걸릴 수 있습니다. *Microsoft insights* 를 사용 하도록 구독을 등록 하지 않은 오류가 표시 되 면 몇 분 후에 다시 확인 하세요.
 
## <a name="understanding-the-data"></a>데이터 이해
프로 비전에서 로그 뷰어를 보내는 기본 데이터 스트림은 거의 동일 합니다. Azure Monitor 로그는 Azure Portal UI 및 Azure API와 거의 동일한 스트림을 가져옵니다. 로그 필드에는 다음 표에 설명 된 것 처럼 몇 가지 **차이점이** 있습니다. 이러한 필드에 대해 자세히 알아보려면 [ProvisioningObjectSummary 나열](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http&preserve-view=true)을 참조 하세요.

|Azure Monitor 로그   |Azure Portal UI   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|상태 |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Azure Monitor 통합 문서

Azure Monitor 통합 문서는 데이터 분석을 위한 유연한 캔버스를 제공 합니다. 또한 Azure Portal 내에서 풍부한 시각적 보고서를 만들 수 있도록 제공 합니다. 자세히 알아보려면 [Azure Monitor 통합 문서 개요](../../azure-monitor/platform/workbooks-overview.md)를 참조 하세요.

응용 프로그램 프로 비전은 미리 작성 된 통합 문서 집합과 함께 제공 됩니다. 이러한 항목은 통합 문서 페이지에서 찾을 수 있습니다. 데이터를 보려면 모든 필터 (timeRange, jobID, appName)가 채워져 있는지 확인 해야 합니다. 앱을 프로 비전 했는지 확인 해야 합니다. 그렇지 않으면 로그에 데이터가 없을 수도 있습니다.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>사용자 지정 쿼리

사용자 지정 쿼리를 만들고 Azure 대시보드에 데이터를 표시할 수 있습니다. 방법을 알아보려면 [Log Analytics 데이터의 대시보드 만들기 및 공유](../../azure-monitor/log-query/get-started-queries.md)를 참조 하세요. 또한 [Azure Monitor에서 로그 쿼리의 개요](../../azure-monitor/log-query/log-query-overview.md)를 확인 해야 합니다.

응용 프로그램 프로 비전을 시작 하는 몇 가지 샘플은 다음과 같습니다.

원본 시스템에서 해당 ID를 기반으로 사용자 a에 대 한 로그를 쿼리 합니다.
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

ErrorCode 당 개수 요약:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

작업별 이벤트 수를 작업별로 요약 합니다.
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

100 이벤트 및 프로젝트 키 속성을 사용 합니다.
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>사용자 지정 경고

Azure Monitor를 사용 하면 프로 비전 관련 주요 이벤트에 대 한 알림을 받을 수 있도록 사용자 지정 경고를 구성할 수 있습니다. 예를 들어 오류 급증에 대 한 경고를 받을 수 있습니다. 또는이 사용 하지 않도록 설정 되거나 삭제 될 수도 있습니다. 경고를 발생 시킬 수 있는 또 다른 예는 어떤 것이 잘못 되었음을 나타내는 프로 비전이 없다는 것입니다.

경고에 대해 자세히 알아보려면 [Azure Monitor 경고를 사용 하 여 이벤트에 응답](../../azure-monitor/learn/tutorial-response.md)을 참조 하세요.

오류가 급증 하는 경우 경고 합니다. JobID를 응용 프로그램에 대 한 jobID로 바꿉니다.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/alert1.png":::

프로 비전 서비스의 실행이 중지 되는 문제가 있을 수 있습니다. 지정 된 시간 간격 동안 프로 비전 이벤트가 없는 경우 다음 경고를 사용 하 여 검색 합니다.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/alert2.png":::

사용 하지 않도록 설정 하거나 삭제할 때 스파이크가 발생 하면 경고 합니다.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="진단 설정 액세스" lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>커뮤니티 기여

응용 프로그램 프로 비전 쿼리 및 대시보드에 대 한 오픈 소스 및 커뮤니티 기반 접근 방법을 사용 하 고 있습니다. 다른 사람이 유용 하다 고 생각 하는 쿼리, 경고 또는 통합 문서를 빌드한 경우 [Azuremonitorcommunity GitHub](https://github.com/microsoft/AzureMonitorCommunity)리포지토리에 게시 해야 합니다. 그런 다음 링크를 포함 한 전자 메일을 보내 주세요. 다른 사용자가 혜택을 받을 수 있도록 서비스를 검토 하 고 게시 합니다. provisioningfeedback@microsoft.com으로 문의할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Log analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Azure Monitor 로그에서 쿼리 시작](../../azure-monitor/log-query/get-started-queries.md)
- [Azure Portal에서 경고 그룹 만들기 및 관리](../../azure-monitor/platform/action-groups.md)
- [Azure Active Directory용 로그 분석 보기 설치 및 사용](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [프로 비전 로그 API](https://docs.microsoft.com/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta.md&preserve-view=true)
