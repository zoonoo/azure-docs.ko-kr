<properties 
   pageTitle="PowerShell을 사용하여 리소스 관리자에서 인터넷 연결 부하 분산 장치 만들기 | Microsoft Azure"
   description="PowerShell을 사용하여 리소스 관리자에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/24/2015"
   ms.author="joaoma" />

# PowerShell을 사용하여 리소스 관리자에서 인터넷 연결 부하 분산 장치 만들기 시작

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다. 또한 [클래식 배포 모델을 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

아래 단계에서는 PowerShell과 함께 Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 보여 줍니다. Azure 리소스 관리자를 사용하면 인터넷 연결 부하 분산 장치를 만드는 항목이 개별적으로 구성된 다음 함께 사용되어 리소스를 만듭니다.

부하 분산 장치를 만들기 위해 수행되는 개별 작업의 순서를 알아보고 부하 분산 장치를 만들기 위해 수행해야 하는 작업을 자세히 설명합니다.

## 인터넷 연결 부하 분산 장치를 만드는 데 필요한 항목은 무엇입니까?

부하 분산 장치를 배포하려면 다음 개체를 만들고 구성해야 합니다.

- 프런트 엔드 IP 구성 - 들어오는 네트워크 트래픽에 대한 공용 IP 주소를 포함합니다. 

- 백 엔드 주소 풀 - 부하 분산 장치의 네트워크 트래픽을 받는 가상 컴퓨터에 대한 NIC(네트워크 인터페이스)를 포함합니다.

- 부하 분산 규칙 - 백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.

- 인바운드 NAT 규칙 - 백 엔드 주소 풀에 있는 특정 가상 컴퓨터에 대한 포트에 부하 분산 장치의 공용 포트를 매핑하는 규칙을 포함합니다.

- 프로브 - 백 엔드 주소 풀의 가상 컴퓨터 인스턴스의 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다.

Azure 리소스 관리자의 분산 장치 구성 요소에 대한 자세한 내용은 [부하 분산 장치에 대한 Azure 리소스 관리자 지원](load-balancer-arm.md)에서 확인할 수 있습니다.


## 리소스 관리자를 사용하도록 PowerShell 설치

PowerShell용 Azure 모듈이 최신 프로덕션 버전이고 Azure 구독에 액세스하도록 PowerShell이 제대로 설치되었는지 확인합니다.

### 1단계

		PS C:\> Login-AzureRmAccount



### 2단계

계정에 대한 구독을 확인합니다.

		PS C:\> get-AzureRmSubscription 

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### 3단계 

사용할 Azure 구독을 선택합니다. <BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### 4단계

새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

    PS C:\> New-AzureRmResourceGroup -Name NRP-RG -location "West US"


## 프런트 엔드 IP 풀에 대한 공용 IP 주소 및 가상 네트워크 만들기

### 1단계

서브넷 및 가상 네트워크를 만듭니다.

	$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
	New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

### 2단계

DNS 이름이 *loadbalancernrp.westus.cloudapp.azure.com*인 프런트 엔드 IP 풀에서 사용할 *PublicIP*라는 PIP(공용 IP 주소)를 만듭니다. 아래 명령은 정적 할당 형식을 사용합니다.

	$publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location "West US" –AllocationMethod Static -DomainNameLabel loadbalancernrp 

>[AZURE.IMPORTANT]부하 분산 장치는 FQDN에 대한 접두사로 공용 IP의 도메인 레이블을 사용합니다. 이는 부하 분산 장치 FQDN으로 클라우드 서비스를 사용하는 클래식 배포 모델의 변경입니다. 이 예제에서는 FQDN이 *loadbalancernrp.westus.cloudapp.azure.com*입니다.

## 프런트 엔드 IP 풀 및 백 엔드 주소 풀 만들기

### 1단계 

*PublicIp* PIP를 사용하는 *LB-Frontend*라는 프런트 엔드 IP 풀을 만듭니다.

	$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP 

### 2단계 

*LB-backend*라는 백 엔드 주소 풀을 만듭니다.

	$beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"

## LB 규칙, NAT 규칙, 프로브 및 부하 분산 장치 만들기

아래 예제에서는 다음 항목을 만듭니다.

- 포트 3441~포트 3389에서 들어오는 모든 트래픽을 변환하는 NAT 규칙
- 포트 3442~포트 3389에서 들어오는 모든 트래픽을 변환하는 NAT 규칙
- 포트 80~포트 80에서 들어오는 모든 트래픽을 백 엔드 풀에 있는 주소로 분산하는 부하 분산 장치 규칙
- 경로 *HealthProbe.aspx* 페이지에 대한 상태를 확인하는 프로브 규칙
- 위의 모든 개체를 사용하는 부하 분산 장치



### 1단계

NAT 규칙을 만듭니다.

	$inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

	$inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

### 2단계

부하 분산 장치를 만듭니다.

	$lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

### 3단계

상태 프로브를 만듭니다.

	$healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

### 4단계

위에서 만든 개체를 사용하여 부하 분산 장치를 만듭니다.

	$NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe 

## NIC 만들기

NIC를 만들고(또는 기존 NIC 수정) NAT 규칙, 부하 분산 장치 규칙 및 프로브에 연결해야 합니다.

### 1단계 

NIC를 만들어야 하는 VNet 및 서브넷을 가져옵니다.

	$vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
	$backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet 

### 2단계

*lb-nic1-be*라는 NIC를 만들고 첫 번째 NAT 규칙 및 첫 번째(유일한) 백 엔드 주소 풀과 연결합니다.
	
	$backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### 3단계

*lb-nic2-be*라는 NIC를 만들고 두 번째 NAT 규칙 및 첫 번째(유일한) 백 엔드 주소 풀과 연결합니다.

	$backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

### 4단계

NIC를 확인합니다.


	PS C:\> $backendnic1

예상된 출력:

	Name                 : lb-nic1-be
	ResourceGroupName    : NRP-RG
	Location             : westus
	Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
	Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
	ProvisioningState    : Succeeded
	Tags                 :
	VirtualMachine       : null
	IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/"d448256a-e1df-413a-9103-a137e07276d1"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
	DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
	AppliedDnsSettings   :
	NetworkSecurityGroup : null
	Primary              : False



### 5단계

`Add-AzureRmVMNetworkInterface` cmdlet을 사용하여 NIC를 다른 VM에 할당합니다.

가상 컴퓨터를 만들고 NIC에 할당하는 방법에 대한 참고 자료는 [리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md#Example)에서 5가지의 옵션을 사용한 예제를 참조하세요.

## 기존 부하 분산 장치 업데이트


### 1단계

위의 예제에서 부하 분산 장치를 사용하여 부하 분산 장치 개체를 Get-AzureLoadBalancer를 사용하는 변수 $slb에 할당

	$slb=get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### 2단계

다음 예제에서는 프런트 엔드에 있는 포트 81과 백 엔드 풀의 포트 8181을 사용하여 새 인바운드 NAT 규칙을 기존 부하 분산 장치에 추가

	$slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### 3단계

Set-AzureLoadBalancer를 사용하여 새 구성 저장

	$slb | Set-AzureRmLoadBalancer

## 부하 분산 장치 제거하기

Remove-AzureLoadBalancer 명령을 사용하여 “NRP-RG”라는 리소스 그룹에서 이전에 생성한 "NRP-LB"라는 부하 분산 장치 삭제

	Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE]선택적 스위치 -Force를 사용하여 삭제에 대한 프롬프트를 방지할 수 있습니다.

## 다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-internal-getstarted.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->