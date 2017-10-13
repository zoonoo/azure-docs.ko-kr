---
title: "Azure CLI 2.0을 사용하여 라우팅 및 가상 어플라이언스 제어 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 라우팅 및 가상 어플라이언스 제어 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 USR(사용자 정의 경로) 만들기

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Azure CLI](virtual-network-create-udr-arm-cli.md)
> * [템플릿](virtual-network-create-udr-arm-template.md)
> * [PowerShell(클래식 배포)](virtual-network-create-udr-classic-ps.md)
> * [CLI(클래식 배포)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전 

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다. 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI 
- [Azure CLI 2.0](#Create-the-UDR-for-the-front-end-subnet) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

아래 샘플 Azure CLI 명령에는 위의 시나리오를 기반으로 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [이 템플릿](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before)을 배포하여 테스트 환경을 구축하고 **Azure에 배포**를 클릭한 다음 필요한 경우 기본 매개 변수 값을 바꾸고 포털의 지침을 따릅니다.


## <a name="create-the-udr-for-the-front-end-subnet"></a>프런트 엔드 서브넷에 대한 UDR 만들기
위의 시나리오에 따라 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 수행합니다.

1. [az network route-table create](/cli/azure/network/route-table#create) 명령을 사용하여 프런트 엔드 서브넷에 대한 경로 테이블을 만듭니다.

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    출력:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. [az network route-table route create](/cli/azure/network/route-table/route#create) 명령을 사용하여 백 엔드 서브넷(192.168.2.0/24)으로 지정된 모든 트래픽을 **FW1** VM(192.168.0.4)으로 전송하는 경로를 만듭니다.

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    출력:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    매개 변수:

    * **--route-table-name**. 경로가 추가될 경로 테이블의 이름입니다. 이 시나리오에서는 *UDR-FrontEnd*입니다.
    * **--address-prefix**. 패킷을 보내는 서브넷에 대한 주소 접두사입니다. 이 시나리오에서는 *192.168.2.0/24*입니다.
    * **--next-hop-type**. 전송할 개체 트래픽 유형입니다. 가능한 값은 *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* 또는 *None*입니다.
    * **--next-hop-ip-address**. 다음 홉에 대한 IP 주소입니다. 이 시나리오에서는 *192.168.0.4*입니다.

3. [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) 명령을 실행하여 위에서 만든 경로 테이블을 **FrontEnd** 서브넷에 연결합니다.

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    출력:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    매개 변수:
    
    * **--vnet-name**. 서브넷이 위치한 VNet의 이름입니다. 이 시나리오에서는 *TestVNet*입니다.

## <a name="create-the-udr-for-the-back-end-subnet"></a>백 엔드 서브넷에 대한 UDR 만들기

위의 시나리오에 따라 백 엔드 서브넷에 필요한 경로 테이블 및 경로를 만들려면 다음 단계를 마칩니다.

1. 다음 명령을 실행하여 백 엔드 서브넷에 대한 경로 테이블을 만듭니다.

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. 다음 명령을 실행하여 경로 테이블에 경로를 만들고 프런트 엔드 서브넷(192.168.1.0/24)으로 보내진 모든 트래픽을 **FW1** VM(192.168.0.4)으로 보냅니다.

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. 다음 명령을 실행하여 경로 테이블을 **BackEnd** 서브넷에 연결합니다.

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>FW1에서 IP 전달을 사용하도록 설정

사용 되는 NIC에서 IP 전달을 사용 하도록 설정 하려면 **FW1**, 다음 단계를 완료 합니다.

1. JMESPATH 필터와 함께 [az network nic show](/cli/azure/network/nic#show) 명령을 실행하여 **IP 전달 사용**에 대한 현재 **enable-ip-forwarding** 값을 표시합니다. *false*로 설정해야 합니다.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    출력

        false

2. 다음 명령을 실행하여 IP 전달을 사용하도록 설정합니다.

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    콘솔로 스트리밍되는 출력을 검사하거나 특정 **enableIpForwarding** 값만 다시 테스트할 수 있습니다.

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    출력:

        true

    매개 변수

    **--ip-forwarding**: *true* 또는 *false*.

