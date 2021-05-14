---
title: Azure Front Door 표준/프리미엄의 메트릭 모니터링
description: 이 문서에서는 Azure Front Door 표준/프리미엄 모니터링 메트릭을 설명합니다.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557329"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Azure Front Door 표준/프리미엄의 실시간 모니터링

> [!Note]
> 이 설명서는 Azure Front Door 표준/프리미엄(미리 보기)용입니다. Azure Front Door에 대한 정보를 찾고 있나요? [여기](../front-door-overview.md)에서 봅니다.

Azure Front Door 표준/프리미엄은 Azure Monitor와 통합되며, Azure Front Door 표준/프리미엄을 실시간으로 모니터링하는 데 도움이 되는 11개의 메트릭이 있어 문제를 추적하고, 해결하고, 디버그할 수 있습니다.  

Azure Front Door 표준/프리미엄은 60초 간격으로 메트릭을 측정하고 전송합니다. 메트릭이 포털에 표시되는 데는 최대 3분 정도 걸릴 수 있습니다. 메트릭은 원하는 차트나 그리드에 표시될 수 있으며 포털, PowerShell, CLI, API를 통해 액세스할 수 있습니다. 자세한 내용은  [Azure Monitor 메트릭](../../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.  

기본 메트릭은 무료입니다. 추가 비용으로 추가 메트릭을 사용할 수 있습니다. 

4XXErrorRate 또는 5XXErrorRate에 대한 임계값과 같은 각 메트릭에 대한 경고를 구성할 수 있습니다. 오류율이 임계값을 초과하는 경우 구성된 대로 경고를 트리거합니다. 자세한 내용은 [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../azure-monitor/alerts/alerts-metric.md)를 참조하세요. 

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Azure Front Door 표준/프리미엄에서 지원되는 메트릭

| metrics  | 설명 | 차원 |
| ------------- | ------------- | ------------- |
| 바이트 적중률 | 총 송신에 대해 계산된 AFD 캐시의 송신 백분율입니다. </br> **바이트 적중률** = (edge에서 송신 - 원본에서 송신)/에 지에서 송신. </br> **바이트 적중률 계산에서 제외되는 시나리오**:</br> 1. 규칙 엔진이나 쿼리 문자열 캐싱 동작을 통해 캐시를 명시적으로 구성하지 않습니다. </br> 2. 저장소나 개인 캐시를 사용하지 않고 캐시 제어 지시문을 명시적으로 구성합니다. </br>3. 대부분의 트래픽이 구성 또는 시나리오에 따라 캐싱에서 제공되는 것이 아니라 원본으로 전달되는 경우 바이트 적중률은 낮을 수 있습니다. | 엔드포인트 |
| RequestCount | CDN에서 제공하는 클라이언트 요청 수. | 엔드포인트, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| ResponseSize | AFD에서 제공하는 클라이언트 요청 수. |엔드포인트, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| TotalLatency | CDN에서 수신한 클라이언트 요청 **으로부터 CDN에서 클라이언트로 보내는 마지막 응답 바이트까지의 총 시간입니다**. |엔드포인트, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| RequestSize | 클라이언트에서 AFD로 요청으로 전송된 바이트 수. | 엔드포인트, 클라이언트 국가, 클라이언트 영역, HTTP 상태, HTTP 상태 그룹 |
| 4XX % ErrorRate | 응답 상태 코드가 4XX인 모든 클라이언트 요청의 백분율. | 엔드포인트, 클라이언트 국가, 클라이언트 영역 |
| 5XX % ErrorRate | 응답 상태 코드가 5XX인 모든 클라이언트 요청의 백분율. | 엔드포인트, 클라이언트 국가, 클라이언트 영역 |
| OriginRequestCount  | AFD에서 원본으로 보낸 요청 수 | 엔드포인트, 원본, HTTP 상태, HTTP 상태 그룹 |
| OriginLatency | AFD 에지가 백 엔드로 요청을 보낸 시점부터 AFD가 밴 엔드로부터 마지막 응답 바이트를 받은 시점까지 얼마나 걸렸는지 계산하여 구하는 시간. | 엔드포인트, 원본 |
| OriginHealth% | AFD에서 원본으로 성공한 상태 프로브의 백분율.| 원본, 원본 그룹 |
| WAF 요청 수 | 일치하는 WAF 요청입니다. | 작업, 규칙 이름, 정책 이름 |

## <a name="access-metrics-in-azure-portal"></a>Azure Portal에서 메트릭에 액세스

1. Azure Portal 메뉴에서 **모든 리소스** >>  **\<your-AFD Standard/Premium (Preview) -profile>** 를 선택합니다.

2. **모니터링** 에서 **메트릭** 을 선택합니다.

3. **메트릭** 에서 추가할 메트릭을 선택합니다.

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="메트릭 페이지의 스크린샷." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. **필터 추가** 를 선택하여 필터를 추가합니다.

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="메트릭에 필터를 추가하는 스크린샷." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. **분할 적용** 을 선택하여 다른 차원으로 데이터를 분할합니다.

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="메트릭에 차원을 추가하는 스크린샷." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. **새 차트** 를 선택하여 새 차트를 추가합니다.

## <a name="configure-alerts-in-azure-portal"></a>Azure Portal에서 경고 구성

1. **모니터링** >> **경고** 를 선택하여 Azure Front Door 표준/프리미엄(미리 보기)에 대한 경고를 설정합니다.

1. 메트릭 섹션에 나열된 메트릭에 대해 **새 경고 규칙** 을 선택합니다.

경고는 Azure Monitor 기반으로 나타납니다. 경고에 대한 자세한 내용은 [Azure Monitor 경고](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Front Door 표준/프리미엄 보고서](how-to-reports.md)에 대해 알아보기.
- [Azure Front Door 표준/프리미엄 로그](how-to-logs.md)에 대해 알아보기.