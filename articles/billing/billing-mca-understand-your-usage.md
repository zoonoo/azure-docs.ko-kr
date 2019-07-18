---
title: Microsoft 고객 계약에 대 한 Azure 사용량 및 요금 파일의 사용 약관
description: 읽고 Azure 사용량 및 청구 프로필에 대 한 요금 CSV 섹션을 이해 하는 방법에 알아봅니다.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490618"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 Azure 사용량 및 요금 파일의 사용 약관

이 문서는 Microsoft 고객 계약에 대 한 대금 청구 계정에 적용 됩니다. [Microsoft 고객 계약에 액세스할 수 있는지 확인](#check-access-to-a-microsoft-customer-agreement)합니다.

Azure 사용량 및 요금이 CSV 파일은 현재 청구 기간 동안 일별 및 미터 수준 사용 요금을 포함합니다.

Azure 사용량 및 요금 파일을 참조 하세요 [뷰와 Azure 사용량 다운로드 및 Microsoft 고객 계약에 대 한 요금은](billing-download-azure-daily-usage.md)합니다. 스프레드시트 애플리케이션에서 열 수 있는 쉼표로 구분된 값(.csv) 파일 형식으로 제공됩니다.

사용 요금은 구독에 대한 총 **월별** 요금입니다. 사용 요금에서는 신용 또는 할인이 고려되지 않습니다.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Azure EA 사용량 및 요금이 변경

EA 고객 인 경우 Azure 청구 프로필 사용 현황 CSV 파일에 Azure EA 사용량 CSV 파일의 조건에서 다른 지를 확인할 수 있습니다. 청구 프로필 사용 약관에 EA 사용 약관의 매핑을 다음과 같습니다.

| Azure EA 사용량 CSV | Microsoft 고객 계약 Azure 사용량 및 요금이 CSV |
| --- | --- |
| Date | date |
| 월| date |
| 일 | date |
| Year | date |
| Product | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| 태그들 | tags |
| StoreServiceIdentifier | N/A |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>자세한 내용 및 설명

다음 용어는 Azure 사용량 및 요금 파일에 표시 됩니다.

용어 | 설명
--- | ---
invoiceId | PDF 청구서에 표시 된 고유한 문서 ID
previousInvoiceId | 이 항목은 환불 하는 경우 원래 송장에 대 한 참조
billingAccountName | 청구 계정의 이름
billingAccountId | 대금 청구 계정 루트에 대 한 고유 식별자
billingProfileId | 송장이 발급 되는 요금을 발생 하는 청구 프로필의 이름
billingProfileName | 송장이 발급 되는 요금을 발생 하는 청구 프로필에 대 한 고유 식별자
invoiceSectionId | 송장 섹션에 대 한 고유 식별자
invoiceSectionName | 송장 섹션의 이름
costCenter | 비용 (진행 청구 기간에 사용 가능)을 추적 하는 것에 대 한 구독에 정의 된 비용 센터
billingPeriodStartDate | 청구서 생성 되는 청구 기간 시작 날짜
billingPeriodEndDate | 청구서 생성 되는 청구 기간 종료 날짜
servicePeriodStartDate | 등급 기간의 시작 날짜는 정의 되어 있고 사용 또는 구매 서비스에 대 한 가격 책정 잠겨
servicePeriodEndDate | 등급 기간의 종료 날짜에 정의 된 있으며 잠긴 사용 또는 구매 서비스에 대 한 가격 책정
date | Azure 및 Marketplace 사용량 기준 요금에 대 한 등급 날짜입니다. (예약, Marketplace)의 일회성 구매 또는 고정된 반복 청구 요금 (지원 제품)에 대 한 구매 날짜입니다.
serviceFamily | 서비스에 속하는 서비스 제품군
productOrderId | 제품 주문에 대 한 고유 식별자
productOrderName | 제품 주문에 대 한 고유 이름
consumedService | 사용 된 서비스의 이름
meterId | 측정기에 대 한 고유 식별자
meterName | 측정기의 이름
meterCategory | 측정기에 대 한 분류 범주 이름입니다. 예를 들어 *Cloud services*하십시오 *네트워킹*등입니다.
meterSubCategory | 미터 하위 분류 범주 이름
meterRegion | 서비스에 대 한 미터를 사용할 수 있는 영역의 이름입니다. 데이터 센터 위치에 따라 가격이 책정 되는 특정 서비스에 대 한 데이터 센터의 위치를 식별 합니다.
제품 | 구매한 제품의 이름
productId | 요금이 발생 하는 제품에 대 한 고유 식별자
product | 요금이 발생 하는 제품 이름
구독 ID | 요금이 발생 하는 구독에 대 한 고유 식별자
subscriptionName | 요금은 발생 구독의 이름
reservationId | 구매한 예약 인스턴스에 대 한 고유 식별자
reservationName | 구매한 예약 인스턴스 이름
publisherType | 게시자의 유형 (값: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | 마켓플레이스 서비스에 대 한 게시자
resourceGroupId | 리소스와 연결 된 리소스 그룹에 대 한 고유 식별자
resourceGroupName | 리소스와 연결 된 리소스 그룹의 이름
resourceId | 리소스 인스턴스에 대 한 고유 식별자
resourceType | 유형의 리소스 인스턴스
resourceLocation | 리소스가 실행 되 고 있는 데이터 센터의 위치를 식별 합니다.
location | 다른 리소스 위치는 동일한 지역에 대해 구성 된 경우 리소스의 정규화 된 위치
quantity | 구매 또는 사용 된 단위 수
unitOfMeasure | 서비스에 대 한 청구에 대 한 측정 단위입니다. 예를 들어, 계산 서비스는 시간당 요금이 청구 됩니다.
chargeType | 무료로 형식입니다. 값 <ul><li>AsCharged-Usage: Azure 서비스의 사용량을 기준으로 계산 하는 요금입니다. 예약된 인스턴스 때문에 요금이 부과 되지 않습니다 하는 Vm에 대해 사용량 포함 됩니다.</li><li>AsCharged-PurchaseMarketplace: Marketplace에서 구매한 제품은에서 일회성 또는 고정 반복 청구 요금</li><li>AsCharged-UsageMarketplace: 소비 단위를 기준으로 부과 되는 Marketplace 서비스 요금</li></ul>
isAzureCreditEligible | 서비스에 대해 요금을 Azure 크레딧을 사용 하 여에 대 한 지급 수 인지를 나타내는 플래그입니다 (값: True, False)
serviceInfo1 | 서비스 특정 메타 데이터
serviceInfo2 | 선택적 서비스 특정 메타데이터를 캡처하는 레거시 필드입니다.
additionalInfo | 추가 서비스 특정 메타 데이터입니다.
tags | 리소스에 할당 된 태그

### <a name="make-sure-that-charges-are-correct"></a>요금이 올바른지 확인

세부 사용량 파일의 요금이 올바른지 확인 하려는 경우 정보를 확인할 수 있습니다. 참조 [청구 프로필의 청구서 요금 이해](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 고객 계약에 대 한 액세스를 확인 합니다.
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

- [보기 및 Microsoft Azure 청구서를 다운로드 합니다.](billing-download-azure-invoice.md)
- [에 Microsoft Azure 사용 및 요금 보기 및 다운로드](billing-download-azure-daily-usage.md)
