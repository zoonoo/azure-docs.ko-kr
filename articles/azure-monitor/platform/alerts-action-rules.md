---
title: Azure Monitor 경고에 대 한 작업 규칙
description: Azure Monitor의 작업 규칙 및 구성 및 관리 방법 이해
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: alerts
ms.openlocfilehash: 6585890395d7656f239ac3098cd374ecd4757842
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80618979"
---
# <a name="action-rules-preview"></a>작업 규칙 (미리 보기)

작업 규칙을 통해 Azure Resource Manager 범위 (Azure 구독, 리소스 그룹 또는 대상 리소스)에서 작업을 정의 하거나 표시 하지 않을 수 있습니다. 작업 하려는 경고 인스턴스의 특정 하위 집합에 대 한 범위를 좁히는 데 도움이 되는 다양 한 필터를 포함 합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>작업 규칙을 사용 해야 하는 이유와 시기

### <a name="suppression-of-alerts"></a>경고 표시 안 함

경고가 생성 하는 알림을 표시 하지 않는 것이 유용한 여러 시나리오가 있습니다. 이러한 시나리오는 업무 시간 외 시간에는 계획 된 유지 관리 기간 동안 억제에서 억제로의 범위입니다. 예를 들어 **ContosoVM** 를 담당 하는 팀은 **ContosoVM** 가 계획 된 유지 관리를 수행 하기 때문에 예정 된 주말에 대 한 경고 알림을 표시 하지 않습니다. 

팀은 **ContosoVM** 에서 수동으로 구성 된 각 경고 규칙을 사용 하지 않도록 설정할 수 있지만 (유지 관리 후 다시 사용 하도록 설정) 간단한 프로세스가 아닙니다. 작업 규칙은 제거 기간을 유연 하 게 구성할 수 있는 기능을 사용 하 여 대규모 경고 억제를 정의 하는 데 도움이 됩니다. 이전 예제에서 팀은 주말의 모든 경고 알림을 표시 하지 않는 **ContosoVM** 에 대 한 하나의 작업 규칙을 정의할 수 있습니다.


### <a name="actions-at-scale"></a>크기 조정 작업

경고 규칙은 경고가 생성 될 때 트리거되는 작업 그룹을 정의 하는 데 도움이 되지만 고객은 종종 작업 범위에서 일반적인 작업 그룹을가지고 있습니다. 예를 들어 리소스 그룹 **ContosoRG** 을 담당 하는 팀은 **ContosoRG**내에 정의 된 모든 경고 규칙에 대해 동일한 작업 그룹을 정의할 수 있습니다. 

작업 규칙은이 프로세스를 간소화 하는 데 도움이 됩니다. 규모에 따라 작업을 정의 하 여 구성 된 범위에서 생성 되는 모든 경고에 대해 작업 그룹을 트리거할 수 있습니다. 이전 예제에서 팀은 **ContosoRG** 에서 생성 된 모든 경고에 대해 동일한 작업 그룹을 트리거하는 하나의 작업 규칙을 정의할 수 있습니다.

> [!NOTE]
> 작업 규칙은 현재 Azure Service Health 알림에 적용 되지 않습니다.

## <a name="configuring-an-action-rule"></a>작업 규칙 구성

Azure Monitor의 **경고** 방문 페이지에서 **작업 관리** 를 선택 하 여 기능에 액세스할 수 있습니다. 그런 다음 **작업 규칙 (미리 보기)** 을 선택 합니다. 경고에 대 한 방문 페이지의 대시보드에서 **작업 규칙 (미리 보기)** 을 선택 하 여 규칙에 액세스할 수 있습니다.

![Azure Monitor 방문 페이지의 작업 규칙](media/alerts-action-rules/action-rules-landing-page.png)

**+ 새 작업 규칙**을 선택 합니다. 

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-new-rule.png)

또는 경고 규칙을 구성 하는 동안 작업 규칙을 만들 수 있습니다.

![새 작업 규칙 추가](media/alerts-action-rules/action-rules-alert-rule.png)

이제 작업 규칙을 만들기 위한 흐름 페이지가 표시 됩니다. 다음 요소를 구성 합니다. 

![새 작업 규칙 만들기 흐름](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>범위

먼저 범위 (Azure 구독, 리소스 그룹 또는 대상 리소스)를 선택 합니다. 단일 구독 내에서 범위 조합을 여러 개 선택할 수도 있습니다.

![작업 규칙 범위](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>필터 조건

또한 필터를 정의 하 여 경고의 특정 하위 집합으로 범위를 좁힐 수 있습니다. 

사용 가능한 필터는 다음과 같습니다. 

* **심각도**: 경고 심각도를 하나 이상 선택 하는 옵션입니다. **심각도 = Sev1** 은 작업 규칙이 Sev1로 설정 된 모든 경고에 적용 될 수 있음을 의미 합니다.
* **모니터 서비스**: 원본 모니터링 서비스를 기반으로 하는 필터입니다. 이 필터는 다중 선택 이기도 합니다. 예를 들어 **Monitor Service = "Application Insights"** 는 모든 Application Insights 기반 경고에 대 한 작업 규칙이 적용 됨을 의미 합니다.
* **리소스 종류**: 특정 리소스 종류를 기반으로 하는 필터입니다. 이 필터는 다중 선택 이기도 합니다. 예를 들어 **리소스 유형 = "Virtual Machines"** 은 작업 규칙을 모든 가상 머신에 적용할 수 있음을 의미 합니다.
* **경고 규칙 id**: 경고 규칙의 리소스 관리자 ID를 사용 하 여 특정 경고 규칙을 필터링 하는 옵션입니다.
* **모니터 조건**: 모니터 조건으로 **발생** 또는 **해결** 된 경고 인스턴스에 대 한 필터입니다.
* **Description**: 경고 규칙의 일부로 정의 된 설명과 일치 하는 문자열을 정의 하는 regex (정규식) 일치 항목입니다. 예를 **들어 설명에 ' r o s t '가 포함** 된 모든 경고는 설명에 "prod" 문자열이 포함 된 모든 경고와 일치 합니다.
* **경고 컨텍스트 (페이로드)**: 경고 페이로드의 경고 컨텍스트 필드에 대 한 문자열 일치를 정의 하는 regex 일치 항목입니다. 예를 **들어, 경고 컨텍스트 (페이로드)는 '** 컴퓨터-01 '이 포함 된 페이로드가 포함 된 모든 경고와 일치 합니다.

이러한 필터는 서로 함께 적용 됩니다. 예를 들어 **리소스 유형 ' = Virtual Machines** 및 **심각도 ' = Sev0**를 설정 하는 경우 vm의 모든 **Sev0** 경고에 대해 필터링 했습니다. 

![작업 규칙 필터](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>비 표시 또는 작업 그룹 구성

그런 다음 경고 비 표시 또는 작업 그룹 지원에 대 한 작업 규칙을 구성 합니다. 둘 다 선택할 수는 없습니다. 구성은 이전에 정의 된 범위 및 필터와 일치 하는 모든 경고 인스턴스에서 작동 합니다.

#### <a name="suppression"></a>표시 안 함

표시 안 함 **을 선택 하**는 경우 작업 및 알림의 표시 안 함 기간을 구성 합니다. 다음 옵션 중 하나를 선택합니다.
* **지금부터 (항상)**: 모든 알림을 무기한 표시 하지 않습니다.
* **예약 된 시간에**: 제한 된 기간 내에 알림을 표시 하지 않습니다.
* **되풀이**: 매일, 매주 또는 매월 일정에 대 한 알림을 표시 하지 않습니다.

![작업 규칙 억제](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>작업 그룹

설정/해제에서 **작업 그룹** 을 선택 하는 경우 기존 작업 그룹을 추가 하거나 새 작업 그룹을 만듭니다. 

> [!NOTE]
> 작업 규칙에 작업 그룹을 하나만 연결할 수 있습니다.

![작업 그룹을 선택 하 여 새 작업 규칙 추가 또는 만들기](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>작업 규칙 세부 정보

마지막으로, 작업 규칙에 대 한 다음 세부 정보를 구성 합니다.
* 속성
* 저장 된 리소스 그룹
* Description 

## <a name="example-scenarios"></a>예제 시나리오

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>시나리오 1: 심각도를 기준으로 경고 표시 안 함

Contoso는 매일 **ContosoSub** 구독 내의 모든 vm에 대 한 모든 Sev4 경고에 대 한 알림을 표시 하지 않도록 하려고 합니다.

**해결 방법:** 다음을 사용 하 여 작업 규칙을 만듭니다.
* 범위 = **ContosoSub**
* 필터
    * 심각도 = **Sev4**
    * 리소스 종류 = **Virtual Machines**
* 되풀이를 매주로 설정 하 고 **토요일과** **일요일** 을 선택 하 여 표시 안 함

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>시나리오 2: 경고 컨텍스트 (페이로드)를 기반으로 하는 경고 표시 안 함

Contoso는 유지 관리를 거치는 동안 **ContosoSub** 에서 **컴퓨터-01** 에 대해 생성 된 모든 로그 경고에 대 한 알림을 무기한 억제 하려고 합니다.

**해결 방법:** 다음을 사용 하 여 작업 규칙을 만듭니다.
* 범위 = **ContosoSub**
* 필터
    * Monitor Service = **Log Analytics**
    * 경고 컨텍스트 (페이로드)에 **-01 컴퓨터가** 포함 되어 있습니다.
* 표시 안 함 **(항상)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>시나리오 3: 리소스 그룹에 정의 된 작업 그룹

Contoso는 [구독 수준에서 메트릭 경고를](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)정의 했습니다. 그러나 리소스 그룹 **ContosoRG**에서 생성 된 경고에 대해 특별히 트리거되는 작업을 정의 하려고 합니다.

**해결 방법:** 다음을 사용 하 여 작업 규칙을 만듭니다.
* 범위 = **ContosoRG**
* 필터 없음
* **ContosoActionGroup** 로 설정 되는 작업 그룹

> [!NOTE]
> *작업 규칙 및 경고 규칙 내에 정의 된 작업 그룹은 중복 제거 없이 독립적으로 작동 합니다.* 앞에서 설명한 시나리오에서 작업 그룹이 경고 규칙에 대해 정의 된 경우 작업 규칙에 정의 된 작업 그룹과 함께 트리거됩니다. 

## <a name="managing-your-action-rules"></a>작업 규칙 관리

목록 뷰에서 작업 규칙을 보고 관리할 수 있습니다.

![작업 규칙 목록 보기](media/alerts-action-rules/action-rules-list-view.png)

여기에서 옆의 확인란을 선택 하 여 scale에서 작업 규칙을 사용 하거나 사용 하지 않도록 설정 하거나 삭제할 수 있습니다. 작업 규칙을 선택 하면 해당 구성 페이지가 열립니다. 이 페이지에서는 작업 규칙의 정의를 업데이트 하 고 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

## <a name="best-practices"></a>모범 사례

[결과 수](alerts-unified-log.md) 옵션을 사용 하 여 만든 로그 경고는 전체 검색 결과 (여러 컴퓨터에 걸쳐 있을 수 있음)를 사용 하 여 단일 경고 인스턴스를 생성 합니다. 이 시나리오에서 작업 규칙은 **경고 컨텍스트 (페이로드)** 필터를 사용 하는 경우 일치 하는 항목이 있는 한 경고 인스턴스에 대해 작동 합니다. 앞서 설명한 시나리오 2에서는 생성 된 로그 경고에 대 한 검색 결과에 **컴퓨터-01** 과 **컴퓨터-02**가 모두 포함 되어 있으면 전체 알림이 표시 되지 않습니다. **컴퓨터 02** 에 대해 전혀 생성 된 알림이 없습니다.

![작업 규칙 및 로그 경고 (결과 수)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

작업 규칙과 함께 로그 경고를 사용 하는 것이 가장 좋습니다. [메트릭 측정](alerts-unified-log.md) 옵션을 사용 하 여 로그 경고를 만듭니다. 이 옵션은 정의 된 그룹 필드에 따라 별도의 경고 인스턴스를 생성 합니다. 그런 다음 시나리오 2에서는 **컴퓨터-01** 및 **컴퓨터-02**에 대 한 별도의 경고 인스턴스가 생성 됩니다. 시나리오에 설명 된 작업 규칙 때문에 **컴퓨터-01** 에 대 한 알림만 표시 되지 않습니다. **컴퓨터 02** 에 대 한 알림은 정상적으로 계속 해 서 발생 합니다.

![작업 규칙 및 로그 경고 (결과 수)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>작업 규칙을 구성 하는 동안 중복 된 알림이 발생 하지 않도록 가능한 모든 겹치는 작업 규칙을 확인 하 고 싶습니다. 이 작업을 수행할 수 있나요?

작업 규칙을 구성할 때 범위를 정의한 후 동일한 범위 (있는 경우)에서 겹치는 작업 규칙의 목록을 볼 수 있습니다. 이러한 겹침은 다음 옵션 중 하나일 수 있습니다.

* 정확 하 게 일치: 예를 들어 정의 하는 작업 규칙과 겹치는 동작 규칙은 동일한 구독에 있습니다.
* 하위 집합입니다. 예를 들어, 정의 하는 작업 규칙은 구독에 있고 겹치는 작업 규칙은 구독 내의 리소스 그룹에 있습니다.
* 상위 집합: 예를 들어, 정의 하는 작업 규칙은 리소스 그룹에 있고 겹치는 작업 규칙은 리소스 그룹을 포함 하는 구독에 있습니다.
* 교집합: 예를 들어 정의 하는 작업 규칙은 **VM1** 및 **v m 2**에 있고 겹치는 작업 규칙은 **v m 2** 및 **v m 3**에 있습니다.

![겹치는 작업 규칙](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>경고 규칙을 구성 하는 동안 정의 하는 경고 규칙에 대해 수행할 수 있는 작업 규칙이 이미 정의 되어 있는지 확인할 수 있나요?

경고 규칙에 대 한 대상 리소스를 정의한 후에는 **작업** 섹션에서 **구성 된 작업 보기** 를 선택 하 여 동일한 범위 (있는 경우)에서 작동 하는 작업 규칙의 목록을 볼 수 있습니다. 이 목록은 범위에 대 한 다음 시나리오에 따라 채워집니다.

* 정확 하 게 일치: 예를 들어 정의 하는 경고 규칙과 작업 규칙이 동일한 구독에 있습니다.
* 하위 집합: 예를 들어 정의 하는 경고 규칙은 구독에 있고 작업 규칙은 구독 내의 리소스 그룹에 있습니다.
* 상위 집합: 예를 들어 정의 하는 경고 규칙은 리소스 그룹에 있고 작업 규칙은 리소스 그룹을 포함 하는 구독에 있습니다.
* 교집합: 예를 들어 정의 하는 경고 규칙은 **VM1** 및 **v m 2**에 있고 작업 규칙은 **v m 2** 및 **v m 3**에 있습니다.
    
![겹치는 작업 규칙](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>작업 규칙에 의해 억제 된 경고를 볼 수 있나요?

[경고 목록 페이지](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)에서 비 표시 ( **Suppression) 상태**라는 추가 열을 선택할 수 있습니다. 경고 인스턴스의 알림이 표시 되지 않으면 목록에 해당 상태가 표시 됩니다.

![표시 되지 않는 경고 인스턴스](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>작업 그룹을 포함 하 고 다른 작업 규칙이 동일한 범위에서 비활성 상태인 경우에는 어떻게 되나요?

비 표시 제거는 항상 동일한 범위에 우선 합니다.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>별도의 두 작업 규칙에서 모니터링 되는 리소스가 있으면 어떻게 되나요? 하나 또는 두 개의 알림을 받을 까 요? 예를 들어, 다음과 같은 시나리오에서는 **v m 2** 입니다.

      action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1

VM1 및 V M 3의 모든 경고에 대해 작업 그룹 AG1이 한 번 트리거됩니다. 작업 규칙은 중복 된 작업을 수행 하지 않으므로 **v m 2**의 모든 경고에 대해 작업 그룹 AG1가 두 번 트리거됩니다. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>별도의 두 작업 규칙에서 리소스를 모니터링 하 고 작업에 대 한 호출을 제거 하는 데 다른 작업을 수행 하는 경우 어떻게 되나요? 예를 들어, 다음과 같은 시나리오에서는 **v m 2** 입니다.

      action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression

VM1의 모든 경고에 대해 작업 그룹 AG1가 한 번 트리거됩니다. V M 2 및 V M 3의 모든 경고에 대 한 작업 및 알림이 표시 되지 않습니다. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>다른 작업 그룹을 호출 하는 동일한 리소스에 대해 정의 된 경고 규칙 및 작업 규칙이 있으면 어떻게 되나요? 예를 들어, 다음과 같은 시나리오에서는 **VM1** 입니다.

      alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1 
 
VM1의 모든 경고에 대해 작업 그룹 AG1가 한 번 트리거됩니다. 경고 규칙 "rule1"가 트리거될 때마다 AG2도 트리거합니다. 작업 규칙 및 경고 규칙 내에 정의 된 작업 그룹은 중복 제거 없이 독립적으로 작동 합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure의 경고에 대해 자세히 알아보기](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
