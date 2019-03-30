---
title: Azure 예약 관리 | Microsoft Docs
description: 구독 범위를 변경하고 Azure 예약에 대한 액세스 권한을 관리하는 방법에 대해 알아봅니다.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 0f6e0f3795e0e6d25f7443473c5911995597ca14
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648642"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure 리소스에 대한 예약 관리

Azure에 대 한 예약을 구입한 후 다른 구독에 예약을 적용 하려면 예약, 관리 또는 예약의 범위를 변경할 수 있는 사용자를 변경 해야 합니다. 또한 예약을 두 개로 분할하여 구입한 일부 인스턴스를 다른 구독에 적용할 수도 있습니다.

Azure Reserved Virtual Machine Instances를 구입한 경우 예약에 대한 최적화 설정을 변경할 수 있습니다. 예약 할인을 동일한 시리즈의 VM에 적용하거나, 특정 VM 크기에 대한 데이터 센터 용량을 예약할 수 있습니다.

## <a name="change-the-scope-for-a-reservation"></a>예약할 범위 변경

 예약 할인 가상 머신, SQL 데이터베이스, Azure Cosmos DB 또는 예약과 일치 하 고 예약 범위에서 실행 하는 다른 리소스에 적용 됩니다. 청구 컨텍스트는 예약을 구입하는 데 사용되는 구독에 따라 달라집니다.

예약의 범위를 업데이트하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. 예약을 선택합니다.
4. **설정** > **구성**을 선택합니다.
5. 범위를 변경합니다.

범위를 공유에서 단일 범위로 변경하는 경우 소유자인 구독만을 선택할 수 있습니다. 동일한 청구 컨텍스트 내에서 예약된 구독만을 선택할 수 있습니다.

범위는 종량제 제안 MS-AZR-0003P 또는 MS-AZR-0023P, Enterprise 제안 MS-AZR-0017P 또는 MS-AZR-0148P, 또는 CSP 구독 유형에만 적용됩니다.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>예약을 관리할 수 있는 사용자 추가 또는 변경

예약에 대한 역할에 사용자를 추가하여 예약의 관리를 위임할 수 있습니다. 기본적으로 예약을 구입한 사용자 및 계정 관리자는 예약에 대한 소유자 역할입니다.

예약 할인을 받은 구독에서 독립적으로 예약에 대한 액세스 권한을 관리할 수 있습니다. 예약을 관리하는 사용 권한을 부여할 때 구독을 관리하는 권한을 제공하는 것은 아닙니다. 또한 누군가에게 예약의 범위 내에서 구독을 관리하는 사용 권한을 부여할 때 예약을 관리하는 권한을 제공하는 것은 아닙니다.

예약에 대한 액세스 관리를 위임하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택하여 액세스할 수 있는 예약을 나열합니다.
3. 다른 사용자에게 액세스 권한을 위임하려는 예약을 선택합니다.
4. **액세스 제어(IAM)** 를 선택합니다.
5. **역할 할당 추가** > **역할** > **소유자**를 선택합니다. 또는 제한된 액세스 권한을 부여하려는 경우 다른 역할을 선택합니다.
6. 소유자로 추가할 사용자의 메일 주소를 입력합니다.
7. 사용자를 선택한 다음 **저장**을 선택합니다.

## <a name="split-a-single-reservation-into-two-reservations"></a>단일 예약을 두 개의 예약으로 분할

 한 예약 내에서 둘 이상의 리소스 인스턴스를 구입한 후에는 예약 내의 인스턴스를 다른 구독에 할당하는 것이 좋습니다. 기본적으로 모든 인스턴스에는 단일 구독 또는 공유 중 하나의 범위가 지정됩니다. 예를 들어 10개의 예약 인스턴스를 구입하고, 범위를 구독 A로 지정했습니다. 이제 7개의 예약 범위를 구독 A로 변경하고 남은 3개를 구독 B로 변경하려고 합니다. 예약을 분할하면 세분화된 범위 관리를 위해 인스턴스를 배포할 수 있습니다. 공유 범위를 선택하여 구독에 대한 할당을 간소화할 수 있습니다. 하지만 비용 관리 또는 예산 목적으로 인해 특정 구독에 수량을 할당할 수 있습니다.

 PowerShell, CLI 또는 API를 통해 예약을 두 개로 분할할 수 있습니다.

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell을 사용하여 예약 분할

1. 다음 명령을 실행하여 예약 주문 ID를 가져옵니다.

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. 예약의 세부 정보를 가져옵니다.

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 예약을 두 개로 분할하고 인스턴스를 배포합니다.

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 다음 명령을 실행하여 범위를 업데이트할 수 있습니다.

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>취소 및 교환

예약 형식에 따라 예약을 취소하거나 교환할 수 있습니다. 자세한 내용은 다음 항목에서 취소 및 교환 섹션을 참조하세요.

- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Azure Reservations에서 SUSE 소프트웨어 요금제에 대한 선불](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Azure SQL Database 예약된 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

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

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure에 대 한 예약 이란?](billing-save-compute-costs-reservations.md)

서비스 계획을 구입 합니다.
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약된 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Cosmos DB 예약된 용량을 사용하여 Azure Cosmos DB 리소스 요금 선결제](../cosmos-db/cosmos-db-reserved-capacity.md)

소프트웨어 플랜을 구입 합니다.
- [Azure 예약에서 Red Hat 소프트웨어 계획에 대 한 요금을 선불합니다](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reservations에서 SUSE 소프트웨어 요금제에 대한 선불](../virtual-machines/linux/prepay-suse-software-charges.md)

할인 및 사용을 이해 합니다.
- [VM 예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [Red Hat Enterprise Linux 소프트웨어 계획 할인이 적용 되는 방식을 이해 합니다.](../billing/billing-understand-rhel-reservation-charges.md)
- [SUSE Linux Enterprise 소프트웨어 요금제 할인이 적용되는 방식 이해](../billing/billing-understand-suse-reservation-charges.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)
