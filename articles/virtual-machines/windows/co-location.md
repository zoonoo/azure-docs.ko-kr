---
title: Windows Azure Vm 공동 배치
description: Azure VM 리소스를 공동 배치 하 여 대기 시간을 개선할 수 있는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374813"
---
# <a name="co-locate-resource-for-improved-latency"></a>대기 시간 향상을 위한 리소스 배치

Azure에서 응용 프로그램을 배포할 때 지역 또는 가용성 영역에 인스턴스를 분산 시키면 네트워크 대기 시간이 만들어지므로 응용 프로그램의 전반적인 성능에 영향을 줄 수 있습니다. 


## <a name="proximity-placement-groups"></a>근접 배치 그룹 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>다음 단계

Azure PowerShell를 사용 하 여 [근접 배치 그룹](proximity-placement-groups.md) 에 VM을 배포 합니다.

[네트워크 대기 시간을 테스트](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법을 알아봅니다.

[네트워크 처리량을 최적화](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법을 알아봅니다.  

[SAP 응용 프로그램에서 근접 배치 그룹을 사용](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법에 대해 알아봅니다.