---
title: "Azure CLI 1.0을 사용하여 여러 IP 주소가 있는 VM | Microsoft Docs"
description: "Azure CLI 1.0 | Resource Manager를 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 9f085dfa1fe4db36d58cb976bb550a46bf241ac7
ms.lasthandoff: 03/28/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-azure-cli-10"></a>Azure CLI 1.0을 사용하여 가상 컴퓨터에 여러 IP 주소 할당

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

이 문서는 Azure CLI 1.0을 사용하여 Azure Resource Manager 배포 모델을 통해 VM(가상 컴퓨터)을 만드는 방법을 설명합니다. 클래식 배포 모델을 통해 생성된 리소스에 여러 IP 주소를 할당할 수 없습니다. Azure 배포 모델에 대해 자세히 알아보려면 [배포 모델 이해](../resource-manager-deployment-model.md) 문서를 참조하세요.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>여러 IP 주소를 사용하여 VM 만들기

Azure CLI 1.0(이 문서) 또는 [Azure CLI 2.0](virtual-network-multiple-ip-addresses-cli.md)을 사용하여 이 태스크를 완료할 수 있습니다. 다음 단계는 시나리오에 설명된 대로 여러 IP 주소를 가진 예시 VM을 만드는 방법을 설명합니다. 변수 이름과 IP 주소 유형을 구현에 필요한 대로 변경합니다.

1. [Azure CLI 설치 및 구성](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서에 나오는 단계에 따라 Azure CLI 1.0을 설치 및 구성하고 `azure-login` 명령을 사용하여 Azure 계정에 로그인합니다.

2. 리소스 그룹 만들기:
    
    ```azurecli
    RgName=myResourceGroup
    Location=westcentralus
    azure group create --name $RgName --location $Location
    ```
3. 가상 네트워크 만들기:

    ```azurecli
    azure network vnet create --resource-group $RgName --location $Location --name myVNet \
    --address-prefixes 10.0.0.0/16
    ```
4. 가상 네트워크에 서브넷 만들기:

    ```azurecli
    azure network vnet subnet create --name mySubnet --resource-group $RgName --vnet-name myVNet \
    --address-prefix 10.0.0.0/24
    ```
    
5. VM에 대한 저장소 계정을 만듭니다. 다음 명령을 실행하기 전에 *mystorageaccount*를 고유한 이름으로 바꿉니다. 이 이름은 Azure에서 고유해야 합니다.

    ```azurecli
    az storage account create --resource-group $RgName --location $Location --name mystorageaccount \
    --kind Storage --sku Standard_LRS
    ```

6. IP 구성, NIC를 만들고 NIC에 IP 구성을 할당합니다. 필요에 따라 구성을 추가, 제거 또는 변경할 수 있습니다. 다음 구성은 시나리오에 설명되어 있습니다.

    **IPConfig-1**

    다음을 만들기 위해 수행할 명령을 입력합니다.

    - 고정 공용 IP 주소가 있는 공용 IP 주소 리소스
    - NIC: 공용 IP 주소와 정적 개인 IP 주소를 할당합니다.
    
    *mypublicdns*를 Azure 위치 내에서 고유한 이름으로 바꿉니다.

      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP1 \
      --domain-name-label mypublicdns --allocation-method Static
        
      azure network nic create --resource-group $RgName --location $Location --name myNic1 \
      --private-ip-address 10.0.0.4 --subnet-name mySubnet --subnet-vnet-name myVNet \
      --subnet-name mySubnet --public-ip-name myPublicIP1
      ```

      > [!NOTE]
      > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.

    **IPConfig-2**

     고정 공용 IP 주소와 고정 개인 IP 주소가 있는 새 공용 IP 주소 리소스와 새 IP 구성을 만들려면 다음 명령을 입력합니다.
    
      ```azurecli
      azure network public-ip create --resource-group $RgName --location $Location --name myPublicIP2
      --domain-name-label mypublicdns2 --allocation-method Static

      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --name IPConfig-2
      --private-ip-address 10.0.0.5 --public-ip-name myPublicIP2
      ```

    **IPConfig-3**

    고정 개인 IP 주소가 있고 공용 IP 주소가 없는 IP 구성을 만들려면 다음 명령을 입력합니다.

      ```azurecli
      azure network nic ip-config create --resource-group $RgName --nic-name myNic1 --private-ip-address 10.0.0.6 \
      --name IPConfig-3
      ```

    >[!NOTE] 
    >이 문서를 통해 모든 IP 구성을 단일 NIC에 할당하면 여러 IP 구성도 VM의 모든 NIC에 할당할 수 있습니다. 여러 NIC로 VM을 만드는 방법에 대해 자세히 알아보려면 여러 NIC를 사용하여 VM 만들기 문서를 참조하세요.

7. Linux VM 만들기 

    ```azurecli
    az vm create --resource-group $RgName --name myVM1 --location $Location --nics myNic1 \
    --image UbuntuLTS --ssh-key-value ~/.ssh/id_rsa.pub --admin-username azureuser
    ```

    예를 들어 VM 크기를 표준 DS2 v2로 변경하려면 단순히 다음 속성 `--vm-size Standard_DS3_v2`을 6단계에서 `azure vm create` 명령에 추가합니다.

8. NIC와 연결된 IP 구성을 보려면 다음 명령을 입력합니다.

    ```azurecli
    azure network nic show --resource-group $RgName    --name myNic1
    ```
9. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 사용자 운영 체제별 단계를 완료하여 개인 IP 주소를 VM 운영 체제에 추가합니다.

## <a name="add"></a>VM에 IP 주소 추가

다음 단계를 완료하여 개인 및 공용 IP 주소를 기존 NIC에 추가할 수 있습니다. 예제는 이 문서에서 설명된 [시나리오](#Scenario)를 기반으로 합니다.

1. Azure CLI를 열고 단일 CLI 세션 내에서 이 섹션의 나머지 단계를 완료합니다. 아직 Azure CLI를 설치 및 구성하지 않은 경우 [Azure CLI 설치 및 구성](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 문서에 나오는 단계를 완료하고 Azure 계정에 로그인합니다.

2. 요구 사항에 따라 다음 섹션 중 하나의 단계를 완료합니다.

    - **개인 IP 주소 추가**
    
        NIC에 개인 IP 주소를 추가하려면 아래 명령을 사용하여 IP 구성을 만들어야 합니다. 고정 주소는 서브넷에 사용되지 않는 주소여야 합니다.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic1 \
        --private-ip-address 10.0.0.7 --name IPConfig-4
        ```
        고유한 구성 이름과 개인 IP 주소를 사용하여 필요한 만큼 구성을 만듭니다(정적 IP 주소를 가진 구성의 경우).

    - **공용 IP 주소 추가**
    
        공용 IP 주소는 새 IP 구성 또는 기존 IP 구성에 연결하면 해당 주소가 추가됩니다. 필요에 따라 이후 섹션 중 하나에 나와 있는 단계를 완료합니다.

        > [!NOTE]
        > 공용 IP 주소에는 명목 요금이 부과됩니다. IP 주소 가격에 대한 자세한 내용은 [IP 주소 가격](https://azure.microsoft.com/pricing/details/ip-addresses) 페이지를 참조하세요. 구독 내에서 사용할 수 있는 공용 IP 주소의 수는 제한되어 있습니다. 이러한 한에 대한 자세한 내용은 [Azure 제한](../azure-subscription-service-limits.md#networking-limits) 문서를 참조하세요.
        >

        **새 IP 구성에 리소스 연결**
    
        새 IP 구성의 공용 IP 주소를 추가할 때마다 모든 IP 구성 시 개인 IP 주소가 있어야 하기 때문에 개인 IP 주소도 추가해야 합니다. 기존 공용 IP 주소 리소스를 추가하거나 새로 만들 수 있습니다. 새 파일을 만들려면 다음 명령을 입력합니다.

        ```azurecli
        azure network public-ip create --resource-group myResourceGroup --location westcentralus --name myPublicIP3 \
        --domain-name-label mypublicdns3
        ```

         고정 개인 IP 주소 및 여기에 연결된 *myPublicP3* 공용 IP 주소 리소스가 있는 새 IP 구성을 만들려면 다음 명령을 입력합니다.

        ```azurecli
        azure network nic ip-config create --resource-group myResourceGroup --nic-name myNic --name IPConfig-4 \
        --private-ip-address 10.0.0.8 --public-ip-name myPublicIP3
        ```

        **기존 IP 구성에 리소스 연결**

        공용 IP 주소 리소스는 아직 연결한 리소스가 없는 IP 구성에만 연결될 수 있습니다. 다음 명령을 입력하면 IP 구성에 연결된 공용 IP 주소가 있는지 여부를 확인할 수 있습니다.

        ```azurecli
        azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
        ```

        반환된 출력에서 IPConfig-3에 대한 것과 유사한 줄을 찾습니다.

        ```            
        Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
        IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
        IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet
        ```
          
        *IpConfig 3*에 대한 **공용 IP** 열이 비어 있기 때문에 현재 공용 IP 주소 리소스가 여기에 연결되어 있지 않습니다. IpConfig-3에 기존 공용 IP 주소 리소스를 추가하거나 다음 명령을 입력하여 새로 만들 수 있습니다.

        ```azurecli
        azure network public-ip create --resource-group  myResourceGroup --location westcentralus \
        --name myPublicIP3 --domain-name-label mypublicdns3 --allocation-method Static
        ```

        *IPConfig 3*이라는 기존 IP 구성에 공용 IP 주소 리소스를 연결하려면 다음 명령을 입력합니다.
        ```azurecli
        azure network nic ip-config set --resource-group myResourceGroup --nic-name myNic1 --name IPConfig-3 \
        --public-ip-name myPublicIP3
        ```

3. NIC에 할당된 개인 IP 주소 및 공용 IP 주소 리소스를 보려면 다음 명령을 입력합니다.

    ```azurecli
    azure network nic ip-config list --resource-group myResourceGroup --nic-name myNic1
    ```

      반환되는 출력은 다음과 유사합니다.
      ```
      Name               Provisioning state  Primary  Private IP allocation Private IP version  Private IP address  Subnet    Public IP
        
      default-ip-config  Succeeded           true     Static                IPv4                10.0.0.4            mySubnet  myPublicIP
      IPConfig-2         Succeeded           false    Static                IPv4                10.0.0.5            mySubnet  myPublicIP2
      IPConfig-3         Succeeded           false    Static                IPv4                10.0.0.6            mySubnet  myPublicIP3
      ```
4. 이 문서의 [VM 운영 체제에 IP 주소 추가](#os-config) 섹션에 나오는 지침에 따라 NIC에 추가한 개인 IP 주소를 VM 운영 체제에 추가합니다. 운영 체제에 공용 IP 주소를 추가하지 마십시오.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

