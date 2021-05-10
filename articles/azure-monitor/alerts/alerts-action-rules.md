---
title: Azure Monitor 경고에 대한 작업 규칙
description: Azure Monitor의 작업 규칙과 구성 및 관리 방법 이해
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: 12e7cf8e72c5423b4a2edd6ea2a0f4537e328b08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105036784"
---
# <a name="action-rules-preview"></a>작업 규칙(미리 보기)

작업 규칙을 사용하면 발생한 경고에 대한 작업 그룹을 추가하거나 표시하지 않을 수 있습니다. 단일 규칙은 특정 리소스(예: 특정 가상 머신)에 대한 경고 또는 구독의 모든 리소스에 대해 발생한 경고와 같은 다양한 범위의 대상 리소스를 포함할 수 있습니다. 필요에 따라 다양한 필터를 추가하여 규칙이 적용되는 경고를 제어하고 이에 대한 일정을 정의할 수 있습니다. 예를 들어, 업무 시간 외에만 또는 계획된 유지 관리 기간 동안에만 적용되도록 할 수 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>작업 규칙을 사용해야 하는 이유와 시기

### <a name="suppression-of-alerts"></a>경고 표시 안 함

경고가 생성하는 알림을 표시하지 않는 것이 도움이 되는 여러 시나리오가 있습니다. 이러한 시나리오는 계획된 유지 관리 기간에 표시하지 않는 경우부터 업무 이외 시간에 표시하지 않는 경우까지 다양합니다. 예를 들어 **ContosoVM** 을 담당하는 팀은 **ContosoVM** 에서 계획된 유지 관리가 진행되고 있기 때문에 다가올 주말에 대한 경고 알림을 표시하지 않으려고 합니다.

팀은 **ContosoVM** 에서 수동으로 구성한 각 경고 규칙을 사용하지 않도록 설정할 수 있지만(유지 관리 후 다시 사용하도록 설정) 이것은 간단한 프로세스가 아닙니다. 작업 규칙은 표시 억제 기간을 유연하게 구성하는 기능을 사용하여 대규모로 경고 억제를 정의하는 데 유용합니다. 이전 예제에서 팀은 주말의 모든 경고 알림을 표시하지 않는 **ContosoVM** 에 대해 하나의 작업 규칙을 정의할 수 있습니다.

### <a name="actions-at-scale"></a>대규모 작업

경고 규칙은 경고가 생성될 때 트리거되는 작업 그룹을 정의하는 데 유용하지만 종종 작업 범위 안에 고객의 일반적인 작업 그룹이 포함됩니다. 예를 들어, 리소스 그룹 **ContosoRG** 를 담당하는 팀은 **ContosoRG** 내에 정의된 모든 경고 규칙에 대해 동일한 작업 그룹을 정의할 수 있습니다.

작업 규칙은 이 프로세스를 간소화하는 데 유용합니다. 대규모로 작업을 정의하여 구성된 범위에서 생성되는 모든 경고에 대해 작업 그룹을 트리거할 수 있습니다. 이전 예제에서 팀은 생성된 모든 경고에 대해 동일한 작업 그룹을 트리거하는 하나의 작업 규칙을 **ContosoRG** 에 대해 정의할 수 있습니다.

> [!NOTE]
> 작업 규칙은 Azure Service Health 경고에는 적용되지 않습니다.

## <a name="configuring-an-action-rule"></a>작업 규칙 구성

### <a name="portal"></a>[포털](#tab/portal)

Azure Monitor의 **경고** 방문 페이지에서 **작업 관리** 를 선택하여 기능에 액세스할 수 있습니다. 그런 다음, **작업 규칙(미리 보기)** 을 선택합니다. 경고에 대한 방문 페이지의 대시보드에서 **작업 규칙(미리 보기)** 을 선택하여 규칙에 액세스할 수 있습니다.

![Azure Monitor 방문 페이지의 작업 규칙](media/alerts-action-rules/action-rules-landing-page.png)

**+ 새 작업 규칙** 을 선택합니다.

![스크린샷에 새 작업 규칙 단추가 강조 표시된 작업 관리 페이지가 표시됩니다.](media/alerts-action-rules/action-rules-new-rule.png)

또는 경고 규칙을 구성하는 동안 작업 규칙을 만들 수 있습니다.

![스크린샷에 작업 규칙 만들기 단추가 강조 표시된 규칙 만들기 페이지가 표시됩니다.](media/alerts-action-rules/action-rules-alert-rule.png)

이제 작업 규칙을 만들기 위한 흐름 페이지가 표시됩니다. 다음 요소를 구성합니다.

![새 작업 규칙 만들기 흐름](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>범위

먼저 범위(Azure 구독, 리소스 그룹 또는 대상 리소스)를 선택합니다. 단일 구독 내에서 범위 조합을 여러 개 선택할 수도 있습니다.

![작업 규칙 범위](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>필터 조건

규칙이 특정 경고 하위 세트 또는 각 경고의 특정 이벤트(예: "발생" 또는 "해결됨"만)에 적용되도록 필요에 따라 필터를 정의할 수 있습니다.

사용 가능한 필터는 다음과 같습니다.

* **심각도**  
이 규칙은 선택한 심각도의 경고에만 적용됩니다.  
예를 들어, **심각도 = Sev1** 은 규칙이 Sev1 심각도의 경고에만 적용됨을 의미합니다.
* **모니터 서비스**  
이 규칙은 선택한 모니터 서비스에서 들어오는 경고에만 적용됩니다.  
예를 들어, **모니터 서비스 = "Azure Backup"** 은 규칙이 백업 경고(Azure Backup에서 제공)에만 적용됨을 의미합니다.
* **리소스 종류**  
이 규칙은 선택한 리소스 유형에 대한 경고에만 적용됩니다.  
예를 들어, **리소스 종류 = "가상 머신"** 은 규칙이 가상 머신에 대한 경고에만 적용됨을 의미합니다.
* **경고 규칙 ID**  
이 규칙은 특정 경고 규칙에서 들어오는 경고에만 적용됩니다. 값은 경고 규칙의 Resource Manager ID여야 합니다.  
예를 들어, **경고 규칙 ID = "/subscriptions/SubId1/resourceGroups/RG1/providers/microsoft.insights/metricalerts/API-Latency"** 는 이 규칙이 "API 대기 시간" 메트릭 경고 규칙에서 들어오는 경고에만 적용됨을 의미합니다.  
_참고 - CLI에서 경고 규칙을 나열하거나 포털에서 특정 경고 규칙을 열고 "속성"을 클릭한 다음, "리소스 ID" 값을 복사하여 적절한 경고 규칙 ID를 가져올 수 있습니다._
* **모니터 조건**  
이 규칙은 지정된 모니터 조건(**발생** 또는 **해결됨**)의 경고 이벤트에만 적용됩니다.
* **설명**  
이 규칙은 경고 설명 필드에 특정 문자열이 포함된 경고에만 적용됩니다. 해당 필드에는 경고 규칙 설명이 포함되어 있습니다.  
예를 들어, **description contains 'prod'** 는 규칙이 설명에 "prod" 문자열이 포함된 경고와만 일치함을 의미합니다.
* **경고 컨텍스트(페이로드)**  
이 규칙은 경고 컨텍스트 필드에 하나 이상의 특정 값이 포함된 경고에만 적용됩니다.  
예를 들어, **alert context (payload) contains 'Computer-01'** 은 규칙이 페이로드에 문자열 "Computer-01"이 포함된 경고에만 적용됨을 의미합니다.

하나의 규칙에 여러 필터를 설정하는 경우 모든 필터가 적용됩니다. 예를 들어, **리소스 종류 = 가상 머신** 및 **심각도 = Sev0** 를 설정하면 규칙이 가상 머신에 대한 Sev0 경고에만 적용됩니다.

![작업 규칙 필터](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>표시 안 함 또는 작업 그룹 구성

그런 다음, 경고 표시 안 함 또는 작업 그룹 지원에 대한 작업 규칙을 구성합니다. 둘 다 선택할 수는 없습니다. 해당 구성은 이전에 정의된 범위 및 필터와 일치하는 모든 경고 인스턴스에 작동합니다.

#### <a name="suppression"></a>표시 안 함

**표시 안 함** 을 선택하는 경우 작업 및 알림의 표시 안 함 기간을 구성합니다. 다음 옵션 중 하나를 선택합니다.
* **지금부터(항상)** : 모든 알림을 무기한 표시하지 않습니다.
* **예약된 시간에**: 제한된 기간 내에는 알림을 표시하지 않습니다.
* **되풀이**: 매일, 매주 또는 매월 되풀이 일정에 대한 알림을 표시하지 않습니다.

![작업 규칙 표시 안 함](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>작업 그룹

토글에서 **작업 그룹** 을 선택하는 경우 기존 작업 그룹을 추가하거나 새 작업 그룹을 만듭니다.

> [!NOTE]
> 하나의 작업 그룹만 작업 규칙에 연결할 수 있습니다.

![작업 그룹을 선택하여 새 작업 규칙 추가 또는 만들기](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>작업 규칙 세부 정보

마지막으로, 작업 규칙에 대한 다음 세부 정보를 구성합니다.
* 이름
* 저장된 리소스 그룹
* Description

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) 명령을 사용하여 Azure CLI에서 작업 규칙을 만들 수 있습니다.  `az monitor action-rule` 참조는 여러 [Azure Monitor용 Azure CLI 참조](/cli/azure/azure-cli-reference-for-monitor)중 하나일 뿐입니다.

### <a name="prepare-your-environment"></a>환경 준비

1. [Azure CLI 설치](/cli/azure/install-azure-cli)

   원한다면 Azure Cloud Shell을 사용하여 이 문서의 단계를 완료할 수도 있습니다.  Azure Cloud Shell은 브라우저를 통해 사용할 수 있는 대화형 셸 환경입니다.  다음 방법 중 하나를 사용하여 Cloud Shell을 시작합니다.

   - [https://shell.azure.com](https://shell.azure.com)으로 이동하여 Cloud Shell 열기

   - [Azure Portal](https://portal.azure.com)의 오른쪽 위 모서리에 있는 메뉴 모음에서 **Cloud Shell** 단추 선택

1. 로그인합니다.

   CLI를 로컬로 설치한 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 로그인합니다.  터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

    ```azurecli
    az login
    ```

1. `alertsmanagement` 확장 설치

   `az monitor action-rule` 명령은 핵심 Azure CLI의 실험적 확장입니다. 확장 참조에 대한 자세한 내용은 [Azure CLI 확장 사용하기](/cli/azure/azure-cli-extensions-overview?)를 참조하세요.

   ```azurecli
   az extension add --name alertsmanagement
   ```

   다음 경고가 표시될 수 있습니다.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Azure CLI를 사용하여 작업 규칙 만들기

필수 및 선택적 매개 변수에 대한 자세한 내용은 [az monitor action-rule create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create)에 대한 Azure CLI 참조 콘텐츠를 참조하세요.

리소스 그룹에서 알림을 표시하지 않는 작업 규칙을 만듭니다.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

주말마다 구독 내의 모든 VM에 대한 모든 Sev4 경고 관련 알림을 표시하지 않는 작업 규칙을 만듭니다.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>예제 시나리오

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>시나리오 1: 심각도를 기준으로 경고 표시 안 함

Contoso는 주말마다 **ContosoSub** 구독 내의 모든 VM에 대한 모든 Sev4 경고 관련 알림을 표시하지 않도록 하려고 합니다.

**해결 방법:** 다음을 사용하여 작업 규칙을 만듭니다.
* 범위 = **ContosoSub**
* 필터
    * 심각도 = **Sev4**
    * 리소스 종류 = **가상 머신**
* 되풀이가 매주로 설정되고 **토요일** 및 **일요일** 이 선택된 상태로 표시 안 함

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>시나리오 2: 경고 컨텍스트(페이로드)를 기준으로 하는 경고 표시 안 함

Contoso는 유지 관리를 거치는 동안 **ContosoSub** 의 **Computer-01** 에 대해 생성된 모든 로그 경고의 알림을 무기한 표시하지 않으려고 합니다.

**해결 방법:** 다음을 사용하여 작업 규칙을 만듭니다.
* 범위 = **ContosoSub**
* 필터
    * 모니터 서비스 = **Log Analytics**
    * 경고 컨텍스트(페이로드)에는 **Computer-01** 이 포함되어 있습니다.
* 표시 안 함이 **지금부터(항상)** 으로 설정되어 있습니다.

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>시나리오 3: 리소스 그룹에 정의된 작업 그룹

Contoso는 [구독 수준에서 메트릭 경고](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)를 정의했습니다. 그러나 리소스 그룹 **ContosoRG** 에서 생성된 경고에 대해 특별히 트리거되는 작업을 정의하려고 합니다.

**해결 방법:** 다음을 사용하여 작업 규칙을 만듭니다.
* 범위 = **ContosoRG**
* 필터 없음
* 작업 그룹이 **ContosoActionGroup** 으로 설정되어 있습니다.

> [!NOTE]
> *작업 규칙 및 경고 규칙 내에 정의된 작업 그룹은 중복 제거 없이 독립적으로 작동합니다.* 앞에서 설명한 시나리오에서 작업 그룹이 경고 규칙에 대해 정의된 경우 작업 규칙에 정의된 작업 그룹과 함께 트리거됩니다.

## <a name="managing-your-action-rules"></a>작업 규칙 관리

### <a name="portal"></a>[포털](#tab/portal)

목록 보기에서 작업 규칙을 보고 관리할 수 있습니다.

![작업 규칙 목록 보기](media/alerts-action-rules/action-rules-list-view.png)

여기에서 옆에 있는 확인란을 선택하여 대규모로 작업 규칙을 사용하거나, 사용하지 않도록 설정하거나, 삭제할 수 있습니다. 작업 규칙을 선택하면 해당 구성 페이지가 열립니다. 이 페이지에서는 작업 규칙의 정의를 업데이트하고 사용하거나 사용하지 않도록 설정할 수 있습니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 [az monitor action-rule](/cli/azure/ext/alertsmanagement/monitor) 명령을 사용하여 작업 규칙을 보고 관리할 수 있습니다.

Azure CLI를 사용하여 작업 규칙을 관리하기 전에 [작업 규칙 구성](#configuring-an-action-rule)에 제공된 지침을 사용하여 환경을 준비합니다.

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>모범 사례

[결과 수](./alerts-unified-log.md) 옵션을 사용하여 만든 로그 경고는 전체 검색 결과(여러 머신에 걸쳐 있을 수 있음)를 사용하여 단일 경고 인스턴스를 생성합니다. 이 시나리오에서 작업 규칙은 **경고 컨텍스트(페이로드)** 필터를 사용하는 경우 일치하는 항목이 있는 한, 경고 인스턴스에 작동합니다. 앞서 설명한 시나리오 2에서는 생성된 로그 경고에 대한 검색 결과에 **Computer-01** 및 **Computer-02** 가 모두 포함되어 있으면 전체 알림이 표시되지 않습니다. **Computer-02** 에 대해 생성되는 알림이 전혀 없습니다.

![다이어그램에는 단일 경고 인스턴스가 강조 표시된 작업 규칙 및 로그 경고가 표시됩니다.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

작업 규칙과 로그 경고를 가장 잘 사용하려면 [메트릭 측정](./alerts-unified-log.md) 옵션에 로그 경고를 만듭니다. 이 옵션은 정의된 그룹 필드에 따라 별도의 경고 인스턴스가 생성됩니다. 그런 다음, 시나리오 2에서는 **Computer-01** 및 **Computer-02** 에 대해 별도의 경고 인스턴스가 생성됩니다. 시나리오에 설명된 작업 규칙 때문에 **Computer-01** 에 대한 알림만 표시되지 않습니다. **Computer-02** 에 대한 알림은 정상적으로 계속해서 발생합니다.

![작업 규칙 및 로그 경고(결과 수)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>작업 규칙을 구성하는 동안 중복된 알림이 발생하지 않도록 가능한 모든 겹치는 작업 규칙을 확인하고 싶습니다. 이 작업을 수행할 수 있나요?

작업 규칙을 구성할 때 범위를 정의한 후 동일한 범위에서 겹치는 작업 규칙(있는 경우) 목록을 볼 수 있습니다. 이러한 중복은 다음 옵션 중 하나일 수 있습니다.

* 정확히 일치: 예를 들어 정의하는 경고 규칙 및 겹치는 작업 규칙이 동일한 구독에 있는 경우
* 하위 세트: 예를 들어 정의하는 작업 규칙이 한 구독에 있고 겹치는 작업 규칙은 이 구독의 리소스 그룹에 있는 경우
* 상위 세트: 예를 들어 정의하는 작업 규칙이 한 리소스 그룹에 있고 겹치는 작업 규칙은 이 리소스 그룹을 포함하는 구독에 있는 경우
* 교집합: 예를 들어 정의하는 작업 규칙이 **VM1** 및 **VM2** 이고 겹치는 작업 규칙은 **VM2** 및 **VM3** 에 있는 경우

![스크린샷에는 동일한 범위 창에 정의된 작업 규칙에 겹치는 작업 규칙이 표시되는 새 작업 규칙 페이지가 표시됩니다.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>경고 규칙을 구성하는 동안 정의하려는 경고 규칙에 작동할 수 있는 작업 규칙이 이미 정의되어 있는지 여부를 알 수 있나요?

경고 규칙에 대한 대상 리소스를 정의한 후에는 **작업** 섹션에서 **구성된 작업 보기** 를 선택하여 동일한 범위(있는 경우)에 작동하는 작업 규칙 목록을 볼 수 있습니다. 이 목록은 범위에 대한 다음 시나리오에 따라 채워집니다.

* 정확히 일치: 예를 들어 정의하는 경고 규칙 및 작업 규칙이 동일한 구독에 있는 경우
* 하위 세트: 예를 들어 정의하는 경고 규칙이 한 구독에 있고 작업 규칙은 이 구독의 리소스 그룹에 있는 경우
* 상위 세트: 예를 들어 정의하는 경고 규칙이 한 리소스 그룹에 있고 작업 규칙은 이 리소스 그룹을 포함하는 구독에 있는 경우
* 교집합: 예를 들어 정의하는 경고 규칙이 **VM1** 및 **VM2** 이고 작업 규칙은 **VM2** 및 **VM3** 에 있는 경우

![겹치는 작업 규칙](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>작업 규칙에 의해 억제된 경고를 볼 수 있나요?

[경고 목록 페이지](./alerts-managing-alert-instances.md)에서 **비표시 상태** 라는 추가 열을 선택할 수 있습니다. 경고 인스턴스의 알림이 표시되지 않으면 목록에 해당 상태가 표시됩니다.

![표시되지 않는 경고 인스턴스](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>같은 범위에서 작업 그룹이 있는 작업 규칙 하나와 비표시가 활성 상태인 다른 작업 규칙이 있는 경우 어떻게 되나요?

비표시는 항상 동일한 범위에서 우선합니다.

### <a name="what-happens-if-i-have-a-resource-that-is-covered-by-two-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>두 작업 규칙이 적용되는 리소스가 있으면 어떻게 되나요? 하나 또는 두 개의 알림을 받나요? 다음과 같은 시나리오의 **VM2** 를 예로 들 수 있습니다.

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

VM1 및 VM3의 모든 경고에 대해 작업 그룹 AG1가 한 번 트리거됩니다. 작업 규칙은 중복된 작업을 제거하지 않으므로 **VM2** 의 모든 경고에 대해, 작업 규칙 AG1이 두 번 트리거됩니다.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>별도의 두 작업 규칙에서 리소스를 모니터링하고, 하나는 작업을 요청하지만 다른 하나는 비표시용이면 어떻게 되나요? 다음과 같은 시나리오의 **VM2** 를 예로 들 수 있습니다.

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

VM1의 모든 경고에 대해 작업 그룹 AG1가 한 번 트리거됩니다. VM2 및 VM3의 모든 경고에 대한 작업 및 알림이 표시되지 않습니다.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>다른 작업 그룹을 호출하는 동일한 리소스에 대해 경고 규칙 및 작업 규칙이 정의되어 있으면 어떻게 되나요? 다음과 같은 시나리오의 **VM1** 을 예로 들 수 있습니다.

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

VM1의 모든 경고에 대해 작업 그룹 AG1가 한 번 트리거됩니다. 경고 규칙 "rule1"이 트리거될 때마다 AG2도 트리거합니다. 작업 규칙 및 경고 규칙 내에 정의된 작업 그룹은 중복 제거 없이 독립적으로 작동합니다.

## <a name="next-steps"></a>다음 단계

- [Azure의 경고에 대해 자세히 알아보기](./alerts-overview.md)
