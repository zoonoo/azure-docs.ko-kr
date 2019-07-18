---
title: Azure Monitor 경고에 대 한 작업 규칙
description: Azure Monitor에서 작업 규칙 이란 무엇 이며 구성 및 관리 하는 방법을 이해 합니다.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656731"
---
# <a name="action-rules-preview"></a>작업 규칙 (미리 보기)

작업 규칙을 쉽게 정의 하거나 모든 Azure 리소스 관리자 범위 (예: Azure 구독, 리소스 그룹 또는 대상 리소스)에서 작업을 표시 하지 않을 수 있습니다. 도움말 좁힐 있습니다 경고 인스턴스의 특정 하위 집합을 작업할 하고자 하는 다양 한 필터가 있습니다.

## <a name="why-and-when-should-you-use-action-rules"></a>이유와 시기 작업 규칙을 사용 하나요?

### <a name="suppression-of-alerts"></a>경고 표시 안 함

경고를 생성 하는 알림을 표시 하지 않으려면 유용한 경우 많은 시나리오가 있습니다. 이러한 시나리오는 계획 된 유지 관리 기간 중에 비 표시에 이르는 업무 외 시간 중에 비 표시 합니다. 예를 들어, 팀 **ContosoVM** 때문에 예정 된 주말에 대 한 알림을 표시 하지 않으려면 하려고 **ContosoVM** 계획 된 유지 관리 하는 중입니다. 

팀에서 구성한 각 경고 규칙을 사용 하지 않도록 설정할 수 있지만 **ContosoVM** 수동으로 (및 유지 관리 후에 다시 사용 하도록 설정)은 간단한 프로세스가 아닙니다. 작업 규칙 유연 하 게 제거 기간을 구성 하는 기능을 사용 하 여 대규모로 경고 제거를 정의할 수 있습니다. 이전 예제에서는 팀 하나의 작업 규칙 정의에 **ContosoVM** 하는 모든 알림을 주말에 대 한 표시 하지 않습니다.


### <a name="actions-at-scale"></a>대규모 작업

경고 규칙에는 경고가 생성 되 면 트리거되는 작업 그룹을 정의할 수 있습니다, 있지만 고객 경우가 일반적인 작업 그룹을 작업의 범위에서. 예를 들어, 팀 리소스 그룹 **ContosoRG** 내에 정의 된 모든 경고 규칙에 대해 동일한 작업 그룹 정의 아마도 **ContosoRG**합니다. 

작업 규칙에서는이 프로세스를 간소화할 수 있습니다. 대규모 작업을 정의 하 여 구성된 된 범위에서 생성 되는 모든 경고에 대 한 작업 그룹을 트리거할 수 있습니다. 이전 예제에서는 팀 하나의 작업 규칙 정의에 **ContosoRG** 동일한 작업 그룹에 생성 된 모든 경고에 대 한 트리거됩니다.

> [!NOTE]
> Azure Service Health 경고는 현재 작업 규칙이 적용 되지 않습니다.

## <a name="configuring-an-action-rule"></a>작업 규칙 구성

선택 하 여 기능을 사용할 수 있습니다 **작업을 관리할** 에서 합니다 **경고** Azure Monitor의 방문 페이지입니다. 그런 다음 선택 **작업 규칙 (미리 보기)** 합니다. 규칙을 선택 하 여 액세스할 수 있습니다 **작업 규칙 (미리 보기)** 대시보드에서 경고에 대 한 방문 페이지입니다.

![Azure Monitor 방문 페이지에서 작업 규칙](media/alerts-action-rules/action-rules-landing-page.png)

선택 **+ 새 작업 규칙**합니다. 

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-new-rule.png)

또는 경고 규칙을 구성 하는 동안 작업 규칙을 만들 수 있습니다.

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-alert-rule.png)

이제 흐름을 만들기 위한 페이지 작업 규칙이 표시 됩니다. 다음 요소를 구성 합니다. 

![새 작업 규칙 만들기 흐름](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>범위

먼저 범위 (예: Azure 구독, 리소스 그룹 또는 대상 리소스)를 선택 합니다. 선택할 수 있습니다도 여러-단일 구독 내에서 범위의 조합입니다.

![작업 규칙 범위](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>필터 조건

또한 경고의 특정 하위 집합으로 범위를 좁힐 수 있는 필터를 정의할 수 있습니다. 

사용 가능한 필터는 다음과 같습니다. 

* **심각도**: 하나 이상의 경고 심각도 선택 하는 옵션입니다. **심각도 = Sev1** Sev1로 모든 경고 설정에 대 한 작업 규칙을 적용할 수 있는지를 의미 합니다.
* **서비스 모니터링**: 원래 모니터링 서비스를 기반으로 하는 필터입니다. 이 필터는 또한 다중 선택 합니다. 예를 들어 **Monitor 서비스 "Application Insights" =** 작업 규칙은 모든 Application Insights 기반 경고에 대해 해당 되는 것을 의미 합니다.
* **리소스 종류**:  특정 리소스 종류를 기준으로 필터입니다. 이 필터는 또한 다중 선택 합니다. 예를 들어 **리소스 종류 = "가상 머신"** 작업 규칙은 모든 가상 컴퓨터에 적용할 수는 것을 의미 합니다.
* **경고 규칙 ID**: 경고 규칙의 Resource Manager ID를 사용 하 여 특정 경고 규칙에 대 한 필터링 하는 옵션입니다.
* **조건 모니터링**:  사용 하 여 경고 인스턴스에 대 한 필터 **발생** 또는 **Resolved** 모니터 조건으로 합니다.
* **설명**: 경고 규칙의 일부로 정의 된 설명에 대 한 문자열 일치를 정의 하는 (정규식) 정규식 일치 항목입니다. 예를 들어 **'prod' 포함 된 설명을** "prod" 문자열이 포함 된 모든 경고의 설명에 일치 합니다.
* **경고 컨텍스트 (페이로드)** : 경고의 페이로드의 경고 컨텍스트 필드에 대해 일치 하는 문자열을 정의 하는 정규식 일치 항목입니다. 예를 들어 **' 컴퓨터-01'를 포함 하는 컨텍스트 (페이로드) 경고** 인 페이로드는 "컴퓨터-01" 문자열을 포함 하는 모든 경고는 일치

이러한 필터는 서로 함께 적용 됩니다. 예를 들어, 설정 하는 경우 **리소스 종류 ' Virtual Machines =** 하 고 **심각도 ' Sev0 =** , 모든 필터링 한 다음 **Sev0** 만 Vm에 대 한 경고입니다. 

![작업 규칙 필터](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>표시 안 함 또는 작업 그룹 구성

다음으로 경고 표시 안 함 또는 작업 그룹 지원에 대 한 작업 규칙을 구성 합니다. 둘 다를 선택할 수 없습니다. 구성 이전에 정의 된 범위 및 필터와 일치 하는 모든 경고 인스턴스에서 작동 합니다.

#### <a name="suppression"></a>표시 안 함

선택 하는 경우 **억제**, 작업 및 알림 억제에 대 한 기간을 구성 합니다. 다음 옵션 중 하나를 선택합니다.
* **지금부터 (항상)** : 무기한 모든 알림을 표시 하지 않습니다.
* **예약된 된 시간에**: 제한 기간 내에서 알림을 표시 하지 않습니다.
* **되풀이 사용 하 여**: 일별, 주별 또는 월별 되풀이 일정에 따라 알림을 표시 하지 않습니다.

![작업 규칙 표시 안 함](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>작업 그룹

선택 하는 경우 **작업 그룹** 에서 설정/해제, 기존 작업 그룹을 추가 하거나 새로 만듭니다. 

> [!NOTE]
> 작업 규칙을 사용 하 여 하나의 작업 그룹을 연결할 수 있습니다.

![추가 하거나 작업 그룹을 선택 하 여 새 작업 규칙 만들기](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>작업 규칙 세부 정보

마지막으로, 작업 규칙에 대 한 다음 세부 정보를 구성 합니다.
* 이름
* 저장 되는 리소스 그룹
* 설명 

## <a name="example-scenarios"></a>예제 시나리오

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>시나리오 1: 심각도에 따라 경고 표시 안 함

Contoso 구독 내에서 모든 Vm에 대 한 모든 Sev4 경고에 대 한 알림을 표시 하지 않으려면 **ContosoSub** 주말 마다.

**해결 방법:** 작업 규칙을 만듭니다.
* Scope = **ContosoSub**
* 필터
    * 심각도 = **Sev4**
    * 리소스 유형 = **Virtual Machines**
* 매주로 설정 하는 되풀이 사용 하 여 제거 하 고 **토요일** 하 고 **일요일** 확인

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>시나리오 2: 경고 컨텍스트 (페이로드)를 기반으로 경고 표시 안 함

Contoso에서는 모든 로그에 대해 생성 된 경고에 대 한 알림을 표시 하지 않으려면 **컴퓨터-01** 에 **ContosoSub** 무기한으로 과정을 진행 유지 관리 합니다.

**해결 방법:** 작업 규칙을 만듭니다.
* Scope = **ContosoSub**
* 필터
    * 서비스 모니터링 = **Log Analytics**
    * 경고 컨텍스트 (페이로드)를 포함 **컴퓨터-01**
* 비 표시로 **지금부터 (항상)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>시나리오 3: 리소스 그룹에서 정의한 작업 그룹

Contoso가 정의한 [구독 수준에서 메트릭 경고](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)합니다. 리소스 그룹에서 생성 된 경고에 대 한 특히 트리거하는 동작을 정의 하려고 하지만 **ContosoRG**합니다.

**해결 방법:** 작업 규칙을 만듭니다.
* Scope = **ContosoRG**
* 필터가 없습니다
* 작업 그룹 설정 **ContosoActionGroup**

> [!NOTE]
> *작업 그룹 작업 규칙 내에서 정의 하 고 경고 규칙 독립적으로 작동할 수 없는 중복 제거를 사용 하 여 키를 누릅니다.* 설명한 시나리오에서 경고 규칙에 대해 정의 된 경우 작업 그룹, 작업 규칙에 정의 된 작업 그룹과 함께에서 트리거됩니다. 

## <a name="managing-your-action-rules"></a>작업 규칙 관리

확인 하 고 목록 보기에서 작업 규칙을 관리할 수 있습니다.

![작업 규칙 목록 보기](media/alerts-action-rules/action-rules-list-view.png)

여기에서 사용 하도록 설정, 해제 또는 옆에 있는 확인란을 선택 하 여 대규모로 작업 규칙을 삭제할 수 있습니다. 작업 규칙을 선택 하면 해당 구성 페이지가 열립니다. 페이지를 사용 하면 작업 규칙의 정의 업데이트 하 고 활성화 하거나 비활성화할 수 있습니다.

## <a name="best-practices"></a>모범 사례

로그 경고로 만든 합니다 [결과의 수](alerts-unified-log.md) 옵션 (여러 컴퓨터에 걸쳐 있을 수 있습니다)는 전체 검색 결과 사용 하 여 단일 경고 인스턴스를 생성 합니다. 작업 규칙을 사용 하는 경우이 시나리오에서는 합니다 **경고 컨텍스트 (페이로드)** 필터 동작 경고 인스턴스에 일치 하는 것으로 합니다. 시나리오 2에서 설명한 생성 된 로그 경고에 대 한 검색 결과가 모두 포함 하는 경우 **컴퓨터-01** 하 고 **컴퓨터-02**, 전체 알림이 표시 되지 않습니다. 에 대해 생성 된 알림이 없기 **컴퓨터-02** 전혀 합니다.

![작업 규칙 및 로그 경고 (결과 수)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

작업 규칙을 사용 하 여 로그 경고를 사용할 가장을 사용 하 여 로그 경고 만들기를 [미터법](alerts-unified-log.md) 옵션입니다. 별도 경고 인스턴스는 해당 정의 된 그룹 필드를 기준으로이 옵션으로 생성 됩니다. 그런 다음 시나리오 2에서 개별 경고 인스턴스 생성 됩니다 **컴퓨터-01** 하 고 **컴퓨터-02**합니다. 에 대 한 알림만 시나리오에서 설명 된 작업 규칙으로 인해 **컴퓨터-01** 표시 되지 않습니다. 에 대 한 알림을 **컴퓨터-02** 계속 정상적으로 실행 됩니다.

![작업 규칙 및 로그 경고 (결과 수)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>작업 규칙을 독립, 하는 동안 알림이 중복 수신 방지할 수 있도록 작업 규칙 겹치는 모든 가능한 참조 싶습니다. 이렇게 하려면 가능한 것은 무엇입니까?

작업 규칙을 구성할 때 범위를 정의한 후 (있는 경우) 동일한 범위에서 겹치는 동작 규칙의 목록을 볼 수 있습니다. 이 처럼 중복 되는 다음 옵션 중 하나일 수 있습니다.

* 정확히 일치: 예를 들어, 정의 하는 작업 규칙와 겹치는 작업 규칙 동일한 구독에 있습니다.
* 하위 집합: 예를 들어, 구독을 정의 하는 작업 규칙은 이며, 겹치는 작업 규칙 구독 내에서 리소스 그룹입니다.
* 상위 집합: 예를 들어, 리소스 그룹을 정의 하는 작업 규칙은 겹치는 작업 규칙은 리소스 그룹을 포함 하는 구독을
* 교집합: 정의 하는 작업 규칙은 예를 들어 **VM1** 하 고 **VM2**, 겹치는 작업 규칙 이며 **VM2** 및 **VM3**합니다.

![겹치는 작업 규칙](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>경고 규칙을 독립를 정의 하는 경고 규칙에서 동작을 수행할 수 있는지 알고 이미 작업 규칙을 정의 하는 수는?

경고 규칙의 대상 리소스를 정의한 후 (있는 경우)을 선택 하 여 동일한 범위에서 작동 하는 작업 규칙의 목록을 볼 수 있습니다 **뷰에서 작업을 구성할** 아래 합니다 **작업** 섹션. 이 목록은 범위에 대 한 다음과 같은 시나리오에 따라 채워집니다.

* 정확히 일치: 예를 들어, 정의 하는 경고 규칙 및 작업 규칙에 동일한 구독입니다.
* 하위 집합: 예를 들어, 구독을 정의 하는 경고 규칙은 구독 내의 리소스 그룹에 있는 작업 규칙 인 하며
* 상위 집합: 예를 들어, 리소스 그룹을 정의 하는 경고 규칙은 리소스 그룹을 포함 하는 구독에 있는 작업 규칙 인 하며
* 교집합: 정의 하는 경고 규칙은 예를 들어 **VM1** 하 고 **VM2**, 및 작업 규칙은 **VM2** 및 **VM3**합니다.
    
![겹치는 작업 규칙](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>작업 규칙에 의해 기록 되지 않은 경고를 볼 수 있나요?

에 [경고 목록 페이지](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), 이라는 추가 열을 선택할 수 있습니다 **표시 안 함 상태**합니다. 경고 인스턴스에 대 한 알림 억제 된 경우 해당 상태 목록에 나타납니다.

![경고 억제 된 인스턴스](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>작업 그룹을 사용 하 여 작업 규칙을 지정 하 고 제거를 사용 하 여 다른 활성에 있으면 같은 범위 어떻게 되나요?

동일한 범위에서 제거 항상 우선합니다.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>두 개의 별도 작업 규칙에서 모니터링 되는 리소스를 질문이 있으면 어떻게 되나요? 하나 또는 두 개의 알림을 가져오나 요? 예를 들어 **VM2** 다음 시나리오에서:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

VM1 및 VM3에 모든 경고에 대 한 작업 그룹 AG1 트리거될 수 한 번입니다. 에 대 한 모든 경고에 대 한 **VM2**, 작업 그룹 AG1 트리거될 수를 두 번 하므로 작업 규칙 작업을 중복 제거 하지 마세요. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>두 개의 별도 작업 규칙에서 모니터링 리소스 있고 제거에 대 한 다른 작업에 대 한 하나를 호출 하는 경우 어떻게 됩니까? 예를 들어 **VM2** 다음 시나리오에서:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

VM1의 모든 경고에 대 한 작업 그룹 AG1 트리거될 수 한 번입니다. 작업 및 VM2 및 VM3에 대 한 모든 경고에 대 한 알림을 표시 되지 것입니다. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>경고 규칙 및 동일한 리소스 호출 하는 다른 작업 그룹에 대해 정의 된 작업 규칙 질문이 있으면 어떻게 되나요? 예를 들어 **VM1** 다음 시나리오에서:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
VM1의 모든 경고에 대 한 작업 그룹 AG1 트리거될 수 한 번입니다. 경고 규칙 "rule1" 트리거될 때마다도 트리거합니다 AG2를 또한. 작업 그룹 작업 규칙 내에서 정의 하 고 경고 규칙 독립적으로 작동할 수 없는 중복 제거를 사용 하 여 키를 누릅니다. 

## <a name="next-steps"></a>다음 단계

- [Azure에서 경고에 자세히 알아보기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
