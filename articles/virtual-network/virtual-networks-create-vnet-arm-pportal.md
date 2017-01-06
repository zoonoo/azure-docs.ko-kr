---
title: "Azure 포털을 사용하여 가상 네트워크 만들기 | Microsoft Docs"
description: "Azure Portal을 사용하여 가상 네트워크를 만드는 방법 알아보기 | Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: ba7a67b8ae57da165f45bd3552a3dfac5f4ef64b
ms.openlocfilehash: c7257faad9a41174fb1d65e04c99cd96a8af3ea9


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Azure 포털을 사용하여 가상 네트워크 만들기

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure에는 Azure Resource Manager 및 클래식이라는 두 가지 배포 모델이 있습니다. Resource Manager 배포 모델을 통해 리소스를 만드는 것이 좋습니다. 두 가지 모델의 차이점에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md) 문서를 참조하세요.
 
이 문서는 Azure Portal을 사용하여 Resource Manager 배포 모델을 통해 VNet을 만드는 방법을 설명합니다. 다른 도구를 사용하여 Resource Manager를 통해 VNet을 만들거나 다음 목록에서 다른 옵션을 선택하여 클래식 배포 모델을 통해 VNet을 만들 수도 있습니다.

> [!div class="op_single_selector"]
- [포털](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [템플릿](virtual-networks-create-vnet-arm-template-click.md)
- [포털(클래식)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell(클래식)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI(클래식)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure Portal을 사용하여 가상 네트워크를 만들려면 다음 단계를 완료하세요.

1. 브라우저에서 http://portal.azure.com으로 이동하고, 필요한 경우 Azure 계정으로 로그인합니다.
2. 다음 그림과 같이 **새로 만들기** > **네트워킹** > **가상 네트워크**를 클릭합니다.

    ![새 가상 네트워크](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. 다음 그림과 같이 **가상 네트워크** 블레이드가 표시되면 *Resource Manager*가 선택되어 있는지 확인하고 **만들기**를 클릭합니다.

    ![Virtual Network](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. 다음 그림과 같이 **가상 네트워크 만들기** 블레이드가 표시되면 **이름**에 *TestVNet*, **주소 공간**에 *192.168.0.0/16*, **서브넷 이름**에 *FrontEnd*, **서브넷 주소 범위**에 *192.168.1.0/24*, **리소스 그룹**에 *TestRG*를 입력하고, **구독**, **위치**를 선택하고 **만들기** 단추를 클릭합니다.

    ![가상 네트워크 만들기](./media/virtual-network-create-vnet-arm-pportal/3.png)

    또는 기존 리소스 그룹을 선택할 수 있습니다. 리소스 그룹에 대해 자세히 알아보려면 [Resource Manager 개요](../azure-resource-manager/resource-group-overview.md#resource-groups) 문서를 참조하세요. 다른 위치를 선택할 수도 있습니다. Azure 위치 및 지역에 대해 자세히 알아보려면 [Azure 지역](https://azure.microsoft.com/regions) 문서를 참조하세요.

5. 포털에서는 VNet을 만들 때 서브넷을 하나만 만들 수 있습니다. 이 시나리오의 경우 두 번째 서브넷은 VNet을 만든 후에 생성되어야 합니다. 두 번째 서브넷을 만들려면 다음 그림과 같이 **모든 리소스**를 클릭한 다음**모든 리소스** 블레이드에서 **TestVNet**을 클릭합니다.

    ![생성된 VNet](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. 다음 그림과 같이 **TestVNet** 블레이드가 표시되면 **서브넷**을 클릭한 후 **+서브넷**을 클릭하고 **서브넷 추가** 블레이드에서 **이름**에 *BackEnd*를, **주소 범위**에 *192.168.2.0/24*를 입력한 후 **확인**을 클릭합니다.

    ![서브넷 추가](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. 다음 그림과 같이 서브넷 두 개가 나열됩니다.
    
    ![VNet의 서브넷 목록](./media/virtual-network-create-vnet-arm-pportal/6.png)

이 문서는 테스트를 위해 두 개의 서브넷으로 가상 네트워크를 만드는 방법을 설명합니다. 프로덕션 용도로 가상 네트워크를 만들기 전에, [가상 네트워크 개요](virtual-networks-overview.md) 및 [가상 네트워크 계획 및 디자인](virtual-network-vnet-plan-design-arm.md) 문서를 읽고 가상 네트워크와 모든 설정을 완전히 이해하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

연결 방법 알아보기:

- 가상 컴퓨터(VM)에서 가상 네트워크 연결은 [Windows VM 만들기](../virtual-machines/virtual-machines-windows-hero-tutorial.md) 또는 [Linux VM 만들기](../virtual-machines/virtual-machines-linux-quick-create-portal.md) 문서를 참조하세요. 해당 문서의 단계에서 VNet 및 서브넷을 만드는 대신 기존 VNet 및 서브넷을 VM에 연결하도록 선택할 수 있습니다.
- 가상 네트워크에서 다른 가상 네트워크 연결은 [VNet 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 문서를 참조하세요.
- 가상 네트워크에서 온-프레미스 네트워크 연결은 사이트 간 VPN(가상 사설망) 또는 ExpressRoute 회로를 사용합니다. 자세한 내용은 [사이트 간 VPN을 사용하여 VNet을 온-프레미스 네트워크에 연결](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) 및 [VNet을 ExpressRoute 회선에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md) 문서를 참조하세요.


<!--HONumber=Jan17_HO1-->


