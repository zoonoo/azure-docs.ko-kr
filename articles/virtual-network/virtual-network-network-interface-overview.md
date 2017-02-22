---
title: "Azure의 네트워크 인터페이스 | Microsoft Docs"
description: "Azure Resource Manager 배포 모델에서 Azure 네트워크 인터페이스에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 3244d5b52785d820698bf26f9bf189de93ef64e4
ms.openlocfilehash: 691b79d7739246dad7191195fa049fd58340c8ff


---
# <a name="network-interfaces-in-azure"></a>Azure의 네트워크 인터페이스
네트워크 인터페이스(NIC)는 VM(가상 컴퓨터)과 기본 소프트웨어 네트워크 간 상호 연결입니다. 이 문서에서는 네트워크 인터페이스란 무엇이며 Azure Resource Manager 배포 모델에서 어떻게 사용되는지에 대해 설명합니다.

Resource Manager 배포 모델을 사용하여 새 리소스를 배포하는 것이 좋지만 [클래식](virtual-network-ip-addresses-overview-classic.md) 배포 모델의 네트워크 연결을 통해 VM을 배포할 수도 있습니다. 클래식 모델에 익숙한 경우 Resource Manager 배포 모델의 VM 네트워킹에 중요한 차이가 있다는 사실에 유의합니다. [가상 컴퓨터 네트워킹 - 클래식](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) 문서를 읽어 이러한 차이점을 자세히 알아봅니다.

Azure의 네트워크 인터페이스:

1. 만들고 삭제할 수 있으며 구성 가능한 자체 설정이 있는 리소스입니다.
2. 만들어질 때 하나의 Azure VNet(가상 네트워크)에 있는 하나의 서브넷에 연결되어야 합니다. VNet에 친숙하지 않은 경우 [가상 네트워크 개요](virtual-networks-overview.md) 문서를 읽어 VNet에 대해 자세히 알아봅니다. NIC는 NIC가 있는 동일한 Azure [위치](https://azure.microsoft.com/regions) 및 [구독](../azure-glossary-cloud-terminology.md#subscription)에 있는 VNet에만 연결해야 합니다. NIC를 만든 후에 연결된 서브넷을 변경할 수 있지만 NIC가 연결된 VNet은 변경할 수 없습니다.
3. NIC를 만든 후에 변경할 수 없는 VNet에 이름을 할당하세요. 이 이름은 Azure [리소스 그룹](../azure-resource-manager/resource-group-overview.md#resource-groups)내에서 고유해야 하지만 구독, NIC가 생성된 Azure 위치 또는 NIC가 연결된 VNet 내에서 고유할 필요는 없습니다. 일부 NIC는 일반적으로 Azure 구독 내에서 만들어집니다. 기본 이름을 사용하는 것보다 더 쉽게 여러 NIC를 관리할 수 있도록 명명 규칙을 고안하는 것이 좋습니다. 권장 사항에 대해서는 [Azure 리소스에 대한 권장되는 명명 규칙](../guidance/guidance-naming-conventions.md) 을 참조하세요.
4. VM에 연결될 수 있지만 NIC가 있는 동일한 위치의 단일 VM에만 연결할 수 있습니다.
5. VM에 연결된 상태로 유지되는 동안 NIC와 함께 유지되는 MAC 주소가 있습니다. MAC 주소는 Azure Portal, Azure PowerShell 또는 Azure 명령줄 인터페이스를 사용하여 VM이 다시 시작되거나(운영 체제 내에서), 중지(할당 취소)되었다가 시작되어도 유지됩니다. NIC는 VM에서 분리된 후 다른 VM에 연결되면 다른 MAC 주소를 받습니다. NIC가 삭제되면 MAC 주소가 다른 NIC에 할당됩니다.
6. 기본 **개인** *IPv4* 정적 또는 동적 IP 주소가 할당되어야 합니다.
7. 하나의 공용 IP 주소 리소스가 연결되어야 합니다.
8. 특정 버전의 Microsoft Windows Server 운영 체제를 실행하는 특정 VM 크기에 대해 단일 루트 I/O 가상화(SR-IOV)를 통해 가속 네트워킹을 지원합니다. 이 미리 보기 기능에 대한 자세한 내용은 [가상 컴퓨터에 대한 가속 네트워킹](virtual-network-accelerated-networking-powershell.md) 문서를 참조하세요.
9. NIC에 대해 IP 전달을 사용하도록 설정하면 할당된 개인 IP 주소를 대상으로 하지 않는 트래픽을 받을 수 있습니다. 예를 들어 VM에서 방화벽 소프트웨어가 실행되는 경우 자체 IP 주소를 대상으로 하지 않는 패킷을 라우팅합니다. VM은 트래픽을 라우팅 또는 전달할 수 있는 소프트웨어를 계속 실행해야 하지만 이렇게 하려면 NIC에 대해 IP 전달을 사용하도록 설정해야 합니다.
10. 반드시 그럴 필요는 없으나 종종 연결된 VM 또는 연결된 동일한 VNet과 동일한 리소스 그룹에서 만들어집니다.

## <a name="vms-with-multiple-network-interfaces"></a>여러 네트워크 인터페이스를 사용하는 VM
여러 NIC를 VM에 연결할 수 있지만 그렇게 하는 경우 다음에 주의하세요.  

* VM 크기가 여러 NIC를 지원해야 합니다. 여러 NIC를 지원하는 VM 크기에 대한 자세한 내용을 보려면 [Windows Server VM 크기](../virtual-machines/virtual-machines-windows-sizes.md) 또는 [Linux VM 크기](../virtual-machines/virtual-machines-linux-sizes.md) 문서를 읽어보세요.
* 최소 두 개의 NIC로 VM을 만들어야 합니다. VM이 하나의 NIC로만 만들어지면 VM 크기가 둘 이상을 지원하더라도 VM을 만든 후에는 VM에 NIC를 더 연결할 수 없습니다. VM이 최소 두 개의 NIC로 만들어지고 VM 크기가 셋 이상의 NIC를 지원하기만 한다면 VM을 만든 후에 VM에 NIC를 더 연결할 수 있습니다.  
* VM에 연결된 NIC가 세 개 이상인 경우 VM에서 보조 NIC(기본 NIC는 분리할 수 없음)를 분리할 수 있습니다. VM에 연결된 NIC가 둘 이하인 경우에는 NIC를 분리할 수 없습니다.  
* VM 내에서 Nic의 순서가 임의로 지정되며, Azure 인프라 업데이트에서 변경할 수도 있습니다. 그러나 IP 주소 및 해당 이더넷 MAC 주소는 동일하게 유지됩니다. 예를 들어, 운영 체제가 Azure NIC1을 Eth1로 식별한다고 가정하겠습니다. Eth1에 IP 주소 10.1.0.100 및 MAC 주소 00-0D-3A-B0-39-0D가 있습니다. Azure 인프라가 업데이트되고 다시 부팅된 후에 운영 체제가 Azure NIC1을 Eth2로 식별할 수 있지만 IP 및 MAC 주소는 운영 체제가 Azure NIC1을 Eth1로 식별했을 때와 동일하게 유지됩니다. 다시 시작은 고객이 시작한 것이며 NIC 순서는 운영 체제 내에 그대로 유지됩니다.  
* VM이 [가용성 집합](../azure-glossary-cloud-terminology.md#availability-set)의 멤버이면 가용성 집합에 포함된 모든 VM에는 단일 NIC 또는 여러 NIC가 있어야 합니다. VM에 여러 NIC가 있는 경우 각 VM에 최소 두 개의 NIC가 있으면 각 VM에 있는 NIC 수가 동일할 필요는 없습니다.

## <a name="next-steps"></a>다음 단계
* [VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 문서를 읽어 단일 NIC로 VM을 만드는 방법을 알아봅니다.
* [여러 NIC를 사용하여 VM 배포](virtual-network-deploy-multinic-arm-ps.md) 문서를 읽어 여러 NIC로 VM을 만드는 방법을 알아봅니다.
* [Azure 가상 컴퓨터에 대한 여러 IP 주소](virtual-network-multiple-ip-addresses-powershell.md) 문서를 읽어 여러 IP 구성으로 NIC를 만드는 방법을 알아봅니다.




<!--HONumber=Jan17_HO5-->


