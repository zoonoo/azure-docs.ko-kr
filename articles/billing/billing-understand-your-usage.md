---
title: 자세한 사용 현황 및 요금 이해 | Microsoft Docs
description: 읽기 및 자세한 사용 현황 및 요금을 이해 하는 방법을 알아봅니다
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683995"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Azure 사용량 및 요금 파일에서 용어 이해

자세한 사용 현황 및 요금 파일에 지정 된 기간에 대 한 협상 된 속도, 구매 (예를 들어, 예약, Marketplace 요금) 및 환불에 따라 매일 평가 된 사용량 포함 되어 있습니다.
크레딧, 세금, 또는 다른 요금 또는 할인 요금이 포함 되지 않습니다.
다음 표는 요금은 각 계정 유형에 대 한 포함를 다룹니다.

계정 유형 | Azure 사용 | Marketplace 사용량 | 구매 | 환불
--- | --- | --- | --- | ---
EA(기업 계약) | 예 | 예 | 예 | 아닙니다.
MCA(Microsoft 고객 계약) | 예 | 예 | 예 | 예
PAYG(종량제) | 예 | 아니오 | 아니요 | 아닙니다.

Marketplace 주문 (외부 서비스 라고도 함)에 대 한 자세한 내용은 참조 하세요 [Azure 외부 서비스 요금의 이해](billing-understand-your-azure-marketplace-charges.md)합니다.

참조 [Azure 청구 송장 및 일간 사용 현황 데이터를 가져오는 방법을](billing-download-azure-invoice-daily-usage-date.md) 다운로드 지침에 대 한 합니다.
사용량 및 요금 파일은 스프레드시트 응용 프로그램에서 열 수 있는 쉼표로 구분 된 값 (.csv) 파일 형식으로 사용할 수 있습니다.

## <a name="list-of-terms-and-descriptions"></a>용어 및 설명의 목록

다음 표에서 Azure 사용량 및 요금 파일의 최신 버전에 사용 되는 중요 한 용어를 설명 합니다.
종 량 제 (PAYG), 엔터프라이즈 규약 (EA) 및 Microsoft 고객 계약 (MCA) 계정 목록에 설명합니다.

용어 | 계정 유형 | 설명
--- | --- | ---
AccountName | EA | 등록 계정의 표시 이름입니다.
계정 소유자 ID | EA | 등록 계정에 대 한 고유 식별자입니다.
추가 정보 | 모두 | 서비스 특정 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다.
BillingAccountId | EA, MCA | 대금 청구 계정 루트에 대 한 고유 식별자입니다.
BillingAccountName | EA, MCA | 청구 계정의 이름입니다.
billingCurrency | EA, MCA | 청구 계정에 연결 된 통화입니다.
BillingPeriod | EA | 요금을 청구 기간입니다.
BillingPeriodEndDate | EA, MCA | 청구 기간의 끝 날짜입니다.
BillingPeriodStartDate | EA, MCA | 청구 기간의 시작 날짜입니다.
BillingProfileId | EA, MCA | EA 등록 또는 MCA 청구 프로필의 고유 식별자입니다.
BillingProfileName | EA, MCA | EA 등록 또는 MCA 청구 프로필의 이름입니다.
ChargeType | EA, MCA | 요금을 사용량을 나타내는지 여부를 나타냅니다 (**사용 현황**)를 구매 (**구매**), 또는 환불이 귀하 (**환불**).
ConsumedQuantity | 종 량 제 | 수량을 참조 하세요.
사용한 서비스 | 모두 | 서비스 요금을는 연결 된 이름입니다.
비용 | EA | CostInBillingCurrency를 참조 하세요.
CostCenter | EA, MCA | 비용 센터 (MCA 계정에 대 한 진행 청구 기간에 사용 가능) 비용 추적에 대 한 구독에 대 한 정의.
CostInBillingCurrency | MCA | 비용 크레딧 또는 세금 하기 전에 청구 통화 요금입니다.
CostInPricingCurrency | MCA | 비용 크레딧 또는 세금 하기 전에 가격 통화 요금입니다.
통화 | 종 량 제 | BillingCurrency를 참조 하세요.
Date | EA, MCA | 무료로 사용 또는 구매 날짜입니다.
ExchangeRateDate | MCA | 환율이 설정 된 날짜입니다.
ExchangeRatePricingToBilling | MCA | 환율 통화 가격 책정에 비용 청구 통화 변환 하는 데 사용 합니다.
Frequency(빈도) | EA, MCA | 요금이 반복 해야 하는지 여부를 나타냅니다. 요금이 발생할 수 있습니다 하거나 한 번 (**OneTime**), 월별 또는 연간 기준 반복 (**되풀이**), 사용량에 따라 수 또는 (**UsageBased**).
IncludedQuantity | 종 량 제 | 현재 청구 기간에 무료로 포함 되는 미터의 양입니다.
InstanceId | PAGY | ResourceId를 참조 하세요.
InvoiceId | EA, MCA | PDF 청구서에 나열 된 고유한 문서 ID입니다.
InvoiceSection | MCA | InvoiceSectionName를 참조 하세요.
InvoiceSectionId | EA, MCA | EA 부서나 MCA 송장 섹션에 대 한 고유 식별자입니다.
InvoiceSectionName | EA, MCA | EA 부서나 MCA 송장 섹션의 이름입니다.
IsAzureCreditEligible | EA, MCA | 요금은 Azure 크레딧을 사용 하 여에 대 한 지급 수 인지 여부를 나타냅니다 (값: True, False)입니다.
Location | EA, MCA | 리소스가 실행 되 고 있는 데이터 센터 위치입니다.
미터 범주 | 모두 | 측정기에 대 한 분류 범주 이름입니다. 예를 들어 *Cloud services* 하 고 *네트워킹*합니다.
MeterId | 모두 | 측정기에 대 한 고유 식별자입니다.
요금제 이름 | 모두 | 측정기의 이름입니다.
요금제 지역 | 모두 | 위치를 기준으로 가격이 책정 되는 서비스에 대 한 데이터 센터 위치의 이름입니다. 위치를 확인 합니다.
미터 하위 범주 | 모두 | 미터 subclassification 범주의 이름입니다.
OfferId | EA, MCA | 구매한 제품의 이름입니다.
PartNumber | EA | 특정 미터 가격을 가져오는 데 사용 하는 식별자입니다.
계획 이름 | EA | Marketplace 계획 이름입니다.
PreviousInvoiceId | MCA | 이 항목은 환불 하는 경우 원래 송장에 대 한 참조입니다.
pricingCurrency | MCA | 협상 된 가격을 기반으로 등급 때 사용 되는 통화입니다.
Product | MCA | ProductName을 참조 하세요.
ProductId | EA, MCA | 제품에 대 한 고유 식별자입니다.
ProductName | EA | 제품의 이름입니다.
ProductOrderId | EA, MCA | 제품 주문에 대 한 고유 식별자입니다.
ProductOrderName | EA, MCA | 제품 주문에 대 한 고유한 이름입니다.
PublisherName | EA, MCA | 마켓플레이스 서비스에 대 한 게시자입니다.
PublisherType | EA, MCA | 게시자의 유형 (값: firstParty, thirdPartyReseller, thirdPartyAgency).
수량 | EA, MCA | 구매 하거나 사용할 단위의 수입니다.
비용 | 종 량 제 | UnitPrice를 참조 하세요.
ReservationId | EA, MCA | 구매한 예약 인스턴스에 대 한 고유 식별자입니다.
ReservationName | EA, MCA | 구매한 예약 인스턴스 이름입니다.
ResourceGroupId | EA, MCA | 에 대 한 고유 식별자를 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 리소스가입니다.
ResourceGroupName | EA, MCA | 이름을 합니다 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 이 리소스는 합니다.
ResourceId | EA, MCA | 고유 식별자를 [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) 리소스입니다.
리소스 위치 | EA, MCA | 리소스가 실행 되 고 있는 데이터 센터 위치입니다. 위치를 확인 합니다.
ResourceName | EA | 리소스의 이름입니다.
ResourceType | MCA | 리소스 인스턴스의 유형입니다.
ServiceFamily | EA, MCA | 서비스에 속하는 서비스 제품군입니다.
서비스 정보 1 | 모두 | 서비스 특정 메타데이터입니다.
서비스 정보 2 | 모두 | 선택적 서비스 특정 메타 데이터를 사용 하 여 레거시 필드입니다.
ServicePeriodEndDate | MCA | 등급 기간의 종료 날짜 정의 사용 또는 구매 서비스에 대 한 가격 책정 잠겨 있습니다.
ServicePeriodStartDate | MCA | 등급 기간의 시작 날짜 정의 사용 또는 구매 서비스에 대 한 가격 책정 잠겨 있습니다.
SubscriptionId | 모두 | 구독에 대 한 고유 식별자입니다.
SubscriptionName | 모두 | 구독의 이름입니다.
태그들 | 모두 | 태그를 리소스에 할당 합니다. 리소스 그룹 태그를 포함 하지 않습니다. 그룹 또는 내부 비용 청구 비용을 배분 하는데 데 사용할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)을 참조하세요.
단위 | 종 량 제 | UnitOfMeasure를 참조 하세요.
측정 단위 | 모두 | 서비스에 대 한 청구에 대 한 측정 단위입니다. 예를 들어, 계산 서비스는 시간당 요금이 청구 됩니다.
UnitPrice | EA | 요금에 대 한 단위당 가격입니다.
UsageDate | 종 량 제 | 날짜를 참조 하세요.

일부 필드는 대/소문자 및 계정 유형 사이의 간격에서 달라질 수 있습니다 note 합니다.
종 량 제 사용량 파일의 이전 버전에는 문 및 일별 사용 현황에 대 한 별도 섹션에 있습니다.

## <a name="ensure-that-your-charges-are-correct"></a>요금의 올바른지 확인

자세한 사용 현황 및 요금에 대 한 자세한 내용은 이해 하는 방법에 대 한 읽기 프로그램 [종 량 제](./billing-understand-your-bill.md) 또는 [Microsoft 고객 계약](billing-mca-understand-your-bill.md) 송장 합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [보기 및 Microsoft Azure 청구서를 다운로드 합니다.](billing-download-azure-invoice.md)
- [에 Microsoft Azure 사용 및 요금 보기 및 다운로드](billing-download-azure-daily-usage.md)
