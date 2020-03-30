---
title: Azure 모니터 메트릭 차트 문제 해결
description: 메트릭 차트 만들기, 사용자 지정 또는 해석문제 해결
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e1ad4e53596b8228bdef5beb18aa250a9512c49f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659665"
---
# <a name="troubleshooting-metrics-charts"></a>메트릭 차트 문제 해결

Azure 메트릭 탐색기에서 차트를 만들거나 사용자 지정하거나 해석하는 문제가 있는 경우 이 문서를 사용합니다. 메트릭을 새로 접하는 경우 [메트릭 탐색기](metrics-getting-started.md) 및 [메트릭 탐색기의 고급 기능을](metrics-charts.md)시작하는 방법에 대해 알아봅니다. 구성된 메트릭 차트의 [예제도](metric-chart-samples.md) 볼 수 있습니다.

## <a name="cant-find-your-resource-to-select-it"></a>선택할 리소스를 찾을 수 없습니다.

**리소스 선택** 단추를 클릭했지만, 리소스 선택 대화 상자에 리소스가 보이지 않습니다.

**솔루션:** 메트릭 탐색기사용 가능한 리소스를 나열하기 전에 구독 및 리소스 그룹을 선택해야 합니다. 리소스가 보이지 않는 경우:

1. **구독** 드롭다운에서 올바른 구독을 선택했는지 확인합니다. 구독이 나열되지 않으면 **디렉터리 + 구독 설정**을 클릭하고 리소스와 함께 구독을 추가합니다.

1. 올바른 리소스 그룹을 선택했는지 확인합니다.
    > [!WARNING]
    > 최상의 성능을 얻을 수 있도록 메트릭 탐색기를 처음으로 열 때 **리소스 그룹** 드롭다운에서 리소스 그룹이 미리 선택되어 있지 않습니다. 리소스를 보려면 그룹을 하나 이상 선택해야 합니다.

## <a name="chart-shows-no-data"></a>차트에 데이터가 표시되지 않습니다.

올바른 리소스 및 메트릭을 선택한 후 차트에 데이터가 표시되지 않을 수 있습니다. 이 문제는 다음과 같은 몇 가지 이유로 인해 발생할 수 있습니다.

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>Microsoft.Insights 리소스 공급자가 구독에 등록되지 않음

메트릭을 살펴보려면 *Microsoft.Insights* 리소스 공급자가 구독에 등록되어야 합니다. 대부분의 경우 자동으로 등록됩니다(즉, 경고 규칙을 구성하거나, 리소스에 대한 진단 설정을 사용자 지정하거나, 자동 크기 조정 규칙을 구성한 후). Microsoft.Insights 리소스 공급자가 등록되지 않은 경우 Azure 리소스 공급자 [및 형식에](../../azure-resource-manager/management/resource-providers-and-types.md)설명된 다음 단계에 따라 수동으로 등록해야 합니다.

**솔루션:** 구독 , **리소스 공급자** **탭을**열고 *Microsoft.Insights가* 구독에 등록되어 있는지 확인합니다.

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>리소스에 대한 액세스 권한 부족

Azure의 메트릭 액세스는 [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)를 통해 제어됩니다. 리소스의 메트릭을 탐색하려면 [모니터링 읽기 권한자](../../role-based-access-control/built-in-roles.md#monitoring-reader), [모니터링 기여자](../../role-based-access-control/built-in-roles.md#monitoring-contributor) 또는 [기여자](../../role-based-access-control/built-in-roles.md#contributor)의 구성원이어야 합니다.

**솔루션:** 메트릭을 탐색하는 리소스에 대한 충분한 권한이 있는지 확인합니다.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>선택한 시간 범위 동안 리소스가 메트릭을 내보내지 않음

일부 리소스는 메트릭을 지속적으로 내보내지 않습니다. 예를 들어 Azure는 중지된 가상 머신의 메트릭을 수집하지 않습니다. 일부 리소스는 특정 조건이 발생하는 경우에만 메트릭을 내보냅니다. 예를 들어 트랜잭션 처리 시간을 보여주는 메트릭은 하나 이상의 트랜잭션이 필요합니다. 선택한 시간 범위에 트랜잭션이 하나도 없으면 당연히 차트가 비어 있습니다. Azure의 메트릭은 대부분 1분마다 수집되지만, 수집 빈도가 이보다 낮은 메트릭도 있습니다. 탐색하려는 메트릭에 대한 자세한 내용은 메트릭 설명서를 참조하세요.

**솔루션:** 차트의 시간을 더 넓은 범위로 변경합니다. 더 큰 시간 세분성을 사용하거나 "자동 시간 세분성" 옵션에 의존하여 "지난 30일"부터 시작할 수 있습니다.

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30일보다 큰 시간 범위를 선택함

[Azure의 메트릭은 대부분 93일 동안 저장됩니다](data-platform-metrics.md#retention-of-metrics). 그러나 단일 차트에서는 30일의 데이터만 쿼리할 수 있습니다. 이 제한은 [로그 기반 메트릭](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics)에는 적용되지 않습니다.

**솔루션:** 빈 차트가 표시되면 차트에 메트릭 데이터의 일부만 표시되는 경우 시간 선택기의 시작 날짜와 종료 날짜 간의 차이가 30일 간격을 초과하지 않는지 확인합니다.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>모든 메트릭 값이 잠긴 y축 범위를 벗어남

[차트 y축의 경계를 잠그면](metrics-charts.md#lock-boundaries-of-chart-y-axis) 의도치 않게 차트 표시 영역에 차트 선이 표시되지 않을 수 있습니다. 예를 들어 y축을 0%-50% 사이의 범위로 잠그고 메트릭이 상수 값 100%를 갖는 경우 선이 항상 가시 영역 외부에 렌더링되므로 빈 차트가 표시됩니다.

**솔루션:** 차트의 y축 경계가 메트릭 값 범위 밖에서 잠겨 있지 않은지 확인합니다. y축 경계가 잠긴 경우 메트릭 값이 차트 범위를 벗어나지 않도록 경계를 다시 설정해야 합니다. **합계**, **최솟값** 및 **최댓값**을 집계하는 차트는 브라우저 창의 크기를 조정하거나 화면 해상도를 전환할 때 값이 변경되므로 자동 단위에 y축 잠금을 사용하지 않는 것이 좋습니다. 단위를 전환할 때 차트의 표시 영역이 비어 있을 수 있습니다.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>게스트 OS 메트릭을 보고 있지만 Azure 진단 확장을 사용하도록 설정하지 않았습니다.

**게스트 OS** 메트릭 컬렉션을 사용하려면 Azure Diagnostics 확장을 구성하거나 해당 리소스의 **진단 설정** 패널을 사용하여 Azure 진단 확장을 설정해야 합니다.

**솔루션:** Azure 진단 확장이 활성화되어 있지만 메트릭을 볼 수 없는 경우 [Azure 진단 확장 문제 해결 가이드에](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)설명된 단계를 따릅니다. [게스트 OS 네임스페이스 및 메트릭을 선택할 수 없음에](metrics-troubleshoot.md#cannot-pick-guest-os-namespace-and-metrics) 대한 문제 해결 단계도 참조하세요.

## <a name="error-retrieving-data-message-on-dashboard"></a>대시보드의 "오류 검색 데이터" 메시지

메트릭을 사용하여 대시보드를 만들었는데 해당 메트릭이 이후에 사용 중지되고 Azure에서 제거되면 이 문제가 발생할 수 있습니다. 이 경우에 해당하는지 확인하려면 리소스의 **메트릭** 탭을 열고, 메트릭 선택기에서 사용 가능한 메트릭을 확인합니다. 메트릭이 표시되지 않으면 Azure에서 메트릭이 제거된 것입니다. 일반적으로 메트릭이 사용 중단되면 리소스 상태에 대한 유사한 정보를 제공하는 개선된 새 메트릭이 제공됩니다.

**솔루션:** 대시보드에서 차트에 대한 대체 메트릭을 선택하여 실패한 타일을 업데이트합니다. [Azure 서비스에 사용 가능한 메트릭 목록](metrics-supported.md)을 볼 수 있습니다.

## <a name="chart-shows-dashed-line"></a>차트는 파선 표시

Azure 메트릭 차트는 파선 스타일을 사용하여 알려진 두 시간 그레인 데이터 포인트 사이에 누락된 값("null 값"이라고도 함)이 있음을 나타냅니다. 예를 들어 시간 선택기에서 "1분" 시간 세분성을 선택했지만 메트릭이 07:26, 07:27, 07:29 및 07:30(초및 세 번째 데이터 점 사이의 분 간격)에 보고된 경우, 파선은 07:27 및 07:29를 연결하여 연결됩니다. 다른 모든 데이터 포인트를 참조하십시오. 메트릭에서 **개수** 및 **합계** 집계를 사용할 때 파선은 0으로 떨어집니다. **평균,** **최소** 또는 **최대** 집계의 경우 파선은 가장 가까운 알려진 두 데이터 점을 연결합니다. 또한 차트의 맨 오른쪽 또는 맨 왼쪽에서 데이터가 누락된 경우 파선은 데이터 요소가 누락된 방향으로 확장합니다.
  ![메트릭 이미지](./media/metrics-troubleshoot/missing-data-point-line-chart.png)

**솔루션:** 이 동작은 의도적으로 설계되었습니다. 누락된 데이터 요소를 식별하는 데 유용합니다. 표범 차트는 고밀도 메트릭의 추세를 시각화하는 데 탁월한 선택이지만 특히 값을 시간 그레인과 상호 연관시키는 것이 중요한 경우 희소한 값을 가진 메트릭을 해석하기 어려울 수 있습니다. 파선은 이러한 차트를 보다 쉽게 읽을 수 있지만, 차트가 여전히 불명확한 경우 다른 차트 유형을 사용하여 메트릭을 보는 방안을 고려해야 합니다. 예를 들어, 동일한 메트릭에 대한 분산 된 플롯 차트는 값이있을 때 점을 시각화하고 값이 누락 된 경우 데이터 ![포인트를 모두 건너 뛰는 측정항목 이미지로 매번 그레인을 명확하게 보여줍니다.](./media/metrics-troubleshoot/missing-data-point-scatter-chart.png)

   > [!NOTE]
   > 여전히 꺾은선형 차트를 메트릭에 사용하려는 경우 마우스 포인터를 차트 위로 이동하여 마우스 포인터가 있는 위치의 데이터 요소를 강조 표시하면 시간 단위를 평가하는 데 도움이 될 수 있습니다.

## <a name="chart-shows-unexpected-drop-in-values"></a>차트는 값의 예기치 않은 하락을 보여줍니다

메트릭 값이 감소했다고 생각하지만, 사실은 차트에 표시되는 데이터를 잘못 이해한 경우가 대부분입니다. Azure에서 아직 마지막 메트릭 데이터 요소를 수신 또는 처리하지 않아서 차트에 표시되는 가장 최신 데이터의 합계 또는 개수가 감소한 것으로 오해할 수 있습니다. 서비스에 따라 메트릭 처리 대기 시간이 몇 분 이내일 수 있습니다. 1분 또는 5분 의 세분성을 가진 최근 시간 범위를 표시하는 차트의 경우 지난 몇 ![분 동안의 값 하락이 더 두드러집니다.](./media/metrics-troubleshoot/drop-in-values.png)

**솔루션:** 이 동작은 의도적으로 설계되었습니다. 저희는 데이터가 전체 중 *일부*이거나 *불완전*한 경우에도 데이터를 받는 즉시 보여주는 것이 좋다고 생각합니다. 이렇게 하면 중요한 결론을 신속하게 내리고 즉시 조사를 시작할 수 있습니다. 예를 들어 실패 횟수를 보여주는 메트릭의 경우 부분 값 X를 보면 특정 시간에 적어도 X건의 실패가 있다는 것을 알 수 있습니다. (별로 중요하지 않을 수도 있는) 이 시간까지 발생한 정확한 실패 횟수를 알 수 있을 때까지 기다리지 않고 즉시 문제 조사를 시작할 수 있습니다. 데이터 세트 전체를 받을 때 차트가 업데이트되지만, 해당 시간의 새로운 불완전 데이터 요소까지 차트에 표시될 수 있습니다.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>게스트 OS 네임스페이스 및 메트릭을 선택할 수 없습니다.

가상 컴퓨터 및 가상 시스템 규모 집합에는 Azure 호스팅 환경에서 수집되는 **가상 컴퓨터 호스트** 메트릭과 가상 컴퓨터에서 실행되는 모니터링 [에이전트가](agents-overview.md) 수집하는 게스트 **OS(클래식)** 메트릭의 두 가지 범주가 있습니다. [Azure 진단 확장](diagnostics-extension-overview.md)을 사용하도록 설정하여 모니터링 에이전트를 설치합니다.

기본적으로 게스트 OS 메트릭은 리소스의 **진단 설정** 탭에서 선택하는 Azure Storage 계정에 저장됩니다. 게스트 OS 메트릭이 수집되지 않거나 메트릭 탐색기가 메트릭에 액세스할 수 없는 경우 **Virtual Machine 호스트** 메트릭 네임스페이스만 표시됩니다.

![메트릭 이미지](./media/metrics-troubleshoot/cannot-pick-guest-os-namespace.png)

**솔루션:** 메트릭 탐색기에서 **게스트 OS(클래식)** 네임스페이스 및 메트릭이 표시되지 않는 경우:

1. [Azure 진단 확장](diagnostics-extension-overview.md)이 사용되고 메트릭을 수집하도록 구성되었는지 확인합니다.
    > [!WARNING]
    > [Log Analytics 에이전트](agents-overview.md#log-analytics-agent)(Microsoft Monitoring Agent 또는 줄여서 MMA라고도 함)를 사용하여 **게스트 OS**를 스토리지 계정으로 보낼 수 없습니다.

1. **Microsoft.Insights** 리소스 공급자가 [구독에 등록되어 있는지 확인합니다.](metrics-troubleshoot.md#microsoftinsights-resource-provider-isnt-registered-for-your-subscription)

1. 스토리지 계정이 방화벽의 보호를 받고 있는지 확인합니다. 메트릭 데이터를 검색하고 차트를 그리려면 Azure Portal에서 스토리지 계정에 액세스해야 합니다.

1. [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 메트릭이 스토리지 계정으로 흘러 들어가고 있는지 확인합니다. 메트릭이 수집되지 않으면 [Azure Diagnostics 확장 문제 해결 가이드](diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)를 따릅니다.

## <a name="next-steps"></a>다음 단계

* [메트릭 탐색기 시작에 대해 알아보기](metrics-getting-started.md)
* [메트릭 탐색기의 고급 기능에 대해 알아보기](metrics-charts.md)
* [Azure 서비스에 사용 가능한 메트릭 목록 보기](metrics-supported.md)
* [구성된 차트 예제 보기](metric-chart-samples.md)
