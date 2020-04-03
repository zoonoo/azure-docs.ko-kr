---
title: Azure 모니터 경고에 대한 작업 규칙
description: Azure Monitor의 작업 규칙과 구성 및 관리 방법을 이해합니다.
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6585890395d7656f239ac3098cd374ecd4757842
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618979"
---
# <a name="action-rules-preview"></a>작업 규칙(미리 보기)

작업 규칙은 Azure 리소스 관리자 범위(Azure 구독, 리소스 그룹 또는 대상 리소스)에서 작업을 정의하거나 억제하는 데 도움이 됩니다. 이러한 필터에는 수행할 경고 인스턴스의 특정 하위 집합을 좁히는 데 도움이 되는 다양한 필터가 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>작업 규칙을 사용해야 하는 이유와 시기는 무엇입니까?

### <a name="suppression-of-alerts"></a>경고 억제

경고가 생성하는 알림을 표시하지 않는 것이 유용한 많은 시나리오가 있습니다. 이러한 시나리오는 계획된 유지 관리 기간 중 의 억제에서 업무 외 시간 동안의 억제에 이르기까지 다양합니다. 예를 들어 **ContosoVM을** 담당하는 팀은 **ContosoVM이** 계획된 유지 관리를 진행중이기 때문에 다가오는 주말에 대한 경고 알림을 표시하지 않습니다. 

팀에서 **ContosoVM에서** 수동으로 구성된 각 경고 규칙을 사용하지 않도록 설정하고 유지 관리 후 다시 활성화할 수 있지만 간단한 프로세스는 아닙니다. 작업 규칙은 억제 기간을 유연하게 구성할 수 있는 기능으로 대규모 경고 억제를 정의하는 데 도움이 됩니다. 이전 예제에서 팀은 주말에 대한 모든 경고 알림을 표시하지 않는 **ContosoVM에** 대한 하나의 작업 규칙을 정의할 수 있습니다.


### <a name="actions-at-scale"></a>규모의 작업

경고 규칙은 경고가 생성될 때 트리거되는 작업 그룹을 정의하는 데 도움이 되지만 고객은 작업 범위 전반에 걸쳐 공통 작업 그룹을 가지고 있는 경우가 많습니다. 예를 들어 리소스 그룹 **ContosoRG를** 담당하는 팀은 **ContosoRG**에 정의된 모든 경고 규칙에 대해 동일한 작업 그룹을 정의할 수 있습니다. 

작업 규칙을 사용하면 이 프로세스를 단순화할 수 있습니다. 대규모로 작업을 정의하면 구성된 범위에서 생성된 모든 경고에 대해 작업 그룹이 트리거될 수 있습니다. 이전 예제에서 팀은 **ContosoRG에서** 생성된 모든 경고에 대해 동일한 작업 그룹을 트리거하는 하나의 작업 규칙을 정의할 수 있습니다.

> [!NOTE]
> 현재 작업 규칙은 Azure 서비스 상태 경고에 적용되지 않습니다.

## <a name="configuring-an-action-rule"></a>작업 규칙 구성

Azure Monitor의 경고 방문 페이지에서 **작업 관리를** 선택하여 기능에 액세스할 수 **있습니다.** 그런 다음 **작업 규칙(미리 보기)을**선택합니다. 경고에 대한 방문 페이지의 대시보드에서 **작업 규칙(미리 보기)을** 선택하여 규칙에 액세스할 수 있습니다.

![Azure 모니터 방문 페이지의 작업 규칙](media/alerts-action-rules/action-rules-landing-page.png)

**+ 새 작업 규칙**선택 . 

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-new-rule.png)

또는 경고 규칙을 구성하는 동안 작업 규칙을 만들 수 있습니다.

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-alert-rule.png)

이제 작업 규칙을 만들기 위한 흐름 페이지가 표시됩니다. 다음 요소를 구성합니다. 

![새 작업 규칙 생성 흐름](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>범위

먼저 범위(Azure 구독, 리소스 그룹 또는 대상 리소스)를 선택합니다. 단일 구독 내에서 범위 조합을 여러 번 선택할 수도 있습니다.

![작업 규칙 범위](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>필터 조건

또한 필터를 정의하여 경고의 특정 하위 집합으로 좁힐 수 있습니다. 

사용 가능한 필터는 다음과 같습니다. 

* **심각도**: 하나 이상의 경고 심각도를 선택하는 옵션입니다. **심각도 = Sev1은 Sev1로** 설정된 모든 경고에 작업 규칙이 적용된다는 것을 의미합니다.
* **모니터 서비스**: 원래 모니터링 서비스를 기반으로 하는 필터입니다. 이 필터는 또한 다중 선택입니다. 예를 들어 **서비스 모니터 = "응용 프로그램 인사이트"는** 모든 응용 프로그램 인사이트 기반 경고에 작업 규칙이 적용된다는 것을 의미합니다.
* **리소스 유형**: 특정 리소스 유형을 기반으로 하는 필터입니다. 이 필터는 또한 다중 선택입니다. 예를 들어 **리소스 유형 = "가상 시스템"은** 모든 가상 시스템에 작업 규칙이 적용됨을 의미합니다.
* **경고 규칙 ID: 경고**규칙의 리소스 관리자 ID를 사용하여 특정 경고 규칙을 필터링하는 옵션입니다.
* **모니터 상태**: 모니터 조건으로 **발생되거나** **해결된** 경고 인스턴스에 대한 필터입니다.
* **설명**: 경고 규칙의 일부로 정의된 설명과 문자열 일치를 정의하는 정규식(정규식) 일치입니다. 예를 들어 **설명에는 'prod'가 포함되어** 있으며 설명에 "prod" 문자열이 포함된 모든 경고가 일치합니다.
* **경고 컨텍스트(페이로드)**: 경고 페이로드의 경고 컨텍스트 필드와 문자열 일치를 정의하는 정규식 일치입니다. 예를 들어 **경고 컨텍스트(페이로드)에는 'Computer-01'이 포함되어** 있으며 페이로드에 "Computer-01"문자열이 포함된 모든 경고가 일치합니다.

이러한 필터는 서로 함께 적용됩니다. 예를 들어 리소스 **유형 ' = 가상 컴퓨터** 및 **심각도 ' = Sev0을**설정 하는 경우 VM에만 모든 **Sev0** 경고에 대 한 필터링 했습니다. 

![작업 규칙 필터](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>억제 또는 작업 그룹 구성

다음으로 경고 억제 또는 작업 그룹 지원에 대한 작업 규칙을 구성합니다. 둘 다 선택할 수 없습니다. 구성은 이전에 정의된 범위 및 필터와 일치하는 모든 경고 인스턴스에 대해 작동합니다.

#### <a name="suppression"></a>표시 안 함

**억제를**선택하면 작업 및 알림 억제 기간을 구성합니다. 다음 옵션 중 하나를 선택합니다.
* **지금부터 (항상)**: 모든 알림을 무기한 억제합니다.
* **예약된 시간**: 경계된 기간 내에 알림을 표시하지 않습니다.
* **되풀이 시**: 매일, 매주 또는 매월 반복되는 일정에 대한 알림을 표시하지 않습니다.

![작업 규칙 억제](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>작업 그룹

토글에서 **작업 그룹을** 선택하면 기존 작업 그룹을 추가하거나 새 작업 그룹을 만듭니다. 

> [!NOTE]
> 하나의 작업 그룹만 작업 규칙과 연결할 수 있습니다.

![작업 그룹을 선택하여 새 작업 규칙 추가 또는 만들기](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>작업 규칙 세부 정보

마지막으로 작업 규칙에 대한 다음 세부 정보를 구성합니다.
* 속성
* 저장되는 리소스 그룹
* 설명 

## <a name="example-scenarios"></a>예제 시나리오

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>시나리오 1: 심각도에 따른 경고 억제

Contoso는 매주 주말 마다 구독 **ContosoSub** 내의 모든 VM에 대한 모든 Sev4 경고에 대한 알림을 표시하지 않습니다.

**솔루션:** 다음과 같은 작업 규칙을 만듭니다.
* 범위 = **콘토소서브**
* 필터
    * 심각도 = **Sev4**
    * 리소스 유형 = **가상 컴퓨터**
* 매주 로 설정된 재발억제, **토요일과** **일요일** 확인

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>시나리오 2: 경고 컨텍스트(페이로드)에 따른 경고 억제

Contoso는 유지 관리를 거치면서 **ContosoSub에서** **Computer-01에** 대해 생성된 모든 로그 경고에 대한 알림을 무기한 억제하려고 합니다.

**솔루션:** 다음과 같은 작업 규칙을 만듭니다.
* 범위 = **콘토소서브**
* 필터
    * 모니터링 서비스 = **로그 분석**
    * 경고 컨텍스트(페이로드)에는 **컴퓨터-01이** 포함되어 있습니다.
* 지금부터 설정된 **억제(항상)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>시나리오 3: 리소스 그룹에 정의된 작업 그룹

Contoso는 [구독 수준에서 메트릭 경고를 정의했습니다.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor) 그러나 리소스 그룹 **ContosoRG에서**생성된 경고를 위해 특별히 트리거되는 작업을 정의하려고 합니다.

**솔루션:** 다음과 같은 작업 규칙을 만듭니다.
* 범위 = **ContosoRG**
* 필터 없음
* **ContosoActionGroup으로** 설정된 작업 그룹

> [!NOTE]
> *작업 규칙 및 경고 규칙 내에 정의된 작업 그룹은 중복 제거 없이 독립적으로 작동합니다.* 앞에서 설명한 시나리오에서 경고 규칙에 대해 작업 그룹이 정의된 경우 작업 규칙에 정의된 작업 그룹과 함께 트리거됩니다. 

## <a name="managing-your-action-rules"></a>작업 규칙 관리

목록 보기에서 작업 규칙을 보고 관리할 수 있습니다.

![작업 규칙 목록 보기](media/alerts-action-rules/action-rules-list-view.png)

여기에서 옆에 있는 확인란을 선택하여 규모에 따라 작업 규칙을 활성화, 비활성화 또는 삭제할 수 있습니다. 작업 규칙을 선택하면 구성 페이지가 열립니다. 이 페이지에서는 작업 규칙의 정의를 업데이트하고 활성화하거나 사용하지 않도록 설정하는 데 도움이 됩니다.

## <a name="best-practices"></a>모범 사례

[결과 수](alerts-unified-log.md) 옵션으로 만든 로그 경고는 전체 검색 결과(여러 컴퓨터에 걸쳐 있을 수 있음)를 사용하여 단일 경고 인스턴스를 생성합니다. 이 시나리오에서 작업 규칙 **경고 컨텍스트 (페이 로드)** 필터를 사용 하는 경우 일치 하는 한 경고 인스턴스에 작동 합니다. 앞에서 설명한 시나리오 2에서 생성된 로그 경고에 대한 검색 결과에 **컴퓨터-01과** **Computer-02가**모두 포함된 경우 전체 알림이 표시되지 않습니다. **Computer-02에** 대해 생성된 알림이 전혀 없습니다.

![작업 규칙 및 로그 경고(결과 수)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

작업 규칙과 함께 로그 경고를 가장 잘 사용하려면 [메트릭 측정](alerts-unified-log.md) 옵션을 사용하여 로그 경고를 만듭니다. 정의된 그룹 필드에 따라 이 옵션에 따라 별도의 경고 인스턴스가 생성됩니다. 그런 다음 시나리오 2에서는 **Computer-01** 및 **Computer-02에**대해 별도의 경고 인스턴스가 생성됩니다. 시나리오에 설명된 작업 규칙으로 인해 **Computer-01에** 대한 알림만 표시되지 않습니다. **Computer-02에** 대한 알림은 정상적으로 계속 발생합니다.

![작업 규칙 및 로그 경고(결과 수)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>작업 규칙을 구성하는 동안 중복 알림을 피할 수 있도록 가능한 모든 중복 작업 규칙을 보고 싶습니다. 그렇게 할 수 있습니까?

작업 규칙을 구성할 때 범위를 정의한 후 동일한 범위(있는 경우)에 겹치는 작업 규칙 목록을 볼 수 있습니다. 이 중복은 다음 옵션 중 하나가 될 수 있습니다.

* 정확히 일치: 예를 들어 정의하는 작업 규칙과 겹치는 작업 규칙이 동일한 구독에 있습니다.
* 하위 집합: 예를 들어 정의하는 작업 규칙이 구독에 있고 겹치는 작업 규칙이 구독 내의 리소스 그룹에 있습니다.
* 수퍼세트: 예를 들어 정의하는 작업 규칙은 리소스 그룹에 있고 겹치는 작업 규칙은 리소스 그룹을 포함하는 구독에 있습니다.
* 교차: 예를 들어, 정의하는 작업 규칙은 **VM1** 및 **VM2에**있고 겹치는 작업 규칙은 **VM2** 및 **VM3에**있습니다.

![겹치는 작업 규칙](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>경고 규칙을 구성하는 동안 정의하고 있는 경고 규칙에 따라 작동할 수 있는 작업 규칙이 이미 정의되어 있는지 알 수 있습니까?

경고 규칙에 대한 대상 리소스를 정의한 후 **작업** 섹션에서 **구성된 작업 보기를** 선택하여 동일한 범위(있는 경우)에서 작동하는 작업 규칙 목록을 볼 수 있습니다. 이 목록은 범위에 대한 다음 시나리오를 기반으로 채워집니다.

* 정확히 일치: 예를 들어 정의하는 경고 규칙과 작업 규칙이 동일한 구독에 있습니다.
* 하위 집합: 예를 들어 정의하는 경고 규칙이 구독에 있고 작업 규칙이 구독 내의 리소스 그룹에 있습니다.
* 수퍼셋: 예를 들어 정의하는 경고 규칙은 리소스 그룹에 있으며 작업 규칙은 리소스 그룹을 포함하는 구독에 있습니다.
* 교차: 예를 들어 정의하는 경고 규칙은 **VM1** 및 **VM2에**있으며 작업 규칙은 **VM2** 및 **VM3에**있습니다.
    
![겹치는 작업 규칙](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>작업 규칙에 의해 억제된 경고를 볼 수 있습니까?

경고 [목록 페이지에서](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances) **억제 상태라는**추가 열을 선택할 수 있습니다. 경고 인스턴스에 대한 알림이 표시되지 않으면 목록에 해당 상태가 표시됩니다.

![억제된 경고 인스턴스](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>작업 그룹과 동일한 범위에서 억제가 활성화된 작업 규칙이 있는 경우 어떻게 됩니까?

억제는 항상 동일한 범위에서 우선합니다.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>두 개의 별도 작업 규칙에서 모니터링되는 리소스가 있으면 어떻게 되나요? 하나 또는 두 개의 알림을 받을 수 있나요? 예를 들어 다음 시나리오에서 **VM2는** 다음과 같은 경우를 예로 들 수 있습니다.

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

VM1 및 VM3의 모든 경고에 대해 작업 그룹 AG1은 한 번 트리거됩니다. **VM2의**모든 경고에 대해 작업 규칙에서 중복 작업을 제거하지 않으므로 작업 그룹 AG1이 두 번 트리거됩니다. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>두 개의 별도 작업 규칙에서 모니터링되는 리소스와 한 리소스가 작업을 호출하는 반면 다른 리소스는 억제를 위해 수행되는 경우 어떻게 됩니까? 예를 들어 다음 시나리오에서 **VM2는** 다음과 같은 경우를 예로 들 수 있습니다.

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

VM1의 모든 경고에 대해 작업 그룹 AG1은 한 번 트리거됩니다. VM2 및 VM3의 모든 경고에 대한 작업 및 알림이 표시되지 않습니다. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>경고 규칙과 동일한 리소스에 대해 정의된 작업 규칙이 다른 작업 그룹을 호출하면 어떻게 됩니까? 예를 들어 다음 시나리오에서 **VM1은** 다음과 같은 경우입니다.

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
VM1의 모든 경고에 대해 작업 그룹 AG1은 한 번 트리거됩니다. 경고 규칙 "rule1"이 트리거될 때마다 AG2도 추가로 트리거됩니다. 작업 규칙 및 경고 규칙 내에 정의된 작업 그룹은 중복 제거 없이 독립적으로 작동합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure의 경고에 대해 자세히 알아보기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
