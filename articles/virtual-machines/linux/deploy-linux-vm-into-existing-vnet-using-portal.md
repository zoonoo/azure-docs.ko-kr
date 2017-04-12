---
title: "기존 네트워크에 Linux VM 배포 - Azure Portal | Microsoft Docs"
description: "포털을 사용하여 기존 Azure Virtual Network에 Linux VM을 배포합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 27704aa20a4514cfca200e70ffcc894059207281
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>포털을 사용하여 기존 VNet 및 NSG에 Linux VM 배포

이 문서에서는 기존 VNet(가상 네트워크)에 VM을 배포하는 방법을 보여 줍니다.  VNet 및 NSG(네트워크 보안 그룹)와 같은 Azure 자산이 정적이고 거의 배포되지 않은 수명이 긴 리소스인 것이 좋습니다.  VNet을 배포하면 인프라에 어떤 부정적인 영향을 주지 않고 상수 재배포에서 다시 사용할 수 있습니다.  VNet을 기존 하드웨어 네트워크 스위치라고 생각하면 배포할 때마다 새로운 하드웨어 스위치를 구성하지 않아도 됩니다.  

올바르게 구성된 VNet으로 VNet에 반복하여 VNet의 수명 동안 필요한 변경 사항을 포함하는 새 서버를 계속 배포할 수 있습니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

먼저 리소스 그룹을 배포하여 연습에서 만드는 모든 항목을 구성합니다.  Azure 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>VNet 만들기

첫 번째 단계는 VM을 시작할 VNet을 빌드하는 것입니다.  VNet에는 하나의 서브넷이 포함되고 나중에 이 서브넷과 NSG를 연결합니다.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>서브넷에 VNic 추가

VNic(가상 네트워크 카드)는 다른 VM에 연결할 수 있으므로 중요합니다. 그렇기 때문에 VM이 임시일 수 있는 반면 VNic는 정적 리소스로 유지됩니다. VNic를 만들고 이전 단계에서 만든 서브넷에 연결합니다.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>NSG 만들기

Azure NSG는 네트워크 계층에서 방화벽과 동일합니다. Azure NSG에 대한 자세한 내용은 [네트워크 보안 그룹이란?](../../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>인바운드 SSH 허용 규칙 추가

Linux VM은 인터넷에서 액세스를 해야 하므로 인바운드 포트 22 트래픽이 Linux VM의 포트 22에 대한 네트워크를 통해 전달되도록 허용하는 규칙을 만듭니다.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>서브넷과 NSG 연결

VNet 및 서브넷이 만들어졌다면 서브넷에 NSG를 연결합니다.  NSG는 전체 서브넷 또는 개별 VNic와 연결될 수 있습니다.  NSG는 서브넷 수준의 방화벽 필터링 트래픽을 사용하여 서브넷 내의 모든 VNic 및 VM을 보호하거나 단일 VNic와 연결하고 하나의 VM을 보호합니다.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet 및 NSG에 VM 배포

Azure Portal을 사용하여 기존 Azure 리소스 그룹, VNet, 서브넷 및 VNic에 Linux VM을 배포합니다.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

기존 리소스를 선택하려면 포털을 사용하여 Azure에서 기존 네트워크 내에 VM을 배포하도록 지시합니다.  다시 말해, VNet 및 서브넷이 배포되면 Azure 지역 내에서 정적 또는 영구적으로 리소스로 유지할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager 템플릿을 사용하여 특정 배포 만들기](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [템플릿을 사용하여 Azure에서 Linux VM 만들기](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

