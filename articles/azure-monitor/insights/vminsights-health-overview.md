---
title: VM용 Azure Monitor 게스트 상태 (미리 보기)
description: 가상 컴퓨터의 상태를 확인 하 고 가상 컴퓨터가 비정상 상태가 될 때 경고를 수신 하는 방법을 비롯 하 여 VM용 Azure Monitor의 상태 기능 개요.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687061"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>VM용 Azure Monitor 게스트 상태 (미리 보기)
VM용 Azure Monitor 게스트 상태를 사용 하 여 게스트 운영 체제에서 일정 한 간격으로 샘플링 되는 성능 측정 집합을 기반으로 가상 컴퓨터의 상태를 볼 수 있습니다. 구독 또는 리소스 그룹의 모든 가상 컴퓨터 상태를 신속 하 게 확인 하 고, 특정 가상 컴퓨터의 세부 상태를 드릴 다운 하거나, 가상 컴퓨터가 비정상 상태가 되 면 사전에 통보 받을 수 있습니다. 

## <a name="enable-virtual-machine-health"></a>가상 컴퓨터 상태 사용
게스트 상태 기능을 사용 하도록 설정 하 고 가상 머신을 온 보 딩 하는 방법에 대 한 자세한 내용은 [VM용 Azure Monitor 게스트 상태 (미리 보기) 사용](vminsights-health-enable.md)

## <a name="pricing"></a>가격 책정
게스트 상태 기능에 대 한 직접적인 비용은 없지만 Log Analytics 작업 영역에서 성능 상태 데이터의 수집 및 저장에 대 한 비용이 있습니다. 모든 데이터는 *HealthStateChangeEvent* 테이블에 저장 됩니다. 가격 책정 모델 및 비용에 대 한 자세한 내용은 [Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리](../platform/manage-cost-storage.md) 를 참조 하세요.

## <a name="view-virtual-machine-health"></a>가상 컴퓨터 상태 보기
**시작** 페이지의 **게스트 VM 상태** 열에는 특정 구독 또는 리소스 그룹에 있는 각 가상 컴퓨터의 상태에 대 한 빠른 보기가 제공 됩니다. 각 그룹에 대 한 아이콘은 현재 해당 그룹의 각 상태에 있는 가상 컴퓨터 수를 보여 주기 때문에 각 가상 컴퓨터의 현재 상태는 표시 됩니다.

[![게스트 VM 상태를 사용 하 여 시작 페이지](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>모니터
가상 컴퓨터의 상태를 클릭 하면 각 모니터에 대 한 자세한 상태를 볼 수 있습니다. 각 모니터는 특정 구성 요소의 상태를 측정 합니다. 가상 컴퓨터의 전반적인 상태는 개별 모니터의 상태에 따라 결정 됩니다. 

![모니터 예제](media/vminsights-health-overview/monitors.png)

다음 표에서는 각 가상 컴퓨터에 대해 현재 사용할 수 있는 집계 및 유닛 모니터를 보여 줍니다. 

| Monitor | 유형 | 설명 |
|:---|:---|:---|
| CPU 사용률 | 단위 | 프로세서 사용률입니다. |
| 파일 시스템 | 집계 | Linux VM에 있는 모든 파일 시스템의 상태를 집계 합니다. |
| 파일 시스템  | 집계 | Linux VM에서 각 개별 파일 시스템의 상태입니다. 모니터 이름은 파일 시스템의 이름입니다. |
| 사용 가능한 공간 | 단위 | 파일 시스템의 사용 가능한 공간 비율입니다. |
| 논리 디스크 | 집계 | Windows VM의 모든 논리 디스크 상태를 집계 합니다. |
| 논리 디스크  | 집계 | Windows VM의 각 개별 논리 디스크 상태 모니터 이름은 디스크 이름입니다. |
| 메모리 | 집계 | VM의 메모리 상태를 집계 합니다. |
| 사용 가능한 메모리 | 단위 | VM에서 사용할 수 있는 메가바이트입니다. |

## <a name="health-states"></a>성능 상태
각 모니터는 1 분 마다 에이전트의 값을 샘플링 하 고 각 성능 상태에 대 한 조건과 샘플링 된 값을 비교 합니다. 특정 상태에 대 한 조건이 true 이면 모니터는 해당 상태로 설정 됩니다. 조건 중 하나라도 충족 되지 않으면 모니터가 정상 상태로 설정 됩니다. 데이터는 3 분 마다 또는 상태를 변경 하는 경우 즉시 에이전트에서 Azure Monitor 전송 됩니다.

각 모니터에는 lookback 창이 있으며 해당 시간 내에 수집 된 모든 샘플을 분석 합니다. 예를 들어, 모니터 240의 lookback 창에는 최소 2 개 이상의 샘플링 된 값 중에서 최대 값을 찾을 수 있지만 마지막 3 개이 하만 포함 될 수 있습니다. 값이 정규 속도로 샘플링 되는 동안에는 특정 창에서 샘플링 된 숫자가 에이전트 작업에 중단이 발생 하는 경우 약간 달라질 수 있습니다.

모니터는 다음 표에 있는 잠재적인 성능 상태를 가지 며, 한 번에 하나씩만 제공 됩니다. 모니터가 초기화 되 면 정상 상태로 시작 됩니다.

| 성능 상태 | 설명 |
|:---|:---|
| 정상  | 모니터가 현재 경고 또는 위험 임계값을 초과 하지 않습니다. |
| 경고  | 모니터가 경고 임계값 (정의 된 경우)을 초과 했습니다. |
| 위험 | 모니터가 위험 임계값 (정의 된 경우)을 초과 했습니다. |
| 알 수 없음  | 상태를 확인 하기 위해 수집 된 데이터가 충분 하지 않습니다. |
| 사용 안 함 | 모니터를 현재 사용할 수 없습니다. |
| 없음     | 모니터가 시작 되었으며 아직 평가 되지 않았거나 모니터링 중인 개체가 더 이상 존재 하지 않습니다. |



다음 표에서는 두 가지 유형의 모니터를 보여 줍니다.

| Monitor | 설명 |
|:---|:---|
| 유닛 모니터 | 리소스 또는 응용 프로그램의 일부 측면을 측정 합니다. 이는 성능 카운터를 확인 하 여 리소스의 성능 또는 가용성을 확인할 수 있습니다. |
| 집계 모니터 | 여러 모니터를 그룹화 하 여 단일 집계 된 상태를 제공 합니다. 집계 모니터는 하나 이상의 유닛 모니터와 기타 집계 모니터를 포함할 수 있습니다. |


  
### <a name="health-rollup-policy"></a>상태 롤업 정책
가상 컴퓨터의 상태는 각 단위 및 집계 모니터의 상태 롤업에 따라 결정 됩니다. 각 집계 모니터는 최하위 상태 롤업 정책을 사용 합니다. 여기서 집계 모니터의 상태는 최하위 상태를 가진 자식 모니터의 상태와 일치 합니다.  

다음 예에서는 **사용 가능한 공간** 모니터의 상태가 위험 상태 이며, 그 다음에는 해당 인스턴스 및 **파일 시스템** 에 대 한 집계 까지의 역할이 있습니다. **CPU 사용률이** 경고 상태인 경우에도 가상 컴퓨터는 그 아래에 최악의 상태 이므로 위험으로 설정 됩니다. 사용 가능한 공간을 정상 상태로 되돌리는 경우 CPU 사용률이 최악의 상태에서 모니터가 되기 때문에 가상 컴퓨터가 경고 상태로 변경 됩니다.

![상태 롤업 예제](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>모니터 세부 정보
모니터를 선택 하 여 다음 탭이 포함 된 세부 정보를 확인 합니다.

**개요** 는 모니터에 대 한 설명, 마지막으로 평가 된 시간 및 샘플링 된 값을 제공 하 여 현재 상태를 확인 합니다. 샘플 수는 모니터의 정의와 값 변동성에 따라 달라질 수 있습니다.

[![모니터 세부 정보 개요](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**기록** 모니터의 상태 변경 기록을 나열 합니다. 상태 변경을 확장 하 여 상태를 확인할 수 있습니다. 상태를 변경한 시점의 모니터 구성을 보려면 **적용 된 구성 보기** 를 클릭 합니다.



[![모니터 정보 기록](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Configuration
선택한 VM에 대 한 모니터의 구성을 확인 하 고 수정 합니다. 자세한 내용은 [VM용 Azure Monitor 게스트 상태 (미리 보기)의 모니터링 구성을](vminsights-health-enable.md) 참조 하세요.

[![모니터 세부 정보 구성](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>다음 단계

- [VM용 Azure Monitor 및 온보드 에이전트에서 게스트 상태를 사용 하도록 설정 합니다.](vminsights-health-enable.md)
- [Azure Portal를 사용 하 여 모니터를 구성 합니다.](vminsights-health-configure.md)
- [데이터 수집 규칙을 사용 하 여 모니터를 구성 합니다.](vminsights-health-configure-dcr.md)