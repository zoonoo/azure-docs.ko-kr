---
title: Azure 가상 컴퓨터 네트워크 처리량 | Microsoft Docs
description: 가상 머신에 대역폭을 할당하는 방법을 포함하여 Azure 가상 머신 네트워크 처리량에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: cb128f9269895f04d1e0dad8e0c8d06c481e86c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576172"
---
# <a name="virtual-machine-network-bandwidth"></a>가상 머신 네트워크 대역폭

Azure는 다양한 VM 크기 및 유형, 다양한 성능 기능을 제공합니다. 한 가지 기능은 네트워크 처리량(또는 대역폭)이며 Mbps(초당 메가비트)로 측정됩니다. 가상 머신은 공유 하드웨어에서 호스트되므로 동일한 하드웨어를 공유하는 가상 머신 간에 네트워크 용량을 공평하게 공유해야 합니다. 용량이 큰 가상 머신은 작은 가상 머신보다 상대적으로 더 많은 대역폭이 할당됩니다.
 
각 가상 머신에 할당된 네트워크 대역폭은 가상 머신에서의 송신(아웃바운드) 트래픽으로 측정됩니다. 가상 컴퓨터에서 나가는 모든 네트워크 트래픽은 대상에 관계없이 할당된 제한에 대해 계산됩니다. 예를 들어 가상 머신에 1,000Mbps 제한이 있는 경우 해당 제한은 송신 트래픽이 동일한 가상 네트워크의 다른 가상 머신을 대상으로 하는지 또는 Azure 외부의 다른 가상 머신을 대상으로 하는지에 관계없이 적용됩니다.
 
수신은 측정되거나 직접 제한되지 않습니다. 그러나 들어오는 데이터를 처리하는 가상 머신의 기능에 영향을 줄 수 있는 다른 요인(예: CPU 및 스토리지 제한)이 있습니다.

가속화된 네트워킹은 대기 시간, 처리량 및 CPU 사용률을 포함하여 네트워크 성능을 향상시키기 위해 설계된 기능입니다. 가속화된 네트워킹은 가상 머신의 처리량을 향상시킬 수 있지만 가상 머신에 할당된 최대 대역폭까지만 가능합니다. 가속 네트워킹에 대한 자세한 내용은 [Windows](create-vm-accelerated-networking-powershell.md) 또는 [Linux](create-vm-accelerated-networking-cli.md) 가상 머신에 대한 가속 네트워킹을 참조하세요.
 
Azure 가상 머신은 하나지만 여기에 연결된 네트워크 인터페이스는 여러 개일 수 있습니다. 가상 머신에 할당된 대역폭은 가상 머신에 연결된 모든 네트워크 인터페이스에서 모든 아웃바운드 트래픽의 총 합계입니다. 즉, 대역폭은 가상 컴퓨터에 연결된 네트워크 인터페이스 수에 관계없이 가상 컴퓨터별로 할당됩니다. 다양한 Azure VM 크기가 지원하는 네트워크 인터페이스 수를 알아보려면 [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기를 참조하세요. 

## <a name="expected-network-throughput"></a>예상 네트워크 처리량

각 VM 크기별로 지원되는 네트워크 인터페이스 수 및 예상 아웃바운드 처리량은 Azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기에 자세히 나와 있습니다. 형식(예: 범용)을 선택한 다음 결과 페이지에서 크기 계열(예: Dv2 계열)을 선택합니다. 각 계열에는 **최대 NIC / 예상 네트워크 성능(Mbps)** 이라는 마지막 열에 네트워킹 사양이 포함된 표가 있습니다. 

처리량 제한은 가상 컴퓨터에 적용됩니다. 처리량은 다음 요인에 영향을 받지 않습니다.
- **네트워크 인터페이스 수**: 대역폭 제한은 가상 머신에서 모든 아웃바운드 트래픽의 누적입니다.
- **가속화된 네트워킹**: 게시된 제한에 도달하는 데 유용한 기능이지만 제한을 변경하지는 않습니다.
- **트래픽 대상**: 모든 대상이 아웃바운드 제한에 대해 계산됩니다.
- **프로토콜**: 모든 프로토콜을 통한 모든 아웃바운드 트래픽이 제한에 대해 계산됩니다.

## <a name="network-flow-limits"></a>네트워크 흐름 한도

대역폭 외에도 특정 시간에 VM에 있는 네트워크 연결 수가 네트워크 성능에 영향을 줄 수 있습니다. Azure 네트워킹 스택은 '흐름'이라는 데이터 구조로 TCP/UDP 연결 각 방향의 상태를 유지 관리합니다. 일반적인 TCP/UDP 연결에서는 인바운드 방향 하나와 아웃바운드 방향 하나의 흐름 2개가 생성됩니다. 

엔드포인트 간 데이터 전송을 위해서는 데이터 전송을 수행하는 흐름 외에도 여러 흐름을 만들어야 합니다. 몇 가지 예를 들면 DNS 확인을 위해 생성되는 흐름과 부하 분산 장치 상태 프로브를 위해 생성되는 흐름이 있습니다. 또한 게이트웨이, 프록시, 방화벽과 같은 NVA(네트워크 가상 어플라이언스)에는 어플라이언스에서 종료되고 어플라이언스에 의해 시작되는 연결을 위해 생성되는 흐름이 있습니다. 

![전달 어플라이언스를 통한 TCP 대화의 흐름 수](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-active-connections-recommendations"></a>흐름 한도 및 활성 연결 권장 사항

현재 Azure 네트워킹 스택은 VM당 100만 개의 총 흐름(50만 개 인바운드 및 50만 개 아웃바운드)을 지원합니다. 다양한 시나리오에서 VM이 처리할 수 있는 총 활성 연결은 다음과 같습니다.
- VNET에 속한 VM은 VM 크기에 상관없이 50만 개의 **활성 연결** 과 _각 방향의 활성 흐름_ 50만 개를 처리할 수 있습니다.  
- 게이트웨이, 프록시, 방화벽 같은 NVA(네트워크 가상 어플라이언스)가 있는 VM은 위의 다이어그램에 나온 것처럼 다음 홉으로의 전달 및 새 연결 설정에서의 추가적인 새 흐름 생성으로 인해 25만 개의 **활성 연결** 과 *_각 방향의 활성 흐름_* 50만 개를 처리할 수 있습니다. 

이 한도에 도달하면 추가 연결이 삭제됩니다. 연결 설정과 종료는 CPU를 패킷 처리 루틴과 공유하기 때문에 연결 설정 및 종료 속도도 네트워크 성능에 영향을 줄 수 있습니다. 예상되는 트래픽 패턴과 비교하여 워크로드를 벤치마크하고 성능 요구 사항에 맞게 워크로드를 적절히 스케일 아웃하는 것이 좋습니다.

[Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines)에서 메트릭을 사용하여 VM 또는 VMSS 인스턴스의 네트워크 흐름 수와 흐름 생성률을 추적할 수 있습니다.

![꺾은선형 차트와 인바운드 및 아웃바운드 흐름 합계가 있는 Azure Monitor의 메트릭 페이지를 보여 주는 스크린샷.](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

연결 설정과 종료는 CPU를 패킷 처리 루틴과 공유하기 때문에 연결 설정 및 종료 속도도 네트워크 성능에 영향을 줄 수 있습니다. 예상되는 트래픽 패턴과 비교하여 워크로드를 벤치마크하고 성능 요구 사항에 맞게 워크로드를 적절히 스케일 아웃하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

- [가상 머신 운영 체제에 대한 네트워크 처리량 최적화](virtual-network-optimize-network-bandwidth.md)
- 가상 머신에 대한 [네트워크 처리량 테스트](virtual-network-bandwidth-testing.md)