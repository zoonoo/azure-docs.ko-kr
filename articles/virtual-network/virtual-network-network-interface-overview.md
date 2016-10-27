<properties 
   pageTitle="네트워크 인터페이스 | Microsoft Azure"
   description="Azure Resource Manager의 Azure 네트워크 인터페이스에 대해 알아봅니다."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />


# <a name="network-interfaces"></a>네트워크 인터페이스

네트워크 인터페이스(NIC)는 VM(가상 컴퓨터)과 기본 소프트웨어 네트워크 간 상호 연결입니다. 이 문서에서는 네트워크 인터페이스란 무엇이며 Azure Resource Manager 배포 모델에서 어떻게 사용되는지에 대해 설명합니다.

Resource Manager 배포 모델을 사용하여 새 리소스를 배포하는 것이 좋지만 [클래식](virtual-network-ip-addresses-overview-classic.md) 배포 모델의 네트워크 연결을 통해 VM을 배포할 수도 있습니다. 클래식 모델에 익숙한 경우 Resource Manager 배포 모델의 VM 네트워킹에 중요한 차이가 있다는 사실에 유의합니다. [가상 컴퓨터 네트워킹 - 클래식](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) 문서를 읽어 이러한 차이점을 자세히 알아봅니다.

Azure의 네트워크 인터페이스:

1. 만들고 삭제할 수 있으며 구성 가능한 자체 설정이 있는 리소스입니다.
2. 만들어질 때 하나의 Azure VNet(가상 네트워크)에 있는 하나의 서브넷에 연결되어야 합니다. VNet에 친숙하지 않은 경우 [가상 네트워크 개요](virtual-networks-overview.md) 문서를 읽어 VNet에 대해 자세히 알아봅니다. NIC는 NIC가 있는 동일한 Azure [위치](https://azure.microsoft.com/regions) 및 [구독](../azure-glossary-cloud-terminology.md#subscription)에 있는 VNet에만 연결해야 합니다. NIC를 만든 후에 연결된 서브넷을 변경할 수 있지만 NIC가 연결된 VNet은 변경할 수 없습니다.
3. NIC를 만든 후에 변경할 수 없는 VNet에 이름을 할당하세요. 이 이름은 Azure [리소스 그룹](../resource-group-overview.md#resource-groups)내에서 고유해야 하지만 구독, NIC가 생성된 Azure 위치 또는 NIC가 연결된 VNet 내에서 고유할 필요는 없습니다. 일부 NIC는 일반적으로 Azure 구독 내에서 만들어집니다. 기본 이름을 사용하는 것보다 더 쉽게 여러 NIC를 관리할 수 있도록 명명 규칙을 고안하는 것이 좋습니다. 권장 사항에 대해서는 [Azure 리소스에 대한 권장되는 명명 규칙](../guidance/guidance-naming-conventions.md) 을 참조하세요.
4. VM에 연결될 수 있지만 NIC가 있는 동일한 위치의 단일 VM에만 연결할 수 있습니다.
5. VM에 연결된 상태로 유지되는 동안 NIC와 함께 유지되는 MAC 주소가 있습니다. MAC 주소는 Azure Portal, Azure PowerShell 또는 Azure 명령줄 인터페이스를 사용하여 VM이 다시 시작되거나(운영 체제 내에서), 중지(할당 취소)되었다가 시작되어도 유지됩니다. NIC는 VM에서 분리된 후 다른 VM에 연결되면 다른 MAC 주소를 받습니다. NIC가 삭제되면 MAC 주소가 다른 NIC에 할당됩니다.
6. 기본 **개인** *IPv4* 정적 또는 동적 IP 주소가 할당되어야 합니다.
8. 하나의 공용 IP 주소 리소스가 연결되어야 합니다.
9. 특정 버전의 Microsoft Windows Server 운영 체제를 실행하는 특정 VM 크기에 대해 단일 루트 I/O 가상화(SR-IOV)를 통해 가속 네트워킹을 지원합니다. 이 미리 보기 기능에 대한 자세한 내용은 [가상 컴퓨터에 대한 가속 네트워킹](virtual-network-accelerated-networking-powershell.md) 문서를 참조하세요.
10. NIC에 대해 IP 전달을 사용하도록 설정하면 할당된 개인 IP 주소를 대상으로 하지 않는 트래픽을 받을 수 있습니다. 예를 들어 VM에서 방화벽 소프트웨어가 실행되는 경우 자체 IP 주소를 대상으로 하지 않는 패킷을 라우팅합니다. VM은 트래픽을 라우팅 또는 전달할 수 있는 소프트웨어를 계속 실행해야 하지만 이렇게 하려면 NIC에 대해 IP 전달을 사용하도록 설정해야 합니다.
11. 반드시 그럴 필요는 없으나 종종 연결된 VM 또는 연결된 동일한 VNet과 동일한 리소스 그룹에서 만들어집니다.

VM 크기가 지원하는 경우 여러 NIC를 동일한 VM에 연결할 수 있습니다. 여러 NIC를 지원하는 VM 크기에 대한 자세한 내용을 보려면 [Windows Server VM 크기](../virtual-machines/virtual-machines-windows-sizes.md) 또는 [Linux VM 크기](../virtual-machines/virtual-machines-linux-sizes.md) 문서를 읽어보세요.

## <a name="next-steps"></a>다음 단계

- [VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 문서를 읽어 단일 NIC로 VM을 만드는 방법을 알아봅니다.
- [여러 NIC를 사용하여 VM 배포](virtual-network-deploy-multinic-arm-ps.md) 문서를 읽어 여러 NIC로 VM을 만드는 방법을 알아봅니다.
- [Azure 가상 컴퓨터에 대한 여러 IP 주소](virtual-network-multiple-ip-addresses-powershell.md) 문서를 읽어 여러 IP 구성으로 NIC를 만드는 방법을 알아봅니다.



<!--HONumber=Oct16_HO2-->


