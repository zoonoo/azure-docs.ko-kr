---
title: "Azure CLI를 사용하여 여러 IP 구성의 부하 분산 | Microsoft Docs"
description: "Azure CLI | Resource Manager를 사용하여 가상 컴퓨터에 여러 IP 주소를 할당하는 방법을 알아봅니다."
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2016
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 57df4ab0b2a1df6631eb6e67a90f69cebb1dfe75
ms.openlocfilehash: 64748a540b20bbd4b354f0b4e1d7de4a969381c6


---
# <a name="load-balancing-on-multiple-ip-configurations"></a>여러 IP 구성의 부하 분산

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
>

이 문서는 가상 NIC(네트워크 인터페이스)당 여러 IP 주소가 있는 경우 Azure Load Balancer를 사용하는 방법을 설명합니다. NIC의 여러 IP 주소에 대한 지원은 현재 미리 보기 릴리스에 포함된 기능입니다. 자세한 내용은 이 문서의 [제한 사항](#limitations) 섹션을 참조하세요. 다음 시나리오는 이 기능이 Azure Load Balancer와 작동하는 방식을 보여줍니다.

이 시나리오에는 Windows를 실행하는 VM이 둘 있고 각각 단일 NIC가 있습니다. 각 NIC에는 여러 IP 구성이 있습니다. 각 VM은 contoso.com 및 fabrikam.com 사이트를 둘 다 호스트합니다. 각 웹 사이트는 NIC의 IP 구성 중 하나로 바인딩됩니다. Load Balancer를 사용하여 웹 사이트의 각 IP 구성에 트래픽을 분산하기 위해 각 웹 사이트에 하나씩 두 개의 프런트 엔드 IP 주소를 노출합니다. 이 시나리오는 양 쪽 프런트 엔드는 물론 양쪽 백 엔드 풀 IP 주소에 동일한 포트 번호를 사용합니다.

![LB 시나리오 이미지](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>제한 사항

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

미리 보기를 등록하려면 구독 ID 및 사용 목적을 적은 전자 메일을 [여러 IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) 로 보냅니다.


## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>여러 IP 구성의 부하를 분산하는 단계

아래 단계에 따라 이 문서에 설명된 시나리오를 수행합니다.

1. 연결된 문서를 참조하여 [Azure CLI를 설치 및 구성](../xplat-cli-install.md)하고 Azure 계정에 로그인합니다.
2. 위의 설명에 따라 *contosofabrikam*이라는 [리소스 그룹을 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-resource-groups-and-choose-deployment-locations).

    ```azurecli
    azure group create contosofabrikam westcentralus
    ```

3. 두 개의 VM에 대한 [가용성 집합을 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-availability-set). 이 시나리오에서는 다음 명령을 사용합니다.

    ```azurecli
    azure availset create --resource-group contosofabrikam --location westcentralus --name myAvailabilitySet
    ```

4. *myVNet*이라는 [가상 네트워크를 만들고](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-virtual-network-and-subnet) *mySubnet*이라는 서브넷을 만듭니다.

    ```azurecli
    azure network vnet create --resource-group contosofabrikam --name myVnet --address-prefixes 10.0.0.0/16  --location westcentralus

    azure network vnet subnet create --resource-group contosofabrikam --vnet-name myVnet --name mySubnet --address-prefix 10.0.0.0/24
    ```

5. *mylb*라는 [부하 분산 장치를 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-and-ip-pools).

    ```azurecli
    azure network lb create --resource-group contosofabrikam --location westcentralus --name mylb
    ```

6. 부하 분산 장치의 프런트 엔드 IP 구성에 대해 두 개의 동적 공용 IP 주소를 만듭니다.

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp1 --domain-name-label contoso --allocation-method Dynamic

    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name PublicIp2 --domain-name-label fabrikam --allocation-method Dynamic
    ```

7. 각각 *contosofe* 및 *fabrikamfe*라는 두 개의 프런트 엔드 IP 구성을 만듭니다.

    ```azurecli
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp1 --name contosofe
    azure network lb frontend-ip create --resource-group contosofabrikam --lb-name mylb --public-ip-name PublicIp2 --name fabrkamfe
    ```

8. *contosopool* 및 *fabrikampool*이라는 백 엔드 주소 풀과 [프로브](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-load-balancer-health-probe) - *HTTP*를 만들고, *HTTPc* 및 *HTTPf*라는 부하 분산 규칙을 만듭니다.

    ```azurecli
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name contosopool
    azure network lb address-pool create --resource-group contosofabrikam --lb-name mylb --name fabrikampool

    azure network lb probe create --resource-group contosofabrikam --lb-name mylb --name HTTP --protocol "http" --interval 15 --count 2 --path index.html

    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPc --protocol tcp --probe-name http--frontend-port 5000 --backend-port 5000 --frontend-ip-name contosofe --backend-address-pool-name contosopool
    azure network lb rule create --resource-group contosofabrikam --lb-name mylb --name HTTPf --protocol tcp --probe-name http --frontend-port 5000 --backend-port 5000 --frontend-ip-name fabrkamfe --backend-address-pool-name fabrikampool
    ```

9. 아래 명령을 실행한 후 출력을 확인하여 [부하 분산 장치가 제대로 만들어졌는지 확인](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#verify-the-load-balancer)합니다.

    ```azurecli
    azure network lb show --resource-group contosofabrikam --name mylb
    ```

10. 아래와 같이 VM1이라는 첫 번째 가상 컴퓨터에 대해 *myPublicIp*라는 [공용 IP를 만들고](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-public-ip-address), *mystorageaccont1*이라는 [저장소 계정을 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-storage-account).

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP --domain-name-label mypublicdns345 --allocation-method Dynamic

    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount1
    ```

11. 아래와 같이 VM1에 대해 *VM1-NIC*라는 [네트워크 인터페이스를 만들고](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-an-nic-to-use-with-the-linux-vm), *VM1-ipconfig2*라는 두 번째 IP 구성을 추가하고, [VM을 만듭니다](../virtual-machines/virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-the-linux-vms).

    ```azurecli
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic --ip-config-name VM1-ipconfig1 --public-ip-name myPublicIP --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic --name VM1-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM1 --location westcentralus --os-type linux --nic-name myNic --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount1 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

12. 두 번째 VM에 대해 10 ~ 11단계를 반복합니다.

    ```azurecli
    azure network public-ip create --resource-group contosofabrikam --location westcentralus --name myPublicIP2 --domain-name-label mypublicdns785 --allocation-method Dynamic
    azure storage account create --location westcentralus --resource-group contosofabrikam --kind Storage --sku-name GRS mystorageaccount3426
    azure network nic create --resource-group contosofabrikam --location westcentralus --subnet-vnet-name myVnet --subnet-name mySubnet --name myNic2 --ip-config-name VM2-ipconfig1 --public-ip-name myPublicIP2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/contosopool"
    azure network nic ip-config create --resource-group contosofabrikam --nic-name myNic2 --name VM2-ipconfig2 --lb-address-pool-ids "/subscriptions/<your subscription ID>/resourceGroups/contosofabrikam/providers/Microsoft.Network/loadBalancers/mylb/backendAddressPools/fabrikampool"
    azure vm create --resource-group contosofabrikam --name VM2 --location westcentralus --os-type linux --nic-name myNic2 --vnet-name VNet1 --vnet-subnet-name Subnet1 --availset-name myAvailabilitySet --vm-size Standard_DS3_v2 --storage-account-name mystorageaccount2 --image-urn canonical:UbuntuServer:16.04.0-LTS:latest --admin-username <your username>  --admin-password <your password>
    ```

13. 마지막으로 DNS 리소스 레코드가 부하 분산 장치의 각 프런트 엔드 IP 주소를 가리키도록 구성해야 합니다. 도메인을 Azure DNS에 호스트할 수 있습니다. Load Balancer와 Azure DNS를 사용하는 방법에 대한 자세한 내용은 [다른 Azure 서비스와 함께 Azure DNS 사용](../dns/dns-for-azure-services.md)을 참조하세요.



<!--HONumber=Dec16_HO1-->


