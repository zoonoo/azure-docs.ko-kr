---
title: Azure 가상 컴퓨터 네트워크 처리량 | Microsoft Docs
description: Azure 가상 머신 네트워크 처리량에 대해 알아봅니다.
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
ms.author: kumud,steveesp, mareat
ms.openlocfilehash: 9d74e53c754367ecfa63642514db93354fcadf25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153719"
---
# <a name="virtual-machine-network-bandwidth"></a>가상 머신 네트워크 대역폭

Azure는 다양한 VM 크기 및 유형, 다양한 성능 기능을 제공합니다. 한 가지 기능은 네트워크 처리량(또는 대역폭)이며 Mbps(초당 메가비트)로 측정됩니다. 가상 머신은 공유 하드웨어에서 호스트되므로 동일한 하드웨어를 공유하는 가상 머신 간에 네트워크 용량을 공평하게 공유해야 합니다. 용량이 큰 가상 머신은 작은 가상 머신보다 상대적으로 더 많은 대역폭이 할당됩니다.
 
각 가상 머신에 할당된 네트워크 대역폭은 가상 머신에서의 송신(아웃바운드) 트래픽으로 측정됩니다. 가상 컴퓨터에서 나가는 모든 네트워크 트래픽은 대상에 관계없이 할당된 제한에 대해 계산됩니다. 예를 들어 가상 머신에 1,000Mbps 제한이 있는 경우 해당 제한은 송신 트래픽이 동일한 가상 네트워크의 다른 가상 머신을 대상으로 하는지 또는 Azure 외부의 다른 가상 머신을 대상으로 하는지에 관계없이 적용됩니다.
 
수신은 측정되거나 직접 제한되지 않습니다. 그러나 들어오는 데이터를 처리하는 가상 머신의 기능에 영향을 줄 수 있는 다른 요인(예: CPU 및 저장소 제한)이 있습니다.

가속화된 네트워킹은 대기 시간, 처리량 및 CPU 사용률을 포함하여 네트워크 성능을 향상시키기 위해 설계된 기능입니다. 가속화된 네트워킹은 가상 머신의 처리량을 향상시킬 수 있지만 가상 머신에 할당된 최대 대역폭까지만 가능합니다. 가속 네트워킹에 대한 자세한 내용은 [Windows](create-vm-accelerated-networking-powershell.md) 또는 [Linux](create-vm-accelerated-networking-cli.md) 가상 머신에 대한 가속 네트워킹을 참조하세요.
 
Azure 가상 머신은 하나지만 여기에 연결된 네트워크 인터페이스는 여러 개일 수 있습니다. 가상 머신에 할당된 대역폭은 가상 머신에 연결된 모든 네트워크 인터페이스에서 모든 아웃바운드 트래픽의 총 합계입니다. 즉, 대역폭은 가상 컴퓨터에 연결된 네트워크 인터페이스 수에 관계없이 가상 컴퓨터별로 할당됩니다. 다양한 Azure VM 크기가 지원하는 네트워크 인터페이스 수를 알아보려면 [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기를 참조하세요. 

## <a name="expected-network-throughput"></a>예상 네트워크 처리량

각 VM 크기별로 지원되는 네트워크 인터페이스 수 및 예상 아웃바운드 처리량은 Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 및 [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 크기에 자세히 나와 있습니다. 형식(예: 범용)을 선택한 다음 결과 페이지에서 크기 계열(예: Dv2 계열)을 선택합니다. 각 계열에는 **최대 NIC / 예상 네트워크 성능(Mbps)** 이라는 마지막 열에 네트워킹 사양이 포함된 표가 있습니다. 

처리량 제한은 가상 컴퓨터에 적용됩니다. 처리량은 다음 요인에 영향을 받지 않습니다.
- **네트워크 인터페이스 수가**: 대역폭 제한은 가상 머신에서 모든 아웃 바운드 트래픽의 누적입니다.
- **가속화 된 네트워킹**: 기능 게시 된 제한에 달성 하는 데 도움이 될 수 있습니다, 있지만 한계는 변경 되지 않습니다.
- **트래픽 대상**: 아웃 바운드 제한에 대해 모든 대상 수입니다.
- **프로토콜**: 모든 프로토콜을 통한 모든 아웃 바운드 트래픽을 제한에 대해 계산 됩니다.

## <a name="network-flow-limits"></a>네트워크 흐름 제한

대역폭을 외에도 언제 든 지 VM에 있는 네트워크 연결 수가 네트워크 성능을 달라질 수 있습니다. Azure 네트워킹 스택에서 '흐름'를 호출 하는 데이터 구조에서 TCP/UDP 연결의 각 방향에 대 한 상태를 유지 관리 합니다. 일반 TCP/UDP 연결을 생성, 인바운드 및 아웃 바운드 방향에 대해 다른 2 개의 흐름을 갖게 됩니다. 

끝점 간의 데이터 전송의 데이터 전송을 수행 하는 것 외에도 몇 가지 흐름 만들기에 필요 합니다. 몇 가지 예로 DNS 확인을 위해 만든 흐름 및 부하 분산 장치 상태 프로브에 대 한 만든 흐름. 또한 네트워크 게이트웨이, 프록시, 방화벽 가상 어플라이언스 (Nva)는 기기에서 종료 되 고 어플라이언스에 의해 시작 된 연결에 대해 생성 되는 흐름 표시 됩니다. 

![전달 어플라이언스를 통해 TCP 대화에 대 한 흐름 수](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>흐름 제한 및 권장 사항

현재 Azure 네트워킹 스택에서 250k 총 네트워크 흐름 지원 뛰어난 성능으로 Vm에 대 한 CPU 코어가 8 개 CPU 코어가 8 개 미만의 개를 사용 하 여 Vm에 대 한 뛰어난 성능으로 k 총 흐름 100 보다 크고를 사용 하 여 합니다. 이 제한 네트워크 지난 성능이 저하 됩니다 정상적으로 추가 흐름 1m의 하드 한계까지 총 인바운드 및 500 500k 흐름에 대 한 추가 배치 되는 삭제 후 아웃 바운드 K.

||사용 하 여 Vm < CPU 코어가 8 개|8 + CPU 코어를 사용 하 여 Vm|
|---|---|---|
|<b>고성능</b>|100k 흐름 |25만 개 흐름|
|<b>성능 저하</b>|100k 위에서 흐름|250k 위에서 흐름|
|<b>흐름 제한</b>|1m 흐름|1m 흐름|

사용할 수 있는 메트릭이 [Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) VM 또는 VMSS 인스턴스에서 네트워크 흐름과 흐름 만들기 속도의 수를 추적 하 합니다.

![azure-monitor-flow-metrics.png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

연결 설정 및 종료 요금도 연결 설정 및 종료 공유로 CPU 패킷 처리 루틴을 사용 하 여 네트워크 성능에 영향을 줄 수 있습니다. 수 작업 부하의 벤치 예상된 트래픽 패턴 및 워크 로드 확장에 대해 적절 하 게 성능 요구 사항을 충족 하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

- [가상 머신 운영 체제에 대한 네트워크 처리량 최적화](virtual-network-optimize-network-bandwidth.md)
- 가상 머신에 대한 [네트워크 처리량 테스트](virtual-network-bandwidth-testing.md)
