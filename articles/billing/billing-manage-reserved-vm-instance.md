---
title: Azure Reservations 관리
description: Azure Reservations를 관리 하는 방법을 알아봅니다.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840002"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure 리소스에 대한 예약 관리

Azure 예약을 구매한 후에는 다른 구독에 예약을 적용 하거나, 예약을 관리할 수 있는 사용자를 변경 하거나, 예약의 범위를 변경 해야 할 수 있습니다. 또한 예약을 두 개로 분할하여 구입한 일부 인스턴스를 다른 구독에 적용할 수도 있습니다.

Azure Reserved Virtual Machine Instances를 구매한 경우 예약에 대 한 최적화 설정을 변경할 수 있습니다. 예약 할인을 동일한 시리즈의 VM에 적용하거나, 특정 VM 크기에 대한 데이터 센터 용량을 예약할 수 있습니다. 그리고 전체 사용을 위해 예약을 최적화 하는 것을 시도해 야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>예약 순서 및 예약

예약을 구매 하는 경우 두 개의 개체가 만들어집니다. **예약 순서** 및 **예약**.

구매 시 예약 주문에는 하나의 예약이 있습니다. 분할, 병합, 부분 환불, exchange 등의 작업은 **예약 순서로**새 예약을 만듭니다.

예약 순서를 보려면 예약으로 이동 하 > 예약을 선택 하 고 **예약 주문 ID**를 클릭 합니다.

![예약 주문 ID를 표시 하는 예약 주문 정보의 예 ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

예약은 예약 순서에서 사용 권한을 상속 합니다.

## <a name="change-the-reservation-scope"></a>예약 범위 변경

 예약 할인은 예약에 일치 하 고 예약 범위에서 실행 되는 가상 컴퓨터, SQL 데이터베이스, Azure Cosmos DB 또는 기타 리소스에 적용 됩니다. 청구 컨텍스트는 예약을 구입하는 데 사용되는 구독에 따라 달라집니다.

예약의 범위를 업데이트하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성**을 선택합니다.
5. 범위를 변경합니다.

공유에서 단일 범위로 변경 하는 경우 소유자 인 구독만 선택할 수 있습니다. 동일한 청구 컨텍스트 내에서 예약된 구독만을 선택할 수 있습니다.

범위는 종 량 제 요금이 있는 개별 구독 (MS-AZR-0017P-0003P 또는 MS-AZR-0017P-0023P 제공), Enterprise 제품 MS-MS-AZR-0017P-0023P 또는 MS-AZR-0017P-Ms-azr-0148p 또는 CSP 구독 유형 에서만 적용 됩니다.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>예약을 관리할 수 있는 사용자 추가 또는 변경

예약 주문 또는 예약에 대 한 역할에 사용자를 추가 하 여 예약 관리를 위임할 수 있습니다. 기본적으로 예약 주문 및 계정 관리자를 배치 하는 사람은 예약 주문 및 예약에 대 한 소유자 역할을 갖습니다.

예약 할인을 받는 구독과는 별도로 예약 주문 및 예약에 대 한 액세스를 관리할 수 있습니다. 사용자에 게 예약 주문 또는 예약을 관리할 수 있는 권한을 부여 하는 경우 구독을 관리할 수 있는 권한을 부여 하지 않습니다. 마찬가지로 예약 범위에서 구독을 관리할 수 있는 권한을 사용자에 게 부여 하는 경우 예약 순서나 예약을 관리할 수 있는 권한을 부여 하지 않습니다.

Exchange 또는 환불을 수행 하려면 사용자에 게 예약 주문에 대 한 액세스 권한이 있어야 합니다. 사용자에 게 권한을 부여 하는 경우 예약이 아닌 예약 주문에 권한을 부여 하는 것이 가장 좋습니다.


예약에 대한 액세스 관리를 위임하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택하여 액세스할 수 있는 예약을 나열합니다.
3. 다른 사용자에게 액세스 권한을 위임하려는 예약을 선택합니다.
4. **액세스 제어(IAM)** 를 선택합니다.
5. **역할 할당 추가** > **역할** > **소유자**를 선택합니다. 또는 제한된 액세스 권한을 부여하려는 경우 다른 역할을 선택합니다.
6. 소유자로 추가할 사용자의 메일 주소를 입력합니다.
7. 사용자를 선택한 다음 **저장**을 선택합니다.

## <a name="split-a-single-reservation-into-two-reservations"></a>단일 예약을 두 개의 예약으로 분할

 한 예약 내에서 둘 이상의 리소스 인스턴스를 구입한 후에는 예약 내의 인스턴스를 다른 구독에 할당하는 것이 좋습니다. 기본적으로 모든 인스턴스에는 단일 구독 또는 공유 중 하나의 범위가 지정됩니다. 예를 들어 10 개의 예약 인스턴스를 구매 하 고 해당 범위를 구독 A로 지정 합니다. 이제 7 개 예약의 범위를 구독 A로, 나머지 3에서 구독 B로 변경할 수 있습니다. 예약을 분할 하면 세분화 된 범위 관리를 위해 인스턴스를 배포할 수 있습니다. 공유 범위를 선택하여 구독에 대한 할당을 간소화할 수 있습니다. 하지만 비용 관리 또는 예산 목적으로 인해 특정 구독에 수량을 할당할 수 있습니다.

 PowerShell, CLI 또는 API를 통해 예약을 두 개로 분할할 수 있습니다.

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell을 사용하여 예약 분할

1. 다음 명령을 실행하여 예약 주문 ID를 가져옵니다.

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 예약의 세부 정보를 가져옵니다.

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 예약을 두 개로 분할하고 인스턴스를 배포합니다.

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 다음 명령을 실행하여 범위를 업데이트할 수 있습니다.

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>취소, 교환 또는 환불 예약

특정 제한 사항을 사용 하 여 예약을 취소, 교환 또는 환불 할 수 있습니다. 자세한 내용은 [셀프 서비스 교환 및 Azure Reservations에 대 한 환불](billing-azure-reservations-self-service-exchange-and-refund.md)를 참조 하세요.

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Reserved VM Instances에 대한 최적화 설정 변경

 Reserved VM Instance를 구매하는 경우 인스턴스 크기 유연성 또는 용량 우선 순위를 선택합니다. 인스턴스 크기 유연성은 동일한 [VM 크기 그룹](https://aka.ms/RIVMGroups)의 다른 VM에 예약 할인을 적용합니다. 용량 우선 순위는 배포를 위해 데이터 센터 용량에서 우선됩니다. 이 옵션을 사용하면 필요할 때 VM 인스턴스를 보다 확실히 시작할 수 있게 됩니다.

기본적으로 예약의 범위를 공유할 경우 인스턴스 크기 유연성이 켜집니다. 데이터 센터 용량은 VM 배포에서 우선적으로 고려되지 않습니다.

범위가 단일인 예약의 경우 VM 인스턴스 크기 유연성 대신 용량 우선 순위에 대한 예약을 최적화할 수 있습니다.

예약에 대한 최적화 설정을 업데이트하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성**을 선택합니다.
5. **다음에 맞게 최적화** 설정을 변경합니다.

## <a name="optimize-reservation-use"></a>예약 사용 최적화

Azure 예약 절감은 리소스를 지속적으로 사용 하는 경우에만 발생 합니다. 예약 구매를 수행 하는 경우 1 년 또는 3 년 동안 가능한 리소스 사용에 대 100 한 사전 투자 비용을 지불 합니다. 최대한 많은 사용과 절감 액을 얻기 위해 예약을 최대화 해 보세요. 다음 섹션에서는 예약을 모니터링 하 고 사용을 최적화 하는 방법을 설명 합니다.

### <a name="view-reservation-use-in-the-azure-portal"></a>Azure Portal에서 예약 사용 보기

예약 사용을 보는 한 가지 방법은 Azure Portal입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **모든 서비스** > [**예약**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) 을 선택 하 고 예약에 대 한 **사용률 (%)** 을 기록 합니다.  
  ![예약 목록을 보여 주는 이미지](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. 예약을 선택 합니다.
4. 시간별 예약 사용 추세를 검토 합니다.  
  ![예약 사용을 보여 주는 이미지 ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>API를 사용 하 여 예약 사용 보기

기업계약 (EA) 고객 인 경우 조직에서 예약을 사용 하는 방법을 프로그래밍 방식으로 볼 수 있습니다. 사용 현황 데이터를 통해 사용 하지 않는 예약을 가져옵니다. 예약 요금을 검토 하는 경우 데이터가 실제 비용과 분할 상환 비용으로 구분 된다는 점에 유의 하세요. 실제 비용은 월간 청구를 조정 하는 데이터를 제공 합니다. 또한 예약 구매 비용 및 예약 응용 프로그램 세부 정보도 있습니다. 분할 상환는 예약 사용에 대 한 유효 가격이 비례 하는 것을 제외 하 고 실제 비용과 비슷합니다. 사용 하지 않는 예약 시간은 분할 상환 cost 데이터에 표시 됩니다. EA 고객의 사용 현황 데이터에 대 한 자세한 내용은 [기업계약 예약 비용 및 사용](billing-understand-reserved-instance-usage-ea.md)을 참조 하세요.

다른 구독 유형의 경우에는 API [예약 요약 목록을 예약 순서 및 예약 별로](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation)사용 합니다.

### <a name="optimize-your-reservation"></a>예약 최적화

조직의 예약을 사용할 수 없는 경우:

- 조직에서 만드는 가상 컴퓨터가 예약에 사용 되는 VM 크기와 일치 하는지 확인 합니다.
- 인스턴스 크기 유연성이 켜져 있는지 확인합니다. 자세한 내용은 [예약 관리 - 예약 VM 인스턴스에 대한 최적화 설정 변경](#change-optimize-setting-for-reserved-vm-instances)을 참조하세요.
- 예약의 범위를 _공유_ 로 변경 하 여 더 광범위 하 게 적용 합니다. 자세한 내용은 [예약의 범위 변경](#change-the-reservation-scope)을 참조 하세요.
- 사용 하지 않는 수량을 교환 하십시오. 자세한 내용은 취소 [및 교환](#cancel-exchange-or-refund-reservations)(영문)을 참조 하세요.


## <a name="need-help-contact-us"></a>도움이 필요하십니까? 문의하세요.

궁금한 사항이 있거나 도움이 필요 하면 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure에 대 한 예약은 무엇 인가요?](billing-save-compute-costs-reservations.md)

서비스 계획 구입:
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 컴퓨팅 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Cosmos DB 예약된 용량을 사용하여 Azure Cosmos DB 리소스 요금 선결제](../cosmos-db/cosmos-db-reserved-capacity.md)

소프트웨어 요금제 구입:
- [Azure Reservations의 Red Hat 소프트웨어 요금제에 대 한 선불](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reservations에서 SUSE 소프트웨어 요금제에 대한 선불](../virtual-machines/linux/prepay-suse-software-charges.md)

할인 및 사용에 대 한 이해:
- [VM 예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [Red Hat Enterprise Linux 소프트웨어 계획 할인이 적용 되는 방법 이해](../billing/billing-understand-rhel-reservation-charges.md)
- [SUSE Linux Enterprise 소프트웨어 요금제 할인이 적용되는 방식 이해](../billing/billing-understand-suse-reservation-charges.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
