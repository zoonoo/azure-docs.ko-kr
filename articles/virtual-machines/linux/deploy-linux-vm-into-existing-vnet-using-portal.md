---
title: "Azure Portal을 사용하여 기존 네트워크에 Linux VM 배포 | Microsoft Docs"
description: "포털을 사용하여 기존 Azure Virtual Network에 Linux VM을 배포합니다."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: ko-kr
ms.lasthandoff: 05/11/2017


---

# <a name="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal"></a>Azure Portal을 사용하여 기존 Azure Virtual Network에 Linux 가상 컴퓨터를 배포하는 방법

이 문서에서는 기존 VNet(가상 네트워크)에 VM(가상 컴퓨터)을 배포하는 방법을 보여 줍니다. VNet 및 네트워크 보안 그룹과 같은 Azure 자산은 정적이고 거의 배포되지 않는 수명이 긴 리소스인 것이 좋습니다. VNet을 배포하면 인프라에 어떤 부정적인 영향을 주지 않고 상수 재배포에서 다시 사용할 수 있습니다. VNet을 기존 하드웨어 네트워크 스위치라고 생각하면 배포할 때마다 새로운 하드웨어 스위치를 구성하지 않아도 됩니다.  

올바르게 구성된 VNet으로 VNet에 반복하여 VNet의 수명 동안 필요한 변경 사항을 포함하는 새 서버를 계속 배포할 수 있습니다.

## <a name="create-the-resource-group"></a>리소스 그룹 만들기

먼저 리소스 그룹을 만들어서 연습에서 만드는 모든 항목을 구성합니다. Azure 리소스 그룹에 대한 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>VNet 만들기

다음으로 VM을 시작할 VNet을 빌드합니다. VNet은 서브넷 1개를 포함하며, 이후 단계에서 이 서브넷이 포함된 네트워크 보안 그룹에 연결됩니다.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>서브넷에 VNic 추가

가상 네트워크 카드(VNic)는 다른 VM에 연결할 수 있으므로 중요합니다. 이 방법을 통해 VM이 임시 리소스가 되는 동안 vNic를 정적 리소스로 유지할 수 있습니다. VNic를 만들고 이전 단계에서 만든 서브넷에 연결합니다.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-network-security-group"></a>네트워크 보안 그룹 만들기

Azure 네트워크 보안 그룹은 네트워크 계층에서 방화벽과 동일합니다. Azure 네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹이란?](../../virtual-network/virtual-networks-nsg.md)을 참조하세요.

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>인바운드 SSH 허용 규칙 추가

VM은 인터넷에서 액세스를 해야 하므로 인바운드 포트 22 트래픽이 VM의 포트 22에 대한 네트워크를 통해 전달되도록 허용하는 규칙이 만들어집니다.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>서브넷과 NSG 연결

VNet 및 서브넷이 만들어졌다면 네트워크 보안 그룹을 서브넷에 연결합니다. 네트워크 보안 그룹은 전체 서브넷 또는 개별 VNic와 연결될 수 있습니다. 서브넷 수준에서 방화벽 필터링 트래픽을 사용할 경우 서브넷 내의 모든 VNic 및 VM이 네트워크 보안 그룹에 의해 보호됩니다. 다른 방법은 네트워크 보안 그룹이 단일 VNic와만 연결되어 하나의 VM만 보호하는 것입니다.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>VNet 및 NSG에 VM 배포

Azure Portal을 사용하여 기존 Azure 리소스 그룹, VNet, 서브넷 및 VNic에 Linux VM을 배포합니다.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

기존 리소스를 선택하려면 포털을 사용하여 Azure에서 기존 네트워크 내에 VM을 배포하도록 지시합니다. VNet 및 서브넷이 배포되면 Azure 지역 내에서 정적 또는 영구적으로 리소스로 유지할 수 있습니다.  

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager 템플릿을 사용하여 특정 배포 만들기](../windows/cli-deploy-templates.md)
* [Azure CLI 명령을 직접 사용하여 Linux VM에 대한 고유한 사용자 지정 환경 만들기](create-cli-complete.md)
* [템플릿을 사용하여 Azure에서 Linux VM 만들기](create-ssh-secured-vm-from-template.md)

