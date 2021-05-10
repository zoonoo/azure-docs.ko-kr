---
title: 백 엔드 풀 관리
titleSuffix: Azure Load Balancer
description: Azure Load Balancer의 백 엔드 풀을 구성하고 관리하는 방법을 알아봅니다.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: c49a721a4db758965c9cf8d71f5d73b5754b6088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654478"
---
# <a name="backend-pool-management"></a>백 엔드 풀 관리
백 엔드 풀은 부하 분산 장치의 중요한 구성 요소입니다. 백 엔드 풀은 지정된 부하 분산 규칙에 대한 트래픽을 제공하는 리소스 그룹을 정의합니다.

백 엔드 풀을 구성하는 두 가지 방법이 있습니다.
* NIC(네트워크 인터페이스 카드)
* IP 주소와 VNET(Virtual Network) 리소스 ID의 조합

기존 가상 머신 및 가상 머신 확장 집합을 사용하는 경우 NIC를 통해 백 엔드 풀을 구성합니다. 이 방법은 리소스와 백 엔드 풀 간에 가장 직접적인 링크를 작성합니다. 

나중에 가상 머신 및 가상 머신 확장 집합을 만드는 데 사용할 IP 주소 범위로 백 엔드 풀을 미리 할당하는 경우 IP 주소 및 VNET ID 조합을 통해 백 엔드 풀을 구성합니다.

동일한 부하 분산 장치에 대해 IP 기반 및 NIC 기반 백엔드 풀을 구성할 수 있지만 동일한 풀 내에서 NIC 및 IP 주소가 대상으로 하는 지원 주소를 혼합하는 단일 백엔드 풀을 만들 수 없습니다.

이 문서의 구성 섹션에서는 다음 항목에 중점을 둡니다.

* Azure PowerShell
* Azure CLI
* REST API
* Azure 리소스 관리자 템플릿 

이 섹션에서는 백 엔드 풀이 각 구성 옵션에 맞게 구성되는 방법에 대한 인사이트를 제공합니다.

## <a name="configuring-backend-pool-by-nic"></a>NIC를 통해 백 엔드 풀 구성
백 엔드 풀은 부하 분산 장치 작업의 일부로 만들어집니다. NIC의 IP 구성 속성은 백 엔드 풀 멤버를 추가하는 데 사용됩니다.

다음 예제에서는 이 워크플로 및 관계를 강조하기 위해 백 엔드 풀의 만들기 및 채우기 작업에 중점을 둡니다.

  >[!NOTE] 
  >네트워크 인터페이스를 통해 구성된 백 엔드 풀은 백 엔드 풀에 대한 작업의 일부로 업데이트할 수 없습니다. 백 엔드 리소스는 네트워크 인터페이스에서 추가하거나 삭제해야 합니다.

### <a name="powershell"></a>PowerShell
새 백 엔드 풀을 만듭니다.
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

새 네트워크 인터페이스를 만들고, 백 엔드 풀에 추가합니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

부하 분산 장치에 대한 백 엔드 풀 정보를 검색하여 이 네트워크 인터페이스가 백 엔드 풀에 추가되었는지 확인합니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

새 가상 머신을 만들고, 네트워크 인터페이스를 연결하여 백 엔드 풀에 배치합니다.

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
백 엔드 풀을 만듭니다.

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

새 네트워크 인터페이스를 만들고, 백 엔드 풀에 추가합니다.

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

백 엔드 풀을 검색하여 IP 주소가 올바르게 추가되었는지 확인합니다.

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

새 가상 머신을 만들고, 네트워크 인터페이스를 연결하여 백 엔드 풀에 배치합니다.

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="resource-manager-template"></a>Resource Manager 템플릿

이 [빠른 시작 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/)에 따라 부하 분산 장치 및 가상 머신을 배포하고, 네트워크 인터페이스를 통해 가상 머신을 백 엔드 풀에 추가합니다.

이 [빠른 시작 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-ip-configured-backend-pool)에 따라 부하 분산 장치 및 가상 머신을 배포하고, IP 주소를 통해 가상 머신을 백 엔드 풀에 추가합니다.


## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>IP 주소 및 가상 네트워크를 통해 백 엔드 풀 구성
미리 채워진 백 엔드 풀을 사용하는 시나리오에서는 IP 및 가상 네트워크를 사용합니다.

모든 백 엔드 풀 관리는 아래 예제에서 강조하듯이 백 엔드 풀 개체에서 직접 수행됩니다.

### <a name="powershell"></a>PowerShell
새 백 엔드 풀을 만듭니다.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

백 엔드 풀을 기존 가상 네트워크의 새 IP로 업데이트합니다.
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

부하 분산 장치에 대한 백 엔드 풀 정보를 검색하여 백 엔드 주소가 백 엔드 풀에 추가되었는지 확인합니다.

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
네트워크 인터페이스를 만들고, 백 엔드 풀에 추가합니다. IP 주소를 백 엔드 주소 중 하나로 설정합니다.

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

VM을 만들고, 백 엔드 풀의 IP 주소를 사용하여 NIC를 연결합니다.
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
CLI를 사용하면 명령줄 매개 변수 또는 JSON 구성 파일을 통해 백 엔드 풀을 채울 수 있습니다. 

명령줄 매개 변수를 통해 백 엔드 풀을 만들고 채웁니다.

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

JSON 구성 파일을 통해 백 엔드 풀을 만들고 채웁니다.

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON 구성 파일:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

부하 분산 장치에 대한 백 엔드 풀 정보를 검색하여 백 엔드 주소가 백 엔드 풀에 추가되었는지 확인합니다.

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

네트워크 인터페이스를 만들고, 백 엔드 풀에 추가합니다. IP 주소를 백 엔드 주소 중 하나로 설정합니다.

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

VM을 만들고, 백 엔드 풀의 IP 주소를 사용하여 NIC를 연결합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
 
### <a name="limitations"></a>제한 사항
IP 주소로 구성된 백 엔드 풀에는 다음과 같은 제한 사항이 있습니다.
  * 표준 부하 분산 장치에만 사용할 수 있습니다.
  * 백 엔드 풀의 IP 주소 100개 제한
  * 백 엔드 리소스가 부하 분산 장치와 동일한 가상 네트워크에 있어야 함
  * IP 기반 백 엔드 풀이 있는 Load Balancer는 Private Link 서비스로 작동할 수 없습니다.
  * 이 기능은 현재 Azure Portal에서 지원되지 않음
  * ACI 컨테이너는 현재 이 기능에서 지원되지 않습니다.
  * 부하 분산 장치 또는 Application Gateway와 같은 서비스를 부하 분산 장치의 백 엔드 풀에 배치할 수 없습니다.
  * 인바운드 NAT 규칙은 IP 주소로 지정할 수 없습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Load Balancer 백 엔드 풀 관리 및 IP 주소 및 가상 네트워크를 통해 백 엔드 풀을 구성하는 방법에 대해 알아보았습니다.

[Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).

IP 기반 백엔드 풀 관리에 대해 [REST API](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerbackendaddresspools/createorupdate)를 검토합니다.
