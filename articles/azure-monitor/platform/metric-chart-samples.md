---
title: Azure Monitor 메트릭 차트 샘플
description: Azure Monitor 데이터를 시각화하는 방법을 알아봅니다.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: bbfeb428d38c23955df4497242184499349aecf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256692"
---
# <a name="metric-chart-samples"></a>메트릭 차트 샘플

Azure 플랫폼에서는 [1,000개가 넘는 메트릭](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)을 제공하며, 대부분의 메트릭에는 차원이 있습니다. [차원 필터](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)를 사용하고, [분할](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)을 적용하고, 차트 유형을 제어하고, 차트 설정을 조정하면 인프라 및 애플리케이션 상태 관련 분석 정보를 제공하는 효율적인 진단 보기와 대시보드를 만들 수 있습니다. 이 문서에서는 [메트릭 탐색기](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)를 사용하여 작성할 수 있는 차트의 몇 가지 예와 이러한 각 차트를 구성하려면 수행해야 하는 단계에 대해 설명합니다.

유용한 차트 예제를 공유하려는 경우 GitHub에서 이 페이지에 차트를 게시하고 이 페이지에서 차트 예제를 공유하세요.

## <a name="website-cpu-utilization-by-server-instances"></a>서버 인스턴스의 웹 사이트 CPU 사용률

이 차트에는 App Service의 CPU 사용률이 적절한 범위 내에 포함되는지 여부가 표시되며, 인스턴스별로 구분된 CPU 사용률이 제공되므로 부하가 적절하게 분산되는지를 확인할 수 있습니다. 이 차트에서는 오전 6시 이전에 단일 서버 인스턴스에서 앱이 실행 중이었으며 그 후에는 다른 인스턴스가 추가되어 앱이 강화되었음을 확인할 수 있습니다.

![서버 인스턴스별 평균 CPU 사용률 백분율이 표시된 꺾은선형 차트](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>이 차트를 구성하는 방법

App Service 리소스를 선택하고 **CPU 백분율** 메트릭을 찾습니다. 그런 다음 **분할 적용**을 클릭하고 **인스턴스** 차원을 선택합니다.

## <a name="application-availability-by-region"></a>지역별 애플리케이션 가용성

지역별 애플리케이션 가용성을 확인하면 문제 발생 지역을 파악할 수 있습니다. 이 차트에는 Application Insights 가용성 메트릭이 표시되어 있습니다. 모니터링 대상 애플리케이션의 미국 동부 데이터 센터 가용성에는 문제가 없지만 미국 서부와 동아시아의 가용성에는 다소 문제가 있음을 확인할 수 있습니다.

![위치별 평균 가용성 차트](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>이 차트를 구성하는 방법

먼저 웹 사이트에 대해 [Application Insights 가용성](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) 모니터링을 설정해야 합니다. 그 후에는 Application Insights 리소스를 선택하고 가용성 메트릭을 선택합니다. **실행 위치** 차원에 분할을 적용합니다.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>API 이름별 스토리지 계정 트랜잭션 볼륨

스토리지 계정 리소스에서 진행되는 트랜잭션의 양이 매우 많습니다. 트랜잭션 메트릭을 사용하면 과도한 부하의 원인이 되는 API를 파악할 수 있습니다. 다음 차트는 확인할 API 호출만 보기에 표시되도록 범위를 좁히기 위해 필터링과 분할에서 같은 차원(API 이름)을 사용하여 구성되었습니다.

![API 트랜잭션 막대 그래프](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>이 차트를 구성하는 방법

메트릭 선택기에서 스토리지 계정과 **트랜잭션** 메트릭을 선택합니다. 차트 유형을 **막대형 차트**로 전환합니다. **분할 적용**을 클릭하고 **API 이름** 차원을 선택합니다. 그런 다음 **필터 추가**를 클릭하고 **API 이름** 차원을 다시 선택합니다. 필터 대화 상자에서 차트에 그릴 API를 선택합니다.

## <a name="next-steps"></a>다음 단계

* Azure Monitor [통합 문서](../../azure-monitor/app/usage-workbooks.md)에 대해 알아보기
* [메트릭 탐색기](metrics-charts.md)에 대해 자세히 알아보기