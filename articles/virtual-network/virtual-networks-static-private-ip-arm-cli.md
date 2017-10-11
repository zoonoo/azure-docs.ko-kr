---
title: "VM에 대한 개인 IP 주소 구성 - Azure CLI 2.0 | Microsoft Docs"
description: "Azure CLI(명령줄 인터페이스) 2.0을 사용하여 가상 컴퓨터에 대한 개인 IP 주소를 구성하는 방법에 대해 알아봅니다."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 071156367c1f819a00d31f1d0335e301391fda81
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2017
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli-20"></a>Azure CLI 2.0을 사용하여 가상 컴퓨터에 대한 개인 IP 주소 구성

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]


## <a name="cli-versions-to-complete-the-task"></a>태스크를 완료하기 위한 CLI 버전 

다음 CLI 버전 중 하나를 사용하여 태스크를 완료할 수 있습니다. 

- [Azure CLI 1.0](virtual-networks-static-private-ip-cli-nodejs.md) - 클래식 및 리소스 관리 배포 모델용 CLI 
- [Azure CLI 2.0](#specify-a-static-private-ip-address-when-creating-a-vm) - 리소스 관리 배포 모델용 차세대 CLI(이 문서)

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. [클래식 배포 모델에서 정적 개인 IP 주소를 관리](virtual-networks-static-private-ip-classic-cli.md)할 수도 있습니다.

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> 아래 샘플 Azure CLI 2.0 명령에는 이미 만들어져 있는 단순한 환경이 필요합니다. 이 문서에 표시된 대로 명령을 실행하려는 경우 먼저 [vnet 만들기](virtual-networks-create-vnet-arm-cli.md)에 설명된 테스트 환경을 구축합니다.

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>VM을 만들 때 정적 개인 IP 주소 지정

*192.168.1.101*의 정적 개인 IP 주소를 사용하여 *TestVNet*이라는 VNet의 *FrontEnd* 서브넷에 *DNS01*이라는 VM을 만들려면 다음 단계를 따르세요.

1. 아직 설치하지 않은 경우 최신 [Azure CLI 2.0](/cli/azure/install-az-cli2)을 설치 및 구성하고 [az login](/cli/azure/#login)을 사용하여 Azure 계정에 로그인합니다. 

2. [az network public-ip create](/cli/azure/network/public-ip#create) 명령을 사용하여 VM의 공용 IP를 만듭니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.

    > [!NOTE]
    > 사용자 환경에 따라 이 단계 및 이후 단계에 다른 값을 사용할 수 있습니다.
   
    ```azurecli
    az network public-ip create \
    --name TestPIP \
    --resource-group TestRG \
    --location centralus \
    --allocation-method Static
    ```

    예상 출력:
   
   ```json
   {
        "publicIp": {
            "idleTimeoutInMinutes": 4,
            "ipAddress": "52.176.43.167",
            "provisioningState": "Succeeded",
            "publicIPAllocationMethod": "Static",
            "resourceGuid": "79e8baa3-33ce-466a-846c-37af3c721ce1"
        }
    }
    ```

   * `--resource-group`: 공용 IP를 만들 리소스 그룹의 이름입니다.
   * `--name`: 공용 IP의 이름입니다.
   * `--location`: 공용 IP를 만들 Azure 지역입니다.

3. [az network nic create](/cli/azure/network/nic#create) 명령을 실행하여 고정 개인 IP를 가진 NIC를 만듭니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    예상 출력:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    매개 변수

    * `--private-ip-address`: NIC에 대한 고정 개인 IP 주소입니다.
    * `--vnet-name`: NIC가 만들어질 VNet의 이름입니다.
    * `--subnet`: NIC가 만들어질 서브넷의 이름입니다.

4. [azure vm create](/cli/azure/vm/nic#create) 명령을 실행하여 위에서 만든 공용 IP 및 NIC를 사용하여 VM을 만듭니다. 출력 다음에 표시되는 목록은 사용되는 매개 변수를 설명합니다.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    예상 출력:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   기본 [az vm create](/cli/azure/vm#create) 매개 변수가 아닌 매개 변수입니다.

   * `--nics`: VM이 연결된 NIC의 이름입니다.
   

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>VM의 정적 개인 IP 주소 정보 검색

만든 고정 개인 IP 주소 정보를 보려면 다음 Azure CLI 명령을 실행하고 *개인 IP alloc-method* 및 *개인 IP 주소*에 대한 값을 확인합니다.

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

예상 출력:

```json
"192.168.1.101"
```

해당 VM에 대한 NIC의 특정 IP 정보를 표시하려면 NIC를 구체적으로 쿼리합니다.

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

출력은 다음과 같습니다.

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>VM에서 정적 개인 IP 주소 제거

Resource Manager 배포를 위해 Azure CLI의 NIC에서 고정 개인 IP 주소를 제거할 수 없습니다. 다음이 필요합니다.
- 동적 IP를 사용하는 새 NIC 만들기
- VM의 NIC가 새로 만든 NIC를 수행하도록 설정합니다. 

위의 명령에 사용된 VM에 대한 NIC를 변경하려면 다음 단계를 따르세요.

1. 새 IP 주소를 가진 동적 IP 할당을 사용하여 새 NIC를 만들기 위해 **azure network nic create** 명령을 실행합니다. IP 주소가 지정되어 있지 않으므로 할당 메서드는 **동적**입니다.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    예상 출력:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. **azure vm set** 명령을 실행하여 VM에 사용된 NIC를 변경합니다.
   
    ```azurecli
    azure vm set -g TestRG -n DNS01 -N TestNIC2
    ```

    예상 출력:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > VM에 하나 이상의 NIC가 있을 정도로 충분히 큰 경우 **azure network nic delete** 명령을 실행하여 이전 NIC를 삭제합니다.
   
## <a name="next-steps"></a>다음 단계
* [예약된 공용 IP](virtual-networks-reserved-public-ip.md) 주소에 대해 알아봅니다.
* [ILPIP(인스턴스 수준 공용 IP)](virtual-networks-instance-level-public-ip.md) 주소에 대해 알아봅니다.
* [예약된 IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx)를 참조합니다.

