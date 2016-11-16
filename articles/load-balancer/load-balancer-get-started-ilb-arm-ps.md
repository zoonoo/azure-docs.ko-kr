---
title: "리소스 관리자에서 PowerShell을 사용하여 내부 부하 분산 장치 만들기 | Microsoft Docs"
description: "리소스 관리자에서 PowerShell을 사용하여 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: c6c98981-df9d-4dd7-a94b-cc7d1dc99369
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 02d32ef115a6c2d9b0bb891231f3b45051ef0675


---
# <a name="create-an-internal-load-balancer-using-powershell"></a>PowerShell을 사용하여 내부 부하 분산 장치 만들기
[!INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

<BR>
[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]

[클래식 배포 모델](load-balancer-get-started-ilb-classic-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

다음 단계에서는 PowerShell과 함께 Azure Resource Manager를 사용하여 인터넷 부하 분산 장치를 만드는 방법을 설명합니다. Azure Resource Manager를 사용하면 내부 부하 분산 장치를 만드는 항목이 개별적으로 구성된 다음 결합되어 부하 분산 장치를 만듭니다.

부하 분산 장치를 배포하려면 다음 개체를 만들고 구성해야 합니다.

* 프런트 엔드 IP 구성 - 들어오는 네트워크 트래픽에 대한 개인 IP 주소를 구성합니다
* 백 엔드 주소 풀 - 프런트 엔드 IP 풀에서 들어오는 부하 분산된 트래픽을 수신하는 네트워크 인터페이스를 구성합니다.
* 부하 분산 규칙 - 부하 분산 장치의 원본 및 로컬 포트 구성입니다.
* 프로브 - 가상 컴퓨터 인스턴스의 상태 프로브를 구성합니다.
* 인바운드 NAT 규칙 - 가상 컴퓨터 인스턴스 중 하나에 직접 액세스하는 포트 규칙을 구성합니다.

Azure 리소스 관리자의 분산 장치 구성 요소에 대한 자세한 내용은 [부하 분산 장치에 대한 Azure 리소스 관리자 지원](load-balancer-arm.md)에서 확인할 수 있습니다.

다음 단계에서는 두 개의 가상 컴퓨터 간에 부하 분산 장치를 구성하는 방법을 설명합니다.

## <a name="setup-powershell-to-use-resource-manager"></a>리소스 관리자를 사용하도록 PowerShell 설치
PowerShell용 Azure 모듈이 최신 프로덕션 버전이고 Azure 구독에 액세스하도록 PowerShell이 제대로 설치되었는지 확인합니다.

### <a name="step-1"></a>1단계
        Login-AzureRmAccount

### <a name="step-2"></a>2단계
계정에 대한 구독을 확인합니다.

        Get-AzureRmSubscription

자격 증명을 사용하여 인증하라는 메시지가 표시됩니다.<BR>

### <a name="step-3"></a>3단계
사용할 Azure 구독을 선택합니다. <BR>

        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>부하 분산 장치에 대한 리소스 그룹 만들기
### <a name="step-4"></a>4단계
새 리소스 그룹을 만듭니다. 기존 리소스 그룹을 사용하는 경우에는 이 단계를 건너뛰세요.

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Azure 리소스 관리자를 사용하려면 모든 리소스 그룹이 위치를 지정해야 합니다. 이 위치는 해당 리소스 그룹에서 리소스의 기본 위치로 사용됩니다. 부하 분산 장치를 만드는 모든 명령에서 동일한 리소스 그룹을 사용하는지 확인합니다.

위 예제에서는 "NRP-RG"라는 리소스 그룹과 "West US"라는 위치를 만들었습니다.

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>프런트 엔드 IP 풀에 대한 개인 IP 주소 및 가상 네트워크 만들기
### <a name="step-1"></a>1단계
가상 네트워크의 서브넷을 만들고 변수 $backendSubnet에 할당합니다.

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

가상 네트워크 만들기:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

가상 네트워크를 만들고 NRPVNet 가상 네트워크에 lb-subnet-be 서브넷을 추가한 $vnet 변수에 할당합니다.

## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>프런트 엔드 IP 풀 및 백 엔드 주소 풀 만들기
들어오는 부하 분산 장치 네트워크 트래픽에 대한 프런트 엔드 IP 풀을 설정하고 부하 분산된 트래픽을 수신할 백 엔드 주소 풀을 설정합니다.

### <a name="step-1"></a>1단계
들어오는 네트워크 트래픽 끝점이 될 서브넷 10.0.2.0/24에 대해 개인 IP 주소 10.0.2.5를 사용하여 프런트 엔드 IP 풀을 만듭니다.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>2단계
프런트 엔드 IP 풀에서 들어오는 트래픽을 받는 데 사용되는 백 엔드 주소 풀을 설정합니다.

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>LB 규칙, NAT 규칙, 프로브 및 부하 분산 장치 만들기
프런트 엔드 IP 풀과 백 엔드 주소 풀을 만든 후에는 부하 분산 장치 리소스에 속하는 규칙을 만들어야 합니다.

### <a name="step-1"></a>1단계
    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


위 예제에서는 다음 항목을 만듭니다.

* 포트 3441로 들어오는 모든 트래픽을 포트 3389로 이동하는 NAT 규칙
* 포트 3442로 들어오는 모든 트래픽을 포트 3389로 이동하는 두 번째 NAT 규칙
* 공용 포트 80의 들어오는 모든 트래픽을 백엔드 주소 풀의 로컬 포트 80으로 부하 분산하는 부하 분산 장치 규칙
* 경로 "HealthProbe.aspx"에 대한 상태를 확인하는 프로브 규칙

### <a name="step-2"></a>2단계
모든 개체(NAT 규칙, 부하 분산 장치 규칙, 프로브 구성)를 함께 추가하는 부하 분산 장치를 만듭니다.

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>네트워크 인터페이스 만들기
내부 부하 분산 장치를 만든 후에는 들어오는 부하 분산된 네트워크 트래픽, NAT 규칙 및 프로브를 수신할 네트워크 인터페이스를 정의해야 합니다. 이 경우 네트워크 인터페이스는 개별적으로 구성되며 나중에 가상 컴퓨터에 할당할 수 있습니다.

### <a name="step-1"></a>1단계
네트워크 인터페이스를 만들 리소스 가상 네트워크 및 서브넷 가져오기:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


이 단계에서는 부하 분산 장치 백 엔드 풀에 속하는 네트워크 인터페이스를 만들고 이 네트워크 인터페이스의 RDP에 대한 첫 번째 NAT 규칙을 연결합니다.

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>2단계
LB-Nic2-BE라는 두 번째 네트워크 인터페이스 만들기:

이 단계에서는 두 번째 네트워크 인터페이스를 만들고 동일한 부하 분산 장치 백 엔드 풀에 할당하고 RDP에 대해 만든 두 번째 NAT 규칙을 연결합니다.

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

최종 결과는 다음과 같이 표시됩니다.

    $backendnic1

예상 출력:

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
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
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



### <a name="step-3"></a>3단계
Add-AzureRmVMNetworkInterface 명령을 사용하여 가상 컴퓨터에 NIC를 할당합니다.

가상 컴퓨터를 만드는 단계별 지침을 찾아서 [PowerShell을 사용하여 Azure VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md) 문서를 따라 NIC에 할당할 수 있습니다.

이미 가상 컴퓨터를 만든 경우 다음 단계로 네트워크 인터페이스를 추가할 수 있습니다.

#### <a name="step-1"></a>1단계
(아직 수행하지 않은 경우) 부하 분산 장치 리소스를 변수로 로드합니다. 사용되는 변수는 $lb라고 하고 위에서 만든 부하 분산 장치 리소스에서 동일한 이름을 사용합니다.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>2단계
백 엔드 구성을 변수로 로드합니다.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>3단계
이미 만든 네트워크 인터페이스를 변수로 로드합니다. 사용되는 변수 이름은 $nic입니다. 사용되는 네트워크 인터페이스 이름은 위의 예제와 같습니다.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>4단계
네트워크 인터페이스에서 백 엔드 구성을 변경합니다.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>5단계
네트워크 인터페이스 개체를 저장합니다.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

네트워크 인터페이스가 부하 분산 장치 백 엔드 풀에 추가된 후 해당 부하 분산 장치 리소스에 대한 부하 분산 규칙에 따라 네트워크 트래픽을 받기 시작합니다.

## <a name="update-an-existing-load-balancer"></a>기존 부하 분산 장치 업데이트
### <a name="step-1"></a>1단계
위의 예제에서 부하 분산 장치를 사용하여 부하 분산 장치 개체를 Get-AzureRmLoadBalancer를 사용하는 변수 $slb에 할당

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>2단계
다음 예제에서는 프런트 엔드에 있는 포트 81과 백 엔드 풀의 포트 8181을 사용하여 새 인바운드 NAT 규칙을 기존 부하 분산 장치에 추가

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>3단계
Set-AzureLoadBalancer를 사용하여 새 구성 저장

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>부하 분산 장치 제거하기
Remove-AzureRmLoadBalancer 명령을 사용하여 "NRP-RG"라는 리소스 그룹에서 이전에 생성한 "NRP-LB"라는 부하 분산 장치 삭제

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

> [!NOTE]
> 선택적 스위치 -Force를 사용하여 삭제에 대한 프롬프트를 방지할 수 있습니다.
> 
> 

## <a name="next-steps"></a>다음 단계
[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


