---
title: 기업 계약에 대 한 Azure 예약 사용량 이해
description: Enterprise 등록에서 Azure Reservation의 적용 방법을 이해하기 위해 사용량을 판독하는 방법을 알아봅니다.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: 8d85dd1c21f952261e838c01843e15dafcc0e931
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415770"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>기업 계약 예약 비용 및 사용량 가져오기

Azure portal 및 REST Api에서 예약 비용 및 사용 현황 데이터는 기업 계약 고객이 이용할 수 있습니다. 이 문서에서는 도와줍니다.

- 예약 구매 데이터 가져오기
- 예약 언더 사용률 데이터 가져오기
- 예약 비용을 상쇄
- 예약 사용량에 대 한 청구
- 예약 비용 절감을 계산 합니다.

Marketplace 요금 사용 현황 데이터에 통합 됩니다. 첫 번째 사용, marketplace 사용량 및 단일 데이터 원본에서 구매에 대 한 요금을 볼 있습니다.

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 사용량 데이터에서 예약 요금

데이터는 별도 두 데이터 집합으로 구분 됩니다. _실제 비용_ 하 고 _비용 상환_합니다. 이러한 두 데이터 집합 차이점:

**실제 비용** -월별 청구서와 조정 하는 데이터를 제공 합니다. 이 데이터는 예약 구입 비용에 있습니다. 예약 할인을 수신 하는 사용량에 대 한 0 EffectivePrice 있습니다.

**비용 상환** -reserved instance의 비례 배분 비용은 예약 할인을 가져오는 EffectiveCost 리소스입니다. 데이터 집합 역시 사용 되지 않는 예약 비용입니다. 예약 비용 및 사용 되지 않는 예약의 합계에는 예약의 일일 분할 상환된 비용을 제공 합니다.

두 데이터 집합의 비교:

| Data | 실제 비용 데이터 집합 | 분할 상환된 비용 데이터 집합 |
| --- | --- | --- |
| 예약 구매 | 이 뷰에서 사용할 수 있습니다.<br>  이 데이터 필터 ChargeType에서 가져오려는 = &quot;구매&quot;합니다. <br> 예약에 대 한 요금은 알아야 ReservationID 또는 ReservationName를 참조 하십시오.  | 이 보기에는 적용 되지 않습니다. <br> 구매 비용 상환된 데이터에 제공 되지 않습니다. |
| EffectivePrice | 값이 0 사용량 가져옵니다 예약 할인에 대 한 합니다. | 값은 사용량에 예약 할인에 대 한 예약 비용이 시간당으로 계산 됩니다. |
| 사용 되지 않는 예약 (예약 되지 않은 하루에 사용 되는 시간 및 통화 값의 낭비입니다 제공) | 이 보기에는 적용 되지 않습니다. | 이 뷰에서 사용할 수 있습니다.<br> 이 데이터를 가져오려면 ChargeType 필터링 = &quot;UnusedReservation&quot;합니다.<br>  예약 된 미달 사용 알아야 ReservationID 또는 ReservationName를 참조 하십시오. 예약의 크기를 낭비에 당일의입니다.  |
| UnitPrice (프로그램 가격표에서 리소스의 가격) | 사용 가능 | 사용 가능 |

Azure 사용량 데이터에서 사용할 수 있는 다른 정보가 변경 되었습니다.

- 제품 및 미터 정보-Azure 하지 대체 원래 사용 된 측정기 ReservationId 및 ReservationName를 사용 하 여 이전에 수행한 것 처럼 합니다.
- ReservationId 및 ReservationName-데이터에서 고유한 필드는 합니다. 이전에 AdditionalInfo 에서만 사용할 수 있습니다.
- ProductOrderId-예약 주문 ID 고유 필드로 추가 합니다.
- ProductOrderName-구매한 예약은의 제품 이름입니다.
- 용어-12 개월 동안 또는 36 개월입니다.
- RINormalizationRatio-AdditionalInfo에서 사용할 수 있습니다. 예약 사용량 레코드에 적용 되는 비율입니다. 인스턴스 크기 유연성에 예약을 사용할 다른 크기를 적용할 수 있습니다. 값 사용 레코드에 대 한 예약을 적용 된 비율을 보여 줍니다.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API를 사용 하 여 Azure 소비 및 예약 사용량 데이터 가져오기

Azure portal에서 다운로드 또는 API를 사용 하 여 데이터를 얻을 수 있습니다.

호출 하는 [사용량 세부 정보 API](/rest/api/consumption/usagedetails/list) api-version을 사용 하 여 &quot;2019-04-01-미리 보기&quot; 새 데이터를 가져올 수 있습니다. 참조 용어에 대 한 자세한 내용은 [사용 약관](billing-understand-your-usage.md)합니다. 호출자에 게 사용 하 여 엔터프라이즈 계약에 대 한 엔터프라이즈 관리자 여야 합니다 [EA 포털](https://ea.azure.com)합니다. 읽기 전용으로 엔터프라이즈 관리자는 데이터를 가져올 수도 있습니다.

데이터를 사용할 수 없습니다 [사용량 세부 정보-기업 고객을 위한 보고 Api](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)합니다.

API에 대 한 예제에서는 호출은 다음과 같습니다.

```
https://consumption.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

자세한 {enrollmentId}에 대 한 정보 및 {billingPeriodId}를 참조 합니다 [사용량 세부 정보 – 목록](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API 문서.

메트릭 및 필터에 대 한 정보는 다음 표에 일반적인 예약 문제를 해결 하는 데 도움이 됩니다.

| **API 데이터 형식** | API 호출 작업 |
| --- | --- |
| **모든 요금 (사용 및 구매)** | 실제 비용을 사용 하 여 {0} 메트릭} 바꾸기 |
| **예약 할인을 받은 사용** | 실제 비용을 사용 하 여 {0} 메트릭} 바꾸기<br>{Filter} 바꿉니다: properties/reservationId%20ne%20 |
| **예약 할인을 받지는 사용** | 실제 비용을 사용 하 여 {0} 메트릭} 바꾸기<br>{Filter} 바꿉니다: properties/reservationId%20eq%20 |
| **분할 상환된 비용 (사용 및 구매)** | {0} 메트릭} AmortizedCost 대체 |
| **사용 되지 않는 예약 보고서** | {0} 메트릭} AmortizedCost 대체<br>{Filter} 바꿉니다: properties/ChargeType%20eq%20'UnusedReservation' |
| **예약 구매** | {0} 메트릭} ActualCostReplace {filter}를 사용 하 여 대체: properties/ChargeType%20eq%20'Purchase'  |
| **환불** | 실제 비용을 사용 하 여 {0} 메트릭} 바꾸기<br>{Filter} 바꿉니다: properties/ChargeType%20eq%20'Refund' |

## <a name="download-the-usage-csv-file-with-new-data"></a>새 데이터를 사용 하 여 사용 현황 CSV 파일 다운로드

EA 관리자 인 경우에 Azure portal에서 새 사용 현황 데이터를 포함 하는 CSV 파일을 다운로드할 수 있습니다. 이 데이터에서 사용할 수 없는 합니다 [EA 포털](https://ea.azure.com)합니다.

Azure portal로 이동 [Cost management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)합니다.

1. 청구 계정을 선택 합니다.
2. 클릭 **사용량 + 요금**합니다.
3. **다운로드**를 클릭합니다.  
![Azure portal에서 CSV 사용 현황 데이터 파일을 다운로드할 수 있는 위치를 보여 주는 예제](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **사용량 다운로드 + 요금** 아래에 있는 **사용량 세부 정보 버전 2** 를 선택 **(사용 및 구매) 모든 요금** 다운로드를 클릭 하 고 합니다. 에 대 한 반복 **요금 (사용 및 구매) 분할 상환**합니다.

다운로드 한 CSV 파일과 실제 비용과 분할 상환된 비용 포함.

## <a name="common-cost-and-usage-tasks"></a>비용 및 사용량에 대 한 일반 작업

다음 섹션은 해당 예약 비용 및 사용 현황 데이터를 사용 하는 대부분의 일반적인 작업입니다.

### <a name="get-reservation-purchase-costs"></a>예약 구매 비용을 확인

예약 구매 비용 실제 비용 데이터에서 사용할 수 있습니다. 에 대 한 필터링 _ChargeType 구매 =_ 합니다. 에 대 한 구매 하는 예약 순서를 결정할 ProductOrderID를 참조 하십시오.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>미달 사용된 예약 수량 및 비용

분할 상환 비용 데이터를 가져오고 필터링 _ChargeType_ _UnusedReservation =_ 합니다. 일일 사용 되지 않는 예약 수량 및 비용을 얻게 됩니다. 예약 또는 예약 순서를 사용 하 여 데이터를 필터링 할 수 있습니다 _ReservationId_ 하 고 _ProductOrderId_ 필드를 각각. 예약을 사용 하는 100% 인 경우 레코드는 양의 0에 있습니다.

### <a name="amortize-reservation-costs"></a>예약 비용을 상쇄

분할 상환 비용 데이터를 가져오고 사용 하 여 예약 주문에 대 한 필터 _ProductOrderID_ 예약에 대 한 일일 분할 상환된 비용을 가져오려고 합니다.

### <a name="chargeback-for-a-reservation"></a>예약에 대 한 청구

구독, 리소스 그룹 또는 태그에 의해 다른 조직에 비용 청구 예약 사용할을 수 있습니다. 분할 상환된 비용 데이터는 다음 데이터 형식에는 예약의 사용률의 금액 가치를 제공합니다.

- 리소스 (예: VM)
- 리소스 그룹
- Tags
- 구독

### <a name="get-the-blended-rate-for-chargeback"></a>비용 청구에 대 한 혼합된 비율 가져오기

혼합된 비율을 확인 하려면 분할 상환된 비용 데이터를 가져오고 전체 비용을 집계 합니다. Vm에 대 한 AdditionalInfo JSON 데이터에서 MeterName 또는 ServiceType 정보를 사용할 수 있습니다. 총 수량을 혼합된 비율을 가져오는 데 사용 하 여 비용을 나눕니다.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>감사 최적의 예약 사용 예를 들어 크기 유연성

사용 하 여 여러 수량 합니다 _RINormalizationRatio_, AdditionalInfo에서. 결과 시간을 사용 하 여 예약 사용량 레코드에 적용 된 것을 나타냅니다.

### <a name="determine-reservation-savings"></a>예약 비용 절감 확인

Amortized 비용 데이터를 가져오고 예약된 인스턴스에 대 한 데이터를 필터링 합니다. 그렇다면

1. 종 량 제 비용이 예상된을 가져옵니다. 곱하기 합니다 _UnitPrice_ 값을 _수량_ 가져올 값 예약 할인을 적용 하지 않은 사용에 적용 되는 경우 종 량 제 비용을 예상 합니다.
2. 예약 비용을 확인 합니다. 합계는 _비용_ 지불한 reserved instance의 통화 값을 검색할 값입니다. 예약의 사용 및 사용 하지 않는 비용을 포함합니다.
3. 예약 비용이 예상된 절감 비율을 확인 하려면 종 량 제 비용 예상된을 뺍니다.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>예약 구매 및 Azure 비용 분석에서 상환

예약된 인스턴스 비용은 영어로 [미리 보기 모드를 분석 하는 Azure 비용](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis)합니다. 기본적으로 비용 데이터 보기 실제 비용입니다. 분할 상환된 비용으로 전환할 수 있습니다. 예제는 다음과 같습니다.

![비용 분석의 분할 상환된 비용을 선택 하는 위치를 보여 주는 예제](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

예약 또는 충전 상태 형식에 의해 사용자 요금을 보려면 필터를 적용 합니다. 예약 이름 예약 별로 비용을 보려면 그룹화 합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
