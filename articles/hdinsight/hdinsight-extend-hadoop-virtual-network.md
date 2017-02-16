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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7f63344455fafafc3a966c45742ba52d23177fa5
ms.openlocfilehash: 8c25c33535ce942ac63b9a259aa9e61765129d0a


---
# <a name="extend-hdinsight-capabilities-by-using-azure-virtual-network"></a>Azure 가상 네트워크를 사용하여 HDInsight 기능 확장
Azure Virtual Network를 사용하면 SQL Server와 같은 온-프레미스 리소스에 통합, 여러 HDInsight 클러스터 유형을 결합, 또는 클라우드의 리소스 간의 안전한 개인 네트워크를 만드는 Hadoop 솔루션을 확장할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure CLI 2.0 (미리 보기): 자세한 내용은 [CLI 2.0 설치 및 구성](https://docs.microsoft.com/cli/azure/install-az-cli2)을 참조하세요.

* Azure PowerShell: 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azureps-cmdlets-docs)을 참조하세요.

> [!NOTE]
> 이 문서의 단계는 최신 버전의 Azure CLI 및 Azure PowerShell로 테스트되었습니다. 이전 버전을 사용하는 경우 명령이 달라질 수 있습니다. 최상의 결과를 위해 이전 링크를 사용하여 최신 버전을 설치합니다.

## <a name="a-idwhatisawhat-is-azure-virtual-network"></a><a id="whatis"></a>Azure 가상 네트워크란?

[Azure 가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/) 에서는 솔루션에 필요한 리소스를 포함하는 안전한 영구 네트워크를 만들 수 있습니다. 가상 네트워크를 통해 다음을 수행할 수 있습니다.

* 개인 네트워크(클라우드 전용)에서 클라우드 리소스를 연결합니다.
  
    ![클라우드 전용 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)
  
    가상 네트워크를 사용하여 Azure HDInsight와 함께 Azure 서비스를 연결하는 다음 시나리오를 사용합니다.
  
    * **HDInsight 서비스 또는 작업을 호출** 합니다.
    * **직접 데이터를 전송** 합니다.
    * **여러 HDInsight 서버를 결합** 합니다. HDInsight 클러스터는 작업 부하 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식을 제공합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다. 가상 네트워크를 사용하면 여러 클러스터가 서로 직접 통신할 수 있습니다.

* VPN(가상 사설망)을 사용하여 클라우드 리소스를 로컬 데이터 센터 네트워크에 연결(사이트 간 또는 지점 및 사이트 간)
  
    사이트 간 구성에서는 하드웨어 VPN 또는 라우팅 및 원격 액세스 서비스를 사용하여 데이터 센터의 여러 리소스를 Azure 가상 네트워크에 연결할 수 있습니다.
  
    ![사이트 간 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)
  
    지점 및 사이트 간 구성에서는 소프트웨어 VPN을 사용하여 Azure 가상 네트워크에 특정 리소스를 연결할 수 있습니다.
  
    ![지점 및 사이트 간 구성 다이어그램](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)
  
    가상 네트워크를 사용하여 클라우드와 데이터 센터에 링크하면 클라우드 전용 구성에 비슷한 시나리오를 사용할 수 있습니다. 클라우드의 리소스에 대한 작업으로 한정되는 것이 아니라 데이터 센터의 리소스에 대해 작업할 수도 있습니다.
  
    * **직접 데이터를 전송** 합니다. 예를 들어 Sqoop을 사용하여 SQL Server에/에서 전송하거나 LOB(기간 업무) 응용 프로그램에서 생성된 데이터를 읽습니다.
    * **HDInsight 서비스 또는 작업을 호출** 합니다. 예를 들어 HBase Java API를 사용하여 HDInsight HBase 클러스터에서 데이터를 저장 및 검색합니다.

가상 네트워크의 기능과 이점에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 참조하세요.

> [!NOTE]
> HDInsight 클러스터를 프로비전하기 전에 Azure 가상 네트워크를 만들어야 합니다. 자세한 내용은 [가상 네트워크 구성 작업](https://azure.microsoft.com/documentation/services/virtual-network/)을 참조하세요.

## <a name="virtual-network-requirements"></a>가상 네트워크 요구 사항

> [!IMPORTANT]
> 가상 네트워크에 HDInsight 클러스터를 만들려면 이 섹션에 설명된 특정 가상 네트워크 구성이 필요합니다.

### <a name="location-based-virtual-networks"></a>위치 기반 가상 네트워크

Azure HDInsight는 위치 기반 가상 네트워크만 지원하며 현재 선호도 그룹을 기반으로 하는 가상 네트워크와는 연동되지 않습니다.

### <a name="classic-or-v2-virtual-network"></a>클래식 또는 v2 가상 네트워크

Windows 기반 클러스터에는 Classic Virtual Network가 필요하고, Linux 기반 클러스터에는 Azure Resource Manager Virtual Network가 필요합니다. 올바른 유형의 네트워크가 없으면, 클러스터를 만들어도 사용할 수 없습니다.

만들려는 클러스터에서 사용할 수 없는 가상 네트워크에 리소스가 있는 경우에는, 클러스터에서 사용할 수 있는 새로운 가상 네트워크를 만들어서 호환되지 않는 가상 네트워크에 연결할 수 있습니다. 그 후 필요한 네트워크 버전에서 클러스터를 만들면 두 네트워크가 연결되어 있기 때문에 다른 네트워크에 있는 리소스에 액세스할 수 있습니다. 클래식 가상 네트워크와 새 가상 네트워크 연결에 대한 내용은 [새 VNet에 클래식 VNet 연결](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)을 참조하세요.

### <a name="custom-dns"></a>사용자 지정 DNS

가상 네트워크를 만들 때 Azure는 네트워크에 설치된 HDInsight 같은 Azure 서비스에 대해 기본 이름 확인 기능을 제공합니다. 그러나 네트워크 간 도메인 이름 확인과 같은 상황의 경우 자체 DNS(Domain Name System)를 사용해야 할 수 있습니다. 예를 들어 연결된 두 가상 네트워크에 있는 서비스 간에 통신하는 경우가 여기에 해당합니다. HDInsight는 Azure 가상 네트워크에서 사용될 경우 사용자 지정 DNS와 기본 Azure 이름 확인을 모두 지원합니다.

Azure 가상 네트워크에서 자체 DNS 서버를 사용하는 방법에 대한 자세한 내용은 **VM 및 역할 인스턴스에 대한 이름 확인** 문서의 [자체 DNS 서버를 사용한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) 섹션을 참조하세요.

### <a name="secured-virtual-networks"></a>보안 가상 네트워크

HDInsight 서비스는 관리되는 서비스이며 프로비전하고 실행하는 동안 인터넷 액세스가 필요합니다. Azure에서 클러스터의 상태를 모니터링하고 클러스터 리소스의 장애 조치를 시작하며 기타 관리 작업 및 크기 조정 작업을 통해 클러스터의 노드 수를 변경할 수 있도록 합니다.

보안 가상 네트워크에 HDInsight를 설치해야 하는 경우 다음 IP 주소에 포트 443 통해 인바운드 액세스를 허용해야 하며 이를 통해 Azure에서 HDInsight 클러스터를 관리할 수 있게 됩니다.

> [!IMPORTANT]
> 허용되어야 하는 IP 주소는 HDInsight 클러스터 및 Virtual Network가 상주하는 하위 지역으로 특정됩니다. 다음을 사용하여 사용하는 하위 지역에 대한 IP 주소를 확인하세요.

__캐나다 동부__ 하위 지역:

* 52.229.127.96
* 52.229.123.172

__캐나다 중부__ 하위 지역:

* 52.228.37.66
* 52.228.45.222

__미국 중서부__ 하위 지역:

* 52.161.23.15
* 52.161.10.167

__미국 서부 2__ 하위 지역:

* 52.175.211.210
* 52.175.222.222

__다른 모든 하위 지역__:

* 168.61.49.99
* 23.99.5.239
* 168.61.48.131
* 138.91.141.162

이 주소에 포트 443에서 인바운드 액세스를 허용하면 보안 가상 네트워크에 HDInsight를 정상적으로 설치할 수 있습니다.

> [!IMPORTANT]
> HDInsight는 아웃바운드 트래픽 제한은 지원하지 않으며 인바운드 트래픽만 제한합니다. HDInsight를 포함하는 서브넷에 대해 네트워크 보안 그룹 규칙을 정의할 때는 인바운드 규칙만 사용합니다.

다음 예제는 필요한 주소를 허용하고 Virtual Network 내의 서브넷에 보안 그룹을 적용하는 새 네트워크 보안 그룹을 만드는 방법을 보여 줍니다. 이 예제에서 사용하는 주소는 위의 __모든 기타 하위 지역__에서 온 것입니다. 명시적으로 나열된 하위 지역 중 한 곳에 있는 경우(예: __미국 중서부__) 스크립트를 사용하여 해당 하위 지역에 대한 IP 주소를 사용하세요.

이러한 단계에서는 HDInsight에 설치하려는 가상 네트워크 및 서브넷을 이미 만들었다고 가정합니다.

> [!IMPORTANT]
> 이 예제에서 사용된 `priority` 값에 유의하세요. 규칙은 우선 순위에 따라 네트워크 트래픽에 대해 순서대로 테스트됩니다. 규칙이 테스트 기준과 일치하여 적용되면 규칙이 더 이상 테스트되지 않습니다.
> 
> 인바운드 트래픽을 광범위하게 차단하는 사용자 지정 규칙(예: **모두 거부** 규칙)이 있는 경우 이 예제 또는 사용자 지정 규칙의 우선 순위 값을 조정하여 예제의 규칙이 액세스를 차단하는 규칙보다 먼저 발생하도록 해야 할 수 있습니다. 그렇지 않으면 **모두 거부** 규칙이 먼저 테스트되며, 이 예제의 규칙은 절대로 적용되지 않습니다. 또한 Azure Virtual Network의 기본 규칙을 차단하지 않도록 주의해야 합니다. 예를 들어 우선 순위가 65000인 기본 **Vnet 인바운드 허용** 규칙보다 먼저 적용되는 **모두 거부** 규칙을 만들면 안됩니다.
> 
> 규칙을 적용하는 방법과 기본 인바운드 및 아웃 바운드 규칙에 대한 자세한 내용은 [ 네트워크 보안 그룹이란?](../virtual-network/virtual-networks-nsg.md)을 참조하세요.

**Azure PowerShell 사용**

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
    # Create a new Network Security Group.
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

**Azure CLI 사용**

1. 다음 명령을 사용하여 `hdisecure`이라는 새 네트워크 보안 그룹을 만듭니다. **RESOURCEGROUPNAME** 및 **LOCATION**을 그룹을 만든 Azure Virtual Network 및 위치(지역)를 포함하는 리소스 그룹으로 바꿉니다.
   
        az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION

    그룹을 만들면 새 그룹에 대한 정보를 받습니다.

2. 다음을 사용하여 Azure HDInsight 상태 및 관리 서비스에서 포트 443에 대한 인바운드 통신을 허용하는 새 네트워크 보안 그룹에 규칙을 추가합니다. **RESOURCEGROUPNAME** 을 Azure 가상 네트워크를 포함하는 리소스 그룹 이름으로 바꿉니다.
    
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
        az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"

3. 규칙을 만들면 다음을 사용하여 이 서브넷 보안 그룹에 대해 고유 ID를 검색합니다.

        az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'

    이 명령은 다음 텍스트와 유사한 값을 반환합니다.

        "/subscriptions/55b1016c-0f27-43d2-b908-b8c373d6d52e/resourceGroups/mygroup/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

4. 다음 명령을 사용하여 네트워크 보안 그룹을 서브넷에 적용합니다. __GUID__ 및 __RESOURCEGROUPNAME__ 값을 이전 단계에서 반환된 값으로 대체합니다. __VNETNAME__ 및 __SUBNETNAME__을 HDInsight 클러스터를 만들 때 사용할 Virtual Network 이름과 서브넷 이름으로 대체합니다.
   
        az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
   
    이 명령이 완료되면 이 단계에 사용되는 서브넷에 대한 보안 가상 네트워크에 HDInsight를 성공적으로 설치할 수 있습니다.

> [!IMPORTANT]
> 위 단계를 사용하면 Azure 클라우드의 HDInsight 상태 및 관리 서비스에 대한 액세스만 열립니다. 이를 통해 서브넷에 HDInsight 클러스터를 설치할 수 있지만 가상 네트워크 외부에서의 HDInsight 클러스터 액세스는 기본적으로 차단됩니다. 가상 네트워크 외부에서 액세스할 수 있도록 하려는 경우 네트워크 보안 그룹 규칙을 더 추가해야 합니다.
> 
> 예를 들어 인터넷에서 SSH 액세스를 허용하려면 다음과 비슷한 규칙을 추가해야 합니다. 
> 
> * Azure PowerShell - ```Add-AzureRmNetworkSecurityRuleConfig -Name "SSSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 304 -Direction Inbound```
> * Azure CLI - ```az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"```

네트워크 보안 그룹에 대한 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/virtual-networks-nsg.md)를 참조하세요. Azure 가상 네트워크에서 라우팅 제어에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

## <a name="a-idtasksatasks-and-information"></a><a id="tasks"></a>작업 및 정보

이 섹션에서는 일반적인 작업 정보 및 가상 네트워크에서 HDInsight를 사용할 때 필요한 정보를 알려줍니다.

### <a name="determine-the-fqdn"></a>FQDN를 결정합니다.

HDInsight 클러스터는 가상 네트워크 인터페이스에 대한 특정 FQDN(정규화된 도메인 이름)이 할당됩니다. 가상 네트워크에 있는 다른 리소스에서 클러스터에 연결할 때 사용해야 하는 주소입니다. FQDN을 확인하려면 다음 URL을 사용하여 Ambari 관리 서비스를 쿼리합니다.

    https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [!NOTE]
> HDInsight과 함께 Ambari를 사용하는 방법에 대한 자세한 내용은 [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링](hdinsight-monitor-use-ambari-api.md)을 참조하세요.

클러스터 이름, 서비스 및 YARN 리소스 관리자와 같은 클러스터에서 실행 중인 구성 요소를 지정해야 합니다.

> [!NOTE]
> 반환되는 데이터는 많은 구성 요소에 대한 정보가 포함된 JSON(JavaScript Object Notation) 문서입니다. FQDN만 추출하려면 JSON 파서를 사용하여 `host_components[0].HostRoles.host_name` 값을 검색해야 합니다.

예를 들어, HDInsight Hadoop 클러스터에서 FQDN을 반환하려면 다음 방법 중 하나를 사용하여 YARN 리소스 관리자에 대한 데이터를 검색할 수 있습니다.

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/        components/resourcemanager"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        $FQDN = $JsonObject.host_components[0].HostRoles.host_name
        Write-host $FQDN

* [cURL](http://curl.haxx.se/) 및 [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### <a name="connecting-to-hbase"></a>HBase에 연결

Java API를 사용하여 원격으로 HBase에 연결하려면 HBase 클러스터에 대한 Zookeeper 쿼럼 주소를 결정하고 응용 프로그램에서 이 값을 지정해야 합니다.

Zookeeper 쿼럼 주소를 얻으려면 다음 방법 중 하나를 사용하여 Ambari 관리 서비스를 쿼리합니다.

* [Azure PowerShell](/powershell/azureps-cmdlets-docs)
  
        $ClusterDnsName = <clustername>
        $Username = <cluster admin username>
        $Password = <cluster admin password>
        $DnsSuffix = ".azurehdinsight.net"
        $ClusterFQDN = $ClusterDnsName + $DnsSuffix
  
        $webclient = new-object System.Net.WebClient
        $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
  
        $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [cURL](http://curl.haxx.se/) 및 [jq](http://stedolan.github.io/jq/)
  
        curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [!NOTE]
> HDInsight과 함께 Ambari를 사용하는 방법에 대한 자세한 내용은 [Ambari API를 사용하여 HDInsight에서 Hadoop 클러스터 모니터링](hdinsight-monitor-use-ambari-api.md)을 참조하세요.

쿼럼 정보를 만든 후 클라이언트 응용 프로그램에서 사용합니다.

예를 들어, HBase API를 사용하는 Java 응용 프로그램의 경우 **hbase-site.xml** 파일을 프로젝트에 추가하고 다음과 같이 파일의 쿼럼 정보를 지정합니다.

```xml
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### <a name="verify-network-connectivity"></a>네트워크 연결 확인

SQL Server와 같은 일부 서비스는 들어오는 네트워크 연결을 제한할 수 있습니다. 이렇게 하면 HDInsight에서 이러한 서비스를 성공적으로 사용할 수 없습니다.

HDInsight에서 서비스에 액세스하는 문제가 발생하는 경우 네트워크 액세스를 사용할 수 있는지 확인하려면 서비스에 대한 설명서를 참조하세요. 동일한 가상 네트워크에 Azure 가상 컴퓨터를 만들어 네트워크 액세스를 확인하고 클라이언트 유틸리티를 사용하여 가상 네트워크를 통해 가상 컴퓨터에서 서비스에 연결할 수 있는지 확인할 수도 있습니다.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>다음 단계

다음 예에서는 Azure 가상 네트워크에서 HDInsight를 사용하는 방법을 보여줍니다.

* [HDInsight에서 Storm 및 HBase를 사용하여 센서 데이터 분석](hdinsight-storm-sensor-data-analysis.md) - 가상 네트워크에서 Storm 및 HBase 클러스터를 구성하는 방법과 Storm에서 HBase로 데이터를 원격으로 작성하는 방법을 보여 줍니다.
* [HDInsight에서 Hadoop 클러스터 프로비전](hdinsight-hadoop-provision-linux-clusters.md) - Azure 가상 네트워크를 사용하는 정보를 포함하여 Hadoop 클러스터를 프로비전하는 방법에 대한 정보를 제공합니다.
* [HDInsight에서 Hadoop과 함께 Sqoop 사용](hdinsight-use-sqoop-mac-linux.md) - Sqoop을 사용하여 가상 네트워크를 통해 SQL Server로 데이터를 전송하는 방법에 대한 정보를 제공합니다.

Azure 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)(영문)를 참조하세요.




<!--HONumber=Jan17_HO2-->


