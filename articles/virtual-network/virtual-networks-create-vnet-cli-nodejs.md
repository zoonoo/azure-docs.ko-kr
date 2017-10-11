---
title: "Azure CLI 1.0을 사용하여 가상 네트워크 만들기 | Microsoft Docs"
description: "Azure CLI 1.0을 사용하여 가상 네트워크를 만드는 방법 알아보기 | Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Azure CLI를 사용하여 가상 네트워크 만들기

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure에는 Azure Resource Manager 및 클래식이라는 두 가지 배포 모델이 있습니다. Resource Manager 배포 모델을 통해 리소스를 만드는 것이 좋습니다. 두 가지 모델의 차이점에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md) 문서를 참조하세요.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) - 리소스 관리 배포 모델용 차세대 CLI
- [Azure CLI 1.0](#create-a-virtual-network) - 클래식 및 리소스 관리 배포 모델용 CLI(이 문서)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure CLI를 사용하여 가상 네트워크를 만들려면 다음 단계를 완료하세요.

1. [Azure CLI 설치 및 구성](../cli-install-nodejs.md) 문서에 나오는 단계에 따라 Azure CLI를 설치 및 구성합니다.

2. VNet 및 서브넷을 만듭니다.

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    예상 출력:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    사용된 매개 변수:

   * **--vnet**. 만들 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*
   * **-e(또는 --address-space)**. VNet 주소 공간입니다. 이 시나리오에서는 *192.168.0.0*
   * **-i(또는 -cidr)**. CIDR 형식의 네트워크 마스크입니다. 이 시나리오에서는 *16*입니다.
   * **-n(또는 --subnet-name**). 첫 번째 서브넷의 이름입니다. 이 시나리오에서는 *FrontEnd*입니다.
   * **-p(또는 --subnet-start-ip)**. 서브넷 또는 서브넷 주소 공간의 시작 IP 주소입니다. 이 시나리오에서는 *192.168.1.0*입니다.
   * **-r(또는 --subnet-cidr)**. 서브넷에 대한 CIDR 형식의 네트워크 마스크입니다. 이 시나리오에서는 *24*입니다.
   * **-l(또는 --location)**. VNet을 만들 Azure 지역입니다. 이 시나리오에서는 *Central US*입니다.

3. 서브넷을 만듭니다.

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    예상 출력:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    사용된 매개 변수:

   * **-t(또는 --vnet-name)**. 서브넷이 만들어지는 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.
   * **-n(또는 --name)**. 새 서브넷의 이름입니다. 이 시나리오에서는 *BackEnd*입니다.
   * **-a(또는 --address-prefix)**. 서브넷 CIDR 블록입니다. 이 시나리오에서는 *192.168.2.0/24*입니다.
   
4. 새 VNet의 속성을 보려면:

    ```azurecli
    azure network vnet show
    ```
   
    예상 출력:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>다음 단계

연결 방법 알아보기:

- 가상 컴퓨터(VM)에서 가상 네트워크 연결은 [Linux VM 만들기](../virtual-machines/linux/quick-create-cli.md) 문서를 참조하세요. 해당 문서의 단계에서 VNet 및 서브넷을 만드는 대신 기존 VNet 및 서브넷을 VM에 연결하도록 선택할 수 있습니다.
- 가상 네트워크에서 다른 가상 네트워크 연결은 [VNet 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 문서를 참조하세요.
- 가상 네트워크에서 온-프레미스 네트워크 연결은 사이트 간 VPN(가상 사설망) 또는 ExpressRoute 회로를 사용합니다. 자세한 내용은 [사이트 간 VPN을 사용하여 VNet을 온-프레미스 네트워크에 연결](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) 및 [VNet을 ExpressRoute 회선에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)을 참조하세요.