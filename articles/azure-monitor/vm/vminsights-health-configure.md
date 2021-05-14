---
title: VM 인사이트 게스트 상태에서 모니터링 구성(미리 보기)
description: Azure Portal를 사용하여 VM 인사이트 게스트 상태(미리 보기)에 대한 기본 모니터링을 수정하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 6bda6c9e5f6e23e9e15c12fd507645fc72159302
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583448"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>VM 인사이트 게스트 상태에서 모니터링 구성(미리 보기)
VM 인사이트 게스트 상태를 사용하면 일정한 간격으로 샘플링되는 성능 측정 집합에 정의된 대로 가상 머신의 상태를 볼 수 있습니다. 이 문서에서는 Azure Portal을 사용하여 기본 모니터링을 수정하는 방법을 설명합니다. 또한 [데이터 수집 규칙을 사용하여 모니터링을 구성](vminsights-health-configure-dcr.md)하는 데 필요한 모니터의 기본 개념을 설명합니다.

## <a name="open-monitor-configuration"></a>모니터 구성 열기
모니터, **구성** 탭을 차례로 선택하여 Azure Portal에서 모니터 구성을 엽니다.

[![모니터 세부 정보 구성](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>모니터 사용 또는 사용 안 함
유닛 모니터 및 집계 모니터 모두 모니터를 사용하거나 사용하지 않도록 설정할 수 있는 **모니터 성능 상태** 설정이 있습니다. 모니터를 사용하도록 설정하면 해당 상태가 표시되고 VM의 상태 설정에 사용됩니다. 모니터를 사용하지 않도록 설정하면 해당 상태가 계산되지 않으며 VM 상태를 설정에 사용되지 않습니다.

| 설정 | Description |
|:---|:---|
| 사용 | 해당 부모의 설정에 상관없이 모니터를 사용할 수 있습니다. |
| 사용 안 함 | 해당 부모의 설정에 상관없이 모니터를 사용할 수 없습니다. |
| 부모와 동일 | 해당 부모 설정에 따라 모니터를 사용하거나 사용하지 않도록 설정할 수 있습니다. |

모니터를 사용하지 않도록 설정하면 다음 예와 같이 사용할 수 없는 기준이 표시됩니다.

![사용 안 함으로 설정되는 모니터](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> 부모 모니터를 사용하지 않도록 설정하면 자식 모니터도 사용하지 않도록 설정됩니다. 자식 모니터를 명시적으로 사용하도록 설정하면 부모도 활성화되지만 구성 상태는 동일하게 유지됩니다. 이 경우 부모 모니터에서 다음 메시지를 받게 됩니다.
>
> *모니터의 구성된 상태는 '사용 안 함'이지만 성능 상태에 반영되지 않아 불일치가 발생합니다. 구성된 변경 내용이 전파 중이거나 자식 모니터가 명시적으로 사용하도록 설정되었기 때문입니다.*

## <a name="enable-or-disable-virtual-machine"></a>가상 머신 사용 또는 사용 안 함
VM에 대한 모니터링을 사용하지 않음으로 설정하여 모든 모니터를 일시적으로 중지할 수 있습니다. 예를 들어 유지 관리를 수행하는 경우 VM에 대한 모니터링을 사용하지 않도록 설정할 수 있습니다.

| 설정 | Description |
|:---|:---|
| 사용  | 컴퓨터의 성능 상태가 표시됩니다. |
| 사용 안 함 | 컴퓨터의 성능 상태가 **사용 안 함** 으로 표시됩니다. 경고는 생성되지 않습니다. |

## <a name="health-state-logic"></a>성능 상태 논리
유닛 모니터에 대한 성능 상태 논리는 해당 성능 상태 설정의 기준을 정의합니다. 모니터의 성능 상태 수와 각 성능 상태 계산 방법에 대한 임계값을 지정할 수 있습니다.

![샘플 상태 조건](media/vminsights-health-configure/sample-health-criteria.png)

집계 모니터는 성능 상태 논리를 지정하지 않습니다. 성능 상태는 해당 성능 상태 롤업에 따라 해당 상태의 유닛 모니터에 의해 설정됩니다.

각 유닛 모니터에는 두 개 또는 세 개의 성능 상태가 있습니다. 각각에는 항상 정상 상태와 선택적으로 경고 상태, 위험 상태 또는 둘 다가 있습니다. 경고 및 위험 상태는 각각 모니터를 이 상태로 설정하는 경우를 정의하는 고유한 기준이 필요합니다. 다른 상태의 조건이 충족되지 않을 때 이 상태가 설정되므로 정상 상태에는 조건이 없습니다.

다음 예제에서는 CPU 사용률이 다음과 같은 성능 상태로 설정됩니다.

- 위험은 90%보다 큰 경우입니다.
- 경고는 80%보다 크거나 같은 경우입니다.
- 정상은 80% 미만인 경우입니다. 이는 다른 조건이 모두 적용되지 않는 조건이기 때문임을 암시합니다.

## <a name="next-steps"></a>다음 단계

- [데이터 수집 규칙을 사용하여 대규모로 모니터를 구성합니다.](vminsights-health-configure-dcr.md)
