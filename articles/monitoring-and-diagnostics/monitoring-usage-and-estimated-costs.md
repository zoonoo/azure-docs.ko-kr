---
title: Azure Monitor의 모니터링 사용량 및 예상 비용 | Microsoft Docs
description: Azure Monitor의 모니터링 사용량 및 예상 비용 프로세스 개요 페이지
author: dalekoetke
manager: carmonmills
editor: mrbullwinkle
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f87705ebbdd14b1bbf7cade481a7dbe7dd3d5131
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>모니터링 사용량 및 예상 비용

Azure Portal의 모니터 허브에서 **사용량 및 예상 비용** 페이지는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 등과 같은 핵심 모니터링 기능의 사용량을 이해하는 데 도움이 되도록 디자인되었습니다. 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 상품을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 확인하고 해당 기간의 사용량 추세를 상세히 파악할 수 있습니다. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.
모니터링 사용량과 그에 따른 비용의 추정치를 보여 주는 예제는 다음과 같습니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/monitoring-usage-and-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다.

![노드당 포함 스크린 샷 - 671.47GB](./media/monitoring-usage-and-estimated-costs/002.png)

다른 비슷한 사용량 및 비용 요약은 다음과 같습니다. 새로운 2018년 4월 구독의 경우로, 사용량 기준 가격 책정 모델입니다. 노드 기반 청구에서는 부족했던 부분과, Log Analytics 및 Application Insights의 데이터 수집 및 보존이 새 공통 미터에서 보고되는 것을 확인해 봅니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>새 가격 책정 모델

2018년 4월, 새 모니터링 가격 책정 모델이 릴리스되었습니다.  이 모델은 클라우드 기반이며 사용을 기준으로 한 가격 책정이라는 특징이 있습니다. 노드 기반 약정 없이 사용한 양만큼만 결제합니다. [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)에 대해 새 가격 책정 모델의 세부 정보를 제공합니다.

2018년 4월 2일 이후 Log Analytics 또는 Application Insights에 가입한 고객의 경우 새 가격 책정 모델만 선택할 수 있습니다. 그 전에 이 서비스를 이미 사용하던 고객의 경우 새 가격 책정 모델로의 전환이 선택 사항입니다.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>새 가격 책정 모델의 영향 평가

새 가격 책정 모델이 미치는 영향은 모니터링 사용량 패턴에 따라 고객마다 달라질 것입니다. 2018년 4월 2일 이전에 Log Analytics 또는 Application Insights를 이미 사용하던 고객의 경우, Azure Monitor의 **사용량 및 예상 비용** 페이지에서 새 가격 책정 모델로 전환할 경우의 비용 변동을 추정하고 새 모델로 구독을 전환하는 방법을 확인할 수 있습니다. 대부분의 고객에게 새 가격 책정 모델이 유리할 것이나, 데이터 사용 패턴이 매우 높은 수준이거나 요금이 비싼 지역의 고객이라면 여기에 해당하지 않을 수 있습니다.

**사용량 및 예상 비용** 페이지에서 선택한 구독의 예상 비용을 보려면 페이지 맨 위에 있는 파란 배너를 클릭합니다. 새 가격 책정 모델은 한 번에 한 구독만 선택할 수 있으므로 한 번에 한 구독에 대해서만 확인해 보는 것이 좋습니다.

![가격 책정 모델 선택 스크린 샷](./media/monitoring-usage-and-estimated-costs/004.png)

이제 녹색 배너가 있는 비슷한 페이지가 표시될 것입니다.

![가격 책정 모델 선택 스크린 샷](./media/monitoring-usage-and-estimated-costs/005.png)

여기서는 새 가격 책정 모델에 해당하는 여러 미터 집합을 볼 수 있습니다. 다음과 같은 데이터 수집 미터를 예로 들 수 있습니다.

1. Insight and Analytics\Overage per Node
2. Insight and Analytics\Included per Node
3. Application Insights\Basic Overage Data
4. Application Insights\Included Data

등이 **Shared Services\Data Ingestion**이라는 새로운 공통 데이터 수집 미터로 결합됩니다. 새 가격 책정 모델에는 노드 기반 포함 데이터 할당이 없기 때문입니다.

또 다른 변화는 요금이 더 비싼 지역의 Log Analytics 또는 Application Insights의 경우 새로운 지역 미터로 이를 제대로 반영하여 표시됩니다. 예를 들어 **“데이터 주입(미국 중서부)**”입니다.

> [!NOTE]
> OMS(Operations Management Suite) 구독이 있다면 구매한 각 노드에 대한 Log Analytics 및 Application Insights 데이터 수집 할당을 받습니다. (구독 수준이 아닌) 계정 수준에서 적용되므로 추정치가 이러한 할당의 결과를 나타내지 못합니다. 이 경우 새 가격 책정 모델에 대한 더 상세한 논의는 고객 담당자에게 문의하세요.

## <a name="changes-when-moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환할 경우의 변경 내용

새 가격 책정 모델로 구독을 전환하면 각 Log Analytics의 가격 책정 계층이 새 GB 기준 계층으로 바뀌며 모든 항목이 전환됩니다(Azure Resource Manager에서 “pergb2018이라 함). 이렇게 하면 Enterprise 요금제에서 Application Insights 리소스도 Basic 요금제로 변경됩니다. 이 변경의 결과는 위의 예상 비용에서 설명하고 있습니다. 

## <a name="moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환

구독의 새 가격 책정 모델을 사용하기로 했다면 **사용량 및 예상 비용** 페이지의 **가격 책정 모델 선택** 옵션을 클릭합니다.

![새 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/006.png)

그러면 앞 페이지에서 본 각각의 구독을 나열하는 **가격 책정 모델 선택** 페이지가 열립니다.

![가격 책정 모델 선택 스크린 샷](./media/monitoring-usage-and-estimated-costs/007.png)

구독을 새 가격 책정 모델로 전환하려면 확인란을 선택하고 **저장**을 클릭하기만 하면 됩니다.  같은 방법으로 기존 가격 책정 모델로 되돌릴 수 있습니다. 가격 책정 모델을 변경하려면 구독 소유자나 참가자 권한이 필요합니다.