---
title: Azure 전면 도어 표준/프리미엄에 대 한 모니터링 메트릭
description: 이 문서에서는 Azure Front 도어 표준/프리미엄 모니터링 메트릭을 설명 합니다.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557329"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Azure 전면 도어 표준/프리미엄의 실시간 모니터링

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure 전면 도어 표준/프리미엄은 Azure Monitor와 통합 되며, Azure Front 도어 표준/프리미엄을 실시간으로 모니터링 하 여 문제를 추적 하 고 문제를 해결 하 고 디버그할 수 있습니다.  

Azure 전면 도어 표준/프리미엄은 60 초 간격으로 메트릭을 측정 하 고 전송 합니다. 메트릭은 포털에 표시 되는 데 최대 3 분 정도 걸릴 수 있습니다. 메트릭은 선택한 차트 또는 그리드에 표시 될 수 있으며 포털, PowerShell, CLI 및 API를 통해 액세스할 수 있습니다. 자세한 내용은 [Azure Monitor 메트릭](../../azure-monitor/essentials/data-platform-metrics.md)을 참조 하세요.  

기본 메트릭은 무료로 제공 됩니다. 추가 비용에 추가 메트릭을 사용할 수 있습니다. 

4XXErrorRate 또는 5XXErrorRate에 대 한 임계값과 같은 각 메트릭에 대 한 경고를 구성할 수 있습니다. 오류 비율이 임계값을 초과 하는 경우 구성 된 경고를 트리거합니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-metric.md)를 참조하세요. 

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Azure Front 도어 Standard/Premium에서 지원 되는 메트릭

| metrics  | 설명 | 차원 |
| ------------- | ------------- | ------------- |
| 바이트 적중률 | 총 송신에 대해 계산 된 AFD 캐시에서의 송신 비율입니다. </br> **바이트 적중률** = (edge에서 송신 원본에서 송신)/에 지에서 송신 </br> **바이트 적중률 계산에서 제외** 되는 시나리오:</br> 1. 규칙 엔진이 나 쿼리 문자열 캐싱 동작을 통해 캐시를 명시적으로 구성 하지 않습니다. </br> 2. 저장소나 개인 캐시를 사용 하지 않고 cache-control 지시문을 명시적으로 구성 합니다. </br>3. 대부분의 트래픽이 구성 또는 시나리오에 따라 캐싱에서 제공 되는 것이 아니라 원본으로 전달 되는 경우 바이트 적중률은 낮을 수 있습니다. | 엔드포인트 |
| RequestCount | CDN에서 제공 하는 클라이언트 요청 수입니다. | 끝점, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| ResponseSize | AFD에서 처리 하는 클라이언트 요청 수입니다. |끝점, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| TotalLatency | Cdn **에서 클라이언트에 마지막 응답 바이트가 전송 될 때까지** cdn에서 받은 총 시간입니다. |끝점, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| RequestSize | 클라이언트에서 AFD로의 요청으로 전송 된 바이트 수입니다. | 끝점, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| 4XX% ErrorRate | 응답 상태 코드가 4XX 인 모든 클라이언트 요청에 대 한 백분율입니다. | 끝점, 클라이언트 국가, 클라이언트 영역 |
| 5XX% ErrorRate | 응답 상태 코드가 5XX 인 모든 클라이언트 요청에 대 한 백분율입니다. | 끝점, 클라이언트 국가, 클라이언트 영역 |
| OriginRequestCount  | AFD에서 원본으로 전송 된 요청 수입니다. | 끝점, 원본, HTTP 상태, HTTP 상태 그룹 |
| OriginLatency | AFD가 백 엔드에서 마지막 응답 바이트를 받을 때까지 AFD edge에서 백 엔드로 요청을 보낸 시점에서 계산 된 시간입니다. | 끝점, 원점 |
| OriginHealth% | AFD에서 원본으로 성공한 상태 프로브의 백분율입니다.| 원본, 원본 그룹 |
| WAF 요청 수 | WAF 요청을 일치 시킵니다. | 작업, 규칙 이름, 정책 이름 |

## <a name="access-metrics-in-azure-portal"></a>Azure Portal에서 메트릭 액세스

1. Azure Portal 메뉴에서 **모든 리소스** 를 선택  >>  **\<your-AFD Standard/Premium (Preview) -profile>** 합니다.

2. **모니터링** 아래에서 **메트릭** 을 선택 합니다.

3. **메트릭** 에서 추가할 메트릭을 선택 합니다.

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="메트릭 페이지의 스크린샷" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. 필터 **추가** 를 선택 하 여 필터를 추가 합니다.

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="메트릭에 필터를 추가 하는 스크린샷" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. **분할 적용** 을 선택 하 여 데이터를 다른 치수로 분할 합니다.

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="메트릭에 차원을 추가 하는 스크린샷" lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. 새 차트를 추가 하려면 **새 차트** 를 선택 합니다.

## <a name="configure-alerts-in-azure-portal"></a>Azure Portal에서 경고 구성

1. 경고 **모니터링** 을 선택 하 여 Azure 전면 도어 표준/프리미엄 (미리 보기)에 대 한 경고를 설정  >>  합니다.

1. 메트릭 섹션에 나열 된 메트릭에 대 한 **새 경고 규칙** 을 선택 합니다.

경고는 Azure Monitor 기반으로 청구 됩니다. 경고에 대 한 자세한 내용은 [Azure Monitor 경고](../../azure-monitor/alerts/alerts-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Front 도어 표준/프리미엄 보고서](how-to-reports.md)에 대해 알아봅니다.
- [Azure Front 도어 표준/프리미엄 로그](how-to-logs.md)에 대해 알아봅니다.