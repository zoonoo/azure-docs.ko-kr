---
title: 메트릭 경고를 통해 Azure Automation Runbook 모니터링
description: 이 문서에서는 runbook 완료 상태에 따라 메트릭 경고를 설정 하는 방법을 설명 합니다.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 8767687f0b72d3469bef570770ac81fa8300097f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88055934"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>메트릭 경고로 Runbook 모니터링

이 문서에서는 runbook 완료 상태에 따라 [메트릭 경고](../azure-monitor/platform/alerts-metric-overview.md) 를 만드는 방법에 대해 알아봅니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털](https://portal.azure.com)

## <a name="create-alert"></a>경고 만들기

경고를 사용하면 모니터링할 조건 및 조건이 충족될 때 수행할 작업을 정의할 수 있습니다.

1. **모든 서비스**를 클릭 한 다음 **Automation 계정**을 검색 하 고 선택 하 여 Azure Portal에서 Azure Automation 서비스를 시작 합니다.

2. Automation 계정 목록에서 경고를 생성 하려는 계정을 선택 합니다. 

3. **모니터링**아래에서 **경고** 를 선택한 다음 **+ 새 경고 규칙**을 선택 합니다. 대상의 범위는 이미 정의 되어 있으며 Automation 계정과 연결 되어 있습니다.

### <a name="configure-alert-criteria"></a>경고 기준 구성

1. **조건 선택**을 클릭 합니다. **신호 유형에**대해 **메트릭** 을 선택 하 고 목록에서 **총 작업** 을 선택 합니다.

2. **신호 논리 구성** 페이지에서는 경고를 트리거하는 논리를 정의합니다. 과거 그래프 아래를 보면 **Runbook 이름** 및 **상태**의 두 가지 차원이 제공됩니다. 차원은 결과 필터링에 사용할 수 있는 메트릭에 대한 서로 다른 속성입니다. **Runbook 이름**에서는 경고할 Runbook을 선택하거나 모든 Runbook에 대해 경고하도록 비워 둡니다. **상태**에서는 모니터링할 상태를 드롭다운에서 선택합니다. 드롭다운에 표시되는 Runbook 이름과 상태 값은 지난 주에 실행된 작업에만 해당합니다.

   드롭다운에 표시 되지 않은 상태 또는 runbook에 대 한 경고를 표시 하려면 차원 옆에 있는 **사용자 지정 값 추가** 옵션을 클릭 합니다. 이 작업을 수행 하면 해당 차원에 대해 내보내지 않은 사용자 지정 값을 지정할 수 있는 대화 상자가 열립니다. 속성에 없는 값을 입력하면 경고가 트리거되지 않습니다.

   > [!NOTE]
   > **Runbook 이름** 차원의 이름을 지정 하지 않으면 숨겨진 시스템 runbook을 포함 하는 상태 조건에 맞는 runbook이 있는 경우 경고를 받게 됩니다.

    예를 들어 runbook에서 _실패_ 상태를 반환 하는 경우 경고를 생성 하는 것 외에도 runbook 이름을 지정 하는 것 외에도 **상태** 차원에 대해 사용자 지정 차원 값을 추가 **하지 못했습니다**.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="사용자 지정 차원 값 지정" border="false":::

3. **경고 논리** 아래에서 경고의 조건 및 임계값을 정의합니다. 정의한 조건의 미리 보기가 그 아래에 표시됩니다.

4. **평가 기준**에서 쿼리의 timespan을 선택 하 고 쿼리를 실행할 빈도를 선택 합니다. 예를 들어 **기간**에 대해 **최근 5 분** 을 선택 하 고 **빈도**에 대해 **1 분 마다** 선택 하면이 경고는 지난 5 분 동안 조건을 충족 하는 runbook의 수를 찾습니다. 이 쿼리는 1분마다 실행되며, 정의한 경고 조건을 5분이라는 기간 내에 더 이상 찾을 수 없으면 경고에서 자체적으로 확인합니다. 여기까지 마쳤으면 **완료**를 클릭합니다.

   ![경고를 사용할 리소스 선택](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>수행할 작업 정의

1. **작업 그룹**에서 **작업 그룹 지정**을 선택 합니다. 작업 그룹은 하나 이상의 경고에서 사용할 수 있는 작업의 그룹입니다. 여기에는 이메일 알림, Runbook, 웹후크 등이 포함되며 이에 국한되지 않습니다. 작업 그룹 및 전자 메일 알림을 보내는 단계를 만드는 단계에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조 하세요.

### <a name="define-alert-details"></a>경고 세부 정보 정의

1. **경고 규칙 세부 정보**에서 경고에 친숙 한 이름 및 설명을 제공 합니다. 경고 조건에 맞는 **심각도**를 설정합니다. 0부터 5까지 다섯 가지 심각도가 있습니다. 경고는 심각도에 관계 없이 동일하게 처리되며, 비즈니스 논리에 맞게 심각도를 일치시킬 수 있습니다.

1. 규칙을 **만들 때 경고 규칙 사용**옵션에 대해 **아니요** 를 선택 하지 않는 한, 기본적으로 규칙을 만들 때 사용 하도록 설정 됩니다. 사용 하지 않도록 설정 된 경고의 경우 나중에 준비 되 면 사용할 수 있습니다. **경고 규칙 만들기** 를 선택 하 여 변경 내용을 저장 합니다.

## <a name="receive-notification"></a>알림 수신

경고 조건이 충족되면 작업 그룹은 정의된 작업을 실행합니다. 이 문서의 예제에서는 이메일이 전송됩니다. 다음 이미지는 경고가 트리거된 후 수신하는 이메일의 예제입니다.

![전자 메일 경고](./media/automation-alert-activity-log/alert-email.png)

메트릭이 더 이상 정의된 임계값을 초과하지 않으면 경고가 비활성화되고 작업 그룹은 정의된 작업을 실행합니다. 이메일 작업 유형을 선택하면 확인되었음을 알리는 확인 이메일이 전송됩니다.

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [경고를 사용하여 Azure Automation Runbook 트리거](automation-create-alert-triggered-runbook.md)를 참조하세요.
