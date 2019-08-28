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
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598105"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>기업계약 예약 비용 및 사용량 가져오기

예약 비용 및 사용 현황 데이터는 Azure Portal 및 REST Api의 기업계약 고객에 게 제공 됩니다. 이 문서는 다음을 지원 합니다.

- 예약 구매 데이터 가져오기
- 예약을 사용 하는 구독, 리소스 그룹 또는 리소스를 확인 합니다.
- 예약 사용률에 대 한 비용 정산
- 예약 비용 계산 계산
- 예약 사용률 데이터 가져오기
- 하고자 예약 비용

Marketplace 요금은 사용 현황 데이터에 통합 됩니다. 단일 데이터 원본에서 자사 사용, marketplace 사용 및 구매에 대 한 요금을 볼 수 있습니다.

## <a name="reservation-charges-in-azure-usage-data"></a>Azure 사용 현황 데이터의 예약 요금

데이터는 두 개의 개별 데이터 집합으로 나뉩니다. _실제 비용_ 및 _분할 상환 비용_ 이러한 두 데이터 집합의 차이점은 다음과 같습니다.

**실제 비용** -월별 청구서를 조정 하는 데이터를 제공 합니다. 이 데이터는 예약 구매 비용 및 예약 응용 프로그램 세부 정보를 포함 합니다. 이 데이터를 사용 하 여 특정 날짜에 예약 할인을 받은 구독 또는 리소스 그룹 또는 리소스를 알 수 있습니다. 예약 할인을 수신 하는 사용량에 대 한 EffectivePrice는 0입니다.

**분할 상환 Cost** -이 데이터 집합은 실제 비용 데이터 집합과 유사 합니다. 단, 예약 할인을 가져오는 사용에 대 한 EffectivePrice는 0이 아닌 예약 비용에 비례 합니다. 이를 통해 구독, 리소스 그룹 또는 리소스에의 한 예약 소비의 통화 가치를 파악 하 고, 예약 사용률을 내부적으로 다시 청구 하는 데 도움이 될 수 있습니다. 데이터 집합에도 사용 되지 않는 예약 시간이 있습니다. 데이터 집합에 예약 구매 레코드가 없습니다. 

두 데이터 집합의 비교:

| 보기 | 실제 비용 데이터 집합 | 분할 상환 Cost 데이터 집합 |
| --- | --- | --- |
| 예약 구매 | 이 보기에서 사용할 수 있습니다.<br><br>  Chargetchar= &quot;Purchase&quot;에서이 데이터 필터를 가져옵니다. <br><br> 요금이 부과 되는 예약을 확인 하려면 ReservationID 또는 ReservationName를 참조 하세요.  | 이 보기에 적용할 수 없습니다. <br><br> 구매 비용은 분할 상환 데이터에 제공 되지 않습니다. |
| EffectivePrice | 예약 할인을 가져오는 사용에 대 한 값은 0입니다. | 이 값은 예약 할인이 적용 된 사용량에 대 한 예약 비용을 시간당 비례 합니다. |
| 사용 하지 않는 예약 (하루에 예약을 사용 하지 않은 시간 및 폐기물 금액 값 제공) | 이 보기에는 적용 되지 않습니다. | 이 보기에서 사용할 수 있습니다.<br><br> 이 데이터를 가져오려면 chargetype &quot;&quot;=를 필터링 합니다.<br><br>  ReservationID 또는 ReservationName을 참조 하 여 사용률이 낮은 예약을 확인 하세요. 이는 하루 동안 얼마나 많은 예약이 낭비 되었는지에 대 한 것입니다.  |
| UnitPrice (가격 책정 시트의 리소스 가격) | 사용 가능 | 사용 가능 |

Azure 사용 현황 데이터에서 사용할 수 있는 다른 정보가 변경 되었습니다.

- 제품 및 측정기 정보-Azure는 이전에 했던 것 처럼 원래 사용 된 측정기를 ReservationId 및 ReservationName로 대체 하지 않습니다.
- ReservationId 및 ReservationName-데이터의 고유한 필드입니다. 이전에는 AdditionalInfo에서 사용할 수 있는 경우에만 사용 되었습니다.
- 제품 Orderid-자체 필드로 추가 된 예약 주문 ID입니다.
- 제품 Ordername-구매한 예약의 제품 이름입니다.
- 12 개월 또는 36 개월입니다.
- RINormalizationRatio-AdditionalInfo 아래에서 사용할 수 있습니다. 사용 레코드에 예약이 적용 되는 비율입니다. 예약에 대해 인스턴스 크기 유연성이 사용 하도록 설정 된 경우 다른 크기에 적용할 수 있습니다. 값은 사용 레코드에 대 한 예약이 적용 된 비율을 표시 합니다.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>API를 사용 하 여 Azure 소비 및 예약 사용량 데이터 가져오기

API를 사용 하 여 데이터를 가져오거나 Azure Portal에서 다운로드할 수 있습니다.

Api 버전 &quot;2019-04-01-미리 보기&quot; 를 사용 하 여 [사용 정보 api](/rest/api/consumption/usagedetails/list) 를 호출 하 여 새 데이터를 가져옵니다. 용어에 대 한 자세한 내용은 [사용 조건](billing-understand-your-usage.md)을 참조 하세요. 호출자는 [EA 포털](https://ea.azure.com)을 사용 하 여 기업 계약에 대 한 엔터프라이즈 관리자 여야 합니다. 읽기 전용 엔터프라이즈 관리자는 데이터를 가져올 수도 있습니다.

[기업 고객을 위한 보고 api-사용량 세부 정보](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)에서 데이터를 사용할 수 없습니다.

다음은 API에 대 한 호출입니다.

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

{등록 정보} 및 {billingPeriodId}에 대 한 자세한 내용은 [사용 세부 정보 – API 나열](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) 문서를 참조 하세요.

메트릭 및 필터에 대 한 다음 표의 정보는 일반적인 예약 문제를 해결 하는 데 도움이 될 수 있습니다.

| **API 데이터 유형** | API 호출 작업 |
| --- | --- |
| **모든 요금 (사용량 및 구매)** | {Metric}을 ActualCost로 바꾸기 |
| **예약 할인이 적용 된 사용량** | {Metric}을 ActualCost로 바꾸기<br><br>{Filter}를 속성/reservationId% 20ne% 20으로 바꿉니다. |
| **예약 할인을 받지 않은 사용량** | {Metric}을 ActualCost로 바꾸기<br><br>{Filter}를 속성/reservationId% 20eq% 20으로 바꿉니다. |
| **분할 상환 요금 (사용량 및 구매)** | {Metric}을 AmortizedCost로 바꾸기 |
| **사용 하지 않는 예약 보고서** | {Metric}을 AmortizedCost로 바꾸기<br><br>{Filter}를 속성/문자 Getchar% 20eq% 20 ' UnusedReservation '로 바꿉니다. |
| **예약 구매** | {Metric}을 ActualCost로 바꾸기<br><br>{Filter}를 속성/문자 Getchar% 20eq% 20 ' Purchase '로 바꿉니다.  |
| **환불** | {Metric}을 ActualCost로 바꾸기<br><br>{Filter}를 속성/문자 Getchar% 20eq% 20 ' 환불 '으로 바꿉니다. |

## <a name="download-the-usage-csv-file-with-new-data"></a>새 데이터를 사용 하 여 사용 현황 CSV 파일 다운로드

EA 관리자 인 경우 Azure Portal에서 새로운 사용 데이터를 포함 하는 CSV 파일을 다운로드할 수 있습니다. 이 데이터는 [EA 포털](https://ea.azure.com)에서 사용할 수 없습니다.

Azure Portal에서 [Cost management + 청구](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)로 이동 합니다.

1. 청구 계정을 선택 합니다.
2. **사용량 + 요금**을 클릭 합니다.
3. **다운로드**를 클릭합니다.  
![Azure Portal에서 CSV 사용 데이터 파일을 다운로드 하는 위치를 보여 주는 예제](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. **사용량 및 요금 다운로드** 의 **사용량 세부 정보 버전 2** 에서 **모든 요금 (사용량 및 구매)** 을 선택 하 고 다운로드를 클릭 합니다. **분할 상환 요금 (사용량 및 구매)** 에 대해 반복 합니다.

다운로드 하는 CSV 파일에는 실제 비용과 분할 상환 비용이 포함 됩니다.

## <a name="common-cost-and-usage-tasks"></a>일반적인 비용 및 사용 작업

다음 섹션에서는 대부분의 사용자가 예약 비용 및 사용 현황 데이터를 보는 데 사용 하는 일반적인 작업을 설명 합니다.

### <a name="get-reservation-purchase-costs"></a>예약 구매 비용 가져오기

예약 구매 비용은 실제 비용 데이터에서 사용할 수 있습니다. _Chargetchar= 구매_를 필터링 합니다. 구매한 예약 순서를 결정 하려면 제품 Orderid를 참조 하세요.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>미달 사용 예약 수량 및 비용 가져오기

분할 상환 Cost 데이터를 가져오고 _Chargetchar=_ _UnusedReservation_에 대 한 필터를 제공 합니다. 매일 사용 하지 않는 예약 수량 및 비용을 얻습니다. _ReservationId_ 및 _제품 orderid_ 필드를 사용 하 여 예약 또는 예약 주문에 대 한 데이터를 각각 필터링 할 수 있습니다. 예약이 100% 사용 된 경우 레코드의 수량은 0입니다.

### <a name="amortize-reservation-costs"></a>하고자 예약 비용

분할 상환 비용 데이터를 가져오고, _제품 orderid_ 를 사용 하 여 예약 주문에 대 한 필터를 사용 하 여 예약에 대 한 일별 분할 상환 비용을 가져옵니다.

### <a name="chargeback-for-a-reservation"></a>예약에 대 한 비용 정산

구독, 리소스 그룹 또는 태그를 통해 예약 사용을 다른 조직에 비용 정산 수 있습니다. 분할 상환 cost 데이터는 다음 데이터 형식에 대 한 예약 사용률의 통화 가치를 제공 합니다.

- 리소스 (예: VM)
- 리소스 그룹
- Tags
- 구독

### <a name="get-the-blended-rate-for-chargeback"></a>비용 정산에 대 한 혼합 률 가져오기

혼합 비율을 확인 하려면 분할 상환 비용 데이터를 가져와 총 비용을 집계 합니다. Vm의 경우 AdditionalInfo JSON 데이터에서 MeterName 또는 ServiceType 정보를 사용할 수 있습니다. 총 비용을 혼합 비율을 가져오는 데 사용 된 수량으로 나눕니다.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>인스턴스 크기 유연성에 대 한 최적 예약 사용 감사

AdditionalInfo의 _RINormalizationRatio_를 사용 하 여 수량을 여러 개 사용 합니다. 결과에는 사용량 레코드에 적용 된 예약 사용 시간 수가 표시 됩니다.

### <a name="determine-reservation-savings"></a>예약 절감 액 결정

분할 상환 비용 데이터를 가져오고 예약 된 인스턴스에 대 한 데이터를 필터링 합니다. 그렇다면

1. 예상 종 량 제 비용을 받으세요. _단가_ 값과 _수량_ 값을 곱하여 예약 할인이 사용에 적용 되지 않은 경우 예상 종 량 제 비용을 얻습니다.
2. 예약 비용을 가져옵니다. _비용_ 값을 합산 하 여 예약 된 인스턴스에 대해 지불 된 금액의 통화 값을 가져옵니다. 여기에는 예약 된 사용 및 사용 되지 않은 비용이 포함 됩니다.
3. 예상 되는 종 량 제 비용에서 예약 비용을 빼서 예상 절감 액을 얻습니다.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>비용 분석에서 예약 구매 및 상환

예약 비용은 [비용 분석](https://aka.ms/costanalysis)에서 제공 됩니다. 기본적으로 비용 분석은 청구서에 비용이 표시 되는 **실제 비용**을 표시 합니다. 이 혜택을 사용 하는 리소스와 연결 된 예약 된 구매 구매를 보려면 **분할 상환 cost**로 전환 합니다.

![비용 분석에서 분할 상환 cost를 선택할 수 있는 위치를 보여 주는 예제](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

사용량, 구매 및 환불의 분할을 보려면 요금을 기준으로 그룹화 합니다. 또는 예약에 따라 예약 및 요청 시 비용을 분석 합니다. 실제 비용을 구매할 때 표시 되는 유일한 예약 비용은 명심 해야 하지만 분할 상환 비용을 확인할 때이를 사용 하는 개별 리소스에 비용이 할당 됩니다. 분할 상환 비용을 확인할 때 새로운 **UnusedReservation** 청구 유형도 표시 됩니다.

## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 컴퓨팅 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
