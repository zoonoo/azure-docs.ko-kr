---
title: "PowerShell cmdlet을 사용하여 가상 컴퓨터 크기 집합 만들기 | Microsoft Docs"
description: "Azure PowerShell cmdlet을 사용하여 첫 번째 Azure 가상 컴퓨터 규모 집합 생성 및 관리 시작하기"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 430d9d64-1f35-48f0-a4fd-9b69910ffa59
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: danielsollondon
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 14f83c6753ce37639b1b2f78a4c632f1d69f585d


---
# <a name="creating-virtual-machine-scale-sets-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 가상 컴퓨터 크기 집합 만들기
VMSS(가상 컴퓨터 크기 집합)를 만드는 방법에 대한 예제입니다. 이 예제에서는 모두 연결된 네트워킹 및 저장소가 있는 세 가지 노드의 VMSS를 만듭니다.

## <a name="first-steps"></a>첫 번째 단계
최신 Azure PowerShell 모듈이 설치되어 있는지 확인합니다. 이 모듈에는 VMSS를 만들고 유지 관리하는 데 필요한 PowerShell commandlet이 포함되어 있습니다.
사용 가능한 최신 Azure 모듈을 보려면 [여기](http://aka.ms/webpi-azps)의 명령줄 도구로 이동하세요.

VMSS 관련 commandlet을 찾으려면 검색 문자열 \*VMSS\*를 사용하세요.

## <a name="creating-a-vmss"></a>VMSS 만들기
##### <a name="create-resource-group"></a>리소스 그룹 만들기
```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### <a name="create-storage-account"></a>저장소 계정 만들기
저장소 계정 유형/이름을 설정합니다.

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### <a name="create-networking-vnet--subnet"></a>네트워킹 만들기(VNET/서브넷)
##### <a name="subnet-specification"></a>서브넷 사양
```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### <a name="vnet-specification"></a>VNET 사양
```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### <a name="create-public-ip-resource-to-allow-external-access"></a>외부 액세스를 허용하도록 공용 IP 리소스 만들기
이는 부하 분산 장치에 바인딩됩니다.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### <a name="create-and-configure-load-balancer"></a>부하 분산 장치 만들기 및 구성
```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### <a name="configure-load-balancer"></a>부하 분산 장치 구성
백 엔드 주소 풀 구성을 만듭니다. 이는 VMSS에 있는 VM의 NIC에서 공유됩니다.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

부하 분산된 프로브 포트를 설정하고 응용 프로그램에 적합하게 설정을 변경합니다.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

부하 분산 장치를 통해 VMSS의 VM에 직접 라우팅 연결(부하 분산되지 않음)을 설정하는 NAT 규칙을 만듭니다. 여기에서는 RDP를 사용하지만 이는 데모를 위한 것이므로 이러한 서버에 RDP를 설정할 때는 내부 VNET 메서드를 사용해야 합니다.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

부하 분산 규칙을 만듭니다. 이 예제에서는 이전 단계의 설정을 사용한 부하 분산 포트 80 요청을 보여 줍니다.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

구성을 사용하여 부하 분산 장치를 만듭니다.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

LB 설정을 확인하고 부하 분산 포트 구성을 확인합니다. VMSS의 VM을 만들 때까지 인바운드 NAT 규칙은 표시되지 않습니다.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### <a name="configure-and-create-vmss"></a>VMSS 구성 및 만들기
이 인프라 예제에서는 VMSS에서 웹 트래픽 분산 및 확장을 설정하는 방법을 보여 주지만 여기에 지정된 VM 이미지에는 웹 서비스가 설치되어 있지 않습니다.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

부하 분산 장치 및 서브넷에 NIC 바인딩

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;
```

VMSS 구성 만들기

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

VMSS 구성 빌드

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

이제 VMSS를 만들었습니다. 다음 예제에서 RDP를 사용하여 개별 VM에 대한 연결을 테스트할 수 있습니다.

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```



<!--HONumber=Nov16_HO3-->


