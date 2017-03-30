---
title: "가상 네트워크 만들기 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI 2.0을 사용하여 가상 네트워크를 만드는 방법을 알아봅니다."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 4f59512d83e6d000dd60b3fba46e483be8466292
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 가상 네트워크 만들기

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure에는 Azure Resource Manager 및 클래식이라는 두 가지 배포 모델이 있습니다. Resource Manager 배포 모델을 통해 리소스를 만드는 것이 좋습니다. 두 가지 모델의 차이점에 대해 자세히 알아보려면 [Azure 배포 모델 이해](../azure-resource-manager/resource-manager-deployment-model.md) 문서를 참조하세요.

## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전
다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다.

- [Azure CLI 1.0](virtual-networks-create-vnet-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI
- [Azure CLI 2.0](#create-a-virtual-network) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)
 
    다른 도구를 사용하여 Resource Manager를 통해 VNet을 만들거나 다음 목록에서 다른 옵션을 선택하여 클래식 배포 모델을 통해 VNet을 만들 수도 있습니다.

> [!div class="op_single_selector"]
> * [포털](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [CLI](virtual-networks-create-vnet-arm-cli.md)
> * [템플릿](virtual-networks-create-vnet-arm-template-click.md)
> * [포털(클래식)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell(클래식)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [CLI(클래식)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]


## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

Azure CLI 2.0을 사용하여 가상 네트워크를 만들려면 다음 단계를 완료하세요.

1. 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치 및 구성하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인합니다.

2. `--name` 및 `--location` 인수를 포함한 [az group create](/cli/azure/group#create) 명령을 사용하여 VNet에 대한 리소스 그룹을 만듭니다.

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. VNet 및 서브넷을 만듭니다.

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    예상 출력:
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    사용된 매개 변수:

    - `--name TestVNet`: 만들 VNet의 이름입니다.
    - `--resource-group TestRG`: # 리소스를 제어하는 리소스 그룹 이름입니다. 
    - `--location centralus`: 배포할 위치입니다.
    - `--address-prefix 192.168.0.0/16`: 주소 접두사와 블록입니다.  
    - `--subnet-name FrontEnd`: 서브넷의 이름입니다.
    - `--subnet-prefix 192.168.1.0/24`: 주소 접두사와 블록입니다.

    다음 명령에서 사용할 기본 정보를 나열하려면 [쿼리 필터](/cli/azure/query-az-cli2)를 사용하여 VNet을 쿼리할 수 있습니다.

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    다음과 같은 출력을 생성합니다.

        Where      Name      Group

        centralus  TestVNet  TestRG

4. 서브넷을 만듭니다.

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    예상 출력:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    사용된 매개 변수:

    - `--address-prefix 192.168.2.0/24`: 서브넷 CIDR 블록입니다.
    - `--name BackEnd`: 새 서브넷의 이름입니다.
    - `--resource-group TestRG`: 리소스 그룹입니다.
    - `--vnet-name TestVNet`: 소유한 VNet의 이름입니다.

5. 새 VNet의 속성을 쿼리합니다.

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    예상 출력:

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. 서브넷의 속성을 쿼리합니다.

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    예상 출력:

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>다음 단계

연결 방법 알아보기:

- 가상 컴퓨터(VM)에서 가상 네트워크 연결은 [Linux VM 만들기](../virtual-machines/virtual-machines-linux-quick-create-cli.md) 문서를 참조하세요. 해당 문서의 단계에서 VNet 및 서브넷을 만드는 대신 기존 VNet 및 서브넷을 VM에 연결하도록 선택할 수 있습니다.
- 가상 네트워크에서 다른 가상 네트워크 연결은 [VNet 연결](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 문서를 참조하세요.
- 가상 네트워크에서 온-프레미스 네트워크 연결은 사이트 간 VPN(가상 사설망) 또는 ExpressRoute 회로를 사용합니다. 자세한 내용은 [사이트 간 VPN을 사용하여 VNet을 온-프레미스 네트워크에 연결](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) 및 [VNet을 ExpressRoute 회선에 연결](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)을 참조하세요.
