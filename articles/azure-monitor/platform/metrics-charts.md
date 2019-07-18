---
title: Azure 메트릭 탐색기의 고급 기능
description: Azure Monitor 메트릭 탐색기의 고급 기능에 대해 알아보기
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 67e4281b24a7489cf202d82bdddbe99992aac095
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60256912"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Azure 메트릭 탐색기의 고급 기능

> [!NOTE]
> 이 문서에서는 메트릭 탐색기의 기본 기능을 사용 하 여 잘 알고 있다고 가정 합니다. 새 사용자를 첫 번째 메트릭 차트를 만들고 참조 하는 방법을 알아 보 려 [Azure 메트릭 탐색기를 사용 하 여 시작](metrics-getting-started.md)합니다.

## <a name="metrics-in-azure"></a>Azure의 메트릭

[Azure Monitor의 메트릭](data-platform-metrics.md)은 시간이 지남에 따라 수집되고 저장된 일련의 측정된 값과 수입니다. 표준(또는 "플랫폼") 메트릭 및 사용자 지정 메트릭이 있습니다. 표준 메트릭은 Azure 플랫폼 자체에서 제공됩니다. 표준 메트릭은 Azure 리소스의 상태 및 사용량 통계를 반영합니다. 사용자 지정 메트릭을 사용 하 여 응용 프로그램에서 Azure로 전송 됩니다 반면 합니다 [사용자 지정 이벤트 및 메트릭 용 Application Insights API](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics)를 [Windows Azure 진단 (WAD) 확장](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), 또는 [Azure REST API 모니터링](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api)합니다.

## <a name="create-views-with-multiple-metrics-and-charts"></a>여러 메트릭 및 차트를 사용 하 여 뷰 만들기

한 번에 여러 메트릭 차트를 표시 또는 여러 메트릭을 줄을 그릴 차트를 만들 수 있습니다. 이 기능을 사용 하면 수 있습니다.

- 상관 관계를 지정 관련된 메트릭의 방법을 값을 확인 하려면 동일한 그래프에는 다른 관련이
- 다른 인접에서 측정 단위를 사용 하 여 메트릭을 표시합니다
- 시각적으로 집계 하 고 여러 리소스의 메트릭을 비교합니다

예를 들어 5 개의 저장소 계정을 있고 서로 되는 총 공간을 확인 하려는 시간에서 특정 지점에서 개별 및 모든 값의 합계를 보여 주는 (누적된) 영역형 차트를 만들면 됩니다.

### <a name="multiple-metrics-on-the-same-chart"></a>동일한 차트에 여러 메트릭

먼저 [새 차트를 만들려면](metrics-getting-started.md#create-your-first-metric-chart)합니다. 클릭 **메트릭을 추가** 하 고 다른 메트릭을 같은 차트에 추가 하는 단계를 반복 합니다.

   > [!NOTE]
   > 일반적으로 하나의 차트에서 다른 단위의 측정값(예: "밀리초" 및 "킬로바이트") 또는 크게 다른 규모를 갖는 메트릭을 사용하지 않습니다. 대신, 여러 차트를 사용하는 것이 좋습니다. [차트 추가] 단추를 클릭하여 메트릭 탐색기에서 여러 개의 차트를 만듭니다.

### <a name="multiple-charts"></a>여러 개의 차트

클릭 합니다 **차트 추가** 하 고 다른 메트릭을 사용 하 여 다른 차트를 만듭니다.

### <a name="order-or-delete-multiple-charts"></a>여러 차트를 삭제 하거나 다시 정렬

를 주문 하거나 여러 차트를 삭제 하려면 줄임표를 클릭 ( **...**  ) 차트 메뉴를 열고 적절 한 메뉴 항목의 기호 **위로**를 **아래로 이동**, 또는 **삭제**합니다.

## <a name="apply-filters-to-charts"></a>차트에 필터 적용

차원을 포함한 메트릭을 보여주는 차트에 필터를 적용할 수 있습니다. 예를 들어 "트랜잭션 수" 메트릭에 차원이 있는 경우 트랜잭션으로부터의 응답 성공 여부를 나타내고 이 차원을 필터링하는 "응답 형식"은 성공적인(또는 실패한) 트랜잭션에 대한 차트 줄을 그립니다. 

### <a name="to-add-a-filter"></a>필터를 추가하려면

1. 차트 위에 있는 **필터 추가**를 선택합니다.

2. 필터링하려는 차원(속성)을 선택합니다.

   ![메트릭 이미지](./media/metrics-charts/00006.png)

3. 차트를 그릴 때 포함하려는 차원 값을 선택합니다(이 예제에서는 성공적인 저장소 트랜잭션을 필터링하도록 표시함).

   ![메트릭 이미지](./media/metrics-charts/00007.png)

4. 필터 값을 선택한 후에 필터 선택기를 닫으려면 바깥쪽을 클릭합니다. 이제 차트에서는 실패한 저장소 트랜잭션 수를 보여줍니다.

   ![메트릭 이미지](./media/metrics-charts/00008.png)

5. 동일한 차트에 여러 필터를 적용하려면 1~4단계를 반복하면 됩니다.



## <a name="apply-splitting-to-a-chart"></a>차트에 분할 적용

차원별로 메트릭 분할하여 메트릭의 다른 세그먼트가 서로 비교되는 방법을 시각화하고 차원의 외부 세그먼트를 식별할 수 있습니다.

### <a name="apply-splitting"></a>분할 적용

1. 차트 위쪽의 **분할 적용**을 클릭합니다.
 
   > [!NOTE]
   > 여러 메트릭 차트를 사용 하 여 분할을 사용할 수 없습니다. 또한 단일 차트에 적용 하는 하나의 분할 차원 이지만 여러 필터 할 수 있습니다.

2. 차트를 분할하려는 차원을 선택합니다.

   ![메트릭 이미지](./media/metrics-charts/00010.png)

   이제 차트에서는 차원의 각 세그먼트에 하나씩 여러 줄을 표시합니다.

   ![메트릭 이미지](./media/metrics-charts/00012.png)

3. **그룹화 선택기**를 닫으려면 바깥쪽을 클릭합니다.

   > [!NOTE]
   > 동일한 차원에서 필터링 및 분할을 둘 다 사용하여 시나리오와 관련 없는 세그먼트를 숨기고 차트를 더 쉽게 읽을 수 있습니다.

## <a name="lock-boundaries-of-chart-y-axis"></a>차트 Y축의 경계 잠그기

차트에 더 큰 값의 변경이 더 작게 표시되면 Y축의 범위를 잠그는 것이 중요해집니다. 

예를 들어 성공한 요청의 볼륨이 99.99%에서99.5%로 떨어지면 서비스 품질의 상당한 저하를 나타낼 수 있습니다. 그러나 기본 차트 설정에서 작은 숫자 값 변동을 확인하는 것은 어렵거나 불가능할 수 있습니다. 이 경우 차트의 최하위 경계를 99%로 잠글 수 있습니다. 이렇게 하면 이 작은 감소가 더 분명해집니다. 

또 다른 예는 사용 가능한 메모리의 변동입니다. 이 경우 값은 기술적으로 0에 도달하지 않습니다. 범위를 더 높은 값으로 수정하면 사용 가능한 메모리의 감소를 더 쉽게 발견할 수 있습니다. 

Y축 범위를 제어하려면 “...” 차트 메뉴를 사용하고 **차트 편집**을 선택하여 고급 차트 설정에 액세스합니다. Y축 범위 섹션의 값을 수정하거나 **자동** 단추를 사용하여 기본값으로 되돌립니다.

![메트릭 이미지](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> 일정 기간 동안 다양한 개수나 합계(이에 따라 사용 개수, 합계, 최소값 또는 최대 집계)를 추적하는 차트의 Y축 경계를 잠그려면 일반적으로 자동 기본값을 사용하지 않고 고정 시간 세분성을 지정해야 합니다. 사용자가 브라우저 창 크기를 조정하거나 화면 해상도를 변경할 때 시간 세분성이 자동으로 수정되면 차트의 값이 변경되므로 이 설정이 필요합니다. 시간 세분성의 변경 결과는 차트 모양에 영향을 주고 Y축 범위의 현재 선택이 무효화됩니다.

## <a name="pin-charts-to-dashboards"></a>대시보드에 차트 고정

차트를 구성한 후에 다른 모니터링 원격 분석의 컨텍스트에서 다시 보거나 팀과 공유할 수 있도록 대시보드에 추가하려고 합니다.

구성된 차트를 대시보드에 고정하려면:

차트를 구성한 후에 차트의 오른쪽의 위 모서리에서 **차트 작업** 메뉴를 클릭하고 **대시보드에 고정**을 클릭합니다.

![메트릭 이미지](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>경고 규칙 만들기

설정한 조건을 사용하여 메트릭을 메트릭 기반 경고 규칙의 토대로 시각화할 수 있습니다. 새 경고 규칙에는 사용자 차트의 대상 리소스, 메트릭, 분할 및 필터 차원이 포함됩니다. 나중에 경고 규칙 생성 창에서 이러한 설정을 수정할 수 있습니다.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>새 경고 규칙을 만들려면 **새 경고 규칙**을 클릭합니다.

![새로운 경고 규칙 단추가 빨간색으로 강조 표시됨](./media/metrics-charts/015.png)

사용자 지정 경고 규칙을 보다 쉽게 생성할 수 있도록 차트의 기본 메트릭 크기가 미리 채워진 경고 규칙 만들기 창으로 이동됩니다.

![경고 규칙 만들기](./media/metrics-charts/016.png)

메트릭 경고를 설정하는 방법을 자세히 알아보려면 이 [문서](alerts-metric.md)를 확인하세요.

## <a name="troubleshooting"></a>문제 해결

*차트에 데이터가 표시되지 않습니다.*

* 필터는 창의 모든 차트에 적용됩니다. 하나의 필터에 포커스를 둔 동안 다른 차트에서 모든 데이터를 제외하는 필터를 설정하지 않았는지 확인하세요.

* 여러 차트에서 서로 다른 필터를 설정하려면 해당 차트를 서로 다른 블레이드를 만들어 별도의 즐겨찾기로 저장합니다. 필요한 경우 대시보드에 고정하여 서로 나란히 볼 수 있습니다.

* 메트릭에 정의되지 않은 속성으로 차트를 분할하는 경우 차트에 아무것도 표시되지 않습니다. 분할(분리)한 속성을 지워 보거나 다른 속성을 선택합니다.

## <a name="next-steps"></a>다음 단계

  메트릭을 사용하여 조치 가능한 대시보드를 만드는 모범 사례에 대한 자세한 내용은 [사용자 지정 KPI 대시보드 만들기](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards)를 참고하세요.

