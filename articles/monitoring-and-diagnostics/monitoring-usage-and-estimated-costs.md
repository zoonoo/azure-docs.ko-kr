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
ms.date: 04/09/2018
ms.author: Dale.Koetke;mbullwin
ms.openlocfilehash: f25c39b602449be3ab9d1cd7e67d6fcfc78afb17
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2018
---
# <a name="monitoring-usage-and-estimated-costs"></a>모니터링 사용량 및 예상 비용

Azure Portal의 모니터 허브에서 **사용량 및 예상 비용** 페이지는 [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) 등과 같은 핵심 모니터링 기능의 사용량을 설명합니다. 2018년 4월 이전 제공된 요금제 고객의 경우 Insights 및 Analytics 상품을 통해 구매한 Log Analytics 사용량도 여기에 포함됩니다.

사용자는 이 페이지에서 구독별로 집계된 지난 31일 간의 리소스 사용량을 볼 수 있습니다. 드릴인은 31일 동안의 사용량 추세를 보여 줍니다. 예상치를 내기 위해서는 상당한 데이터를 취합해야 하므로 페이지 로드에 다소 시간이 소요됩니다.

이 예에서는 모니터링 사용량과 그에 따른 비용의 추정치를 보여 줍니다.

![사용량 및 예상 비용 포털 스크린 샷](./media/monitoring-usage-and-estimated-costs/001.png)

월별 사용량 열의 링크를 클릭하면 지난 31일 간의 사용량 추세를 보여 주는 차트가 열립니다.

![노드당 포함 가로 막대형 차트 스크린 샷](./media/monitoring-usage-and-estimated-costs/002.png)

다른 비슷한 사용량 및 비용 요약은 다음과 같습니다. 이 예제에서는 새로운 2018년 4월 구독으로, 사용량 기준 가격 책정 모델을 보여 줍니다. 노드 기반 청구에서는 부족했던 부분을 확인합니다. Log Analytics 및 Application Insights의 데이터 수집 및 보존이 새 공통 미터에서 보고됩니다.

![사용량 및 예상 비용 포털 스크린 샷 - 2018년 4월 가격 책정](./media/monitoring-usage-and-estimated-costs/003.png)

## <a name="new-pricing-model"></a>새 가격 책정 모델

2018년 4월, 새 모니터링 가격 책정 모델이 릴리스되었습니다. 클라우드 기반이며 사용량을 기준으로 한 가격 책정이라는 특징이 있습니다. 노드 기반 약정 없이 사용한 양만큼만 결제합니다. [경고, 메트릭, 알림](https://azure.microsoft.com/pricing/details/monitor/), [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) 및 [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)에 대해 새 가격 책정 모델의 세부 정보를 제공합니다.

2018년 4월 2일 이후 Log Analytics 또는 Application Insights를 사용하기 시작한 고객의 경우 새 가격 책정 모델만 선택할 수 있습니다. 이 서비스를 이미 사용하던 고객의 경우 새 가격 책정 모델로의 전환이 선택 사항입니다.

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>새 가격 책정 모델의 영향 평가

새 가격 책정 모델이 미치는 영향은 모니터링 사용량 패턴에 따라 고객마다 달라질 것입니다. 2018년 4월 2일 이전에 Log Analytics 또는 Application Insights를 사용한 고객의 경우 Azure Monitor에서 **사용량 및 예상 비용** 페이지에 새 가격 책정 모델로 전환할 경우 비용 변동 예상치가 나타납니다. 구독을 새 모델로 전환하는 방법을 제공합니다. 대부분의 고객에게 새 가격 책정 모델이 유리합니다. 데이터 사용 패턴이 매우 높은 수준이거나 요금이 비싼 지역의 고객이라면 여기에 해당하지 않을 수 있습니다.

**사용량 및 예상 비용** 페이지에서 선택한 구독의 예상 비용을 보려면 페이지 맨 위에 있는 파란 배너를 선택합니다. 새 가격 책정 모델은 한 번에 한 구독만 선택할 수 있으므로 한 번에 한 구독에 대해서만 확인해 보는 것이 좋습니다.

![새 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/004.png)

새 페이지에서는 이전 페이지의 유사한 버전을 녹색 배너와 함께 보여 줍니다.

![현재 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/005.png)

이 페이지는 새 가격 책정 모델에 해당하는 다양한 미터 집합도 보여 줍니다. 다음은 예제 목록입니다.

- Insight and Analytics\Overage per Node
- Insight and Analytics\Included per Node
- Application Insights\Basic Overage Data
- Application Insights\Included Data

새 가격 책정 모델에는 노드 기반 포함 데이터 할당이 없습니다. 따라서 이러한 데이터 수집 미터가 **Shared Services\Data Ingestion**이라는 새로운 공통 데이터 수집 미터로 결합됩니다. 

비용이 많이 드는 지역에서 Log Analytics 또는 Application Insights에 수집된 데이터는 또 다른 변화가 있습니다. 비용이 많이 드는 지역에 대한 데이터는 새 지역별 미터로 표시됩니다. 예로 **데이터 수집(미국 중서부)** 이 있습니다.

> [!NOTE]
> 구독당 예상 비용은 OMS(Operations Management Suite) 구독의 계정 수준 노드별 자격의 영향을 받지 않습니다. 이 경우 새 가격 책정 모델에 대한 더 상세한 논의는 고객 담당자에게 문의하세요.

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>새 가격 책정 모델 및 Operations Management Suite 구독 자격

Microsoft Operations Management Suite E1 및 E2를 구매한 고객은 [Log Analytics](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite) 및 [Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-pricing#the-price-plans)에 대한 노드별 데이터 수집 자격이 있습니다. 지정된 구독에서 Log Analytics 작업 영역 또는 Application Insights 대해 이러한 자격을 얻으려면 해당 구독의 가격 책정 모델이 2018년 4월 이전의 가격 책정 모델을 유지해야 합니다. 이 모델은 Log Analytics “노드별(OMS)” 가격 책정 계층 및 Application Insights “Enterprise” 가격 책정 계획이 적용됩니다. 조직에서 구매한 제품군의 노드 수에 따라, 일부 구독을 새 가격 책정 모델로 전환하는 것이 여전히 이로울 수 있습니다. 하지만 신중히 결정해야 합니다.

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환할 경우의 변경 내용

새 가격 책정 모델로 구독을 전환하면 각 Log Analytics의 가격 책정 계층이 새 GB 기준 계층으로 바뀌며 모든 항목이 전환됩니다(Azure Resource Manager에서 “pergb2018이라 함). 이렇게 하면 Enterprise 요금제에서 Application Insights 리소스도 Basic 요금제로 변경됩니다. 비용 예측에서 이러한 변경의 효과를 보여 줍니다.

## <a name="moving-to-the-new-pricing-model"></a>새 가격 책정 모델로 전환

구독의 새 가격 책정 모델을 사용하기로 했다면 **사용량 및 예상 비용** 페이지의 **가격 책정 모델 선택** 옵션을 선택합니다.

![새 가격 책정 모델의 사용량 및 예상 비용 모니터링 스크린 샷](./media/monitoring-usage-and-estimated-costs/006.png)

**가격 책정 모델 선택** 페이지가 열립니다. 이전 페이지에서 본 각 구독 목록을 보여 줍니다.

![가격 책정 모델 선택 스크린 샷](./media/monitoring-usage-and-estimated-costs/007.png)

구독을 새 가격 책정 모델로 전환하려면 확인란을 선택하고 **저장**을 선택하기만 하면 됩니다. 같은 방법으로 기존 가격 책정 모델로 되돌릴 수 있습니다. 가격 책정 모델을 변경하려면 구독 소유자나 참가자 권한이 필요합니다.
