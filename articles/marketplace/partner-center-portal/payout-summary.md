---
title: 상업적 marketplace 지급 요약 | Azure Marketplace
description: 지급 요약은 제품을 사용 하 여 달성 한 비용에 대 한 세부 정보를 보여줍니다. 또한 지불을 받는 시기와 유료 정도를 알 수 있습니다.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980271"
---
# <a name="payout-reporting"></a>지급 보고

**지급 요약** 은 Microsoft에서 달성 한 비용에 대 한 세부 정보를 보여줍니다. 또한 지불을 받는 시기와 유료 정도를 알 수 있습니다.

Azure Marketplace에서 제품을 판매 하는 경우 **지급 요약**에 성공 지급 대 한 정보도 표시 됩니다. Azure Marketplace 결제에 대 한 자세한 내용은 [Microsoft Azure Marketplace 참여 정책](https://go.microsoft.com/fwlink/p/?LinkId=722436) 및 [Microsoft Azure Marketplace 게시자 계약](https://go.microsoft.com/fwlink/p/?LinkID=699560)을 참조 하세요.

> [!NOTE]
> 지급에 적합 하려면 진행이 $50의 [지불 임계값](payment-thresholds-methods-timeframes.md) 에 도달 해야 합니다. 지불 임계값에 대 한 자세한 내용은이 페이지를 참조 하 고 [Microsoft Azure Marketplace 게시자 규약](https://go.microsoft.com/fwlink/p/?LinkID=699560)을 검토 하십시오.

- [지급 보고서: Cloud 파트너 포털와 파트너 센터 간의 차이점](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [고객 유형](#customer-types)
- [지급와 usage 간의 Corelation](#corelation-between-payout-and-usage)
- [트랜잭션 기록 다운로드](#transaction-history-download-export)
- [청구 질문 및 지원](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>지급 보고서: Cloud 파트너 포털와 파트너 센터 간의 차이점

| | Felhőpartnerportál | Partnerközpont |
|---------|---------|---------|
| Hivatkozások | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory és https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Navigáció | Insights 지급에 제공 된 지급 보고 | 파트너 센터에서 제공 하는 지급 보고-지급 아이콘 |
| Hatókör | <ul> <li>진행 중인 컬렉션, 수집 된 컬렉션 및 유료 항목에 대 한 줄 당 트랜잭션 항목 표시 </li> <li>보고 – 진행 중인 컬렉션과 대금 청구 및 아직 유료으로 사용할 수 없는 수집 상태와 품목을 포함 하 여 구매 주문이 만들어진 후 모든 품목을 표시 합니다. </li> </ul> | <ul> <li>적격 소득으로 간주 되는 경우 품목을 표시 합니다.</li> <li>고객은 먼저 Microsoft에 요금을 지불 하 고 Isv는 지급 보고서를 시작 하는 것을 볼 수 있습니다.</li> <li>지급 보고서는 진행 중인 컬렉션과 청구 중인 컬렉션은 표시 하지 않습니다.  </li> </ul>  |
| 지급 준비가 되지 않은 트랜잭션 | 청구 진행 중 | 다음 예상 결제: 지급 상태가 처리 되지 않음 상태입니다.  |
| 지급 상태 |  | 일별 <br> 이러한 획득은 지불에 적합 합니다. 이 상태는 동기 프로그램의 프로그램 가이드에 정의 된 대로 냉각 기간 동안 유지 됩니다. <br> <br> 그 <br> 지불 순서-지불을 처리 하기 전에 보류 중인 내부 검토를 생성 했습니다. <br> <br> 냈 <br> 요금을 은행으로 보냈습니다. |

## <a name="customer-types"></a>고객 유형 

### <a name="enterprise-agreement"></a>기업 계약

기업계약 없는 고객에 게는 marketplace 소프트웨어 라이선스에 대 한 월별 요금이 청구 됩니다. 기업계약 있는 고객은 분기별로 제공 된 청구서를 통해 매달 청구 됩니다.

### <a name="credit-cards-and-monthly-invoice"></a>신용 카드 및 월별 청구서

고객은 신용 카드 및 월별 청구서를 사용 하 여 비용을 지불할 수도 있습니다. 이 경우 소프트웨어 라이선스 요금은 매월 청구 됩니다.

### <a name="csp-and-direct-pay-users"></a>CSP 및 직접 종 량 제 사용자

예를 들어 고객이 신용 카드를 사용 하 여 구매 하는 경우

## <a name="corelation-between-payout-and-usage"></a>지급와 usage 간의 Corelation 

|Leírás    |    Dátum  | 주문/사용  | Kifizetés |
|----------|----------|-----------|-------------|
|주문 기간   | 8 월 15 일, 2019-8 월 30 일, 2019 | **상관 관계 특성 주문** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Használat** <br> <ul> <li>CustomerId </li> <li>고객 이름</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> 예상 연장 요금 <br> 예상 지급 (PC) </li> </ul> |  |
|종료 기간 (월)   | 8 월 30 일, 2019 | | |
|청구 날짜 | 2019 년 9 월 1 일 | | |
|고객 지불 날짜 | 2019 년 9 월 1 일 | | |
|에스크로 기간 (신용 카드만, 30 일) | 2019 년 9 월 1 일-9 월 30 2019 일 | | **상관 관계 특성 주문:** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> 고객 이름</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 일별 |
|수집 기간 시작 | 2019 년 9 월 1 일 | | |
|수집 기간 종료 (최대, 30 일) | 2019 년 9 월 30 일 | | |
|지급 계산 날짜 (매월 15 일) | 2019 년 10 월 1 일 | | **상관 관계 특성** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li>고객 이름</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 그 |
|지급 날짜 | 2019 년 10 월 15 일 | | **상관 관계 특성** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> 고객 이름</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 지불 전송 |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>기업 계약 (분기별/월별 고객)

| Leírás |    Dátum  | Használat | Kifizetés |
|----------|----------|---------|-----------|
|주문 기간 | 8 월 15 일, 2019-8 월 30 일, 2019 | **상관 관계 특성 주문** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **사용 보고서** <br> <ul> <li>CustomerId </li> <li>고객 이름</li> <li>(UsageReference) PurchaseRecordId/LineItemId</li> <li> 예상 연장 요금 <br> 예상 지급 (PC) </li> </ul> | |
|기간 종료 (사분기) | 2019 년 9 월 30 일 | | |
|청구 날짜 | 2019 년 10 월 15 일 | | |
|에스크로 기간 (신용 카드만, 30 일) | – | | |
|수집 기간 시작 | 2019 년 10 월 15 일 | | |
|신용 카드만 30 일 | 2019 년 11 월 1 일-2019 년 11 월 30 일 | | |
|수집 기간 종료 (최대, 90 일) | 2020 년 1 월 15 일 | | |
|고객 지불 날짜 | 2019 년 12 월 30 일 | | |
|지급 계산 | 2020 년 1 월 15 일 | | |
|지급 날짜 | 2 월 15 일 2020 | | **분기별 기반 고객의 경우** <br> <br> **주문 보고서** <br> <ul><li>AssetId</li> <li>Ügyfél-azonosító</li> <li> 고객 이름</li> </ul> <br> **Használat** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>고객 이름</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **지급 상태:** 보냄 |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>트랜잭션 기록 다운로드 내보내기

이 옵션은 트랜잭션 기록 페이지에 표시 되는 각 줄 항목의 다운로드를 제공 하 고, 형식, 날짜, 연결 된 트랜잭션 금액, 고객, 제품 및 성과급 프로그램에 적용할 수 있는 기타 트랜잭션 정보를 제공 합니다. 

| 열 이름     | Leírás    | 
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
| paymentStatus            | 지불 상태                                            |
| paymentStatusDescription            | 지불 상태 설명                                            |
| CustomerId                     | 항상 비어 있습니다.                                                                                                                     |
| customerName                   | 항상 비어 있습니다.                                                                                                                     |
| partNumber                     | 항상 비어 있습니다.                                                                                                                     |
| 제품                    | 트랜잭션에 연결 된 제품 이름                                                                                                       |
| productId                      | 고유 제품 식별자                                                                                                                |
| parentProductId                | 고유한 부모 제품 식별자입니다. 참고: 트랜잭션에 대 한 부모 제품이 없으면 부모 제품 ID = 제품 ID입니다. |
| parentProductName              | 부모 제품의 이름입니다. 참고: 트랜잭션에 대 한 부모 제품이 없으면 부모 제품 이름 = 제품 이름입니다.   |
| productType                    | 제품 유형 (예: 앱, 추가 기능, 게임 등)                                                                                        |
| invoiceNumber                  | 송장 번호 (EA에만 적용 가능)                                                                                                  |
| resellerId                     | 대리점 식별자                                                                                                                      |
| resellerName                   | Viszonteladó neve                                                                                                                            |
| transactionType                | 트랜잭션 유형 (예: 구매, 환불, 반전, 비용 정산 등)                                                               |
| localProviderSeller            | 로컬 공급자/판매자 레코드                                                                                                          |
| taxRemitted                    | 세금 송금 (판매, 사용 또는 VAT/GST 세금).                                                                                   |
| taxRemitModel                  | Remitting 세금 (판매, 사용 또는 VAT/GST 세금)을 담당 하는 파티.                                                                    |
| 인 경우 요금                       | 스토어에서 앱 또는 추가 기능을 사용할 수 있도록 하기 위한 요금으로 Microsoft에서 보유 하는 금액입니다.                                            |
| transactionPaymentMethod       | 트랜잭션에 사용 되는 고객 지불 방법 (예: 카드, 모바일 운송 업체 청구, PayPal 등)                                |
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

## <a name="billing-questions-and-support"></a>청구 질문 및 지원

청구 관련 질문에 대 한 도움말을 보려면 [상용 마켓플레이스 게시자 지원](https://aka.ms/marketplacepublishersupport)에 문의 하세요.
