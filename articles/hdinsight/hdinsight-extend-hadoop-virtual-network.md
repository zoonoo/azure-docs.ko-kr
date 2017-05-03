---
title: "Virtual Network로 HDInsight 확장 | Microsoft 문서"
description: "Azure 가상 네트워크를 사용하여 HDInsight 다른 클라우드 리소스 또는 데이터 센터에서 리소스에 연결하는 방법을 알아봅니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 52038c02901d49f787226ce629a1ca5942414e4a
ms.lasthandoff: 04/21/2017


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Azure 가상 네트워크를 사용하여 HDInsight 기능 확장

HDInsight에서 Azure Virtual Networks를 사용하여 다음 시나리오를 구현하는 방법에 대해 배워보세요.

* HDInsight에 대한 액세스 제한. 예를 들어 인터넷으로부터의 인바운드 트래픽을 방지합니다.

* 인터넷에 노출되지 않은 HDInsight에서 서비스에 직접 액세스합니다. 예를 들어 Kafka broker와 직접 작업하거나 HBase Java API를 사용합니다.

* 서비스를 HDInsight에 직접 연결. 예를 들어 Oozie를 사용하여 데이터 센터 내에서 SQL Server로 데이터를 가져오거나 내보냅니다.

* 여러 HDInsight 클러스터가 포함된 솔루션 만들기. 예를 들어 Spark 또는 Storm을 사용하여 Kafka에 저장된 데이터를 분석합니다.

## <a name="prerequisites"></a>필수 조건

* Azure CLI 2.0: 자세한 내용은 [Azure CLI 2.0 설치 및 구성](https://docs.microsoft.com/cli/azure/install-az-cli2)을 참조하세요.

* Azure PowerShell: 자세한 내용은 [Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/overview)을 참조하세요.

> [!NOTE]
> 이 문서의 단계는 최신 버전의 Azure CLI 및 Azure PowerShell로 테스트되었습니다. 이전 버전을 사용하는 경우 명령이 달라질 수 있습니다. 최상의 결과를 위해 이전 링크를 사용하여 최신 버전을 설치합니다.

## <a id="whatis"></a>Azure Virtual Network란

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/) 에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다.

다음은 Virtual Network에서 HDInsight를 사용할 때 고려할 사항입니다.

* __클래식 및 Resource Manager Virtual Network__: 다음 표를 사용하여 HDInsight 클러스터 운영 체제를 기반으로 사용할 네트워크 유형을 결정합니다.

    | HDInsight 운영 체제 | 클래식 Virtual Network | Resource Manager Virtual Network |
    | ---- | ---- | ---- |
    | Linux | no | yes |
    | Windows | yes | no |

    호환되지 않는 Virtual Network의 리소스에 액세스하려면 두 네트워크를 연결합니다. 클래식과 Resource Manager Virtual Network 연결에 대한 내용은 [새 VNet에 클래식 VNet 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)을 참조하세요.

* __사용자 지정 DNS__: Azure는 Azure Virtual Network에 설치된 Azure 서비스에 대한 이름 확인을 제공합니다. 이 이름 확인은 Virtual Network 밖으로 확장되지 않습니다. Virtual Network 밖의 리소스에 대한 이름 확인을 사용하려면 사용자 지정 DNS 서버를 사용해야 합니다. 사용자 지정 DNS 서버에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 문서를 참조하세요.

* __강제 터널링__: HDInsight는 Azure Virtual Network의 강제 터널링 구성을 지원하지 않습니다.

* __네트워크 트래픽 제한__: HDInsight에서는 네트워크 보안 그룹을 사용하여 네트워크 트래픽을 제한할 수 없지만 여러 Azure IP에 대한 무제한 액세스를 요구합니다. 자세한 내용은 [보안 가상 네트워크](#secured-virtual-networks) 섹션을 참조하십시오.

### <a name="connect-cloud-resources-together-in-a-private-network-cloud-only"></a>개인 네트워크(클라우드 전용)에서 클라우드 리소스 연결

![클라우드 전용 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

가상 네트워크를 사용하여 Azure HDInsight와 함께 Azure 서비스를 연결하는 다음 시나리오를 사용합니다.

* **HDInsight 서비스 또는 작업을 호출** 합니다.

* **직접 데이터를 전송** 합니다.

* **여러 HDInsight 서버를 결합** 합니다. 워크로드 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식의 HDInsight 클러스터가 있습니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다. 가상 네트워크를 사용하면 여러 클러스터가 서로 직접 통신할 수 있습니다.

### <a name="connect-cloud-resources-to-a-local-datacenter-network"></a>로컬 데이터 센터 네트워크로 클라우드 리소스 연결

사이트 간 구성을 통해 데이터센터의 여러 리소스를 Azure Virtual Network에 연결할 수 있습니다. 하드웨어 VPN 장치 또는 라우팅 및 원격 액세스 서비스를 사용하여 연결할 수 있습니다.

![사이트 간 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.

![지점 및 사이트 간 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

가상 네트워크를 사용하여 클라우드와 데이터 센터에 링크하면 클라우드 전용 구성에 비슷한 시나리오를 사용할 수 있습니다. 클라우드의 리소스에 대한 작업으로 한정되는 것이 아니라 데이터 센터의 리소스에 대해 작업할 수도 있습니다.

* **직접 데이터를 전송** 합니다. 예를 들어 Sqoop을 사용하여 SQL Server에/에서 전송하거나 LOB(기간 업무) 응용 프로그램에서 생성된 데이터를 읽습니다.

* **HDInsight 서비스 또는 작업을 호출** 합니다. 예를 들어 HBase Java API를 사용하여 HDInsight HBase 클러스터에서 데이터를 저장 및 검색합니다.

가상 네트워크의 기능과 이점에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

> [!NOTE]
> HDInsight 클러스터를 프로비전하기 전에 Azure Virtual Network를 만든 다음 클러스터를 만들 때 네트워크를 지정하십시오. 자세한 내용은 [가상 네트워크 구성 작업](https://azure.microsoft.com/documentation/services/virtual-network/)을 참조하세요.

## <a name="secured-virtual-networks"></a>보안 가상 네트워크

HDInsight 서비스는 관리되는 서비스이며 프로비전하는 동안 및 실행하는 동안 Azure 관리 서비스에 대한 액세스가 필요합니다. Azure 관리는 다음과 같은 서비스를 수행합니다.

* 클러스터 상태 모니터링
* 클러스터 리소스의 장애 조치 시작
* 크기 조정 작업을 통해 클러스터에서 노드 수를 변경합니다.
* 기타 관리 작업

> [!NOTE]
> 이러한 작업은 인터넷에 대한 전체 액세스가 필요하지 않습니다. 인터넷 액세스를 제한할 경우 다음 IP 주소에 대해 포트 443의 인바운드 액세스를 허용하세요. 그러면 Azure에서 HDInsight를 관리할 수 있습니다.

허용되어야 하는 IP 주소는 HDInsight 클러스터 및 Virtual Network가 상주하는 하위 지역으로 특정됩니다. 다음 표를 사용하여 사용하는 하위 지역에 대한 IP 주소를 확인하세요.

| 국가 | 지역 | 허용된 IP 주소 | 허용되는 포트 |
| ---- | ---- | ---- | ---- |
| 브라질 | 브라질 남부 | 191.235.84.104</br>191.235.87.113 | 443 |
| 캐나다 | 캐나다 동부 | 52.229.127.96</br>52.229.123.172 | 443 |
| &nbsp; | 캐나다 중부 | 52.228.37.66</br>52.228.45.222 | 443 |
| 독일 | 독일 중부 | 51.4.146.68</br>51.4.146.80 | 443 |
| &nbsp; | 독일 북동부 | 51.5.150.132</br>51.5.144.101 | 443 |
| 인도 | 인도 중부 | 52.172.153.209</br>52.172.152.49 | 443 |
| 영국 | 영국 서부 | 51.141.13.110</br>51.141.7.20 | 443 |
| &nbsp; | 영국 남부 | 51.140.47.39</br>51.140.52.16 | 443 |
| 미국 | 미국 중서부 | 52.161.23.15</br>52.161.10.167 | 443 |
| &nbsp; | 미국 서부 2 | 52.175.211.210</br>52.175.222.222 | 443 |

__거주하는 지역이 표에 없을 경우__ 다음 IP 주소에 대해 포트 __443__에 대한 트래픽을 허용하세요.

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> HDInsight는 아웃바운드 트래픽 제한은 지원하지 않으며 인바운드 트래픽만 제한합니다. HDInsight를 포함하는 서브넷에 대해 네트워크 보안 그룹 규칙을 정의할 경우 __인바운드 규칙만 사용__합니다.

### <a name="working-with-hdinsight-in-secured-virtual-networks"></a>보안 가상 네트워크에서 HDInsight 작업

인터넷 액세스를 차단할 경우 클러스터를 위한 공용 게이트웨이를 통해 정상 노출되는 HDInsight 서비스를 사용할 수 없습니다. 여기에는 Ambari와 SSH가 포함됩니다. 대신 클러스터 헤드 노드의 내부 IP 주소를 사용하여 서비스에 액세스해야 합니다.

헤드 노드의 내부 IP 주소를 찾으려면 [내부 IP 및 FQDN](#internal-ips-and-fqdns) 섹션에 나와 있는 스크립트를 사용하세요.

### <a name="example-secured-virtual-network"></a>예: 보안 가상 네트워크

다음 예는 다음 IP 주소에서 포트 443에서의 인바운드 트래픽을 허용하는 네트워크 보안 그룹을 만드는 방법에 대해 설명합니다.

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

> [!IMPORTANT]
> 이러한 주소는 특정 IP 주소가 나열되지 않은 지역에만 해당합니다. 거주 지역의 IP 주소를 찾으려면 [보안 가상 네트워크](#secured-virtual-networks) 섹션에 나와 있는 정보를 사용하십시오.

이러한 단계에서는 HDInsight에 설치하려는 가상 네트워크 및 서브넷을 이미 만들었다고 가정합니다. [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)를 참조하세요.

> [!WARNING]
> 규칙은 네트워크 트래픽에 대해 __우선 순위__에 따라 순서대로 테스트됩니다. 규칙이 테스트 기준과 일치하면 해당 규칙이 적용되고 해당 요청에 대해서는 더 이상 규칙 테스트가 수행되지 않습니다. 인바운드 트래픽을 광범위하게 차단하는 규칙이 있는 경우(예: **deny all** 규칙) __반드시__ 트래픽을 허용하는 규칙 다음에 와야 합니다.
>
> 네트워크 보안 그룹 규칙에 대한 자세한 내용은 [네트워크 보안 그룹이란](../virtual-network/virtual-networks-nsg.md) 문서를 참조하세요.

**예: Azure 리소스 관리 템플릿**

다음과 같은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)의 리소스 관리 템플릿을 사용하여 보안 네트워크 구성으로 VNet에서 HDInsight 클러스터를 만듭니다.

[VNet 내에서 보안 유지된 Azure VNet 및 HDInsight Hadoop 클러스터 배포](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

**예: Azure PowerShell**

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that HDInsight will be installed into"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

**예: Azure CLI**

1. 다음 명령을 사용하여 `hdisecure`이라는 새 네트워크 보안 그룹을 만듭니다. **RESOURCEGROUPNAME**을 Azure Virtual Network를 포함하는 리소스 그룹으로 바꿉니다. **LOCATION**을 그룹이 만들어진 위치(지역)로 바꿉니다.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    그룹을 만들면 새 그룹에 대한 정보를 받습니다.

2. 다음을 사용하여 Azure HDInsight 상태 및 관리 서비스에서 포트 443에 대한 인바운드 통신을 허용하는 새 네트워크 보안 그룹에 규칙을 추가합니다. **RESOURCEGROUPNAME** 을 Azure 가상 네트워크를 포함하는 리소스 그룹 이름으로 바꿉니다.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162/24" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    ```

3. 규칙을 만들면 다음을 사용하여 이 서브넷 보안 그룹에 대해 고유 ID를 검색합니다.

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    예상된 결과를 얻지 못한 경우 명령에서 id에 따옴표를 넣어 사용합니다.

4. 다음 명령을 사용하여 네트워크 보안 그룹을 서브넷에 적용합니다. __GUID__ 및 __RESOURCEGROUPNAME__ 값을 이전 단계에서 반환된 값으로 대체합니다. __VNETNAME__ 및 __SUBNETNAME__을 HDInsight 클러스터를 만들 때 사용할 Virtual Network 이름과 서브넷 이름으로 대체합니다.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    이 명령이 완료되면 이 단계에 사용되는 서브넷에 대한 보안 가상 네트워크에 HDInsight를 성공적으로 설치할 수 있습니다.

> [!IMPORTANT]
> 위 단계를 사용하면 Azure 클라우드의 HDInsight 상태 및 관리 서비스에 대한 액세스만 열립니다. Virtual Network 외부에서 HDInsight 클러스터에 대한 기타 액세스는 차단됩니다. 가상 네트워크 외부에서 액세스할 수 있도록 하려는 경우 네트워크 보안 그룹 규칙을 추가해야 합니다.
>
> 다음 예제는 인터넷에서 SSH 액세스를 사용 설정하는 방법을 보여 줍니다.
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound
> ```
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
> ```

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/virtual-networks-nsg.md)를 참조하세요. Azure 가상 네트워크에서 라우팅 제어에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

## <a name="retrieve-internal-ips-and-fqdns"></a>내부 IP 및 FQDN 검색

가상 네트워크를 사용하여 HDInsight에 연결하는 경우 클러스터의 노드에 직접 연결할 수 있습니다. 다음 스크립트를 사용하여 클러스터의 노드에 대한 내부 IP 주소와 FQDN(정규화된 도메인 이름)을 확인합니다.

**Azure PowerShell**

```powershell
$resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

__Azure CLI__

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

> [!IMPORTANT]
> Azure CLI 2.0 예제에서 `<resourcegroupname>`을 가상 네트워크가 포함된 리소스 그룹 이름으로 바꿉니다.

이 스크립트는 해당 클러스터의 가상 NIC(네트워크 인터페이스 카드)를 쿼리하여 작동합니다. NIC는 HDInsight에서 사용하는 가상 네트워크가 포함된 리소스 그룹에 있습니다.

## <a id="nextsteps"></a>다음 단계

다음 예에서는 Azure 가상 네트워크에서 HDInsight를 사용하는 방법을 보여줍니다.

* [Azure Virtual Network의 HBase 클러스터](hdinsight-hbase-provision-vnet.md)

* [HDInsight의 Storm 및 HBase를 사용하여 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md)

* [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md)

* [HDInsight에서 Hadoop과 Sqoop 사용](hdinsight-use-sqoop-mac-linux.md)

Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)(영문)를 참조하세요.


