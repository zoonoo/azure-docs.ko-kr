---
title: 지급 요약 개요-Azure Marketplace
description: 지급 요약은 제품을 사용 하 여 달성 한 비용에 대 한 세부 정보를 보여줍니다. 또한 지불을 받는 시기와 유료 정도를 알 수 있습니다.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 48644f2f8148a7aa1974be0d7f761e9b3a55612d
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783514"
---
# <a name="payout-summary-overview"></a>지급 요약 개요

[지급 요약](./payout-summary.md) 은 Microsoft에서 달성 한 비용에 대 한 세부 정보를 보여줍니다. 또한 지불을 받는 시기와 유료 정도를 알 수 있습니다.

Azure Marketplace에서 제품을 판매 하는 경우 지급 요약에 성공 지급 대 한 정보도 표시 됩니다. Azure Marketplace 결제에 대 한 자세한 내용은 [Azure Marketplace 참여 정책](https://docs.microsoft.com/legal/marketplace/participation-policy) 및 [Microsoft Azure Marketplace 게시자 계약](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)을 참조 하세요.

> [!NOTE]
> 지급에 적합 하려면 진행이 $50의 [지불 임계값](./payment-thresholds-methods-timeframes.md) 에 도달 해야 합니다. 지불 임계값에 대 한 자세한 내용은 [Microsoft Azure Marketplace 게시자 계약](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3ypvt)을 참조 하세요.

포털의 오른쪽 위 모퉁이에 있는이 아이콘을 사용 하 여 액세스 하는 파트너 센터의 분석 섹션에서 모든 transact-sql 게시 옵션에 대 한 보고 및 정보를 사용할 수 있습니다.

![파트너 센터 포털의 오른쪽 위 모퉁이에 있는 지급 아이콘을 보여 줍니다.](./media/payout-overview.png)

## <a name="roles-and-permissions"></a>역할 및 권한

다음은 지급 보고서에 액세스할 수 있는 역할 및 사용 권한입니다.

| 보고서/페이지 | 계정 소유자 | Manager | 개발자 | 비즈니스 참여자 | 재무 참여자 | 마케터 |
| --- | --- | --- | --- | --- | --- | --- |
| 취득 보고서 (거의 실시간 데이터 포함) | 보기 가능 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 | 보기 가능 | 액세스 권한 없음 |
| 사용자 의견 보고서/응답 | 사용자 의견을 보고 보낼 수 있습니다. | 사용자 의견을 보고 보낼 수 있습니다. | 사용자 의견을 보고 보낼 수 있습니다. | 액세스 권한 없음 | 액세스 권한 없음 | 사용자 의견을 보고 보낼 수 있습니다. |
| --- | --- | --- | --- | --- | --- | --- |
| 상태 보고서 (거의 실시간 데이터 포함) | 보기 가능 | 보기 가능 | 보기 가능 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 |
| 사용 보고서 | 보기 가능 | 보기 가능 | 보기 가능 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 |
| 지급 계정 | 업데이트할 수 있음 | 액세스 권한 없음 | 액세스 권한 없음 | 액세스 권한 없음 | 업데이트할 수 있음 | 액세스 권한 없음 |
| 세금 프로필 | 업데이트할 수 있음 | 액세스 권한 없음 | 액세스 권한 없음 | 액세스 권한 없음 | 업데이트할 수 있음 | 액세스 권한 없음 |
| 지급 요약 | 보기 가능 | 액세스 권한 없음 | 액세스 권한 없음 | 액세스 권한 없음 | 보기 가능 | 액세스 권한 없음  |
| | | | | | | |

## <a name="payout-report-differences"></a>지급 보고서 차이점

지급 보고서는 Cloud 파트너 포털 (이전)와 파트너 센터 (신규) 사이에서 차이가 있습니다.

| 클라우드 파트너 포털 | 파트너 센터 |
| --- | --- |
| **링크**:https://cloudpartner.azure.com/ | **링크**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 및https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **탐색**: Insights 지급에 제공 된 지급 보고 | **탐색**: 파트너 센터에 제공 된 지급 보고-지급 아이콘 |
| **범위**:<ul><li>진행 중인 컬렉션, 수집 및 유료에 대 한 줄 당 트랜잭션 항목이 표시 됩니다.</li><li>보고 – 진행 중인 컬렉션과 대금 청구 및 아직 유료으로 사용할 수 없는 수집 상태와 품목을 포함 하 여 구매 주문이 만들어진 후 모든 품목을 표시 합니다.</li></ul> | **범위**:<ul><li>적격 소득으로 간주 된 후의 품목을 표시 합니다.</li><li>고객은 먼저 Microsoft에 요금을 지불 하 고 Isv는 지급 보고서를 시작 하는 것을 볼 수 있습니다.</li><li>지급 보고서는 진행 중인 컬렉션과 청구 중인 컬렉션을 표시 하지 않습니다.</li></ul> |
| **지급에 대 한 트랜잭션이 준비 되지 않음**: 청구 진행 중 | **지급에 대 한 트랜잭션이 준비 되지 않음**: 다음 예상 지불: 지급 상태가 처리 되지 않음 상태입니다. |
| **지급 상태**: 해당 없음 | **지급 상태**:<ul><li>처리 되지 않음: 획득은 지불에 적합 합니다.</li><li>예정: 다음 월간 지급 게시자에 게 획득 됩니다.</li><li>보낸 사람: 결제 은행으로 전송 되었습니다.</li></ul> |
| | |

## <a name="payment-schedules"></a>지불 일정

기간, 파트너 가시성, 고객이 신용 카드나 송장을 사용 하는 경우 등 결제 일정에 대 한 자세한 내용은 **지급 details** 항목의 [결제 일정](./payout-policy-details.md#payment-schedules) 섹션을 참조 하세요.

## <a name="transaction-history-download-export"></a>트랜잭션 기록 다운로드 내보내기

이 옵션은 트랜잭션 기록 페이지에 표시 되는 각 줄 항목의 다운로드를 제공 합니다. 여기에는 성과급 프로그램에 관련 된 유형, 날짜, 연결 된 트랜잭션 금액, 고객, 제품 및 기타 트랜잭션 세부 정보가 포함 됩니다.

| 열 이름 | 설명 |
| --- | --- |
| earningId | 각 획득에 대 한 고유 식별자 |
| participantId | 파트너의 주 id는 프로그램에서 획득 합니다. |
| participantIdType | 프로그램이 스토어 프로그램 및 Azure Marketplace 경우에는 동기 프로그램 및 판매자의 프로그램 ID |
| participantName | 획득 파트너의 이름입니다. |
| partnerCountryCode | 획득 파트너의 위치/국가 |
| programName | 동기/스토어 프로그램 이름 |
| transactionId | 트랜잭션의 고유 식별자 |
| transactionCurrency | 원래 고객 거래가 발생 한 통화 (파트너 위치 통화가 아님) |
| transactionDate | 트랜잭션의 날짜입니다. 많은 트랜잭션이 하나의 획득에 기여 하는 프로그램에 유용 합니다. |
| transactionExchangeRate | 해당 하는 트랜잭션 USD 금액을 표시 하는 데 사용 되는 환율 |
| transactionAmount | 획득 된 획득을 기준으로 원래 트랜잭션 통화의 트랜잭션 양 |
| transactionAmountUSD | 미국 달러 (USD)의 트랜잭션 금액 |
| 레버 | 획득에 대 한 비즈니스 규칙 |
| earningRate | 획득을 생성 하기 위해 트랜잭션 양에 적용 되는 동기 율 |
| quantity | 트랜잭션 프로그램의 청구 수량입니다. 프로그램에 따라 다릅니다. |
| quantityType | 수량 유형 (예: 청구 된 수량, MAU (월간 활성 사용자)) |
| earningType | 요금, 리베이트, coop, 판매 등 인지를 나타냅니다. |
| earningAmount | 원래 트랜잭션 통화 금액을 획득 합니다. |
| earningAmountUSD | USD 단위로 금액 획득 |
| earningDate | 획득 날짜 |
| calculationDate | 시스템이 시스템에서 계산 된 날짜 |
| earningExchangeRate | 해당 USD amount를 표시 하는 데 사용 되는 환율 |
| exchangeRateDate | EarningAmount USD를 계산 하는 데 사용 되는 환율 날짜 |
| paymentAmountWOTax | 전송&quot; 상환액에 대 한 지불 금액에서 금액 (세금 &quot;제외)을 획득 합니다. |
| paymentCurrency | 지불 프로필에서 파트너가 선택한 통화로 요금을 지불 합니다. 전송 된 지불에 대해서만 표시 됩니다. |
| paymentExchangeRate | ExchangeRateDate를 사용 하 여 지불 통화로 paymentAmountWOTax 계산 하는 데 사용 되는 환율 |
| paymentId | 지불의 고유 식별자입니다. 이 번호는 은행 명세서에서 볼 수 있습니다. |
| paymentStatus | 지불 상태 |
| paymentStatusDescription | 지불 상태 설명 |
| customerId | 항상 비어 있습니다. |
| customerName | 항상 비어 있습니다. |
| partNumber | 항상 비어 있습니다. |
| 제품 | 트랜잭션에 연결 된 제품 이름 |
| productId | 고유 제품 식별자 |
| parentProductId | 고유한 부모 제품 식별자입니다. 트랜잭션에 대 한 부모 제품이 없으면 부모 제품 ID = 제품 ID입니다. |
| parentProductName | 부모 제품의 이름입니다. 트랜잭션에 대 한 부모 제품이 없으면 부모 제품 이름 = 제품 이름입니다. |
| productType | 제품 유형 (예: 앱, 추가 기능 및 게임) |
| invoiceNumber | 송장 번호 (기업 계약에만 해당) |
| resellerId | 대리점 식별자 |
| resellerName | 재판매인 이름 |
| transactionType | 트랜잭션 유형 (예: 구매, 환불, 반전 및 비용 정산) |
| localProviderSeller | 로컬 공급자/판매자 레코드 |
| taxRemitted | 세금 송금 (판매, 사용 또는 VAT/GST 세금). |
| taxRemitModel | Remitting 세금 (판매, 사용 또는 VAT/GST 세금)을 담당 하는 파티. |
| 인 경우 요금 | 상업적 marketplace에서 앱 또는 추가 기능을 사용할 수 있도록 하기 위한 요금으로 Microsoft에서 보유 하 고 있는 금액입니다. |
| transactionPaymentMethod | 트랜잭션에 사용 되는 고객 지불 방법 (예: 카드, 모바일 운송 업체 청구 및 PayPal) |
| tpan | 타사 ad 네트워크 |
| customerCountry | 고객 국가 |
| customerCity | 고객 도시 |
| customerState | 고객 상태 |
| customerZip | 고객 우편 번호 |
| TenantID | 테 넌 트의 ID입니다. |
| externalReferenceId | 프로그램에 대 한 고유 식별자 |
| externalReferenceIdLabel | 고유 식별자 레이블 |
| transactionCountryCode | 트랜잭션이 발생 한 국가 코드 |
| taxCountry | 고객의 국가 |
| taxState | 고객 상태 |
| taxCity | 고객의 구/군/시 |
| taxZipCode | 고객의 우편 번호 |
| LicensingProgramName | 라이선스 프로그램의 이름입니다. |
| 프로그램 코드 | 프로그램 이름으로 매핑할 문자열 |
| earningAmountInLastPaymentCurrency | 마지막 지불 통화 금액을 획득 합니다 (이전 지불액을 지불 하지 않은 경우 필드는 비어 있음). |
| lastPaymentCurrency | 마지막 지불 통화 (지불 되지 않은 경우 필드는 비어 있음) |
| AssetId | Marketplace 서비스의 고객 주문에 대 한 고유 식별자입니다. 이는 구매 라인 항목을 나타냅니다. 여러 자산이 있을 수 있습니다. |
| OrderId | 고객의 청구서와 관련이 있습니다. |
| LineItemId | 고객 송장의 개별 줄 |
| 고객 국가 | 고객이 제공한 국가 이름입니다. 이는 고객의 Azure 구독에 있는 국가와 다를 수 있습니다. |
| 고객 EmailAddress | 고객이 제공한 전자 메일 주소입니다. 이는 고객의 Azure 구독에 있는 전자 메일 주소와 다를 수 있습니다. |
| SkuId | 게시 하는 동안 정의 된 SKU ID입니다. 한 제품에 여러 SKU가 포함될 수는 있지만 각 SKU는 제품 하나에만 연결할 수 있습니다. |

> [!NOTE]
> Transact-sql 게시 옵션에 대 한 모든 보고 및 정보는 파트너 센터의 분석 섹션에서 찾을 수 있습니다.

## <a name="billing-questions-and-support"></a>청구 관련 질문 및 지원
청구 지원을 위해 상용 marketplace [게시자 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

## <a name="next-step"></a>다음 단계

- [지급 요약](./payout-summary.md)
