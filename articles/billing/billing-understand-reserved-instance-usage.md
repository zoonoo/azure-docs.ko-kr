---
title: 종량제 구독의 Azure 예약 사용량 이해 | Microsoft Docs
description: 종량제 구독에서 Azure Reservations의 적용 방법을 이해하기 위해 사용량을 판독하는 방법을 알아봅니다.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: manshuk
ms.openlocfilehash: 1226b2f73d556da2ff7d73f6f322e0bd1590f915
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307061"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>종량제 구독의 Azure Reservations 사용량 이해

[예약 페이지](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)의 ReservationId 및 [Azure 계정 포털](https://account.azure.com)의 사용량 파일을 사용하여 예약 사용량을 평가합니다.

기업계약이 있는 고객인 경우 [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)를 참조하세요.

이 문서는 예약이 단일 구독에 적용된다고 가정합니다. 예약이 둘 이상의 구독에 적용되면 예약 혜택이 여러 사용량 CSV 파일에 걸쳐 있을 수 있습니다.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Reserved Virtual Machines Instances에 대한 사용량

다음 섹션에서는 미국 동부 지역에서 Standard_DS1_v2 Windows VM을 실행하고 예약 VM 인스턴스 정보가 다음 표와 같다고 가정합니다.

| 필드 | 값 |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|수량 |1|
|SKU | Standard_DS1_v2|
|지역 | eastus |

배포된 VM이 예약 특성과 일치하기 때문에 VM의 하드웨어 부분이 포함됩니다. Reserved VM Instance에서 포함되지 않는 Windows 소프트웨어를 확인하려면 [Azure Reserve VM Instances Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)을 참조하세요.

### <a name="statement-section-of-csv-file-for-vms"></a>VM에 대한 CSV 파일의 명령문 섹션

CSV 파일의 이 섹션에서는 예약에 대한 총 사용량을 보여 줍니다. **“예약-”** 을 포함하는 **요금제 하위 범주** 필드에 필터를 적용합니다. 다음 스크린샷과 유사한 출력이 표시됩니다.

![필터링된 예약 사용량 세부 정보 및 비용의 스크린샷](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

**예약 - 기본 VM** 줄에는 예에 포함되는 총 시간이 있습니다. 이 줄은 예약에 포함되기 때문에 $0.00입니다. **Reservation-Windows Svr(1개 코어)** 줄은 Windows 소프트웨어의 비용을 포함합니다.

### <a name="daily-usage-section-of-csv-file"></a>CSV 파일의 일일 사용량 섹션

**추가 정보**를 필터링하고 **Reservation ID**를 입력합니다. 다음 스크린샷에서는 예약과 관련된 필드를 보여줍니다.

![일일 사용량 세부 정보 및 요금 스크린샷](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **추가 정보** 필드의 **ReservationId**는 VM에 적용된 예약입니다.
2. **ConsumptionMeter**는 VM에 대한 요금제 ID입니다.
3. **예약 - 기본 VM** **요금제 하위 범주** 줄은 명령문 섹션에 있는 $0인 비용을 나타냅니다. 예약에서 이미 이 VM 실행 비용을 지불했습니다.
4. **요금제 ID**는 예약에 대한 요금제 ID입니다. 이 요금제의 비용은 $0입니다. 이 요금제 ID는 예약 할인을 받을 수 있는 VM에 표시됩니다.
5. Standard_DS1_v2는 하나의 vCPU VM이며 해당 VM은 Azure Hybrid Benefit 없이 배포됩니다. 따라서 이 요금제는 Windows 소프트웨어의 추가 요금을 포함합니다. D 시리즈 1개 코어 VM에 해당하는 요금제를 찾으려면 [Azure Reserve VM Instances Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)을 참조하세요. Azure 하이브리드 혜택이 있는 경우 이 추가 비용이 적용되지 않습니다.

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>SQL Database 예약된 용량 예약에 대한 사용량

다음 섹션에서는 미국 동부 지역에서 SQL Database Gen 4를 실행하고 예약 정보가 다음 표와 같다고 가정합니다.

| 필드 | 값 |
|---| --- |
|ReservationId |446ec809-423d-467c-8c5c-bbd5d22906b1|
|수량 |2|
|제품| SQL Database Gen 4(2개 코어)|
|지역 | eastus |

### <a name="statement-section-of-csv-file"></a>CSV 파일의 명령문 섹션

**예약된 인스턴스 사용량** 요금제 이름을 필터링합니다. 다음 스크린샷과 유사한 출력이 표시됩니다.

![SQL Database 예약된 용량에 대한 CSV 파일](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

**예약된 인스턴스 사용량** 줄에는 예약에 포함되는 총 코어 시간이 있습니다. 예약에서 비용을 포함하므로 이 줄에 대한 요금은 $0입니다.

### <a name="detail-section-of-csv-file"></a>CSV 파일의 세부 정보 섹션

**추가 정보**를 필터링하고 **Reservation ID**를 입력합니다. 다음 스크린샷은 SQL Database 예약된 용량 예약과 관련된 필드를 보여 줍니다.

![SQL Database 예약된 용량에 대한 CSV 파일](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **추가 정보** 필드의 **ReservationId**는 SQL Database 리소스에 적용되는 SQL Database 예약된 용량 예약입니다.
2. **ConsumptionMeter**는 SQL Database 리소스에 대한 요금제 ID입니다.
3. **요금제 ID**는 예약 요금제입니다. 이 요금제의 비용은 $0입니다. 예약 할인을 받을 수 있는 모든 SQL Database 리소스는 이 요금제 ID를 CSV 파일로 표시합니다.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약된 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [예약에 포함되지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
