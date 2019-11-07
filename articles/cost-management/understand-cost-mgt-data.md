---
title: Azure Cost Management 데이터 이해 | Microsoft Docs
description: 이 문서는 Azure Cost Management에 포함 된 데이터와 처리, 수집, 표시 및 종결 되는 빈도를 더 잘 이해 하는 데 도움이 됩니다.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721358"
---
# <a name="understand-cost-management-data"></a>Cost Management 데이터 이해

이 문서는 Azure Cost Management에 포함 된 Azure 비용 및 사용 현황 데이터를 더 잘 이해 하는 데 도움이 됩니다. 데이터를 처리 하 고, 수집 하 고, 표시 하 고, 닫는 방법을 설명 합니다. Azure 사용 요금은 매월 청구됩니다. 청구 주기는 월별 기간 이지만 주기 시작 및 종료 날짜는 구독 유형에 따라 달라 집니다. Cost Management가 사용량 데이터를 받는 빈도는 다양한 요인에 따라 달라집니다. 이러한 요인에는 데이터 처리에 걸리는 시간, Azure 서비스에서 사용량을 청구 시스템으로 내보내는 빈도 등이 있습니다.

Cost Management에는 기업계약 (EA) 계정에 대 한 예약 및 타사 제품을 비롯 한 모든 사용량 및 구매가 포함 됩니다. Microsoft 고객 계약 계정 및 종 량 제 요금으로 개별 구독은 Azure 및 Marketplace 서비스의 사용만 포함 합니다. 지원 및 기타 비용은 포함 되지 않습니다. 비용은 송장이 생성 될 때까지 예상 되며 크레딧에는 영향을 주지 않습니다.

## <a name="supported-microsoft-azure-offers"></a>지원 되는 Microsoft Azure 제공

Azure Cost Management에서 현재 지원되는 [Microsoft Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)음 다음과 같습니다. Azure 제품이란 사용자가 소유한 Azure 구독의 유형입니다. 데이터는 날짜 **에서 사용할 수 있는 데이터** 에서 시작 하 여 Cost Management에서 사용할 수 있습니다. 구독이 변경 되는 경우 제품 변경 날짜 이전의 비용은 사용할 수 없습니다.

| **범주**  | **제품 이름** | **할당량 ID** | **제품 번호** | **데이터 사용 가능** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 년 5 월<sup>1</sup> |
| **EA(기업 계약)** | Enterprise 개발/테스트                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 년 5 월<sup>1</sup> |
| **EA(기업 계약)** | [Microsoft Azure 엔터프라이즈](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 년 5 월<sup>1</sup> |
| **Microsoft 고객 계약** | [Microsoft Azure 계획](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 해당 없음 | <sup>3 월 2019</sup> |
| **Microsoft 고객 계약** | [개발/테스트에 대 한 Microsoft Azure 계획](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | 해당 없음 | <sup>3 월 2019</sup> |
| **파트너에서 지원 되는 Microsoft 고객 계약** | Microsoft Azure 계획 | CSP_2015-05-01, CSP_MG_2017-01 및 CSPDEVTEST_2018-01-01<br><br>할당량 ID는 Microsoft 고객 계약 및 레거시 CSP 구독에 다시 사용 됩니다. 현재 Microsoft 고객 계약 구독만 지원 됩니다. | 해당 없음 | 10 월 2019 |
| **MSDN(Microsoft Developer Network)** | [MSDN 플랫폼](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018 년 10 월<sup>2 일</sup> |
| **종량제** | [종량제](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018 년 10 월<sup>2 일</sup> |
| **종량제** | [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018 년 10 월<sup>2 일</sup> |
| **종량제** | [Microsoft 파트너 네트워크](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018 년 10 월<sup>2 일</sup> |
| **종량제** | [무료 평가판](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018 년 10 월<sup>2 일</sup> |
| **종량제** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018 년 10 월<sup>2 일</sup> |
| **종량제** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2018 년 10 월<sup>2 일</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018 년 10 월<sup>2 일</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018 년 10 월<sup>2 일</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018 년 10 월<sup>2 일</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018 년 10 월<sup>2 일</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018 년 10 월<sup>2 일</sup> |

_<sup>**1**</sup> 5 월 2014 이전 데이터의 경우 [Azure Enterprise portal](https://ea.azure.com)을 방문 하세요._

_<sup>**2**</sup> 10 월 2 2018 일 이전 데이터의 경우 [Azure 계정 센터](https://account.azure.com/subscriptions)를 참조 하세요._

_<sup>**3**</sup> 3 월 2019 일에 시작 된 Microsoft 고객 계약에는이 시점 이전의 기록 데이터가 없습니다._

_<sup>**4**</sup> 신용 기반 및 종 량 제 구독에 대 한 기록 데이터가 청구서와 일치 하지 않을 수 있습니다. [기록 데이터는 아래 청구서와 일치 하지 않을 수 있습니다](#historical-data-might-not-match-invoice) .를 참조 하세요._

다음 제안은 아직 지원 되지 않습니다.

| Category  | **제품 이름** | **할당량 ID** | **제품 번호** |
| --- | --- | --- | --- |
| **Azure 독일** | [Azure 독일 종량제](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **CSP(클라우드 솔루션 공급자)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **CSP(클라우드 솔루션 공급자)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **CSP(클라우드 솔루션 공급자)** | Microsoft 클라우드 독일용 CSP의 Azure 독일   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **종량제**                 | Azure for Students Starter | DreamSpark_2015-01 | MS-AZR-0144P |
| **종량제** | [학생용 Azure](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **종량제**                 | [Microsoft Azure 스폰서쉽](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **지원 플랜** | 표준 지원                    | Default_2014-09-01 | MS-AZR-0041P |
| **지원 플랜** | Professional Direct 지원         | Default_2014-09-01 | MS-AZR-0042P |
| **지원 플랜** | 개발자 지원                   | Default_2014-09-01 | MS-AZR-0043P |
| **지원 플랜** | 독일 지원 플랜                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **지원 플랜** | Azure Government 표준 지원   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **지원 플랜** | Azure Government Pro-Direct 지원 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **지원 플랜** | Azure Government 개발자 지원  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>제안 유형 결정
구독 데이터가 표시되지 않고 구독이 지원되는 제품에 해당하는지 확인하려는 경우 구독이 지원되는지 확인할 수 있습니다. Azure 구독이 지원되는지 확인하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다. 그런 다음, 왼쪽 메뉴 창에서 **모든 서비스**를 선택합니다. 서비스 목록에서 **구독**을 선택합니다. 구독 목록 메뉴에서 확인할 구독을 클릭합니다. 구독이 개요 탭에 표시되며, **제품** 및 **제품 ID**를 확인할 수 있습니다. 다음 이미지에 예가 나와 있습니다.

![제품 및 제품 ID가 표시된 구독 개요 탭의 예](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Cost Management에 포함된 비용

다음 표에는 Cost Management에 포함된 데이터와 포함되지 않은 데이터가 나와 있습니다. 모든 비용은 송장이 생성 될 때까지 예상 됩니다. 표시 되는 비용은 무료 및 선불 크레딧을 포함 하지 않습니다.

**비용 및 사용량 데이터**

| **포함됨** | **포함되지 않음** |
| --- | --- |
| Azure 서비스 사용<sup>5</sup>        | 지원 요금 - 자세한 내용은 [청구서 용어 설명](../billing/billing-understand-your-invoice.md)을 참조하세요. |
| Marketplace 제공 사용<sup>6</sup> | 세금 - 자세한 내용은 [청구서 용어 설명](../billing/billing-understand-your-invoice.md)을 참조하세요. |
| Marketplace 구매<sup>6</sup>      | 크레딧 - 자세한 내용은 [청구서 용어 설명](../billing/billing-understand-your-invoice.md)을 참조하세요. |
| 예약 구매<sup>7</sup>      |  |
| 예약 구매<sup>7</sup> 의 상환      |  |

_<sup>**5**</sup> Azure 서비스 사용량은 예약 및 협상 된 가격을 기준으로 합니다._

_<sup>**6**</sup> Marketplace 구매는 이번에는 종 량 제, MSDN 및 Visual Studio 제품에 사용할 수 없습니다._

_<sup>**7**</sup> 이 시점에는 기업계약 (EA) 계정에 대해서만 예약 구매를 사용할 수 있습니다._

**Metadata**

| **포함됨** | **포함되지 않음** |
| --- | --- |
| 리소스 태그<sup>8</sup> | 리소스 그룹 태그 |

_<sup>**8**</sup> 개 리소스 태그는 각 서비스에서 사용이 내보내지고 사용 현황에 대 한 소급 사용할 수 없으므로 적용 됩니다._

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

[Cost Management api](index.yml)Power BI를 사용 하 여 데이터를 검색 하는 경우 또는 Azure Portal를 사용 하 여 데이터를 검색 하는 경우에는 송장이 닫힐 때까지 현재 청구 기간의 요금이 계산 되 고 결과적으로 변경 될 수 있습니다.

## <a name="cost-management-data-fields"></a>Cost Management 데이터 필드

다음 데이터 필드는 사용 현황 정보 파일 및 Cost Management Api에 있습니다. 다음 굵게 표시 된 필드의 경우 파트너는 비용 분석에서 필터 및 그룹화 기능을 사용 하 여 여러 필드를 기준으로 비용을 분석할 수 있습니다. 굵게 표시 된 필드는 파트너가 지 원하는 Microsoft 고객 계약에만 적용 됩니다.

| **필드 이름** | **설명** |
| --- | --- |
| invoiceId | 특정 트랜잭션에 대해 청구서에 표시 된 청구서 ID입니다. |
| previousInvoiceID | 원래 청구서에 대 한 참조에는 환불 (음의 비용)이 있습니다. 환불 된 경우에만 채워집니다. |
| billingAccountName | 파트너를 나타내는 청구 계정의 이름입니다. Microsoft 고객 계약에 등록 하 고 SaaS, Azure Marketplace, 예약 등의 권리를 구매한 고객에 대 한 모든 비용을 발생 시킬 수 있습니다. |
| billingAccountID | 파트너를 나타내는 청구 계정의 식별자입니다. |
| billingProfileID | 청구서에 대 한 비용을 청구 하는 청구 프로필에 대 한 식별자로, Microsoft 고객 계약을 등록 하 고 SaaS, Azure Marketplace 및와 같은 권한 부여를 구매한 고객에 게 서 예약. |
| billingProfileName | 청구서에 대 한 비용을 청구 하는 청구 프로필의 이름으로, Microsoft 고객 계약을 등록 하 고, SaaS, Azure Marketplace 및와 같은 권한 부여를 만든 CSP 고객과 예약. |
| invoiceSectionName | 청구서에서 청구 되는 프로젝트의 이름입니다. 파트너의 등록 Microsoft 고객 계약에는 적용 되지 않습니다. |
| invoiceSectionID | 청구서에서 청구 되는 프로젝트의 식별자입니다. 파트너의 등록 Microsoft 고객 계약에는 적용 되지 않습니다. |
| **CustomerTenantID** | 고객&#39;구독의 Azure Active Directory 테 넌 트 식별자입니다. |
| **CustomerName** | 고객&#39;구독에 대 한 Azure Active Directory 테 넌 트의 이름입니다. |
| **CustomerTenantDomainName** | 고객&#39;구독의 Azure Active Directory 테 넌 트에 대 한 도메인 이름입니다. |
| **대 여** | 파트너&#39;Azure Active Directory 테 넌 트의 식별자입니다. |
| **PartnerName** | 파트너 Azure Active Directory 테 넌 트의 이름입니다. |
| **ResellerMPNID** | 구독과 연결 된 대리점에 대 한 MPNID입니다. |
| costCenter | 구독과 관련 된 비용 센터입니다. |
| billingPeriodStartDate | 청구서에 표시 된 청구 기간 시작 날짜입니다. |
| billingPeriodEndDate | 청구서에 표시 된 청구 기간 종료 날짜입니다. |
| servicePeriodStartDate | 요금 등급이 청구 된 등급 기간의 시작 날짜입니다. Azure 서비스 가격은 평가 기간에 따라 결정 됩니다. |
| servicePeriodEndDate | 서비스 사용량이 요금으로 평가 된 기간의 종료 날짜입니다. Azure 서비스 가격은 평가 기간에 따라 결정 됩니다. |
| date | Azure 소비 데이터의 경우 등급이 지정 된 사용량의 날짜를 표시 합니다. 예약 된 인스턴스의 경우 구매한 날짜를 표시 합니다. 반복 요금 및 Marketplace 및 지원과 같은 일회성 요금은 구매 날짜를 표시 합니다. |
| productID | 소비 또는 구매 별 요금을 계산 하는 제품의 식별자입니다. 파트너 센터에 표시 된 것 처럼 productID 및 SKuID의 연결 된 키입니다. |
| product | 청구서에 표시 된 대로 소비 또는 구매 별 요금을 계산 하는 제품의 이름입니다. |
| serviceFamily | 구매한 제품의 서비스 제품군을 표시 합니다. 예를 들면 저장소 또는 계산입니다. |
| 제품 Orderid | 구독이 속한 자산의 식별자 또는 Azure 계획 이름입니다. 예: Azure 요금제. |
| productOrderName | 구독이 속한 Azure 계획의 이름입니다. 예: Azure 요금제. |
| consumedService | 레거시 EA 사용 세부 정보에 사용 되는 서비스 (레거시 분류)를 사용 합니다. |
| meterID | 측정 된 소비에 대 한 계량 된 식별자입니다. |
| meterName | 측정 된 사용량에 대 한 미터의 이름을 식별 합니다. |
| meterCategory | 사용에 대 한 최상위 서비스를 식별 합니다. |
| meterSubCategory | 요금에 영향을 줄 수 있는 Azure 서비스의 유형 또는 하위 범주를 정의 합니다. |
| meterRegion | 데이터 센터 위치에 따라 가격이 책정되는 특정 서비스에 대한 데이터 센터의 위치를 식별합니다. |
| 구독 ID | Azure 구독에 대해 고유한 Microsoft 생성 식별자입니다. |
| subscriptionName | Azure 구독의 이름입니다. |
| 용어 | 제품의 유효 기간을 표시합니다. 예를 들어 예약 된 인스턴스는 예약 된 인스턴스의 연간 기간 12 개월을 표시 합니다. 일회성 구매 또는 되풀이 구매의 경우 기간은 SaaS, Azure Marketplace 및 지원에 대해 한 달을 표시 합니다. Azure 소비에는 적용 되지 않습니다. |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | 자사, 타사 대리점 또는 타사 에이전시로 게시자를 식별 하는 게시자의 유형입니다. |
| partNumber | 사용 하지 않는 예약 된 인스턴스와 Azure Marketplace 서비스에 대 한 부품 번호입니다. |
| publisherName | Microsoft 또는 타사 게시자를 포함 하는 서비스의 게시자 이름입니다. |
| reservationId | 예약 인스턴스 구매의 식별자입니다. |
| reservationName | 예약 된 인스턴스의 이름입니다. |
| reservationOrderId | 예약 된 인스턴스의 OrderID입니다. |
| frequency | 예약 된 인스턴스의 지불 빈도입니다. |
| resourceGroup | 수명 주기 리소스 관리에 사용 되는 Azure 리소스 그룹의 이름입니다. |
| instanceID (또는) ResourceID | 리소스 인스턴스의 식별자입니다. |
| resourceLocation | 리소스 위치의 이름입니다. |
| 위치 | 리소스의 정규화 된 위치입니다. |
| effectivePrice | 가격 책정 통화 단위의 서비스의 유효 단가입니다. 제품, 서비스 제품군, 측정기 및 제안에 대해 고유 합니다. 청구 계정에 대 한 가격표의 가격 책정과 함께 사용 됩니다. 계층화 된 가격 책정 또는 포함 된 수량이 있으면 소비에 대 한 혼합 가격이 표시 됩니다. |
| 수량 | 구매 하거나 사용한 측정 된 수량입니다. 청구 기간 동안 사용 된 측정기의 양입니다. |
| unitOfMeasure | 서비스 요금이 청구되는 단위를 식별합니다. 예를 들면 GB와 시간입니다. |
| pricingCurrency | 단가를 정의 하는 통화입니다. |
| billingCurrency | 청구 된 비용을 정의 하는 통화입니다. |
| chargeType | 구매 및 환불과 같은 Azure Cost Management 비용을 나타내는 청구 유형을 정의 합니다. |
| costinBillingCurrency | 세금이 청구 된 통화에 대 한 ExtendedCost 또는 블렌드된 비용입니다. |
| costinPricingCurrency | 가격과의 상관 관계를 위해 가격 책정 통화의 세금에 대 한 ExtendedCost 또는 혼합 비용. |
| **costinUSD** | 세율의 세금 이전 예상 ExtendedCost 또는 혼합 비용입니다. |
| **paygCostInBillingCurrency** | 가격 책정이 소매 가격 인 경우 비용을 표시 합니다. 청구 통화로 종 량 제 가격을 표시 합니다. RBAC 범위 에서만 사용할 수 있습니다. |
| **paygCostInUSD** | 가격 책정이 소매 가격 인 경우 비용을 표시 합니다. USD로 종 량 제 가격을 표시 합니다. RBAC 범위 에서만 사용할 수 있습니다. |
| exchangeRate | 가격 책정 통화에서 청구 통화로 변환 하는 데 사용 되는 환율입니다. |
| exchangeRateDate | 가격 책정 통화에서 청구 통화로 변환&#39;하는 데 사용 되는 환율 날짜입니다. |
| isAzureCreditEligible | 비용이 Azure 크레딧을 통해 지불 대상이 될 수 있는지 여부를 나타냅니다. |
| serviceInfo1 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. |
| serviceInfo2 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다. |
| additionalInfo | 서비스 특정 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다. |
| tags | 미터에 할당 하는 태그입니다. 태그를 사용하여 청구 레코드를 그룹화합니다. 예를 들어 측정기를 사용하는 부서는 비용을 배분하는 데 태그를 사용할 수 있습니다. |
| **partnerEarnedCreditRate** | 파트너 관리자 링크 액세스를 기반으로 하는 파트너 획득 신용 (PEC)가 있는 경우 적용 되는 할인 률입니다. |
| **partnerEarnedCreditApplied** | 파트너의 획득 크레딧을 적용 했는지 여부를 나타냅니다. |


## <a name="usage-data-update-frequency-varies"></a>사용량 데이터 업데이트 빈도 변동

Cost Management에서 발생한 사용량 데이터의 사용 가능 여부는 다음과 같은 몇 가지 요인에 따라 달라집니다.

- Azure 서비스(예: 스토리지, 컴퓨팅, CDN 및 SQL)에서 사용량을 내보내는 빈도
- 평가 엔진 및 비용 관리 파이프라인을 통해 사용량 데이터를 처리하는 데 걸리는 시간

일부 서비스는 다른 서비스보다 더 빈번하게 사용량을 내보냅니다. 따라서 일부 서비스에 대한 데이터는 데이터를 내보내는 빈도가 더 낮은 다른 서비스보다 먼저 Cost Management에 표시될 수 있습니다. 일반적으로 서비스 사용량이 Cost Management에 표시되기까지 8-24시간이 걸립니다. 업데이트는 누적되므로 사용량이 증가함에 따라 진행 중인 월의 데이터가 새로 고쳐집니다.

## <a name="historical-data-might-not-match-invoice"></a>기록 데이터가 청구서와 일치 하지 않을 수 있습니다.

신용 기반 및 종 량 제 제품에 대 한 기록 데이터가 청구서와 일치 하지 않을 수 있습니다. 일부 Azure 종 량 제, MSDN 및 Visual Studio 제품에는 청구서에 적용 되는 Azure 크레딧 및 고급 지불이 있을 수 있습니다. 그러나 Cost Management에 표시 되는 기록 데이터는 예상 사용량 요금에 따라 결정 됩니다. Cost Management 기록 데이터는 지불액과 크레딧을 포함 하지 않습니다. 따라서 다음 제품에 대해 표시 된 기록 데이터가 청구서와 정확히 일치 하지 않을 수 있습니다.

- 학생용 Azure (MS MS-AZR-0017P-0170P)
- Azure in Open (MS MS-AZR-0017P-0111P)
- Azure Pass (MS-MS-AZR-0017P-0120P, MS-MS-AZR-0017P-0123P, MS-MS-AZR-0017P-0125P, MS-MS-AZR-0017P-0128P, MS-MS-AZR-0017P-0125P)
- 무료 평가판 (MS-MS-AZR-0017P-0044P)
- MSDN (MS-AZR-0017P-0062P)
- Visual Studio (MS MS-AZR-0017P-0029P, MS-AZR-0017P-0029P, ms-MS-AZR-0017P-0060P, ms-MS-AZR-0017P-0060P, MS-MS-AZR-0017P-0029P)

## <a name="see-also"></a>참고 항목

- Cost Management에 대한 첫 번째 빠른 시작을 아직 완료하지 않은 경우 [비용 분석 시작](quick-acm-cost-analysis.md)을 참조하세요.
