---
title: Azure SQL Database에 대 한 예약 할인을 이해 | Microsoft Docs
description: Azure SQL Database를 실행 하는 예약 할인은 적용 하는 방법에 대해 알아봅니다.
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 4b4c6b390e9b3a0cf764f998523fe3c1cdc66026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370290"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-databases"></a>Azure SQL Database에 예약 할인은 적용 하는 방법

Azure SQL Database 예약된 용량을 구입한 후 예약 할인이 예약의 특성 및 수량과 일치하는 SQL Database에 자동으로 적용됩니다. 예약은 SQL Database의 계산 비용을 포함합니다. 정상 요금으로 소프트웨어, 저장소 및 네트워킹에 대한 요금이 청구됩니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 SQL Database에 대한 라이선스 비용을 처리할 수 있습니다.

Reserved Virtual Machine Instances의 경우 [Azure Reserved VM Instances 할인 이해](billing-understand-vm-reservation-charges.md)를 참조하세요.

## <a name="how-reservation-discount-is-applied"></a>예약 할인은 적용 하는 방법

예약 할인은 "*사용 하 여-it 또는-손실-it*"입니다. 따라서 모든 시간에 대 한 일치 하는 리소스 없다면, 손실 된 경우 예약 수량을 해당 시간에 대 한 합니다. 수행할 수 없습니다. 사용 되지 않는 예약 된 시간을 전달 합니다.

리소스를 종료 하면 예약 할인은 지정된 된 범위에서 일치 하는 다른 리소스에 자동으로 적용 됩니다. 일치 하는 리소스가 없는 지정된 된 범위에 있는 경우 예약 된 시간은 *손실*합니다.

## <a name="discount-applied-to-sql-databases"></a>SQL Database에 적용 되는 할인

 SQL Database 예약된 용량 할인은 매시간으로 실행 중인 SQL Database에 적용됩니다. 구입하는 예약은 실행 중인 SQL Database에서 내보낸 계산 사용량과 일치합니다. 전체 시간 동안 실행되지 않는 SQL Database의 경우 예약 특성과 일치하는 다른 SQL Database에 예약이 자동으로 적용됩니다. 할인은 동시에 실행되는 SQL Database에 적용할 수 있습니다. 예약 특성과 일치하는 전체 시간 동안 실행되는 SQL Database가 없는 경우 해당 시간 동안 예약 할인의 모든 이점을 활용할 수 없습니다.

다음 예제에서는 구입한 코어 수 및 실행되는 시기에 따라 SQL Database 예약된 용량 할인이 적용되는 방법을 보여줍니다.

- 시나리오 1: 8개 코어 SQL Database에 대한 SQL Database 예약 용량을 구입합니다. 예약의 나머지 특성과 일치하는 16코어 SQL Database를 실행합니다. SQL Database 계산 사용량의 8개 코어에 대한 종량제 가격으로 요금이 청구됩니다. 8코어 SQL Database 계산 사용량의 1시간 동안 예약 할인을 받게 됩니다.

이러한 예제의 나머지의 경우 구입하는 SQL Database 예약된 용량이 16코어 SQL Database에 대한 것이며 나머지 예약 특성이 실행 중인 SQL Database와 일치한다고 가정합니다.

- 시나리오 2: 두 개의 8개 코어 SQL Database를 각각 1시간 동안 실행합니다. 16코어 예약 할인은 8개의 코어 SQL Database 모두에 대한 계산 사용량에 적용됩니다.
- 시나리오 3: 하나의 16개 코어 SQL Database를 오후 1시에서 오후 1시 30분까지 실행합니다. 오후 1시 30분에서 오후 2시까지 다른 16코어 SQL Database를 실행합니다. 둘 다 예약 할인에 포함됩니다.
- 시나리오 4: 하나의 16개 코어 SQL Database를 오후 1시에서 오후 1시 45분까지 실행합니다. 오후 1시 30분에서 오후 2시까지 다른 16코어 SQL Database를 실행합니다. 15분의 중복에 대한 종량제 가격으로 요금이 청구됩니다. 예약 할인은 나머지 시간에 대한 계산 사용량에 적용됩니다.

청구 사용량 보고서에서 Azure 예약의 애플리케이션을 이해하고 보려면 [Azure 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

문의 사항이 있거나 도움이 필요한 경우 [지원 요청을 만드는](https://go.microsoft.com/fwlink/?linkid=2083458)합니다.

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 계산 리소스 요금 선결제](../sql-database/sql-database-reserved-capacity.md)
- [Azure Reservations 관리](billing-manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [엔터프라이즈 등록의 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [CSP 구독의 예약 사용량 이해](/partner-center/azure-reservations)
