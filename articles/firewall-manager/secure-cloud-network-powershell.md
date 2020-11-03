---
title: '자습서: Azure PowerShell을 사용하여 가상 허브 보호'
description: 이 문서에서는 허브에서 Azure Firewall을 사용하도록 설정된 한 지역에서 Azure Virtual WAN을 만드는 방법을 설명합니다.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: d9d9da9cd01b4bb9b3cabcf069990cd3cbd38d38
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428554"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>자습서: Azure PowerShell을 사용하여 가상 허브 보호

이 자습서에서는 한 지역에서 가상 허브가 있는 Virtual WAN 인스턴스를 만들고, Azure Firewall을 가상 허브에 배포하여 연결을 보호합니다. 이 예제에서는 Virtual Network 간의 보안 연결을 보여 줍니다. 가상 네트워크와 사이트 간, 지점 및 사이트 간 또는 ExpressRoute 지점 간의 트래픽도 가상 보안 허브에서 지원됩니다.

이 자습서에서는 다음 방법에 관해 알아봅니다.

> [!div class="checklist"]
> * 가상 WAN 배포
> * Azure Firewall 배포 및 사용자 지정 라우팅 구성
> * 연결 테스트

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- PowerShell 7

   이 자습서에서는 PowerShell 7에서 Azure PowerShell을 로컬로 실행해야 합니다. PowerShell 7을 설치하려면 [Windows PowerShell 5.1에서 PowerShell 7로 마이그레이션](https://docs.microsoft.com/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7)을 참조하세요.
- Az.Network 버전 3.2.0

    Az.Network 버전 3.4.0 이상이 있는 경우 이 자습서의 일부 명령을 사용하려면 다운그레이드해야 합니다. Az.Network 모듈의 버전은 `Get-InstalledModule -Name Az.Network` 명령을 사용하여 확인할 수 있습니다. Az.Network 모듈을 제거하려면 `Uninstall-Module -name az.network`를 실행합니다. Az.Network 3.2.0 모듈을 설치하려면 `Install-Module az.network -RequiredVersion 3.2.0 -force`를 실행합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>초기 Virtual WAN 배포

첫 번째 단계로 일부 변수를 설정하고 리소스 그룹, 가상 WAN 인스턴스 및 가상 허브를 만들어야 합니다.

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

두 개의 가상 네트워크를 만들고 이를 스포크로 허브에 연결합니다.

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

이 시점에서 모든 기능을 갖춘 Virtual WAN을 통해 임의 연결을 제공합니다. 보안을 강화하려면 Azure Firewall을 각 가상 허브에 배포해야 합니다. 방화벽 정책을 사용하여 가상 WAN Azure Firewall 인스턴스를 효율적으로 관리할 수 있습니다. 따라서 방화벽 정책도 다음 예제에서 만들어집니다.

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Azure Firewall에서 Azure Monitor로 로깅하도록 설정하는 것은 선택 사항이지만, 다음 예제에서는 방화벽 로그를 사용하여 트래픽이 방화벽을 통과하고 있음을 증명합니다.

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Azure Firewall 배포 및 사용자 지정 라우팅 구성

이제 Azure Firewall이 허브에 있지만 Virtual WAN이 방화벽을 통해 가상 네트워크 및 분기에서 트래픽을 보내도록 라우팅을 수정해야 합니다. 이 작업은 다음 두 단계로 수행됩니다.

1. `None` 경로 테이블에 전파되도록 모든 가상 네트워크 연결(및 있는 경우 분기 연결)을 구성합니다. 이 구성의 효과는 다른 가상 네트워크 및 분기에서 해당 접두사를 학습하지 않으므로 이러한 접두사에 도달할 수 있는 라우팅이 없다는 것입니다.
1. 이제 모든 트래픽을 Azure Firewall로 보내도록 정적 경로를 `Default` 경로 테이블(기본적으로 모든 가상 네트워크 및 분기가 연결되어 있는 위치)에 삽입할 수 있습니다.

> [!NOTE]
> 이는 Azure Firewall Manager를 사용하여 Azure Portal에서 연결을 보호할 때 배포되는 구성입니다.

첫 번째 단계부터 시작하여 가상 네트워크 연결이 `None` 경로 테이블로 전파되도록 구성합니다.

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

이제 두 번째 단계를 계속 진행하여 정적 경로를 `Default` 경로 테이블에 추가할 수 있습니다. 다음 예제에서는 Virtual WAN에서 연결을 보호하는 경우 Azure Firewall Manager에서 생성하는 기본 구성을 적용하지만, 특정 요구 사항에 맞게 정적 경로의 접두사 목록을 변경할 수 있습니다.

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>연결 테스트

이제 완벽하게 작동하는 보안 허브가 있습니다. 연결을 테스트하려면 허브에 연결된 각 스포크 가상 네트워크에 가상 머신이 하나씩 있어야 합니다.

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

방화벽 정책의 기본 구성은 모든 항목을 삭제하는 것입니다. 따라서 일부 규칙을 구성해야 합니다. 테스트 가상 머신에서 Firewall의 공용 IP 주소를 통해 액세스할 수 있도록 DNAT 규칙으로 시작합니다.

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

이제 몇 가지 규칙 예제를 구성할 수 있습니다. SSH 트래픽을 허용하는 네트워크 규칙 및 `ifconfig.co` 정규화된 도메인 이름에 대한 인터넷 액세스를 허용하는 애플리케이션 규칙을 정의합니다. 이 URL은 HTTP 요청에 표시되는 원본 IP 주소를 반환합니다.

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

실제로 트래픽을 보내기 전에 가상 머신의 유효 경로를 검사할 수 있습니다. Virtual WAN에서 학습한 접두사(`0.0.0.0/0` + RFC1918)를 포함해야 하지만 다른 스포크의 접두사는 포함할 수 없습니다.

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

이제 한 Virtual Machine에서 다른 Virtual Machine으로의 트래픽을 생성하고, Azure Firewall에서 해당 트래픽이 삭제되었는지 확인합니다. 다음 SSH 명령에서는 가상 머신 지문을 수락하고, 가상 머신을 만들 때 정의한 암호를 제공해야 합니다. 이 예제에서는 5개의 ICMP 에코 요청 패킷을 spoke1의 가상 머신에서 spoke2로 보내고, `nc` Linux 유틸리티를 사용하여 22 포트에서 TCP 연결을 시도합니다(`-vz` 플래그를 사용하면 연결 요청을 보내고 결과를 표시함). 이는 이전에 구성한 네트워크 규칙에서 허용되므로 ping이 실패하고 22 포트의 TCP 연결 시도가 성공했다고 표시됩니다.

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

또한 인터넷 트래픽을 확인할 수 있습니다. 방화벽 정책에서 허용한 FQDN(`ifconfig.co`)에 대한 `curl` 유틸리티를 통한 HTTP 요청은 작동하지만, 다른 대상에 대한 HTTP 요청은 실패합니다(다음 예제에서는 `bing.com`을 사용하여 테스트함).

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

방화벽에서 패킷이 삭제되었는지 확인하는 가장 쉬운 방법은 로그를 확인하는 것입니다. 로그를 Azure Monitor로 보내도록 Azure Firewall을 구성했으므로 Kusto 쿼리 언어를 사용하여 Azure Monitor에서 관련 로그를 검색할 수 있습니다.

> [!NOTE]
> Azure Monitor로 보낸 로그가 표시되는 데 1분 정도 걸릴 수 있습니다.

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

이전 명령에서는 다음과 같은 다른 항목이 표시됩니다.

* DNAT가 수행된 SSH 연결
* 스포크의 VM(10.1.1.4 및 10.1.2.4) 간에 삭제된 ICMP 패킷
* 스포크의 VM 간에 허용된 SSH 연결

위의 명령으로 생성되는 샘플 출력은 다음과 같습니다.

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

애플리케이션 규칙에 대한 로그(허용되거나 거부된 HTTP 연결 설명)를 보거나 로그가 표시되는 방법을 변경하려면 다른 KQL 쿼리를 사용하여 사용해 볼 수 있습니다. 몇 가지 예제는 [Azure Firewall에 대한 Azure Monitor 로그](../firewall/log-analytics-samples.md)에서 확인할 수 있습니다.


## <a name="clean-up-resources"></a>리소스 정리

테스트 환경을 삭제하려면 모든 개체가 포함된 리소스 그룹을 제거할 수 있습니다.

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [신뢰할 수 있는 보안 파트너에 대해 알아보기](trusted-security-partners.md)