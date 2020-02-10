---
title: Azure 예약 권장 사항을 만드는 방법
description: 가상 머신에 대한 Azure 예약 권장 사항을 만드는 방법을 알아봅니다.
author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/28/2020
ms.author: banders
ms.openlocfilehash: a43e0c4d86bd47c953b50ab9fb1fd8df00e7df3d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76851354"
---
# <a name="how-reservation-recommendations-are-created"></a>예약 권장 사항을 만드는 방법

Azure RI(예약 인스턴스) 구매 권장 사항은 Azure Consumption [예약 권장 사항 API](/rest/api/consumption/reservationrecommendations)에 의해 생성됩니다. API의 권장 사항은 [Azure Advisor](../..//advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)에서도 사용됩니다. Advisor에는 Azure Portal의 권장 사항이 표시됩니다.

Azure에서 실행되는 VM이 있는 경우 RI의 할인된 가격 책정 및 VM에 대한 사전 요금을 활용할 수 있습니다. Microsoft Consumption 권장 사항 API는 7일, 30일 및 60일에 대한 사용량을 평가하고, RI에 대한 최적의 구성을 권장합니다. 비용을 최적화하기 위해 RI를 사용하는 비용에 대해 RI를 사용하지 않은 경우 요금을 지불하는 비용을 계산합니다.

Azure Advisor는 30일 기간을 기준으로 권장 사항을 보여 줍니다.

간단한 설명을 위해 다음 예제에서는 7일 권장 사항에 대해 발생하는 계산을 보여 줍니다. 30일 또는 60일 권장 사항을 계산할 때 동일한 메서드가 적용됩니다.

## <a name="calculation-method-example"></a>Calculation 메서드 예제

특정 SKU 및 지역에 대한 시간별 Windows VM 사용량이 7일(168시간)에 따라 달라지는 것으로 가정합니다. 최소 사용량은 65 단위이며 최대 사용량은 7일 동안 127 단위입니다. 이 예제에서 시간 79는 80개의 VM을 사용하고 75 RI를 구매했습니다.

75개의 예약 인스턴스를 구매하는 경우 시간 79에 대해 다음과 같은 비용이 청구됩니다.

- 75개의 예약 인스턴스 이 비용은 RI를 구입할 때 선불 부과됩니다.
- 예약 인스턴스는 VM을 실행하는 하드웨어 비용을 포함하므로 소프트웨어 전용 가격으로 75시간에 대한 비용을 지불합니다.
- 시간 79에 대한 사용량은 80이므로 Windows의 5시간과 하드웨어 조합 미터 가격에 대한 비용을 지불합니다. 이러한 조합 가격은 EA(기업계약) 또는 종량제 요금을 기준으로 합니다.

75 RI를 구매하는 경우 이전 시간당 비용을 더하여 총 비용을 계산할 수 있습니다. 요금을 사용하여 현재 비용을 계산할 수도 있습니다. 이 예제에서 두 금액의 차이는 7일 동안 절감액입니다.

API는 각 특정 사용량 지점에 대해 계산을 수행합니다. 그런 다음, 절감액이 최대화되는 권장 수량을 반환합니다. 다음 예제에서 그래프는 68에 대한 최대 절감액을 보여 줍니다. 이러한 절감액은 나중에 감소하므로 API에서 68을 권장합니다.

![최대 절감액을 보여 주는 다이어그램](./media/reserved-instance-purchase-recommendations/peak-savings.png)

## <a name="other-expected-api-behavior"></a>기타 예상 API 동작

- API는 혜택을 사용하는 경우 Windows에 대한 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)으로 가능한 절감액을 보여 줍니다. 혜택을 사용하지 않는 경우 API 권장 사항은 핵심 Windows 비용을 기준으로 합니다. 사용할 수 있는 경우 Azure 하이브리드 혜택을 사용하여 절감액을 높이는 것이 좋습니다.
- 7일 간의 조회 기간을 사용하는 경우 VM이 하루 넘게 종료될 때 권장 사항을 얻지 못할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- [Azure 예약 할인이 가상 머신에 적용되는 방법](../manage/understand-vm-reservation-charges.md)에 대해 알아봅니다.
