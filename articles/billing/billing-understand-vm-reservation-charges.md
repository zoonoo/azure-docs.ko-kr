---
title: Azure 예약 인스턴스 할인 이해 | Microsoft Docs
description: 실행 중인 VM에 Azure Reserved Virtual Machine Instances 할인이 적용되는 방식을 알아봅니다.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: yashar
ms.openlocfilehash: d1229a49a5bb3bf3198c91a748ed37b7a626c506
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064471"
---
# <a name="understand-how-the-reserved-instance-discount-is-applied"></a>예약 인스턴스 할인 적용 방법 이해
Azure Reserved VM Instance를 구입하면 예약 인스턴스의 특성 및 수량과 일치하는 가상 머신에 예약 인스턴스 할인이 자동으로 적용됩니다. 예약 인스턴스는 가상 머신의 인프라 비용을 포함합니다. 다음 표에서는 예약 인스턴스를 구입한 후의 가상 머신에 대한 비용을 설명합니다. 모든 경우에는 정상 요금으로 저장소 및 네트워킹에 대한 요금이 청구됩니다.

| 가상 머신 형식  | 예약 인스턴스 요금 |    
|-----------------------|--------------------------------------------|
|추가 소프트웨어가 없는 Linux VM | 예약 인스턴스가 VM 인프라 비용을 포함합니다.|
|소프트웨어 요금이 포함된 Linux VM(예: Red Hat) | 예약 인스턴스가 인프라 비용을 포함합니다. 추가 소프트웨어에 대한 요금이 청구됩니다.|
|추가 소프트웨어가 없는 Windows VM |예약 인스턴스가 인프라 비용을 포함합니다. Windows 소프트웨어에 대한 요금이 청구됩니다.|
|추가 소프트웨어가 있는 Windows VM(예: SQL Server) | 예약 인스턴스가 인프라 비용을 포함합니다. Windows 소프트웨어 및 추가 소프트웨어에 대한 요금이 청구됩니다.|
|[Azure Hybrid Benefit](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)을 포함한 Windows VM | 예약 인스턴스가 인프라 비용을 포함합니다. Windows 소프트웨어 비용은 Azure Hybrid Benefit에 포함되어 있습니다. 추가 소프트웨어는 별도로 청구됩니다.| 

## <a name="application-of-reserved-instance-discount-to-non-windows-vms"></a>비 Windows VM에 대한 예약 인스턴스 할인 적용
 Azure Reserved Instance 할인은 시간 단위로 실행 중인 VM 인스턴스에 적용됩니다. 구입한 예약 인스턴스는 예약 인스턴스 할인을 적용할 실행 중인 VM의 사용량과 일치합니다. 전체 시간을 실행하지 못한 VM의 경우 예약 인스턴스는 현재 실행 중인 VM을 비롯하여 예약 인스턴스를 사용하지 않는 다른 VM에서 채워집니다. 시간이 종료되면 해당 시간의 VM에 대한 예약 인스턴스 적용이 잠깁니다. VM이 한 시간 동안 실행되지 않았거나 시간 내 동시 VM이 예약 인스턴스의 시간을 채우지 않은 경우, 예약 인스턴스가 해당 시간 동안 덜 사용된 것입니다. 다음 그래프는 청구 가능한 VM 사용에 대한 예약 인스턴스 적용을 설명합니다. 이 그림은 예약 인스턴스 구매 1건과 일치하는 VM 인스턴스 2개를 기초로 합니다.

![적용된 하나의 Reserved Instance 및 일치하는 두 개의 VM 인스턴스에 대한 스크린샷](media/billing-reserved-vm-instance-application/billing-reserved-vm-instance-application.png)

1.  예약 인스턴스 선을 초과하는 사용량은 일반 종량제 요금으로 청구됩니다. 예약 인스턴스 선을 초과하지 않는 사용량은 이미 Reserved Instance 구매의 일부로 지불되었기 때문에 청구되지 않습니다.
2.  시간 1 중에 인스턴스 1은 0.75시간 동안 실행되고 인스턴스 2는 0.5시간 동안 실행됩니다. 시간 1의 총 사용량은 1.25시간입니다. 나머지 0.25시간에 대한 종량제 요금이 청구됩니다.
3.  시간 2 및 시간 3의 경우 두 인스턴스는 1시간 동안 실행되었습니다. 하나의 인스턴스는 예약 인스턴스에 포함되고 다른 하나는 종량제 요금으로 요금이 부과됩니다.
4.  시간 4의 경우 인스턴스 1은 0.5시간 동안 실행되고 인스턴스 2는 1시간 동안 실행됩니다. 인스턴스 1은 예약 인스턴스에 완전히 포함되고 인스턴스 2 중 0.5시간도 포함됩니다. 나머지 0.5시간에 대한 종량제 요금이 청구됩니다.

청구 사용량 보고서에서 Azure 예약 인스턴스 적용을 이해하고 보려면 [예약 인스턴스 사용량 이해](https://go.microsoft.com/fwlink/?linkid=862757)를 참조하세요.

## <a name="application-of-reserved-instance-discount-to-windows-vms"></a>Windows VM에 예약 인스턴스 할인 적용
Windows VM 인스턴스를 실행하면 인프라 비용을 포함하도록 예약 인스턴스가 적용됩니다. Windows VM의 VM 인프라 비용에 예약 인스턴스를 적용하는 것은 Windows 외 VM의 경우와 동일합니다. vCPU를 기준으로 Windows 소프트웨어에 별도로 청구됩니다. [예약 인스턴스에서 Windows 소프트웨어 비용](https://go.microsoft.com/fwlink/?linkid=862756)을 참조하세요. [Windows Server용 Azure 하이브리드 혜택](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing))으로 Windows 라이선스 비용을 처리할 수 있습니다.

## <a name="next-steps"></a>다음 단계
예약 인스턴스에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Reserved VM Instances란?](billing-save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure에서 예약 인스턴스 관리](billing-manage-reserved-vm-instance.md)
- [예약 인스턴스 할인이 적용되는 방식 이해](billing-understand-vm-reservation-charges.md)
- [종량제 구독에서 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage.md)
- [기업 등록계약의 예약 인스턴스 사용량 이해](billing-understand-reserved-instance-usage-ea.md)
- [CSP 구독에서 예약 인스턴스 사용량 이해](https://docs.microsoft.com/partner-center/azure-reservations)
- [예약 인스턴스를 포함하지 않는 Windows 소프트웨어 비용](billing-reserved-instance-windows-software-costs.md)


## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

계속해서 다른 질문이 있는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
