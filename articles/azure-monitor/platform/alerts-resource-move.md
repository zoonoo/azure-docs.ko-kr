---
title: 대상 리소스가 다른 Azure 지역으로 이동 될 때 경고 규칙 또는 작업 규칙을 업데이트 하는 방법
description: 대상 리소스를 다른 Azure 지역으로 이동할 때 경고 규칙 또는 작업 규칙을 업데이트 하는 방법에 대 한 배경 및 지침입니다.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 4ea5c8552d35db67a1d2caf20c0143c74cdd642e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505485"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>대상 리소스가 다른 Azure 지역으로 이동 될 때 경고 규칙 또는 작업 규칙을 업데이트 하는 방법

이 문서에서는 지역 간에 다른 Azure 리소스를 이동할 때 기존 [경고 규칙](./alerts-overview.md) 및 [작업 규칙이](./alerts-action-rules.md) 영향을 받을 수 있는 이유와 이러한 문제를 식별 하 고 해결 하는 방법을 설명 합니다. 주요 리소스 이동 [설명서](../../azure-resource-manager/management/move-region.md) 에서 유용한 지역 간 리소스 이동의 경우와 이동 프로세스를 디자인 하는 검사 목록에 대 한 추가 정보를 확인 하세요.

## <a name="why-the-problem-exists"></a>문제가 존재 하는 이유

경고 규칙 및 작업 규칙은 다른 Azure 리소스를 참조 합니다. 예를 들면 [Azure vm](../../site-recovery/azure-to-azure-tutorial-migrate.md), [azure SQL](../../azure-sql/database/move-resources-across-regions.md)및 [Azure Storage](../../storage/common/storage-account-move.md)있습니다. 이러한 규칙이 참조 하는 리소스를 이동 하는 경우 규칙은 참조 하는 리소스를 찾을 수 없기 때문에 올바르게 작동 하지 않을 수 있습니다.

대상 리소스를 이동한 후 규칙의 작동이 중지 될 수 있는 두 가지 주요 이유는 다음과 같습니다.

- 규칙의 범위는 기존 리소스를 명시적으로 참조 합니다.
- 경고 규칙은 메트릭을 기반으로 합니다.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>규칙 범위는 기존 리소스를 명시적으로 참조 합니다.

리소스를 이동 하면 대부분의 경우 리소스 ID가 변경 됩니다. 시스템은 백그라운드에서 기존 지역에서 삭제 하기 전에 리소스를 새 지역에 복제 합니다. 이 프로세스를 수행 하려면 두 개의 리소스와 두 개의 다른 리소스 Id가 작은 시간 동안 동시에 존재 해야 합니다. 리소스 Id는 고유 해야 하므로 프로세스 중에 새 ID를 만들어야 합니다. 

**리소스 이동이 기존 규칙에 미치는 영향**

경고 규칙 및 작업 규칙에는 적용 되는 리소스 범위가 있습니다. 범위는 전체 구독, 리소스 그룹 또는 하나 이상의 특정 리소스가 될 수 있습니다.
예를 들어 다음은 두 개의 리소스 (2 개의 가상 머신)가 있는 범위에 대 한 규칙입니다.

![다중 리소스 경고 규칙](media/alerts-resource-move/multi-resource-alert-rule.png)

규칙 범위에서 리소스를 명시적으로 언급 하 고 해당 리소스가 이동 하 여 리소스 ID를 변경한 경우 해당 규칙은 잘못 되었거나 존재 하지 않는 리소스를 검색 하므로 실패 합니다.

**문제를 해결 하는 방법**

영향을 받는 규칙을 업데이트 하거나 다시 만들어 새 리소스를 가리키도록 합니다. 범위를 업데이트 하는 프로세스는이 문서의 뒷부분에 나와 있습니다.

이 문제는 다음 규칙 유형에 적용 됩니다.

- 활동 로그 경고 규칙
- 작업 규칙
- 클래식 경고
- 메트릭 경고 – 자세한 내용은 [메트릭 기반 경고 규칙](#alert-rules-based-on-metrics)다음 섹션을 참조 하세요.

> [!NOTE]
> 로그 검색 경고 규칙 및 스마트 탐지기 경고 규칙의 범위는 작업 영역 이거나 Application Insights 이므로 영향을 받지 않습니다. 현재 이러한 범위는 지역 이동을 지원 하지 않습니다.

## <a name="alert-rules-based-on-metrics"></a>메트릭 기반 경고 규칙

Azure 리소스에서 내보내는 메트릭은 지역입니다. 리소스가 새 지역으로 이동 될 때마다 해당 새 지역에서 해당 메트릭의 내보내기가 시작 됩니다. 따라서 메트릭을 기반으로 하는 모든 경고 규칙은 올바른 지역의 현재 메트릭 스트림을 가리키도록 업데이트 하거나 다시 만들어야 합니다.

이 설명은 [메트릭 경고 규칙과](alerts-metric-overview.md) [가용성 테스트 경고 규칙](../app/monitor-web-app-availability.md)에 모두 적용 됩니다.

범위에 있는 **모든** 리소스를 이동한 경우 규칙을 다시 만들 필요가 없습니다. 경고 규칙 설명과 같은 경고 규칙의 모든 필드를 업데이트 하 고 저장할 수 있습니다.
범위에 있는 리소스 중 **일부만** 이동한 경우 기존 규칙에서 이동한 리소스를 제거 하 고 이동한 리소스만 포함 하는 새 규칙을 만들어야 합니다.

## <a name="procedures-to-fix-problems"></a>문제를 해결 하는 절차

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Azure Portal 이동 된 리소스와 연결 된 규칙 식별

- **경고 규칙에 대 한** 경고로 이동 하 여 경고 규칙 관리 > 포함 하는 구독 및 이동한 리소스를 기준으로 필터링을 > 합니다.
> [!NOTE]
> 활동 로그 경고 규칙은이 프로세스를 지원 하지 않습니다. 활동 로그 경고 규칙의 범위를 업데이트 하 고 다른 구독의 리소스를 가리키도록 할 수는 없습니다. 대신, 기존 규칙을 대체할 새 규칙을 만들 수 있습니다.

- **작업 규칙에 대 한** 경고로 이동 하 여 작업 규칙 > 작업 규칙 (미리 보기) > 포함 된 구독과 이동한 리소스를 기준으로 필터링 > 합니다.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Azure Portal 규칙의 범위 변경

1. 이전 단계에서 확인 한 규칙을 클릭 하 여 엽니다.
2. **리소스**에서 **편집** 을 클릭 하 고 필요에 따라 범위를 조정 합니다.
3. 필요에 따라 규칙의 다른 속성을 조정 합니다.
4. **저장**을 클릭합니다.

![경고 규칙 범위 변경](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 규칙의 범위 변경

1. 규칙의 Azure Resource Manager 템플릿을 가져옵니다.  Azure Portal에서 규칙의 템플릿을 내보내려면:
   1. 포털에서 리소스 그룹 섹션으로 이동 하 고 규칙을 포함 하는 리소스 그룹을 엽니다.
   2. 개요 섹션에서 **숨김 유형 표시** 확인란을 선택 하 고 규칙의 관련 유형을 기준으로 필터링 합니다.
   3. 관련 규칙을 선택 하 여 세부 정보를 확인 합니다.
   4. **설정**아래에서 **템플릿 내보내기**를 선택 합니다.
2. 템플릿을 수정 합니다. 필요한 경우 위에 나와 있는 것 처럼 메트릭 경고의 경우와 관련 된 두 가지 규칙으로 분할 합니다.
3. 템플릿을 다시 배포합니다.

### <a name="change-scope-of-a-rule-using-rest-api"></a>REST API를 사용 하 여 규칙의 범위 변경

1. 기존 규칙 가져오기 ([메트릭 경고](/rest/api/monitor/metricalerts/get), [활동 로그 경고](/rest/api/monitor/activitylogalerts/get))
2. 범위 수정 ([활동 로그 경고](/rest/api/monitor/activitylogalerts/update))
3. 규칙 다시 배포 ([메트릭 경고](/rest/api/monitor/metricalerts/createorupdate), [활동 로그 경고](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>PowerShell을 사용 하 여 규칙의 범위 변경

1. 기존 규칙 ([메트릭 경고](/powershell/module/az.monitor/get-azmetricalertrulev2), [활동 로그 경고](/powershell/module/az.monitor/get-azactivitylogalert), [작업 규칙](/powershell/module/az.alertsmanagement/get-azactionrule))을 가져옵니다.
2. 범위를 수정 합니다. 필요한 경우 위에 나와 있는 것 처럼 메트릭 경고의 경우와 관련 된 두 가지 규칙으로 분할 합니다.
3. 규칙을 다시 배포 합니다 ([메트릭 경고](/powershell/module/az.monitor/add-azmetricalertrulev2), [활동 로그 경고](/powershell/module/az.monitor/enable-azactivitylogalert), [동작 규칙](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Azure CLI를 사용 하 여 규칙의 범위 변경

1.  기존 규칙 ([메트릭 경고](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-show), [활동 로그 경고](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list))을 가져옵니다.
2.  규칙 범위를 직접 업데이트 ([메트릭 경고](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), [활동 로그 경고](/cli/azure/monitor/activity-log/alert/scope))
3.  필요한 경우 위에 나와 있는 것 처럼 메트릭 경고의 경우와 관련 된 두 가지 규칙으로 분할 합니다.

## <a name="next-steps"></a>다음 단계

[경고 알림](alerts-troubleshoot.md), [메트릭 경고](alerts-troubleshoot-metric.md)및 [로그 경고](alerts-troubleshoot-log.md)와 관련 된 기타 문제를 해결 하는 방법에 대해 알아봅니다. 
