---
title: Azure 예약 Windows 소프트웨어 비용 | Microsoft Docs
description: Azure Reserve VM Instance 비용에 포함되지 않은 Windows 소프트웨어 미터에 대해 알아봅니다.
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
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: ec4869ed5c4d823a8144ba430ebb29c65c2abc13
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302069"
---
# <a name="windows-software-costs-not-included-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances에 포함되지 않는 Windows 소프트웨어 비용

예약 가상 머신 인스턴스에 Azure Hybrid Use Benefit이 없는 경우, 다음 섹션에 나열된 Windows 소프트웨어 요금제에 대한 요금이 청구됩니다.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows 소프트웨어 요금제는 예약 요금에 포함되지 않습니다.

| MeterId | 사용량 파일의 MeterName | VM별 사용 |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Burst(1 코어) | B 시리즈 |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Burst(2 코어) | B 시리즈 |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Burst(4 코어) | B 시리즈 |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Burst(8 코어) | B 시리즈 |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr(1 코어) | B 시리즈를 제외한 모두 |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr(2 코어) | B 시리즈를 제외한 모두 |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr(4 코어) | B 시리즈를 제외한 모두 |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr(6 코어) | B 시리즈를 제외한 모두 |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr(8 코어) | B 시리즈를 제외한 모두 |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr(12 코어) | B 시리즈를 제외한 모두 |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr(16 코어) | B 시리즈를 제외한 모두 |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr(20 코어) | B 시리즈를 제외한 모두 |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr(24 코어) | B 시리즈를 제외한 모두 |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr(32 코어) | B 시리즈를 제외한 모두 |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr(40 코어) | B 시리즈를 제외한 모두 |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr(64 코어) | B 시리즈를 제외한 모두 |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr(72 코어) | B 시리즈를 제외한 모두 |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr(128 코어) | B 시리즈를 제외한 모두 |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr(256 코어) | B 시리즈를 제외한 모두 |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr(96 코어) | B 시리즈를 제외한 모두 |

Azure RateCard API를 통해 이러한 미터 각각의 비용을 알아볼 수 있습니다. Azure 미터에 대한 요금을 알아보는 방법에 대한 정보는 [Azure 구독에서 사용되는 리소스에 대한 가격 및 메타데이터 정보 가져오기](https://msdn.microsoft.com/library/azure/mt219004)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.



