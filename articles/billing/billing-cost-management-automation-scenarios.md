---
title: Azure 청구 및 비용 관리 자동화 시나리오 | Microsoft Docs
description: 일반적인 청구 및 비용 관리 시나리오가 다양한 API에 어떻게 매핑되는지 알아봅니다.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247669"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>청구 및 비용 관리 자동화 시나리오

청구 및 비용 관리 공간에 대한 일반적인 시나리오는 아래에서 식별되어 해당 시나리오에서 사용할 수 있는 여러 API에 매핑됩니다. 사용 가능한 모든 API 및 제공되는 기능에 대한 요약은 시나리오-API 매핑 아래에 있습니다. 

## <a name="common-scenarios"></a>일반적인 시나리오 

다양한 시나리오에서 청구 및 비용 관리 API를 사용하여 비용 및 사용량 관련 질문에 답변할 수 있습니다.  일반적인 시나리오의 개요가 아래에 나와 있습니다.

- **청구서 조정** - Microsoft가 적절한 금액을 청구했나요?  내 청구서는 무엇이며 내가 직접 계산할 수 있나요?

- **교차 청구** - 청구 금액은 알았는데, 조직에서 누가 지불해야 하나요?

- **비용 최적화** - 청구된 금액은 확인했지만, 어떻게 하면 Azure에서 지출하는 것보다 더 많은 돈을 벌 수 있나요?

- **비용 추적** - Azure를 사용하여 내가 얼마나 지출하고 있는지를 시간별로 알고 싶습니다. 추세란 무엇인가요? 어떻게 더 개선할 수 있나요?

- **해당월 Azure 소비** - 이번 달 현재까지 지출액은 얼마인가요? Azure의 지출 및/또는 사용량을 조정해야 하나요? 해당 월 중에 언제 나는 Azure를 가장 많이 소비했나요?

- **경고 설정** - 리소스 기반 소비 또는 통화 기반 알림을 설정하고 싶습니다.

## <a name="scenario-to-api-mappings"></a>시나리오-API 매핑

|         API/시나리오        | 청구서 조정    | 교차 청구    | 비용 최적화    | 비용 추적    | 월 중순 지출    | Alerts    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| 예산                     |                           |                  |           X          |                  |                    |     X     |
| Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| 가격표                 |             X             |         X        |           X          |         X        |          X         |           |
| 예약 권장 사항 |                           |                  |           X          |                  |                    |           |
| 예약 세부 정보         |                           |                  |           X          |         X        |                    |           |
| 예약 요약       |                           |                  |           X          |         X        |                    |           |
| 사용량 세부 정보               |             X             |         X        |           X          |         X        |          X         |     X     |
| 청구 기간             |             X             |         X        |           X          |         X        |                    |           |
| 송장                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| 요금 미지정 사용량               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> 아래 시나리오-API 매핑은 기업 소비량 API를 포함하지 않습니다. 가능한 경우, 일반적인 Consumption API를 활용하여 향후 진행될 새로운 개발 시나리오를 해결해 주세요.

## <a name="api-summaries"></a>API 요약

### <a name="consumption"></a>Consumption
(*웹 직접 + 기업 고객 - 아래 나와 있는 API를 제외한 모든 API*)

-   **예산**(*기업 고객만*): [예산 API](https://docs.microsoft.com/rest/api/consumption/budgets)를 사용하여 리소스, 리소스 그룹 또는 청구 측정 단위에 대한 비용 또는 사용 예산을 만들 수 있습니다.  예산을 만든 후에는 사용자 정의 예산 임계값이 초과되면 이를 알리도록 경고를 구성할 수 있습니다. 예산액에 도달할 때 발생할 작업도 구성할 수 있습니다.
-   **Marketplace**: [Marketplace 요금 API](https://docs.microsoft.com/rest/api/consumption/marketplaces)를 사용하여 모든 Marketplace 리소스(Azure 타사 제품)에 대한 요금 및 사용 현황 데이터를 가져옵니다. 이 데이터는 모든 Marketplace 리소스에 걸쳐 비용을 합산하거나 특정 리소스에 대한 비용/사용량을 조사하는 데 사용할 수 있습니다.
-   **가격표**(*기업 고객만*): 기업 고객은 [가격표 API](https://docs.microsoft.com/rest/api/consumption/pricesheet)를 사용하여 모든 미터에 대한 사용자 지정 가격을 검색할 수 있습니다. 기업은 사용량 세부 정보 및 마켓플레이스 사용 정보를 이 데이터와 함께 사용하여 사용량 및 마켓플레이스 데이터를 사용한 비용 계산을 수행할 수 있습니다. 
-   **예약 권장 사항**: [예약 권장 사항 API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations)를 사용하여 VM 예약 인스턴스 구매에 대한 권장 사항을 가져옵니다. 권장 사항은 고객이 예상 비용 절감 및 구매 금액을 분석할 수 있도록 설계되었습니다.
-   **예약 세부 정보**: [예약 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails)를 사용하면 예약된 소비량과 실제로 사용된 양과 같은 이전에 구매한 VM 예약 정보를 볼 수 있습니다. VM 수준별로 데이터를 자세히 볼 수 있습니다.
-   **예약 요약**: [예약 요약 API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries)를 사용하면 예약된 소비량과 실제로 사용된 양과 같은 이전에 구매한 VM 예약 정보를 볼 수 있습니다. 
-   **사용량 세부 정보**: [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/consumption/usagedetails)를 사용하여 모든 Azure 자사 리소스에 대한 요금 및 사용량을 가져옵니다. 정보는 하루에 미터당 한 번씩 보내지는 사용량 세부 정보 레코드 형식으로 제공됩니다. 정보는 모든 리소스에 걸쳐 비용을 합산하거나 특정 리소스에 대한 비용/사용량을 조사하는 데 사용할 수 있습니다.
-   **RateCard**: 웹 직접 고객은 [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx)를 사용하여 자신의 요금제를 가져옵니다. 그런 다음, 해당 리소스 사용 정보를 반환된 정보와 함께 사용하여 예상된 청구서를 직접 계산할 수 있습니다. 
-   **요금 미지정 사용량**: [요금 미지정 사용량 API](https://msdn.microsoft.com/library/azure/mt219003.aspx)를 사용하여 Azure에 의해 계량/청구되기 전에 가공되지 않은 사용량 정보를 얻을 수 있습니다.

### <a name="billing"></a>결제
-   **청구 기간**: [청구 기간 API](https://docs.microsoft.com/rest/api/billing/billingperiods)를 사용하여 해당 기간의 청구서 ID와 함께 분석할 청구 기간을 결정합니다. 청구서 ID는 아래 청구서 API와 함께 사용할 수 있습니다. 
-   **청구서**: [청구서 API](https://docs.microsoft.com/rest/api/billing/invoices)를 사용하여 지정된 청구 기간에 대한 청구서의 다운로드 URL을 PDF 형식으로 가져옵니다.

### <a name="enterprise-consumption"></a>기업 소비량
*(모든 API 기업만)*

-   **잔액 요약** - [잔액 요약 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)를 사용하여 잔액, 신규 구매, Azure Marketplace 서비스 요금, 조정 및 초과 요금에 대한 월별 정보 요약을 가져옵니다. 현재 청구 기간 또는 과거의 특정 기간에 대해 이 정보를 얻을 수 있습니다. 기업은 이 데이터를 사용하여 수동으로 계산한 요약 요금과 비교할 수 있습니다. 이 API는 리소스 관련 정보 또는 비용에 대한 집계 보기는 제공하지 않습니다.
-   **사용량 세부 정보**: [사용량 세부 정보 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)를 사용하여 당월, 특정 청구 기간 또는 사용자 지정 기간에 대한 자사 Azure 사용량 세부 정보를 가져옵니다. 기업은 이 데이터를 사용하여 요금 및 소비량을 기반으로 청구서를 직접 계산할 수 있으며 제공된 부서/조직 정보를 사용하여 조직 전체의 비용을 산출할 수 있습니다. 데이터는 리소스별 사용량/비용 보기를 제공합니다.
-   **Marketplace 저장소 요금**: [Marketplace 저장소 요금 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)를 사용하여 당월, 특정 청구 기간 또는 사용자 지정 기간에 대한 타사 Azure 사용량 세부 정보를 가져옵니다. 기업은 이 데이터를 사용하여 요금 및 소비량을 기반으로 청구서를 직접 계산할 수 있으며 제공된 부서/조직 정보를 사용하여 조직 전체의 비용을 산출할 수 있습니다. Marketplace 저장소 요금 API는 리소스별 사용량/비용 보기를 제공합니다.
-   **가격표**: [가격표 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)는 지정된 등록 및 청구 기간에 대한 각 측정기에 적용할 수 있는 가격을 제공합니다. 이 요금 정보를 사용량 세부 정보 및 마켓플레이스 사용 정보와 함께 사용하여 예상된 청구서를 직접 계산할 수 있습니다.
-   **청구 기간**: [청구 기간 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)를 사용하여 해당 청구 기간과 관련된 4가지 Enterprise API 데이터 집합(BalanceSummary, UsageDetails, Marketplace Charges, PriceSheet)에 대한 API 경로를 가리키는 속성과 함께 청구 기간 목록을 가져옵니다.
-   **예약 인스턴스 권장 사항**: [예약 인스턴스 권장 사항 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)는 고객의 7일, 30일 또는 60일의 가상 머신 사용량을 확인하고 단일 및 공유 구매 권장 사항을 제공합니다. 고객은 예약 인스턴스 API를 통해 예상 비용 절감 및 권장 구매 금액을 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Azure 청구 API를 사용하여 Azure 사용량에 대한 정보를 프로그래밍 방식으로 얻는 방법에 대한 자세한 내용은 [Azure 청구 API 개요](billing-usage-rate-card-overview.md)를 참조하세요.
