---
title: Azure Cost Management 데이터 이해 | Microsoft Docs
description: This article helps you better understand data that's included in Azure Cost Management and how frequently it's processed, collected, shown, and closed.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 25a20ca40b517209fca7b969e3a7399a1c7c7387
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229757"
---
# <a name="understand-cost-management-data"></a>Cost Management 데이터 이해

This article helps you better understand Azure cost and usage data that's included in Azure Cost Management. It explains how frequently data is processed, collected, shown, and closed. Azure 사용 요금은 매월 청구됩니다. Although billing cycles are monthly periods, cycle start and end dates vary by subscription type. Cost Management가 사용량 데이터를 받는 빈도는 다양한 요인에 따라 달라집니다. 이러한 요인에는 데이터 처리에 걸리는 시간, Azure 서비스에서 사용량을 청구 시스템으로 내보내는 빈도 등이 있습니다.

Cost Management includes all usage and purchases, including reservations and third-party offerings for Enterprise Agreement (EA) accounts. Microsoft Customer Agreement accounts and individual subscriptions with pay-as-you-go rates  only include usage from Azure and Marketplace services. Support and other costs are not included. Costs are estimated until an invoice is generated and do not factor in credits.

## <a name="supported-microsoft-azure-offers"></a>Supported Microsoft Azure offers

Azure Cost Management에서 현재 지원되는 [Microsoft Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)음 다음과 같습니다. Azure 제품이란 사용자가 소유한 Azure 구독의 유형입니다. Data is available in Cost Management starting on the **Data available from** date. If a subscription changes offers, costs before the offer change date will not be available.

| **범주**  | **제품 이름** | **Quota ID** | **제품 번호** | **Data available from** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | May 2014<sup>1</sup> |
| **EA(기업 계약)** | 엔터프라이즈 개발/테스트                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | May 2014<sup>1</sup> |
| **EA(기업 계약)** | [Microsoft Azure 엔터프라이즈](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | May 2014<sup>1</sup> |
| **Microsoft 고객 계약** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | March 2019<sup>3</sup> |
| **Microsoft 고객 계약** | [Microsoft Azure Plan for Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | March 2019<sup>3</sup> |
| **Microsoft Customer Agreement supported by partners** | Microsoft Azure 계획 | CSP_2015-05-01, CSP_MG_2017-12-01, and CSPDEVTEST_2018-05-01<br><br>The quota ID is reused for Microsoft Customer Agreement and legacy CSP subscriptions. Currently, only Microsoft Customer Agreement subscriptions are supported. | N/A | 2019년 10월 |
| **MSDN(Microsoft Developer Network)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | October 2, 2018<sup>2</sup> |
| **종량제** | [종량제](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | October 2, 2018<sup>2</sup> |
| **종량제** | [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | October 2, 2018<sup>2</sup> |
| **종량제** | [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | October 2, 2018<sup>2</sup> |
| **종량제** | [Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | October 2, 2018<sup>2</sup> |
| **종량제** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | October 2, 2018<sup>2</sup> |
| **종량제** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | October 2, 2018<sup>2</sup> |

_<sup>**1**</sup> For data before May 2014, visit the [Azure Enterprise portal](https://ea.azure.com)._

_<sup>**2**</sup> For data before October 2, 2018, visit the [Azure Account Center](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft Customer Agreements started in March 2019 and do not have any historical data before this point._

_<sup>**4**</sup> Historical data for credit-based and pay-in-advance subscriptions might not match your invoice. See [Historical data may not match invoice](#historical-data-might-not-match-invoice) below._

The following offers are not supported yet:

| 범주  | **제품 이름** | **Quota ID** | **제품 번호** |
| --- | --- | --- | --- |
| **Azure 독일** | [Azure 독일 종량제](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government 종량제 | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **CSP(클라우드 솔루션 공급자)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP(클라우드 솔루션 공급자)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP(클라우드 솔루션 공급자)** | Microsoft Cloud Germany에 대한 Azure Germany in CSP   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **종량제**                 | 학생용 Azure 스타터 | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **종량제** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **종량제**                 | [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **지원 플랜** | 표준 지원                    | Default_2014-09-01 | MS-AZR-0041P |
| **지원 플랜** | Professional Direct 지원         | Default_2014-09-01 | MS-AZR-0042P |
| **지원 플랜** | 개발자 지원                   | Default_2014-09-01 | MS-AZR-0043P |
| **지원 플랜** | 독일 지원 플랜                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **지원 플랜** | Azure Government 표준 지원   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **지원 플랜** | Azure Government Pro-Direct 지원 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **지원 플랜** | Azure Government 개발자 지원  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determine your offer type
구독 데이터가 표시되지 않고 구독이 지원되는 제품에 해당하는지 확인하려는 경우 구독이 지원되는지 확인할 수 있습니다. Azure 구독이 지원되는지 확인하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다. 그런 다음, 왼쪽 메뉴 창에서 **모든 서비스**를 선택합니다. 서비스 목록에서 **구독**을 선택합니다. 구독 목록 메뉴에서 확인할 구독을 클릭합니다. 구독이 개요 탭에 표시되며, **제품** 및 **제품 ID**를 확인할 수 있습니다. 다음 이미지에 예가 나와 있습니다.

![제품 및 제품 ID가 표시된 구독 개요 탭의 예](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Cost Management에 포함된 비용

다음 표에는 Cost Management에 포함된 데이터와 포함되지 않은 데이터가 나와 있습니다. All costs are estimated until an invoice is generated. Costs shown do not include free and prepaid credits.

**비용 및 사용량 데이터**

| **포함됨** | **포함되지 않음** |
| --- | --- |
| Azure service usage<sup>5</sup>        | 지원 요금 - 자세한 내용은 [청구서 용어 설명](../billing/billing-understand-your-invoice.md)을 참조하세요. |
| Marketplace offering usage<sup>6</sup> | 세금 - 자세한 내용은 [청구서 용어 설명](../billing/billing-understand-your-invoice.md)을 참조하세요. |
| Marketplace purchases<sup>6</sup>      | 크레딧 - 자세한 내용은 [청구서 용어 설명](../billing/billing-understand-your-invoice.md)을 참조하세요. |
| Reservation purchases<sup>7</sup>      |  |
| Amortization of reservation purchases<sup>7</sup>      |  |

_<sup>**5**</sup> Azure service usage is based on reservation and negotiated prices._

_<sup>**6**</sup> Marketplace purchases are not available for Pay-As-You-Go, MSDN, and Visual Studio offers at this time._

_<sup>**7**</sup> Reservation purchases are only available for Enterprise Agreement (EA) accounts at this time._

**Metadata**

| **포함됨** | **포함되지 않음** |
| --- | --- |
| Resource tags<sup>8</sup> | 리소스 그룹 태그 |

_<sup>**8**</sup> Resource tags are applied as usage is emitted from each service and aren't available retroactively to historical usage._

## <a name="rated-usage-data-refresh-schedule"></a>평가된 사용량 데이터 새로 고침 일정

비용 및 사용량 데이터는 Azure Portal의 비용 관리 + 청구와 [지원 API](index.yml)에서 확인할 수 있습니다. 비용을 검토할 때 다음 사항에 유의하세요.

- 현재 청구 기간의 예상 요금은 하루에 6번 업데이트됩니다.
- 현재 청구 기간의 예상 요금은 사용량이 증가함에 따라 변경될 수 있습니다.
- 각 업데이트는 누적되며, 이전 업데이트의 모든 라인 항목 및 정보를 포함합니다.
- Azure는 청구 기간이 종료된 후 최대 72시간(3일) 이내에 현재 청구 기간을 ‘마감’합니다.

다음 예제에서는 청구 기간이 종료될 수 있는 방식을 보여 줍니다.

EA(기업계약) 구독 - 청구 월이 3월 31일에 종료되는 경우 예상 요금이 최대 72시간 후에 업데이트됩니다. 이 예제에서는 4월 4일 자정(UTC)까지입니다.

종량제 구독 - 청구 월이 5월 15일에 종료되는 경우 예상 요금이 최대 72시간 후에 업데이트될 수 있습니다. 이 예제에서는 5월 19일 자정(UTC)까지입니다.

### <a name="rerated-data"></a>데이터 재평가

Whether you use the [Cost Management APIs](index.yml), Power BI, or the Azure portal to retrieve data, expect the current billing period's charges to get rerated, and consequently change, until the invoice is closed.

## <a name="usage-data-update-frequency-varies"></a>사용량 데이터 업데이트 빈도 변동

Cost Management에서 발생한 사용량 데이터의 사용 가능 여부는 다음과 같은 몇 가지 요인에 따라 달라집니다.

- Azure 서비스(예: 스토리지, 컴퓨팅, CDN 및 SQL)에서 사용량을 내보내는 빈도
- 평가 엔진 및 비용 관리 파이프라인을 통해 사용량 데이터를 처리하는 데 걸리는 시간

일부 서비스는 다른 서비스보다 더 빈번하게 사용량을 내보냅니다. 따라서 일부 서비스에 대한 데이터는 데이터를 내보내는 빈도가 더 낮은 다른 서비스보다 먼저 Cost Management에 표시될 수 있습니다. 일반적으로 서비스 사용량이 Cost Management에 표시되기까지 8-24시간이 걸립니다. 업데이트는 누적되므로 사용량이 증가함에 따라 진행 중인 월의 데이터가 새로 고쳐집니다.

## <a name="historical-data-might-not-match-invoice"></a>Historical data might not match invoice

Historical data for credit-based and pay-in-advance offers might not match your invoice. Some Azure pay-as-you-go, MSDN, and Visual Studio offers can have Azure credits and advanced payments applied to the invoice. However, the historical data shown in Cost Management is based on your estimated consumption charges only. Cost Management historical data doesn't include payments and credits. As a result, the historical data shown for the following offers may not match exactly with your invoice.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Free Trial (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>참고 항목

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
