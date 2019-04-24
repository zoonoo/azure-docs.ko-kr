---
title: 엔터프라이즈용 Azure 예약 사용량 이해 | Microsoft Docs
description: Enterprise 등록에서 Azure Reservation의 적용 방법을 이해하기 위해 사용량을 판독하는 방법을 알아봅니다.
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
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: daa7f6a116578fa8d1f2b5bf825a6f4cd48f7f64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370699"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>엔터프라이즈 등록에서 Azure Reservation 사용량 이해

[예약 페이지](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade)의 **ReservationId** 및 [EA 포털](https://ea.azure.com)의 사용량 파일을 사용하여 예약 사용량을 평가합니다. [EA 포털](https://ea.azure.com)의 사용량 요약 섹션에서도 예약 사용량을 확인할 수 있습니다.

종량제 청구 컨텍스트에서 예약을 구매한 경우 [종량제 구독에서 예약 사용량 이해](billing-understand-reserved-instance-usage.md)를 참조하세요.

## <a name="usage-for-reserved-virtual-machines-instances"></a>Reserved Virtual Machines Instances에 대한 사용량

다음 섹션에서는 미국 동부 지역에서 Standard_D1_v2 Windows VM을 실행하고 예약 정보가 다음 표와 같다고 가정합니다.

| 필드 | Value |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|수량 |1|
|SKU | Standard_D1|
|지역 | eastus |

배포된 VM이 예약 특성과 일치하기 때문에 VM의 하드웨어 부분이 포함됩니다. 예약에 포함되지 않는 Windows 소프트웨어를 확인하려면 [Azure Reserve VM Instances Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)을 참조하세요.

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Reserved VM Instances에 대해 CSV 파일에서 사용

엔터프라이즈 포털에서 Enterprise 사용량 CSV 파일을 다운로드할 수 있습니다. CSV 파일에서 **추가 정보**를 필터링하고 **ReservationID**를 입력합니다. 다음 스크린샷에서는 예약과 관련된 필드를 보여줍니다.

![Azure Reservation에 대한 EA(기업계약) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **추가 정보** 필드의 **ReservationId**는 VM에 적용된 예약을 나타냅니다.
2. **ConsumptionMeter**는 VM에 대한 요금제 ID입니다.
3. **요금제 ID**는 $0 비용의 예약 요금제입니다. 실행 중인 VM의 비용은 예약 VM 인스턴스로 지불됩니다.
4. Standard_D1은 하나의 vCPU VM이며 해당 VM은 Azure 하이브리드 혜택 없이 배포됩니다. 따라서 이 요금제는 Windows 소프트웨어의 추가 요금을 포함합니다. D 시리즈 1 코어 VM에 해당하는 요금제를 찾으려면 [Azure Reserve VM Instances Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)을 참조하세요.  Azure 하이브리드 혜택이 있는 경우 이 추가 비용이 적용되지 않습니다.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>SQL Database 및 Cosmos DB 예약된 용량 예약의 사용량

다음 섹션에서는 Azure SQL Database를 예로 사용하여 사용 현황 보고서를 설명합니다. 동일한 단계를 사용하여 Azure Cosmos DB의 사용 현황도 확인할 수 있습니다.

미국 동부 지역에서 SQL Database Gen 4를 실행하고 예약 정보가 다음 표와 같다고 가정합니다.

| 필드 | Value |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|수량 |2|
|제품| SQL Database Gen 4(2개 코어)|
|지역 | eastus |

### <a name="usage-in-csv-file"></a>CSV 파일에서 사용

**추가 정보**로 필터링하고, **예약 ID**를 입력하고, 필수 **미터 범주**(Azure SQL Database 또는 Azure Cosmos DB)를 선택합니다. 다음 스크린샷에서는 예약과 관련된 필드를 보여줍니다.

![SQL Database 예약된 용량에 대한 EA(기업계약) csv](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **추가 정보** 필드의 **ReservationId**는 SQL Database 리소스에 적용된 예약입니다.
2. **ConsumptionMeter**는 SQL Database 리소스에 대한 요금제 ID입니다.
3. **요금제 ID**는 $0 비용의 예약 요금제입니다. 예약이 가능한 모든 SQL Database 리소스는 이 요금제 ID를 CSV 파일로 표시합니다.

## <a name="usage-summary-page-in-enterprise-portal"></a>엔터프라이즈 포털의 사용량 요약 페이지

Azure 예약 사용량은 엔터프라이즈 포털의 사용량 요약 섹션에도 표시됩니다. ![EA(기업계약) 사용량 요약](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. VM의 하드웨어 구성 요소는 예약에서 처리되므로 이에 대해서는 청구되지 않습니다. SQL Database 예약의 경우 Azure SQL Database 예약된 용량 사용량으로 **서비스 이름**이 포함된 줄이 표시됩니다.
2. 이 예제에서는 Azure 하이브리드 혜택이 없으므로 VM과 함께 사용되는 Windows 소프트웨어에 대한 요금이 부과됩니다.

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

