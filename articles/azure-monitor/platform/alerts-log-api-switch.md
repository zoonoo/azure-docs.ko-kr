---
title: 현재 Azure Monitor 로그 경고 API로 업그레이드
description: 로그 경고 ScheduledQueryRules API로 전환 하는 방법에 대해 알아봅니다.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 868a8eb6cf38d471eb9dc1f47c903404d05ffc0c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91294515"
---
# <a name="upgrade-to-the-current-log-alerts-api-from-legacy-log-analytics-alert-api"></a>레거시 Log Analytics 경고 API에서 현재 로그 경고 API로 업그레이드

> [!NOTE]
> 이 문서는 Azure 공용 (Azure Government 또는 Azure 중국 클라우드**아님** )에만 해당 됩니다.

> [!NOTE]
> 사용자가 기본 설정을 현재 [SCHEDULEDQUERYRULES api](/rest/api/monitor/scheduledqueryrules) 로 전환 하기로 선택 하면 이전 [레거시 Log Analytics 경고 API](api-alerts.md)로 되돌릴 수 없습니다.

이전에는 사용자가 [레거시 Log Analytics 경고 API](api-alerts.md) 를 사용 하 여 로그 경고 규칙을 관리 했습니다. 현재 작업 영역에서는 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)를 사용 합니다. 이 문서에서는 레거시 API에서 현재 API로 전환 하는 과정 및 이점에 대해 설명 합니다.

## <a name="benefits"></a>이점

- 경고 규칙을 만들기 위한 단일 템플릿 (이전에는 세 개의 별도 템플릿이 필요 함).
- Log Analytics 작업 영역 또는 Application Insights 리소스 모두에 대 한 단일 API입니다.
- [PowerShell cmdlet 지원](alerts-log.md#managing-log-alerts-using-powershell).
- 다른 모든 경고 유형과 심각도 맞춤.
- Log Analytics 작업 영역 또는 Application Insights 리소스와 같은 여러 외부 리소스에 걸쳐 있는 [작업 영역 로그 경고](../log-query/cross-workspace-query.md) 를 만들 수 있습니다.
- 사용자는 ' Aggregate On ' 매개 변수를 사용 하 여 경고를 나눌 차원을 지정할 수 있습니다.
- 로그 경고에는 최대 2 일 동안의 데이터가 연장 됩니다 (이전에는 1 일로 제한 됨).

## <a name="impact"></a>영향

- 현재 API를 사용 하 여 모든 새 규칙을 만들고 편집 해야 합니다. [Azure 리소스 템플릿을 통한 샘플 사용](alerts-log-create-templates.md) 및 [PowerShell을 통한 샘플 사용](alerts-log.md#managing-log-alerts-using-powershell)을 참조 하세요.
- 규칙은 현재 API에서 추적 된 리소스가 Azure Resource Manager 되며 고유 해야 합니다. 규칙 리소스 ID는이 구조 ()로 변경 됩니다. `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` 경고 규칙의 표시 이름은 변경 되지 않고 그대로 유지 됩니다.

## <a name="process"></a>Process

전환 과정은 대화형이 아니며 대부분의 경우 수동 단계가 필요 하지 않습니다. 경고 규칙은 스위치 도중 이나 중단 되지 않습니다.
특정 Log Analytics 작업 영역과 연결 된 모든 경고 규칙을 전환 하려면이 호출을 수행 합니다.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

아래 JSON을 포함 하는 요청 본문:

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

위의 API 호출을 간소화 하는 오픈 소스 명령줄 도구인 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용 하는 예제는 다음과 같습니다.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

스위치가 성공적으로 수행 되 면 응답은 다음과 같습니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

## <a name="check-switching-status-of-workspace"></a>작업 영역의 전환 상태 확인

이 API 호출을 사용 하 여 스위치 상태를 확인할 수도 있습니다.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

[ARMClient](https://github.com/projectkudu/ARMClient) 도구를 사용할 수도 있습니다.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Log Analytics 작업 영역이 [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)로 전환 된 경우 응답은 다음과 같습니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Log Analytics 작업 영역이 전환 되지 않은 경우 응답은 다음과 같습니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor - 로그 경고](alerts-unified-log.md)에 대해 알아봅니다.
- [API를 사용 하 여 로그 경고를 관리](alerts-log-create-templates.md)하는 방법을 알아봅니다.
- [PowerShell을 사용 하 여 로그 경고를 관리](alerts-log.md#managing-log-alerts-using-powershell)하는 방법을 알아봅니다.
- [Azure Alerts 환경](./alerts-overview.md)에 대해 자세히 알아봅니다.
