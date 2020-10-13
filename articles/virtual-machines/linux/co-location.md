---
title: Linux Vm 공동 배치
description: Linux 용 Azure VM 리소스를 공동 배치 하 여 대기 시간을 개선할 수 있는 방법에 대해 알아봅니다.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 822d67211982526bdd7a36cb5922f33b448eb7c7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973379"
---
# <a name="co-locate-resources-for-improved-latency"></a>대기 시간 향상을 위한 리소스 공동 배치

Azure에서 응용 프로그램을 배포할 때 지역 또는 가용성 영역에 인스턴스를 분산 시키면 네트워크 대기 시간이 만들어지므로 응용 프로그램의 전반적인 성능에 영향을 줄 수 있습니다. 

## <a name="proximity-placement-groups"></a>근접 배치 그룹

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용 하 여 [근접 배치 그룹](proximity-placement-groups.md) 에 VM을 배포 합니다.

[네트워크 대기 시간을 테스트](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json)하는 방법을 알아봅니다.

[네트워크 처리량을 최적화](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하는 방법을 알아봅니다.  

[SAP 응용 프로그램에서 근접 배치 그룹을 사용](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)하는 방법에 대해 알아봅니다.