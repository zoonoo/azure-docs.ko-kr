---
title: 상업적 marketplace 지급 요약 | Azure Marketplace
description: 지급 요약은 제품을 사용 하 여 달성 한 비용에 대 한 세부 정보를 보여줍니다. 또한, 지급 요약을 통해 언제 지급받을 수 있는지와 지급받는 금액도 알 수 있습니다.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 3836e815d296456ff95f87f0592e3277503420fa
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427154"
---
# <a name="payout-reporting"></a>지급 보고

[**지급 요약**](https://docs.microsoft.com/windows/uwp/publish/payout-summary) 은 Microsoft에서 달성 한 비용에 대 한 세부 정보를 보여줍니다. 또한, 지급 요약을 통해 언제 지급받을 수 있는지와 지급받는 금액도 알 수 있습니다.

Azure Marketplace에서 제품을 판매 하는 경우 **지급 요약**에 성공 지급 대 한 정보도 표시 됩니다. Azure Marketplace 결제에 대 한 자세한 내용은 [Microsoft Azure Marketplace 참여 정책](https://go.microsoft.com/fwlink/p/?LinkId=722436) 및 [Microsoft Azure Marketplace 게시자 계약](https://go.microsoft.com/fwlink/p/?LinkID=699560)을 참조 하세요.

> [!NOTE]
> 지급에 적합 하려면 진행이 $50의 [지불 임계값](payment-thresholds-methods-timeframes.md) 에 도달 해야 합니다. 지불 임계값에 대 한 자세한 내용은이 페이지를 참조 하 고 [Microsoft Azure Marketplace 게시자 규약](https://go.microsoft.com/fwlink/p/?LinkID=699560)을 검토 하십시오.

- [지급 보고서에 액세스할 수 있는 역할 및 사용 권한](#roles-and-permission-to-access-the-payout-report)
- [지급 보고서: Cloud 파트너 포털와 파트너 센터 간의 차이점](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [고객 유형](#customer-types)
- [지급와 usage 간의 Corelation](#corelation-between-payout-and-usage)
- [트랜잭션 기록 다운로드](#transaction-history-download-export)
- [청구 질문 및 지원](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>지급 보고서에 액세스할 수 있는 역할 및 사용 권한

| 보고서/페이지    | 계정 소유자    | Manager  | 개발자 | 비즈니스 참여자 |  재무 참여자 | 마케팅 담당자 |
|------------------|------------------|----------|-----------|----|----|-----|
| 취득 보고서 (거의 실시간 데이터 포함) | 볼 수 있음 | 볼 수 있음 | 권한 없음 | 권한 없음 | 볼 수 있음 | 권한 없음 |
| 피드백 보고서/응답 | 볼 수 있고 피드백 보낼 수 있음 | 볼 수 있고 피드백 보낼 수 있음 | 볼 수 있고 피드백 보낼 수 있음 | 권한 없음 | 권한 없음 | 볼 수 있고 피드백 보낼 수 있음 |
| 상태 보고서 (거의 실시간 데이터 포함) | 볼 수 있음 | 볼 수 있음 | 볼 수 있음 | 볼 수 있음 | 권한 없음 | 권한 없음 |
| 사용 보고서 | 볼 수 있음 | 볼 수 있음 | 볼 수 있음 | 볼 수 있음 | 권한 없음 | 권한 없음 |
| 지급 계좌 | 업데이트할 수 있음 | 권한 없음 | 권한 없음 | 권한 없음 | 업데이트할 수 있음 | 권한 없음 |
| 세금 프로필 | 업데이트할 수 있음 | 권한 없음 | 권한 없음 | 권한 없음 | 업데이트할 수 있음 | 권한 없음 |
| 지급 요약 | 볼 수 있음 | 권한 없음 | 권한 없음 | 권한 없음 | 볼 수 있음 | 권한 없음 |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>지급 보고서: Cloud 파트너 포털와 파트너 센터 간의 차이점

| | 클라우드 파트너 포털 | 파트너 센터 |
|---------|---------|---------|
| 링크 | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory 및 https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| 탐색 | Insights 지급에 제공 된 지급 보고 | 파트너 센터에서 제공 하는 지급 보고-지급 아이콘 |
| 범위 | <ul> <li>진행 중인 컬렉션, 수집 된 컬렉션 및 유료 항목에 대 한 줄 당 트랜잭션 항목 표시 </li> <li>보고 – 진행 중인 컬렉션과 대금 청구 및 아직 유료으로 사용할 수 없는 수집 상태와 품목을 포함 하 여 구매 주문이 만들어진 후 모든 품목을 표시 합니다. </li> </ul> | <ul> <li>적격 소득으로 간주 되는 경우 품목을 표시 합니다.</li> <li>고객은 먼저 Microsoft에 요금을 지불 하 고 Isv는 지급 보고서를 시작 하는 것을 볼 수 있습니다.</li> <li>지급 보고서는 진행 중인 컬렉션과 청구 중인 컬렉션은 표시 하지 않습니다.  </li> </ul>  |
| 지급 준비가 되지 않은 트랜잭션 | 청구 진행 중 | 다음 예상 결제: 지급 상태가 처리 되지 않음 상태입니다.  |
| 지급 상태 |  | 일별 <br> 이러한 획득은 지불에 적합 합니다. 이 상태는 동기 프로그램의 프로그램 가이드에 정의 된 대로 냉각 기간 동안 유지 됩니다. <br> <br> 그 <br> 지불 순서-지불을 처리 하기 전에 보류 중인 내부 검토를 생성 했습니다. <br> <br> 보냄: <br> 요금을 은행으로 보냈습니다. |

## <a name="customer-types"></a>고객 유형 

### <a name="enterprise-agreement"></a>기업 계약

기업 계약을 사용하지 않는 고객은 Marketplace 소프트웨어 라이선스에 대해 월별로 청구됩니다. 기업 계약을 사용하는 고객은 분기별로 표시된 청구서를 통해 월별로 청구됩니다.

### <a name="credit-cards-and-monthly-invoice"></a>신용 카드 및 월별 청구서

고객은 신용 카드 및 월별 청구서를 사용하여 지불할 수도 있습니다. 이 경우 소프트웨어 라이선스 요금은 매월 청구 됩니다.

### <a name="csp-and-direct-pay-users"></a>CSP 및 직접 종 량 제 사용자

예를 들어 고객이 신용 카드를 사용 하 여 구매 하는 경우

## <a name="corelation-between-payout-and-usage"></a>지급와 usage 간의 Corelation 

|Description    |    날짜  | 주문/사용  | 지급 |
|----------|----------|-----------|-------------|
|주문 기간   | 8 월 15 일, 2019-8 월 30 일, 2019 | **상관 관계 특성 주문** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **사용 현황** <br> <ul> <li>CustomerId </li> <li>고객 이름</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> 예상 연장 요금 <br> 예상 지급액(PC) </li> </ul> |  |
|기간 종료일(월)   | 8 월 30 일, 2019 | | |
|청구 날짜 | 2019 년 9 월 1 일 | | |
|고객 지불 날짜 | 2019 년 9 월 1 일 | | |
|에스크로 기간(신용 카드만 해당, 30일) | 2019 년 9 월 1 일-9 월 30 2019 일 | | **상관 관계 특성 주문:** <br> <ul><li>AssetId</li> <li>고객 ID</li> <li> 고객 이름</li> </ul> <br> **사용 현황** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 일별 |
|수금 기간 시작일 | 2019 년 9 월 1 일 | | |
|수금 기간 종료일(최대 30일) | 2019 년 9 월 30 일 | | |
|지불 계산 날짜(매월 15일) | 2019 년 10 월 1 일 | | **상관 관계 특성** <br> <ul><li>AssetId</li> <li>고객 ID</li> <li>고객 이름</li> </ul> <br> **사용 현황** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 그 |
|지급 날짜 | 2019 년 10 월 15 일 | | **상관 관계 특성** <br> <ul><li>AssetId</li> <li>고객 ID</li> <li> 고객 이름</li> </ul> <br> **사용 현황** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 지불 전송 |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>기업 계약 (분기별/월별 고객)

| Description |    날짜  | 사용량 | 지급 |
|----------|----------|---------|-----------|
|주문 기간 | 8 월 15 일, 2019-8 월 30 일, 2019 | **상관 관계 특성 주문** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **사용 보고서** <br> <ul> <li>CustomerId </li> <li>고객 이름</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> 예상 연장 요금 <br> 예상 지급액(PC) </li> </ul> | |
|기간 종료일(분기) | 2019 년 9 월 30 일 | | |
|청구 날짜 | 2019 년 10 월 15 일 | | |
|에스크로 기간(신용 카드만 해당, 30일) | n/a | | |
|수금 기간 시작일 | 2019 년 10 월 15 일 | | |
|신용 카드만 30 일 | 2019 년 11 월 1 일-2019 년 11 월 30 일 | | |
|수금 기간 종료일(최대 90일) | 2020 년 1 월 15 일 | | |
|고객 지불 날짜 | 2019 년 12 월 30 일 | | |
|지급 계산 | 2020 년 1 월 15 일 | | |
|지급 날짜 | 2 월 15 일 2020 | | **분기별 기반 고객의 경우** <br> <br> **주문 보고서** <br> <ul><li>AssetId</li> <li>고객 ID</li> <li> 고객 이름</li> </ul> <br> **사용 현황** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>earningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 보냄 |

## <a name="transaction-history-download-export"></a>트랜잭션 기록 다운로드 내보내기

이 옵션은 트랜잭션 기록 페이지에 표시 되는 각 줄 항목의 다운로드를 제공 하 고, 형식, 날짜, 연결 된 트랜잭션 금액, 고객, 제품 및 성과급 프로그램에 적용할 수 있는 기타 트랜잭션 정보를 제공 합니다. 

| 열 이름     | Description    | 
|-------------|-------------------------------|
| earningId                      | 각 획득에 대 한 고유 식별자                                                                                                       |
| participantId                  | 파트너의 주 id는 프로그램에서 획득 합니다.                                                                            | 
| participantIdType              | 매장 프로그램 및 Azure Marketplace에 대 한 경우 일반적으로 동기 프로그램 및 판매자의 프로그램 ID                                          | 
| participantName                | 획득 파트너의 이름입니다.                                                                                                              | 
| partnerCountryCode             | 획득 파트너의 위치/국가                                                                                                  |
| programName                    | 동기/스토어 프로그램 이름                                                                                                             | 
| transactionId                  | 트랜잭션의 고유 식별자                                                                                                    | 
| transactionCurrency            | 원래 고객 거래가 발생 한 통화 (파트너 위치 통화가 아님)                                     | 
| transactionDate                | 트랜잭션의 날짜입니다. 많은 트랜잭션이 하나의 획득에 기여 하는 프로그램에 유용 합니다.                                           | 
| transactionExchangeRate        | 해당 하는 트랜잭션 USD 금액을 표시 하는 데 사용 되는 환율                                                                 | 
| transactionAmount              | 획득 된 획득을 기준으로 원래 트랜잭션 통화의 트랜잭션 양                                              | 
| transactionAmountUSD           | USD의 트랜잭션 양                                                                                                                | 
| 레버                          | 획득에 대 한 비즈니스 규칙을 나타냅니다.                                                                                                  | 
| earningRate                    | 획득을 생성 하기 위해 트랜잭션 양에 적용 되는 동기 율                                                                      | 
| quantity                       | 프로그램에 따라 다릅니다. 트랜잭션 프로그램의 청구 수량을 나타냅니다.                                                            | 
| quantityType                   | 수량 유형을 나타냅니다 (예: 청구 수량, MAU).                                                                                     |
| earningType                    | 요금, 리베이트, coop, 판매 등 인지를 나타냅니다.                                                                                          | 
| earningAmount                  | 원래 트랜잭션 통화 금액을 획득 합니다.                                                                                      |
| earningAmountUSD               | USD 단위로 금액 획득                                                                                                                    |
| earningDate                    | 획득 날짜                                                                                                                      |
| calculationDate                | 시스템이 시스템에서 계산 된 날짜                                                                                            |
| earningExchangeRate            | 해당 USD amount를 표시 하는 데 사용 되는 환율                                                                                  |
| exchangeRateDate               | EarningAmount USD를 계산 하는 데 사용 되는 환율 날짜                                                                                   | 
| paymentAmountWOTax             | "보낸 사람" 지불에 대해서만 요금을 지불 하는 금액 (세금 제외)                                                                 |
| paymentCurrency                | 지불 프로필에서 파트너가 선택한 통화로 요금을 지불 합니다. 전송 된 지불에 대해서만 표시 됩니다.                                                   |
| paymentExchangeRate            | ExchangeRateDate를 사용 하 여 지불 통화로 paymentAmountWOTax 계산 하는 데 사용 되는 환율                                            |
| paymentId            | 지불의 고유 식별자입니다. 이 번호는 은행 명세서에서 볼 수 있습니다.                                            |
| paymentStatus            | 지급 상태                                            |
| paymentStatusDescription            | 지불 상태 설명                                            |
| customerId                     | 항상 비어 있습니다.                                                                                                                     |
| customerName                   | 항상 비어 있습니다.                                                                                                                     |
| partNumber                     | 항상 비어 있습니다.                                                                                                                     |
| productName                    | 트랜잭션에 연결 된 제품 이름                                                                                                       |
| productId                      | 고유 제품 식별자                                                                                                                |
| parentProductId                | 고유한 상위 제품 식별자입니다. 참고: 트랜잭션에 대 한 부모 제품이 없으면 부모 제품 ID = 제품 ID입니다. |
| parentProductName              | 상위 제품의 이름입니다. 참고: 트랜잭션에 대 한 부모 제품이 없으면 부모 제품 이름 = 제품 이름입니다.   |
| productType                    | 제품 유형입니다(예: 앱, 애드온, 게임 등).                                                                                        |
| invoiceNumber                  | 송장 번호 (EA에만 적용 가능)                                                                                                  |
| resellerId                     | 대리점 식별자                                                                                                                      |
| resellerName                   | 재판매인 이름                                                                                                                            |
| transactionType                | 거래 유형입니다(예: 구매, 환불, 취소, 지불 거절 등).                                                               |
| localProviderSeller            | 로컬 공급자/판매자 레코드                                                                                                          |
| taxRemitted                    | 송금된 세금 금액(판매세, 이용세 또는 VAT/GST 세금)입니다.                                                                                   |
| taxRemitModel                  | 세금(판매세, 이용세 또는 부가가치세/물품용역세(Goods and Services Tax) 세금) 송금 담당자입니다.                                                                    |
| 인 경우 요금                       | 스토어에서 앱 또는 추가 기능을 사용할 수 있도록 하기 위한 요금으로 Microsoft에서 보유 하는 금액입니다.                                            |
| transactionPaymentMethod       | 거래에 사용된 고객 결제 방법입니다(카드, 모바일 통신사 청구, PayPal 등).                                |
| tpan                           | 타사 ad 네트워크를 나타냅니다.                                                                                                     |
| customerCountry                | 고객 국가                                                                                                                         |
| customerCity                   | 고객 도시                                                                                                                            |
| customerState                  | 고객 상태                                                                                                                           |
| customerZip                    | 고객 우편 번호                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | 프로그램에 대 한 고유 식별자                                                                                                        |
| externalReferenceIdLabel       | 고유 식별자 레이블                                                                                                                  |
| transactionCountryCode       | 트랜잭션이 발생 한 국가 코드                                                                                                                  |
| taxCountry       | 고객 국가에 판매                                                                                                                  |
| taxState       | 고객 상태에 판매                                                                                                                  |
| taxCity       | 고객 도시에 판매                                                                                                                  |
| taxZipCode       | 고객 Zip에 판매                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| 프로그램 코드       | 프로그램 이름으로 매핑할 문자열                                                                                                                   |
| earningAmountInLastPaymentCurrency       | 마지막 지불 통화 금액을 획득 합니다 (이전 지불액을 지불 하지 않은 경우 필드는 비어 있음).                                                                                                                   |
| lastPaymentCurrency       | 마지막 지불 통화 (지불 되지 않은 경우 필드는 비어 있음)                                                                                                                   |
| AssetId       | Marketplace 서비스의 고객 주문에 대 한 고유 식별자입니다.  트랜잭션 된 구매 라인 항목을 나타냅니다. 여러 자산이 있을 수 있습니다.                                                                                                                   |
| OrderId       | 고객의 청구서와 관련이 있습니다.                                                                                                                   |
| LineItemId       | 고객 송장의 개별 줄                                                                                                                   |
| 고객 국가       | 고객이 제공한 국가 이름입니다.  이는 고객의 Azure 구독에 있는 국가와 다를 수 있습니다.                                                                                                                   |
| 고객 EmailAddress       | 최종 고객이 제공한 전자 메일 주소입니다.  이는 고객의 Azure 구독에 있는 전자 메일 주소와 다를 수 있습니다.                                                                                                                   |
| SkuId       | 게시 하는 동안 정의 된 SKU ID입니다. 제품에는 많은 Sku가 있을 수 있지만 SKU는 단일 제품에만 연결 될 수 있습니다.                                                                                                                   |

>[!Note]
>Transact-sql 게시 옵션에 대 한 모든 보고 및 정보는 파트너 센터의 Cloud 파트너 포털 또는 분석 섹션의 Insights 섹션을 통해 제공 됩니다.

## <a name="billing-questions-and-support"></a>청구 관련 질문 및 지원

청구 관련 질문에 대 한 도움말을 보려면 [상용 마켓플레이스 게시자 지원](https://aka.ms/marketplacepublishersupport)에 문의 하세요.
