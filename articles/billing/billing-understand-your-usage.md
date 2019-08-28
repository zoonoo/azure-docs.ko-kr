---
title: 자세한 사용량과 요금 이해 | Microsoft Docs
description: 자세한 사용량과 요금을 읽고 이해 하는 방법 알아보기
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
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954334"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Azure 사용량 및 요금 파일의 용어 이해

자세한 사용 현황 및 요금 파일에는 지정 된 기간에 대 한 협상 된 속도, 구매 (예: 예약, Marketplace 요금) 및 환불에 따라 매일 등급이 지정 된 사용량이 포함 됩니다.
요금은 크레딧, 세금, 기타 요금 또는 할인을 포함 하지 않습니다.
다음 표에서는 각 계정 유형에 대해 포함 되는 요금을 설명 합니다.

계정 형식 | Azure 사용량 | Marketplace 사용 | 구매 | 환불
--- | --- | --- | --- | ---
EA(기업 계약) | 예 | 예 | 예 | 아니요
MCA(Microsoft 고객 계약) | 예 | 예 | 예 | 예
PAYG(종량제) | 예 | 예 | 아니오 | 아니요

Marketplace 주문 (외부 서비스 라고도 함)에 대해 자세히 알아보려면 [Azure 외부 서비스 요금 이해](billing-understand-your-azure-marketplace-charges.md)를 참조 하세요.

다운로드 지침은 [Azure 청구서 및 일간 사용 현황 데이터를 가져오는 방법](billing-download-azure-invoice-daily-usage-date.md)을 참조 하세요.
Microsoft Excel 또는 다른 스프레드시트 응용 프로그램에서 사용 현황 및 요금 CSV 파일을 열 수 있습니다.

## <a name="list-of-terms-and-descriptions"></a>용어 및 설명 목록

다음 표에서는 최신 버전의 Azure 사용량 및 요금 파일에 사용 되는 중요 한 용어에 대해 설명 합니다.
이 목록에는 종 량 제 (PAYG), 기업계약 (EA) 및 Microsoft MCA (고객 계약) 계정이 포함 되어 있습니다.

용어 | 계정 형식 | 설명
--- | --- | ---
계정 이름 | EA, PAYG | EA 등록 계정 또는 PAYG 청구 계정의 표시 이름입니다.
AccountOwnerId<sup>1</sup> | EA, PAYG | EA 등록 계정 또는 PAYG 청구 계정에 대 한 고유 식별자입니다.
AdditionalInfo | 모두 | 서비스 특정 메타데이터입니다. 예를 들어 가상 머신용 이미지 형식입니다.
BillingAccountId<sup>1</sup> | 모두 | 루트 청구 계정에 대 한 고유 식별자입니다.
BillingAccountName | 모두 | 청구 계정의 이름입니다.
BillingCurrency | 모두 | 청구 계정과 연결 된 통화입니다.
BillingPeriod | EA, PAYG | 요금 청구 기간입니다.
BillingPeriodEndDate | 모두 | 청구 기간의 종료 날짜입니다.
BillingPeriodStartDate | 모두 | 청구 기간의 시작 날짜입니다.
BillingProfileId<sup>1</sup> | 모두 | EA 등록, PAYG 구독, MCA 청구 프로필 또는 AWS 통합 계정의 고유 식별자입니다.
BillingProfileName | 모두 | EA 등록, PAYG 구독, MCA 청구 프로필 또는 AWS 통합 계정의 이름입니다.
ChargeType | 모두 | 요금이 사용량 (**사용량**), 구매 (**구매**) 또는 환불 (**환불**)을 나타내는지 여부를 나타냅니다.
ConsumedService | 모두 | 요금이 부과 되는 서비스의 이름입니다.
CostCenter<sup>1</sup> | EA, MCA | 비용을 추적 하는 구독에 대해 정의 된 비용 센터 (MCA 계정에 대 한 오픈 청구 기간 에서만 사용 가능).
비용 | EA, PAYG | CostInBillingCurrency를 참조 하세요.
CostInBillingCurrency | MCA | 크레딧 또는 세금 이전 청구 통화 요금입니다.
CostInPricingCurrency | MCA | 크레딧 또는 세금 이전의 가격 책정 통화 요금입니다.
통화 | EA, PAYG | BillingCurrency를 참조 하세요.
날짜<sup>1</sup> | 모두 | 요금 사용 또는 구매 날짜입니다.
EffectivePrice | 모두 | 기간의 혼합 단위 가격입니다. 혼합 가격은 시간이 지남에 따라 수량이 증가 함에 따라 가격을 낮추는 그라데이션 계층화와 같은 단가의 변동에 대해 평균을 계산 합니다.
ExchangeRateDate | MCA | 환율이 설정 된 날짜입니다.
ExchangeRatePricingToBilling | MCA | 가격 책정 통화의 비용을 청구 통화로 변환 하는 데 사용 되는 환율입니다.
빈도 | 모두 | 요금이 반복 될 것으로 예상 되는지 여부를 나타냅니다. 요금은 한 번 수행 (**OneTime**) 하거나 매월 또는 매년 반복 하거나 (**되풀이**) 사용을 기준으로 할 수 있습니다 (**UsageBased**).
InvoiceId | PAYG, MCA | 송장 PDF에 나열 된 고유 문서 ID입니다.
InvoiceSection | MCA | InvoiceSectionName를 참조 하세요.
InvoiceSectionId<sup>1</sup> | EA, MCA | EA 부서 또는 MCA 송장 섹션에 대 한 고유 식별자입니다.
InvoiceSectionName | EA, MCA | EA 부서 또는 MCA 청구서 섹션의 이름입니다.
IsAzureCreditEligible | 모두 | Azure 크레딧을 사용 하기 위해 요금이 청구 될 수 있는지 여부를 나타냅니다 (값: True, False)입니다.
위치 | MCA | 리소스가 실행 되 고 있는 데이터 센터 위치입니다.
MeterCategory | 모두 | 미터의 분류 범주 이름입니다. 예: *클라우드 서비스* 및 *네트워킹*.
MeterId<sup>1</sup> | 모두 | 측정기에 대 한 고유 식별자입니다.
MeterName | 모두 | 미터의 이름입니다.
MeterRegion | 모두 | 위치에 따라 가격이 책정 되는 서비스의 데이터 센터 위치 이름입니다. 위치를 참조 하세요.
MeterSubCategory | 모두 | 측정기 하위 분류 범주의 이름입니다.
OfferId<sup>1</sup> | 모두 | 구매한 제품의 이름입니다.
PartNumber<sup>1</sup> | EA, PAYG | 특정 측정기 가격 책정을 가져오는 데 사용 되는 식별자입니다.
PlanName | EA, PAYG | Marketplace 계획 이름입니다.
PreviousInvoiceId | MCA | 이 품목을 상환 하는 경우 원래 청구서에 대 한 참조입니다.
PricingCurrency | MCA | 협상 된 가격을 기준으로 등급을 매기는 경우 사용 되는 통화입니다.
Product | 모두 | 제품의 이름입니다.
ProductId<sup>1</sup> | MCA | 제품의 고유 식별자입니다.
ProductOrderId | 모두 | 제품 주문의 고유 식별자입니다.
ProductOrderName | 모두 | 제품 주문에 대 한 고유 이름입니다.
PublisherName | 모두 | Marketplace 서비스의 게시자입니다.
PublisherType | 모두 | 게시자 유형 (값: **Azure**, **AWS**, **Marketplace**).
수량 | 모두 | 구입 또는 소비 된 단위 수입니다.
ReservationId | EA, MCA | 구매한 예약 인스턴스에 대 한 고유 식별자입니다.
ReservationName | EA, MCA | 구매한 예약 인스턴스의 이름입니다.
ResourceGroup | 모두 | 리소스가 있는 [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 의 이름입니다.
ResourceId<sup>1</sup> | 모두 | [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) 리소스의 고유 식별자입니다.
ResourceLocation | 모두 | 리소스가 실행 되 고 있는 데이터 센터 위치입니다. 위치를 참조 하세요.
ResourceName | EA, PAYG | 리소스의 이름입니다.
ResourceType | MCA | 리소스 인스턴스의 유형입니다.
ServiceFamily | MCA | 서비스가 속한 서비스 제품군입니다.
ServiceInfo1 | 모두 | 서비스 특정 메타데이터입니다.
ServiceInfo2 | 모두 | 선택적인 서비스별 메타 데이터를 포함 하는 레거시 필드입니다.
ServicePeriodEndDate | MCA | 사용 되거나 구매한 서비스의 가격 책정을 정의 하 고 잠근 등급 기간의 종료 날짜입니다.
ServicePeriodStartDate | MCA | 사용 되거나 구매한 서비스의 가격 책정을 정의 하 고 잠근 등급 기간의 시작 날짜입니다.
SubscriptionId<sup>1</sup> | 모두 | Azure 구독에 대 한 고유 식별자입니다.
구독 이름 | 모두 | Azure 구독의 이름입니다.
태그<sup>1</sup> | 모두 | 리소스에 할당 된 태그입니다. 리소스 그룹 태그를 포함 하지 않습니다. 내부 비용 정산에 대 한 비용을 그룹화 하거나 배포 하는 데 사용할 수 있습니다. 자세한 내용은 [태그를 사용하여 Azure 리소스 구성](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)을 참조하세요.
용어 | 모두 | 제품의 유효성을 검사 하는 용어를 표시 합니다. 예를 들어: 예약 된 인스턴스의 경우 12 개월을 용어로 표시 합니다. 일회성 구매 또는 되풀이 구매의 경우 기간은 1 개월 (SaaS, Marketplace 지원)입니다. Azure 소비에는 적용 되지 않습니다.
UnitOfMeasure | 모두 | 서비스에 대 한 청구 측정 단위입니다. 예를 들어 계산 서비스는 시간당 청구 됩니다.
UnitPrice | EA, PAYG | 요금 단위당 가격입니다.

_단일 비용 레코드에 대 한 고유 ID를 작성 하는 데 사용 되는 필드 <sup>**1 개**</sup>_

참고 일부 필드는 대/소문자 구분과 계정 유형 간의 간격이 다를 수 있습니다.
종 량 제 사용 파일의 이전 버전에는 문 및 일일 사용에 대 한 별도의 섹션이 있습니다.

### <a name="list-of-terms-from-older-apis"></a>이전 Api의 용어 목록
다음 표에서는 이전 Api에서 사용 되는 용어를 새로운 용어로 매핑합니다. 이러한 설명은 위의 표를 참조 하세요.

이전 용어 | 새 용어
--- | ---
사용한 수량 | 수량
IncludedQuantity | 해당 사항 없음
InstanceId | resourceId
요금 | EffectivePrice
단위 | UnitOfMeasure
UsageDate | 날짜
UsageEnd | 날짜
UsageStart | 날짜


## <a name="ensure-charges-are-correct"></a>요금이 올바른지 확인

자세한 사용 현황 및 요금에 대 한 자세한 내용은 [종 량](./billing-understand-your-bill.md) 제 또는 [Microsoft 고객 계약](billing-mca-understand-your-bill.md) 청구서를 이해 하는 방법을 참고 하세요.

## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 청구서 보기 및 다운로드](billing-download-azure-invoice.md)
- [Microsoft Azure 사용량과 요금을 보고 다운로드 합니다.](billing-download-azure-daily-usage.md)
