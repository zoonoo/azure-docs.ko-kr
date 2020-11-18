---
title: VM용 Azure Monitor 게스트 상태에서 모니터링 구성 (미리 보기)
description: Azure Portal를 사용 하 여 VM용 Azure Monitor 게스트 상태 (미리 보기)에 대 한 기본 모니터링을 수정 하는 방법을 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686987"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>VM용 Azure Monitor 게스트 상태에서 모니터링 구성 (미리 보기)
VM용 Azure Monitor 게스트 상태를 사용 하면 일정 한 간격으로 샘플링 되는 성능 측정 집합에 정의 된 대로 가상 컴퓨터의 상태를 볼 수 있습니다. 이 문서에서는 Azure Portal를 사용 하 여 기본 모니터링을 수정 하는 방법을 설명 합니다. 또한 [데이터 수집 규칙을 사용 하 여 모니터링을 구성 하는](vminsights-health-configure-dcr.md)데 필요한 모니터의 기본 개념을 설명 합니다.

## <a name="open-monitor-configuration"></a>모니터 구성 열기
모니터 구성 bin 열기 모니터를 선택 하 고 **구성** 탭을 선택 하 여 Azure Portal 합니다.

[![모니터 세부 정보 구성](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>모니터 사용 또는 사용 안 함
유닛 모니터와 집계 모니터에는 모두 모니터를 사용 하거나 사용 하지 않도록 설정할 수 있는 **상태 모니터 상태** 설정이 있습니다. 모니터를 사용 하도록 설정 하면 해당 상태가 표시 되 고 VM의 상태를 설정 하는 데 사용 됩니다. 모니터를 사용 하지 않도록 설정 하면 해당 상태가 계산 되지 않으며 VM의 상태를 설정 하는 데 사용 되지 않습니다.

| 설정 | 설명 |
|:---|:---|
| 사용 | 모니터는 부모 설정에 관계 없이 사용할 수 있습니다. |
| 사용 안 함 | 해당 부모 설정에 관계 없이 모니터를 사용할 수 없습니다. |
| 부모와 같음 | 해당 부모 설정에 따라 모니터가 사용 되거나 사용 하지 않도록 설정 됩니다. |

모니터가 사용 하지 않도록 설정 되 면 다음 예제와 같이 모든 조건이 사용할 수 없음으로 표시 됩니다.

![사용 안 함 모니터](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>가상 컴퓨터 사용 또는 사용 안 함
VM에 대 한 모니터링을 사용 하지 않도록 설정 하 여 일시적으로 모든 모니터를 중지할 수 있습니다. 예를 들어 유지 관리를 수행 하는 경우 VM에 대 한 모니터링을 사용 하지 않도록 설정할 수 있습니다.

| 설정 | 설명 |
|:---|:---|
| 사용  | 컴퓨터의 상태가 표시 됩니다. |
| 사용 안 함 | 컴퓨터의 상태가 **사용 안 함으로** 표시 됩니다. 경고가 생성 되지 않습니다. |

## <a name="health-state-logic"></a>상태 논리
유닛 모니터의 상태 논리는 해당 상태를 설정 하는 조건을 정의 합니다. 모니터의 상태 상태 수와 각 상태를 계산 하는 방법에 대 한 임계값을 지정할 수 있습니다.

![샘플 상태 조건](media/vminsights-health-configure/sample-health-criteria.png)

집계 모니터는 성능 상태 논리를 지정 하지 않습니다. 상태는 해당 상태 롤업에 따라 해당 상태의 유닛 모니터에 의해 설정 됩니다.

각 유닛 모니터에는 두 가지 또는 세 가지 상태가 있습니다. 각에는 항상 정상 상태와 선택적으로 경고 상태, 위험 상태 또는 둘 다가 있습니다. 경고 및 위험 상태는 모니터를이 상태로 설정 하는 경우를 정의 하는 고유한 기준이 필요 합니다. 다른 상태의 조건이 충족 되지 않을 때이 상태가 설정 되므로 정상 상태에는 조건이 없습니다.

다음 예제에서는 CPU 사용률이 다음 상태로 설정 됩니다.

- 90% 보다 큰 경우에는 중요 합니다.
- 80% 보다 크거나 같은 경우 경고가 발생 합니다.
- 80%의 경우 정상입니다. 이는 다른 조건이 모두 적용 되지 않는 조건 이므로 암시 됩니다.

## <a name="next-steps"></a>다음 단계

- [데이터 수집 규칙을 사용 하 여 대규모로 모니터를 구성 합니다.](vminsights-health-configure-dcr.md)