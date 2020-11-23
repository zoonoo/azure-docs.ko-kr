---
title: 데이터 수집 규칙을 사용 하 여 VM용 Azure Monitor 게스트 상태에서 모니터링 구성 (미리 보기)
description: 리소스 관리자 템플릿을 사용 하 여 대규모로 VM용 Azure Monitor 게스트 상태에서 기본 모니터링을 수정 하는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 2bbc57d8ddc004c1926da7e0037efdc1fcf2d76e
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95318102"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-using-data-collection-rules-preview"></a>데이터 수집 규칙을 사용 하 여 VM용 Azure Monitor 게스트 상태에서 모니터링 구성 (미리 보기)
[VM용 Azure Monitor 게스트 상태](vminsights-health-overview.md) 를 사용 하면 일정 한 간격으로 샘플링 되는 성능 측정 집합에 정의 된 대로 가상 컴퓨터의 상태를 볼 수 있습니다. 이 문서에서는 데이터 수집 규칙을 사용 하 여 여러 가상 컴퓨터에서 기본 모니터링을 수정 하는 방법을 설명 합니다.


## <a name="monitors"></a>모니터
가상 컴퓨터의 상태는 각 모니터의 [상태 롤업에](vminsights-health-overview.md#health-rollup-policy) 따라 결정 됩니다. 다음 표에 표시 된 것 처럼 VM용 Azure Monitor 게스트 상태에는 두 가지 유형의 모니터가 있습니다.

| 모니터 | 설명 |
|:---|:---|
| 유닛 모니터 | 리소스 또는 응용 프로그램의 일부 측면을 측정 합니다. 이는 성능 카운터를 확인 하 여 리소스의 성능 또는 가용성을 확인할 수 있습니다. |
| 집계 모니터 | 여러 모니터를 그룹화 하 여 단일 집계 된 상태를 제공 합니다. 집계 모니터는 하나 이상의 유닛 모니터와 기타 집계 모니터를 포함할 수 있습니다. |

VM용 Azure Monitor 게스트 상태 및 해당 구성에서 사용 하는 모니터 집합은 직접 변경할 수 없습니다. 기본 구성의 동작을 수정 하는 경우 [재정의](#overrides) 를 만들 수 있습니다. 재정의는 데이터 수집 규칙에 정의 되어 있습니다. 필요한 모니터링 구성을 얻기 위해 여러 재정의를 포함 하는 여러 데이터 수집 규칙을 만들 수 있습니다.

## <a name="monitor-properties"></a>모니터 속성
다음 표에서는 각 모니터에서 구성할 수 있는 속성에 대해 설명 합니다.

| 속성 | 모니터 | 설명 |
|:---|:---|:---|
| 사용 | 집계<br>단위 | True 이면 상태 모니터가 계산 되어 가상 컴퓨터의 상태에 기여 합니다. 경고를 사용 하도록 설정 된 경고를 트리거할 수 있습니다. |
| 경고 | 집계<br>단위 | True 이면 비정상 상태로 이동할 때 모니터에 대 한 경고가 트리거됩니다. False 이면 모니터의 상태는 여전히 경고를 트리거할 수 있는 가상 컴퓨터의 상태에 영향을 주지 않습니다. |
| 경고 | 단위 | 경고 상태에 대 한 조건입니다. 없으면 모니터가 경고 상태를 입력 하지 않습니다. |
| 위험 | 단위 | 위험 상태에 대 한 조건입니다. None 인 경우 모니터는 위험 상태로 전환 되지 않습니다. |
| 평가 빈도 | 단위 | 성능 상태를 평가 하는 빈도입니다. |
| Lookback | 단위 | Lookback 창의 크기 (초)입니다. 자세한 설명은 [Monitorconfiguration 요소](#monitorconfiguration-element) 를 참조 하세요. |
| 평가 유형 | 단위 | 샘플 집합에서 사용할 값을 정의 합니다. 자세한 설명은 [Monitorconfiguration 요소](#monitorconfiguration-element) 를 참조 하세요. |
| Min 샘플 | 단위 | 값을 계산 하는 데 사용할 최소 값 수입니다. |
| Max 샘플 | 단위 | 값을 계산 하는 데 사용할 최대 값 수입니다. |


## <a name="default-configuration"></a>기본 구성
다음 표에는 각 모니터의 기본 구성이 나열 되어 있습니다. 이 기본 구성은 직접 변경할 수 없지만 특정 가상 컴퓨터에 대 한 모니터 구성을 수정 하는 [재정의](#overrides) 를 정의할 수 있습니다.


| 모니터 | 사용 | 경고 | 경고 | 위험 | 평가 빈도 | Lookback | 평가 유형 | Min 샘플 | 최대 샘플 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU 사용률  | True | False | None | \> 90%    | 60초 | 240 초 | 최소값 | 2 | 3 |
| 사용 가능한 메모리 | True | False | None | \< 100 M B | 60초 | 240 초 | 최대값 | 2 | 3 |
| 파일 시스템      | True | False | None | \< 100 M B | 60초 | 120 초 | 최대값 | 1 | 1 |


## <a name="overrides"></a>재정의
*재정의* 는 모니터의 속성을 하나 이상 변경 합니다. 예를 들어 재정의는 기본적으로 사용 하도록 설정 된 모니터를 사용 하지 않도록 설정 하거나, 모니터에 대 한 경고 조건을 정의 하거나, 모니터의 위험 임계값을 수정할 수 있습니다. 

재정의는 [데이터 수집 규칙 (DCR)](../platform/data-collection-rule-overview.md)에 정의 되어 있습니다. 서로 다른 재정의 집합을 사용 하 여 여러 개의 가상 컴퓨터를 만들고 여러 가상 컴퓨터에 적용할 수 있습니다. [Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)](../platform/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)에 설명 된 대로 연결을 만들어 가상 머신에 DCR을 적용 합니다.


## <a name="multiple-overrides"></a>다중 재정의

단일 모니터에 여러 재정의가 있을 수 있습니다. 재정의에서 다른 속성을 정의 하는 경우 결과 구성은 모든 재정의의 조합입니다.

예를 들어 `memory|available` 모니터는 경고 임계값을 지정 하거나 경고를 기본적으로 사용 하도록 설정 하지 않습니다. 이 모니터에 적용 되는 다음 재정의를 고려 하십시오.

- Override 1 `alertConfiguration.isEnabled` 은 다음으로 속성 값을 정의 합니다. `true`
- Override 2는 `monitorConfiguration.warningCondition` 의 임계값 조건으로를 정의 `< 250` 합니다.

결과 구성은 사용 가능한 메모리의 250Mb 미만이 될 때 경고 성능 상태로 전환 되는 모니터이 고, 사용 가능한 메모리의 100Mb 미만을 사용할 수 있는 경우에도 위험 상태가 되 고, 경고 심각도 1을 생성 하거나 (이미 존재 하는 경우 심각도 2에서 1로 기존 경고를 변경 함)

두 재정의가 동일한 모니터에서 동일한 속성을 정의 하는 경우 한 값이 우선적으로 적용 됩니다. 재정의는 해당 [범위](#scopes-element)에 따라 적용 되며 가장 일반적인에서 가장 구체적인 것까지 적용 됩니다. 즉, 가장 구체적인 재정의가 적용 될 가능성이 가장 큽니다. 구체적인 순서는 다음과 같습니다.

1. 전역 
2. Subscription
3. Resource group
4. 가상 머신 

동일한 범위 수준에서 여러 재정의가 동일한 모니터에 동일한 속성을 정의 하는 경우 DCR에 표시 되는 순서 대로 적용 됩니다. 재정의가 다른 dba에 있는 경우 DCR 리소스 Id의 알파벳 순서로 적용 됩니다.


## <a name="data-collection-rule-configuration"></a>데이터 수집 규칙 구성
다음 섹션에서는 재정의를 정의 하는 데이터 수집 규칙의 JSON 요소에 대해 설명 합니다. 예제 [데이터 수집 규칙](#sample-data-collection-rule)에서 전체 예제를 제공 합니다.

## <a name="extensions-structure"></a>확장 구조
게스트 상태는 Azure Monitor 에이전트에 대 한 확장으로 구현 되므로 재정의는 `extensions` 데이터 수집 규칙의 요소에 정의 됩니다. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| 요소 | 필수 | 설명 |
|:---|:---|:---|
| `name` | 예 | 확장 프로그램에 대 한 사용자 정의 문자열입니다. |
| `streams` | 예 | 게스트 상태 데이터가 전송 될 스트림의 목록입니다. **Microsoft HealthStateChange** 를 포함 해야 합니다.  |
| `extensionName` | 예 | 확장의 이름입니다. **HealthExtension** 여야 합니다. |
| `extensionSettings` | 예 | `healthRuleOverride`기본 구성에 적용 되는 요소의 배열입니다. |


## <a name="extensionsettings-element"></a>extensionSettings 요소
확장 프로그램에 대 한 설정을 포함 합니다.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| 요소 | 필수 | 설명 |
|:---|:---|:---|
| `schemaVersion` | 예 | 요소의 예상 스키마를 나타내기 위해 Microsoft에서 정의 하는 문자열입니다. 현재 1.0로 설정 되어야 합니다. |
| `contentVersion` | 아니요 | 필요한 경우 다양 한 버전의 상태 구성을 추적 하기 위해 사용자가 정의한 문자열입니다. |
| `healthRuleOverrides` | 예 | `healthRuleOverride`기본 구성에 적용 되는 요소의 배열입니다. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides 요소
`healthRuleOverride`각각 재정의를 정의 하는 하나 이상의 요소를 포함 합니다.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| 요소 | 필수 | 설명 |
|:---|:---|:---|
| `scopes` | 예 | 이 재정의를 적용할 수 있는 가상 컴퓨터를 지정 하는 하나 이상의 범위 목록입니다. DCR가 가상 컴퓨터와 연결 되어 있더라도 재정의를 적용 하려면 가상 컴퓨터가 범위 내에 있어야 합니다. |
| `monitors` | 예 | 이 재정의를 받을 모니터를 정의 하는 하나 이상의 문자열 목록입니다.  |
| `monitorConfiguration` | 아니요 | 성능 상태 및 계산 방법을 포함 하 여 모니터에 대 한 구성입니다. |
| `alertConfiguration` | 아니요 | 모니터에 대 한 경고 구성입니다. |
| `isEnabled` | 아니요 | 모니터 사용 여부를 제어 합니다. 사용 하지 않도록 설정 된 모니터는 사용 하지 않도록 설정한 경우를 제외 하 고는 사용 하지 않도록 설정 *된 특수 한 상태* 생략 하는 경우 모니터는 계층의 부모 모니터에서 해당 상태를 상속 합니다. |


## <a name="scopes-element"></a>범위 요소
각 재정의에는 재정의를 적용 해야 하는 가상 컴퓨터를 정의 하는 범위가 하나 이상 있습니다. 범위는 구독, 리소스 그룹 또는 단일 가상 컴퓨터 일 수 있습니다. 재정의가 특정 가상 컴퓨터에 연결 된 DCR에 있는 경우에도 가상 컴퓨터가 재정의 범위 중 하나에 있는 경우에만 해당 가상 컴퓨터에 적용 됩니다. 이를 통해 더 적은 수의 Vcrs를 Vm 집합에 연결할 수 있지만 DCR 자체 내에서 각 재정의의 할당에 대 한 세부적인 제어를 제공 합니다. 범위 요소를 사용 하 여 해당 가상 컴퓨터의 다른 하위 집합에 대해 상태 모니터 재정의를 지정 하는 반면, 정책을 사용 하 여 작은 Crs 집합을 만들고 가상 컴퓨터 집합에 연결 하는 것이 좋습니다.

다음 표에서는 다양 한 범위의 예를 보여 줍니다.

| Scope | 예제 |
|:---|:---|
| 단일 가상 컴퓨터 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| 리소스 그룹의 모든 가상 머신 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| 구독의 모든 가상 머신 | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| 데이터 수집 규칙이 연결 된 모든 가상 머신 | `*` |


## <a name="monitors-element"></a>monitor 요소
상태 계층에서이 재정의를 받을 모니터를 정의 하는 하나 이상의 문자열 목록입니다. 각 요소는이 재정의를 수신 하는 하나 이상의 모니터와 일치 하는 모니터 이름 또는 형식 이름일 수 있습니다. 

```json
"monitors": [
    "<monitor name>"
 ],
```



다음 표에서는 현재 사용 가능한 모니터 이름을 나열 합니다.

| 형식 이름 | Name | 설명 |
|:---|:---|:---|
| 루트 | 루트 | 가상 컴퓨터 상태를 나타내는 최상위 모니터입니다. | |
| cpu 사용률 | cpu 사용률 | CPU 사용률 모니터입니다. | |
| 논리 디스크 | 논리 디스크 | Windows 가상 컴퓨터에서 모니터링 되는 모든 디스크의 상태를 집계 하는 모니터입니다. | |
| 논리 디스크\|* | 논리 디스크 \| C:<br>논리 디스크 \| D: | Windows 가상 머신에서 지정 된 디스크의 상태를 추적 하는 집계 모니터입니다. | 
| 논리 디스크 \| * \| 사용 가능한 공간 | 논리 디스크 \| C: \| 사용 가능한 공간<br>논리 디스크 \| D: \| 사용 가능한 공간 | Windows 가상 머신에서 사용 가능한 디스크 공간 모니터입니다. |
| filesystems | filesystems | Linux 가상 머신에 있는 모든 파일 시스템의 상태에 대 한 집계 모니터입니다. |
| 파일 시스템\|* | 파일 시스템\|/<br>파일 시스템 \| /mnt | Linux 가상 머신 파일 시스템의 집계 모니터 상태를 추적 합니다. | filesystems|/var/log |
| 파일 시스템 \| * \| 사용 가능한 공간 | 파일 시스템 \| / \| 사용 가능한 공간<br>파일 시스템 \| /mnt \| free space | Linux 가상 머신 파일 시스템의 디스크 사용 가능 공간 모니터입니다. | 
| 메모리 | 메모리 | 가상 컴퓨터 메모리 상태에 대 한 집계 모니터입니다. | |
| \|사용 가능한 메모리| \|사용 가능한 메모리 | 가상 머신에서 사용 가능한 메모리 추적을 모니터링 합니다. | |


## <a name="alertconfiguration-element"></a>alertConfiguration 요소
모니터에서 경고를 만들지 여부를 지정 합니다.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| 요소 | 필수 | 설명 | 
|:---|:---|:---|
| `isEnabled` | 아니요 | True로 설정 되 면 모니터는 위험 또는 경고 상태로 전환 될 때 경고를 생성 하 고 정상 상태로 돌아갈 때 경고를 해결 합니다. False 이거나 생략 된 경우 경고가 생성 되지 않습니다.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration 요소
유닛 모니터에만 적용 됩니다. 성능 상태 및 계산 방법을 포함 하 여 모니터에 대 한 구성을 정의 합니다.

매개 변수는 임계값과 비교할 메트릭 값을 계산 하는 알고리즘을 정의 합니다. 모니터는 기본 메트릭에 있는 하나의 데이터 샘플을 사용 하는 대신 평가 시간 및 전 시간에서 창 내에서 받은 몇 가지 메트릭 샘플을 평가 합니다 `lookbackSec` . 해당 기간 내에 수신 되는 모든 샘플이 고려 되며 샘플 수가 보다 크면 `maxSamples` 위의 이전 샘플이 `maxSamples` 무시 됩니다. 

Lookback interval에 더 작은 샘플이 있는 경우 `minSamples` 모니터는 기본 메트릭의 상태에 대 한 의사 결정을 내릴 수 있는 충분 한 데이터가 없다는 것을 *알 수 없음* 상태로 전환 합니다. 그런 다음 `minSamples` 사용할 수 있는 샘플이 많을 경우 evaluationType 매개 변수에서 지정 하는 집계 함수는 집합에 대해를 실행 하 여 단일 값을 계산 합니다.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| 요소 | 필수 | 설명 | 
|:---|:---|:---|
| `evaluationFrequencySecs` | 아니요 | 상태 평가 빈도를 정의 합니다. 각 모니터는 에이전트가 시작 될 때와 이후에이 매개 변수에 의해 정의 된 정기적인 간격에 따라 평가 됩니다. |
| `lookbackSecs`   | 아니요 | Lookback 창의 크기 (초)입니다. |
| `evaluationType` | 아니요 | `min` – 전체 샘플 집합에서 최소 값을 가져옵니다.<br>`max` -전체 샘플 집합에서 최대값을 사용 합니다.<br>`avg` – 샘플 집합 값의 평균을 사용 합니다.<br>`all` – 집합의 모든 단일 값을 임계값으로 비교 합니다. 집합의 모든 샘플이 임계값 조건을 만족 하는 경우에만 모니터 상태를 모니터링 합니다. |
| `minSamples`     | 아니요 | 값을 계산 하는 데 사용할 최소 값 수입니다. |
| `maxSamples`     | 아니요 | 값을 계산 하는 데 사용할 최대 값 수입니다. |
| `warningCondition`  | 아니요 | 경고 조건에 대 한 임계값 및 비교 논리입니다. |
| `criticalCondition` | 아니요 | 위험 조건에 대 한 임계값 및 비교 논리입니다. |


## <a name="warningcondition-element"></a>warningCondition 요소
경고 조건에 대 한 임계값 및 비교 논리를 정의 합니다. 이 요소가 포함 되지 않은 경우 모니터는 경고 성능 상태로 전환 되지 않습니다.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 속성 | 필수 | 설명 | 
|:---|:---|:---|
| `isEnabled` | 아니요 | 조건을 사용할 수 있는지 여부를 지정 합니다. **False** 로 설정 하면 임계값 및 연산자 속성을 설정할 수 있지만 조건이 사용 되지 않습니다. |
| `threshold` | 아니요 | 평가 값을 비교 하는 임계값을 정의 합니다. |
| `operator`  | 아니요 | 임계값 식에 사용할 비교 연산자를 정의 합니다. 가능한 값은 >, <, >=, <=, = =입니다. |


## <a name="criticalcondition-element"></a>criticalCondition 요소
위험 조건에 대 한 임계값 및 비교 논리를 정의 합니다. 이 요소가 포함 되지 않으면 모니터가 위험 상태로 전환 되지 않습니다.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 속성 | 필수 | 설명 | 
|:---|:---|:---|
| `isEnabled` | 아니요 | 조건을 사용할 수 있는지 여부를 지정 합니다. **False** 로 설정 하면 임계값 및 연산자 속성을 설정할 수 있지만 조건이 사용 되지 않습니다. |
| `threshold` | 아니요 | 평가 값을 비교 하는 임계값을 정의 합니다. |
| `operator`  | 아니요 | 임계값 식에 사용할 비교 연산자를 정의 합니다. 가능한 값은 >, <, >=, <=, = =입니다. |

## <a name="sample-data-collection-rule"></a>샘플 데이터 수집 규칙
다음 샘플 데이터 수집 규칙은 모니터링을 구성 하는 재정의의 예를 보여 줍니다.


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [데이터 수집 규칙](../platform/data-collection-rule-overview.md)을 참조 하세요.