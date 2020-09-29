---
title: Azure VMware 솔루션 예약 인스턴스를 사용 하 여 비용 절감
description: Azure VMware 솔루션에 대 한 예약 인스턴스를 구매 하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: a477b1730d70ec729a2be333b545b6faeb009998
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91492489"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Azure VMware 솔루션을 사용 하 여 비용 절감

Azure VMware 솔루션의 예약 된 인스턴스로 커밋하는 경우 비용을 절감할 수 있습니다. 예약 할인은 예약 범위 및 특성과 일치 하는 실행 중인 Azure VMware 솔루션 호스트 수에 자동으로 적용 됩니다. 할인을 얻기 위해 전용 호스트에 예약을 할당할 필요가 없습니다. 예약 인스턴스 구매는 사용량의 계산 부분만 다루며 소프트웨어 라이선스 비용이 포함 됩니다.  [Azure VMware 솔루션 개요](introduction.md)를 참조 하세요.

## <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

예약 된 인스턴스는 일부 예외와 함께 사용할 수 있습니다.

-   **클라우드**   -예약은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) 페이지에 나열 된 지역 에서만 사용할 수 있습니다.

-   **할당량 부족**   -단일/공유 구독으로 범위가 지정 된 예약에는 새 예약 인스턴스의 구독에서 사용할 수 있는 호스트 할당량이 있어야 합니다.  [할당량 증가 요청을 만들어](enable-azure-vmware-solution.md) 이 문제를 해결할 수 있습니다.

-   **제품 자격**-Microsoft와 함께 [AZURE 기업계약 (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements)가 필요   합니다.

-   **용량 제한**   -드문 경우 지만 Azure는 지역에 용량이 부족 하기 때문에 Azure VMware 솔루션 호스트 Sku에 대 한 새 예약 구매를 제한 합니다.

## <a name="buy-a-reservation"></a>예약 구입

 [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)에서 Azure VMware 솔루션 호스트 인스턴스의 예약 인스턴스를 구입할 수 있습니다.

예약에 대 한 요금을 지불 [하거나 매월 지불](../cost-management-billing/reservations/prepare-buy-reservation.md)합니다.

이러한 요구 사항은 예약 된 전용 호스트 인스턴스를 구입 하는 데 적용 됩니다.

-   하나 이상의 EA 구독에 대 한 소유자 역할 또는 종 량 제 요금이 있는 구독 이어야 합니다.

-   EA 구독의 경우 Ea 포털에서 **예약 된 인스턴스 추가**   옵션을 사용 하도록 [EA portal](https://ea.azure.com/)설정 해야 합니다. 또는 해당 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.

인스턴스를 구매하려면

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2.  **모든 서비스**   >  **예약**을 선택 합니다.

3.  **추가**   를 선택 하 여 새 예약을 구매 하 고 **Azure VMware 솔루션**을 선택 합니다.

4. 필수 필드를 입력합니다. 선택한 특성과 일치 하는 Azure VMware 솔루션 호스트를 실행 하 여 예약 할인을 받습니다. 할인을 받는 Azure VMware 솔루션 호스트의 실제 수는 선택 된 범위와 수량에 따라 달라 집니다.

   EA 계약이 있는 경우 추가 **옵션**을 사용   하 여 인스턴스를 빠르게 추가할 수 있습니다. 다른 구독 유형에 대해서는이 옵션을 사용할 수 없습니다.

   | 필드        |  설명 |
   | ------------ | ------------ |
   | Subscription | 예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독의 결제 방법으로 예약 요금이 청구됩니다. 구독 유형은 기업 계약 (제품 번호: MS-AZR-0017P-0017P 또는 MS-AZR-0017P-Ms-azr-0148p) 또는 Microsoft 고객 계약 또는 종 량 제 요금 (제품 번호: MS-MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0017P)을 포함 하는 개별 구독 이어야 합니다. 요금은 약정 잔액에서 차감되거나(있는 경우) 초과분 요금으로 청구됩니다. 종 량 제 요금이 있는 구독의 경우 요금 청구는 구독에 대 한 신용 카드 또는 청구서 지불 방법으로 청구 됩니다. |
   | Scope        | 예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우:<br><ul><li><b>단일 리소스 그룹 범위-선택한 리소스 그룹의 일치 하는 리소스에만 예약 할인을 적용 합니다.</li><li><b>단일 구독 범위-선택한 구독의 일치 하는 리소스에 예약 할인을 적용 합니다.</li><li><b>공유 범위-청구 컨텍스트에 있는 적격 구독의 일치 하는 리소스에 예약 할인을 적용 합니다. EA 고객의 경우 요금 청구 컨텍스트가 등록입니다. 종량제 요금이 적용되는 개별 구독의 경우 청구 범위는 계정 관리자가 만든 모든 적격 구독입니다.</li></ul>       |
   | Region       | 예약 범위에 해당하는 Azure 지역입니다.   |
   | 호스트 크기    | AV36    |
   | 용어         | 1년 또는 3년입니다.  |
   | 수량     | 예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 Azure VMware 솔루션 호스트의 수입니다.    |

## <a name="usage-data-and-reservation-utilization"></a>사용 현황 데이터 및 예약 사용률

예약 할인이 적용되는 사용량의 경우 사용량 데이터의 유효 가격은 0입니다. 각 예약에 대해 예약 할인을 받은 Azure VMware 솔루션 인스턴스를 확인할 수 있습니다.

사용 현황 데이터에 예약 할인이 표시 되는 방법에 대 한 자세한 내용 및 EA 고객은 [기업 등록에 대 한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)를 참조 하세요. 개별 구독이 있는 경우 [종 량 제 구독에 대 한 Azure 예약 사용량 이해](../cost-management-billing/reservations/understand-reserved-instance-usage.md)를 참조 하세요.

## <a name="change-a-reservation-after-purchase"></a>구매 후 예약 변경

구매한 후 예약에 대해 다음과 같은 유형의 변경을 수행할 수 있습니다.

-   예약 범위 업데이트

-   인스턴스 크기 유연성 (해당 하는 경우)

-   소유권

또한 예약을 더 작은 청크 또는 병합 예약으로 분할할 수 있습니다. 새 상업용 트랜잭션을 발생 시키거나 예약의 종료 날짜를 변경 하지 않습니다.

>[!NOTE]
>예약을 구매한 후에는 다음과 같은 유형의 변경을 직접 수행할 수 없습니다.
>
> - 기존 예약 영역
> - SKU
> - 수량
> - 기간
>
>그러나 *exchange*   변경을 수행 하려는 경우 예약을 교환할 수 있습니다.

## <a name="cancel-exchange-or-refund-reservations"></a>예약 취소, 교환 또는 환불

예약을 취소, 교환 또는 환불할 수 있지만 몇 가지 제한 사항은 있습니다. 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대 한 환불](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)를 참조 하세요.
