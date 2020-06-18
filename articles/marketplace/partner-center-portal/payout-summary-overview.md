---
title: 지급 요약 개요 - Azure Marketplace
description: 지급 요약에는 제품으로 창출한 수익에 대한 세부 정보가 표시됩니다. 또한 결제되는 금액과 시기도 알려줍니다.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: a872331238946de0d57e6d42164f1ce7fb1c7357
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83746201"
---
# <a name="payout-summary-overview"></a>지급 요약 개요

[지급 요약](./payout-summary.md)에는 Microsoft에서 창출한 수익에 대한 세부 정보가 표시됩니다. 또한 결제되는 금액과 시기도 알려줍니다.

Azure Marketplace에서 제품을 판매하는 경우 지급 요약에는 성공적인 지급에 대한 정보도 표시됩니다. Azure Marketplace 결제에 대한 자세한 내용은 [Azure Marketplace 참가 정책](https://docs.microsoft.com/legal/marketplace/participation-policy) 및 [Microsoft Azure Marketplace 게시자 계약](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)을 참조하세요.

> [!NOTE]
> 지급을 사용할 수 있으려면 수익이 $50의 [결제 임계값](./payment-thresholds-methods-timeframes.md)에 도달해야 합니다. 지불 임계값에 대한 자세한 내용은 [Microsoft Azure Marketplace 게시자 계약](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)을 참조하세요.

포털의 오른쪽 위 모서리에 있는 이 아이콘을 사용하여 액세스하는 파트너 센터의 분석 섹션에서 거래 게시 옵션의 모든 보고 및 인사이트를 사용할 수 있습니다.

![파트너 센터 포털의 오른쪽 위 모서리에 있는 지급 아이콘을 보여 줍니다.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>역할 및 권한

다음은 지급 보고서에 액세스할 수 있는 역할 및 사용 권한입니다.

| 보고서/페이지 | 계정 소유자 | Manager | Developer | 비즈니스 기여자 | 재무 기여자 | 마케터 |
| --- | --- | --- | --- | --- | --- | --- |
| 취득 보고서(근 실시간 데이터 포함) | 보기 가능 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 | 보기 가능 | 액세스 권한 없음 |
| 사용자 의견 보고서/응답 | 사용자 의견을 보고 보낼 수 있음 | 사용자 의견을 보고 보낼 수 있음 | 사용자 의견을 보고 보낼 수 있음 | 액세스 권한 없음 | 액세스 권한 없음 | 사용자 의견을 보고 보낼 수 있음 |
| --- | --- | --- | --- | --- | --- | --- |
| 상태 보고서(근 실시간 데이터 포함) | 보기 가능 | 보기 가능 | 보기 가능 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 |
| 사용량 보고서 | 보기 가능 | 보기 가능 | 보기 가능 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 |
| 지급 계정 | 업데이트할 수 있음 | 액세스 권한 없음 | 액세스 권한 없음 | 액세스 권한 없음 | 업데이트할 수 있음 | 액세스 권한 없음 |
| 세금 프로필 | 업데이트할 수 있음 | 액세스 권한 없음 | 액세스 권한 없음 | 액세스 권한 없음 | 업데이트할 수 있음 | 액세스 권한 없음 |
| 지급 요약 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 | 액세스 권한 없음 | 보기 가능 | 액세스 권한 없음  |
| | | | | | | |

## <a name="payout-report-differences"></a>지급 보고서 차이점

Cloud 파트너 포털(이전)와 파트너 센터(신규)의 지급 보고서는 다릅니다.

| 클라우드 파트너 포털 | 파트너 센터 |
| --- | --- |
| **링크**: https://cloudpartner.azure.com/ | **링크**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 및 https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation**: 인사이트 지급에 제공되는 지급 보고 | **Navigation**: 파트너 센터에 제공되는 지급 보고 - 지급 아이콘 |
| **범위**:<ul><li>진행 중, 수집 및 유료 컬렉션에 대한 품목별 거래가 표시됩니다.</li><li>보고 – 수집 진행 중 및 청구 진행 중인 품목을 포함하여 구매 주문이 생성된 후의 모든 품목과 아직 유료로 사용할 수 없는 품목 및 수집 상태를 표시합니다.</li></ul> | **범위**:<ul><li>적격 소득으로 간주된 이후의 품목을 표시합니다.</li><li>고객이 먼저 Microsoft에 요금을 지불하면 ISV는 지급 보고서가 시작되는 것을 볼 수 있습니다.</li><li>지급 보고서는 진행 중인 수집과 진행 중인 청구를 표시하지 않습니다.</li></ul> |
| **거래에 대한 지급이 준비되지 않음**: 청구 진행 중 | **거래에 대한 지급이 준비되지 않음**: 다음 예상 지급액: 지급 상태가 처리 안 됨입니다. |
| **지급 상태**: 해당 없음 | **지급 상태**:<ul><li>처리 안 됨: 지급해야 하는 수익입니다.</li><li>예정: 다음 월별 지급 시 게시자에게 전송되는 수익입니다.</li><li>송금: 결제 금액이 거래 은행으로 송금되었습니다.</li></ul> |
| | |

## <a name="payment-schedules"></a>지불 일정

보류 기간, 파트너 가시성, 고객이 신용 카드나 송장을 사용하는 경우를 포함하는 지불 일정에 대한 설명은 **지급 세부 정보** 항목의 [결제 일정](./payout-policy-details.md#payment-schedules) 섹션을 참조하세요.

## <a name="transaction-history-download-export"></a>거래 기록 다운로드 내보내기

이 옵션은 거래 기록 페이지에 표시되는 각 품목의 다운로드를 제공합니다. 여기에는 성과급 프로그램과 관련된 수익 유형, 날짜, 연결된 거래 금액, 고객, 제품 및 기타 거래 세부 정보가 포함됩니다.

| 열 이름 | Description |
| --- | --- |
| earningId | 각 수익의 고유 식별자 |
| participantId | 프로그램에 따라 수익을 창출하는 파트너의 기본 ID |
| participantIdType | 프로그램이 Store 프로그램/Azure Marketplace에 해당되는 경우 인센티브 프로그램에 대한 프로그램 ID |
| participantName | 수익 파트너의 이름 |
| partnerCountryCode | 수익 파트너의 위치/국가/지역 |
| programName | 인센티브/Store 프로그램 이름 |
| transactionId | 거래에 대한 고유 식별자 |
| transactionCurrency | 원래 고객 거래가 발생한 지역의 통화(파트너 위치의 통화가 아님) |
| TransactionDate | 거래 날짜. 많은 거래가 하나의 수익에 기여하는 프로그램에 유용합니다. |
| transactionExchangeRate | 해당 거래 USD 금액을 표시하는 데 사용되는 환율 |
| transactionAmount | 생성된 수익에 기반한 거래 금액(원래 거래 통화) |
| transactionAmountUSD | 미국 달러(USD)로 나타낸 거래 금액 |
| lever | 수익에 대한 비즈니스 규칙 |
| earningRate | 수익을 생성하기 위해 거래 금액에 적용되는 인센티브율 |
| quantity | 거래 프로그램에 청구된 수량. 프로그램에 따라 다릅니다. |
| quantityType | 수량 유형입니다. 예를 들면 다음과 같습니다. 청구 수량, MAU(월간 활성 사용자) |
| earningType | 수수료, 환불, 협업, 판매 등인지를 나타냅니다. |
| earningAmount | 원래 거래 통화의 수익 금액 |
| earningAmountUSD | 수익 금액(USD) |
| earningDate | 수익 창출 날짜 |
| calculationDate | 시스템에서 수익을 계산한 날짜 |
| earningExchangeRate | 해당 USD 금액을 표시하는 데 사용되는 환율 |
| exchangeRateDate | EarningAmount USD를 계산하는 데 사용되는 환율 날짜 |
| paymentAmountWOTax | &quot;송금&quot; 지급에 대한 수익 금액(결제 통화)(세금 제외) |
| paymentCurrency | 결제 프로필에서 파트너가 선택한 통화. 송금 지급에 대해서만 표시됩니다. |
| paymentExchangeRate | ExchangeRateDate를 사용하여 paymentAmountWOTax를 결제 통화로 계산하는 데 사용되는 환율 |
| paymentId | 결제에 대한 고유 식별자. 이 번호는 은행 거래 내역서에 표시됩니다. |
| paymentStatus | 결제 상태 |
| paymentStatusDescription | 지불 상태에 대한 설명 |
| customerId | 항상 비어 있음 |
| customerName | 항상 비어 있음 |
| partNumber | 항상 비어 있음 |
| productName | 거래에 연결된 제품 이름 |
| productId | 고유 제품 식별자 |
| parentProductId | 고유한 부모 제품 식별자. 거래에 대한 부모 제품이 없는 경우 부모 제품 ID는 제품 ID입니다. |
| parentProductName | 부모 제품의 이름. 거래에 대한 부모 제품이 없는 경우 부모 제품 이름은 제품 이름입니다. |
| productType | 제품의 유형(예: 앱, 추가 기능 또는 게임) |
| invoiceNumber | 청구서 번호(기업계약에만 해당) |
| ResellerID | 재판매인 식별자 |
| ResellerName | 재판매인 이름 |
| TransactionType | 거래의 유형(예: 구매, 환불, 환입 및 지급 거절) |
| localProviderSeller | 레코드의 현지 공급 기업/판매자 |
| taxRemitted | 세금 납부 금액(판매세, 사용세 또는 VAT/GST 세금) |
| taxRemitModel | 세금(판매세, 사용세 또는 VAT/GST 세금)을 납부할 책임이 있는 당사자 |
| storeFee | 상업용 Marketplace에서 앱 또는 추가 기능을 사용할 수 있도록 하기 위한 수수료로 Microsoft에서 보유하는 금액 |
| transactionPaymentMethod | 카드, 이동 통신 회사 청구 및 PayPal과 같은 거래에 사용되는 고객 결제 수단 |
| tpan | 타사 AD 네트워크 |
| customerCountry | 고객 국가/지역 |
| customerCity | 고객 구/군/시 |
| customerState | 고객 시/도 |
| customerZip | 고객 우편 번호 |
| TenantID | 테넌트의 ID |
| externalReferenceId | 프로그램에 대한 고유 식별자 |
| externalReferenceIdLabel | 고유 식별자 레이블 |
| transactionCountryCode | 거래가 발생한 국가/지역 코드 |
| taxCountry | 고객 국가/지역 |
| taxState | 고객 상태 |
| taxCity | 고객 구/군/시 |
| taxZipCode | 고객 우편 번호 |
| LicensingProgramName | 라이선스 프로그램의 이름 |
| 프로그램 코드 | 프로그램 이름으로 매핑할 문자열 |
| earningAmountInLastPaymentCurrency | 마지막 지불 통화의 수익금(이전 지불액을 지불하지 않은 경우 필드는 비어 있음) |
| lastPaymentCurrency | 마지막 지불 통화(이전 지불액을 지불하지 않은 경우 필드는 비어 있음) |
| AssetId | Marketplace 서비스의 고객 주문에 대한 고유 식별자. 구매 품목을 나타냅니다. 여러 자산이 있을 수 있습니다. |
| OrderID | 고객의 청구서와 관련이 있음 |
| LineItemId | 고객 청구서의 개별 품목 |
| 고객 국가/지역 | 고객이 입력한 국가/지역 이름. 고객의 Azure 구독에서 국가/지역과 다를 수 있습니다. |
| 고객 메일 주소 | 고객이 입력한 메일 주소. 고객의 Azure 구독에서 메일 주소와 다를 수 있습니다. |
| SkuId | 게시 중에 정의한 SKU ID. 한 제품에 여러 SKU가 포함될 수는 있지만 각 SKU는 제품 하나에만 연결할 수 있습니다. |

> [!NOTE]
> 거래 게시 옵션에 대한 모든 보고 및 인사이트는 파트너 센터의 분석 섹션에서 찾을 수 있습니다.

## <a name="billing-questions-and-support"></a>청구 관련 질문 및 지원
청구 지원에 대해서는 상업용 Marketplace [게시자 지원 서비스](https://partner.microsoft.com/support/v2/?stage=1)에 문의하세요.

## <a name="next-step"></a>다음 단계

- [지급 요약](./payout-summary.md)
