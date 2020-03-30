---
title: 리눅스 VM공동 배치
description: Azure VM 리소스를 공동 배치하면 대기 시간을 개선하는 방법에 대해 알아봅니다.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250284"
---
# <a name="co-locate-resources-for-improved-latency"></a>대기 시간 개선을 위한 리소스 공동 배치

Azure에서 응용 프로그램을 배포할 때 지역 또는 가용성 영역에 인스턴스를 분산하면 네트워크 대기 시간이 생성되어 응용 프로그램의 전반적인 성능에 영향을 미칠 수 있습니다. 

## <a name="proximity-placement-groups"></a>근접 배치 그룹

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 [근접 배치 그룹에](proximity-placement-groups.md) VM을 배포합니다.

[네트워크 대기 시간을 테스트하는](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)방법에 대해 알아봅니다.

[네트워크 처리량을 최적화하는](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)방법에 대해 알아봅니다.  

[SAP 응용 프로그램에서 근접 배치 그룹을 사용하는](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)방법에 대해 알아봅니다.
