---
title: "자습서 - Azure VM에서 고가용성 응용 프로그램 빌드 | Microsoft Docs"
description: "Azure에서 부하 분산 장치를 사용하여 3개의 Windows VM에서 고가용성 및 보안 응용 프로그램을 만드는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/30/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Azure의 Windows 가상 컴퓨터에서 부하 분산된 고가용성 응용 프로그램 빌드

이 자습서에서는 유지 관리 이벤트에 대해 복원력이 뛰어난 고가용성 응용 프로그램을 만듭니다. 앱은 부하 분산 장치, 가용성 집합 및 3개의 Windows VM(가상 컴퓨터)을 사용합니다. 이 자습서를 사용하여 동일한 고가용성 구성 요소 및 지침에 따라 다른 응용 프로그램 프레임워크를 배포할 수 있지만 이 자습서에서는 IIS를 설치합니다. 

## <a name="step-1---azure-prerequisites"></a>1단계 - Azure 필수 구성 요소

이 자습서를 완료하려면 최신 [Azure PowerShell](/powershell/azure/overview) 모듈을 설치했는지 확인합니다.

먼저 Login-AzureRmAccount 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다른 Azure 리소스를 만들려면 먼저 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)을 사용하여 리소스 그룹을 만들어야 합니다. 다음 예제에서는 `westeurope` 지역에 `myResourceGroup`이라는 리소스 그룹을 만듭니다. 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>2단계 - 가용성 집합 만들기

논리적 장애 및 업데이트 도메인 간에 가상 컴퓨터를 만들 수 있습니다. 각 논리 도메인은 기본 Azure 데이터 센터의 하드웨어 부분을 나타냅니다. 둘 이상의 VM을 만들 경우 Compute 및 저장소 리소스가 이러한 도메인 간에 분산됩니다. 이러한 분산은 하드웨어 구성 요소의 유지 관리가 필요할 경우 앱의 가용성을 유지해 줍니다. 가용성 집합을 통해 이러한 논리적 장애 및 업데이트 도메인을 정의할 수 있습니다.

[New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset)을 사용하여 가용성 집합을 만듭니다. 다음 예제는 `myAvailabilitySet`라는 가용성 집합을 만듭니다.

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>3단계 - 부하 분산 장치 만들기

Azure Load Balancer는 부하 분산 장치 규칙을 사용하여 정의된 VM 집합 간에 트래픽을 분산시킵니다. 상태 프로브가 각 VM에서 지정된 포트를 모니터링하고 작동하는 VM으로만 트래픽을 분산합니다.

### <a name="create-public-ip-address"></a>공용 IP 주소 만들기

인터넷에서 앱에 액세스하려면 공용 IP 주소를 부하 분산 장치에 할당합니다. [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)를 사용하여 공용 IP 주소를 만듭니다. 다음 예제에서는 `myPublicIP`라는 공용 IP 주소를 만듭니다.

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>부하 분산 장치 만들기

[New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig)를 사용하여 프런트 엔드 IP 주소를 만듭니다. 다음 예제에서는 `myFrontEndPool`라는 프런트 엔드 IP 주소를 만듭니다. 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

[New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig)를 사용하여 백 엔드 주소 풀을 만듭니다. 다음 예제는 `myBackEndPool`이라는 백 엔드 주소 풀을 만듭니다.

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

이제 [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer)를 사용하여 부하 분산 장치를 만듭니다. 다음 예제는 `myPublicIP` 주소를 사용하여 `myLoadBalancer`라는 부하 분산 장치를 만듭니다.

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>상태 프로브 만들기

부하 분산 장치가 앱의 상태를 모니터링하도록 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 기본적으로 VM은 15초 간격으로 두 번의 연속 실패 후에 부하 분산 장치 분산에서 제거됩니다.

[Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig)를 사용하여 상태 프로브를 만듭니다. 다음 예제에서는 각 VM을 모니터링하는 `myHealthProbe`라는 상태 프로브를 만듭니다.

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

부하 분산 장치 규칙은 VM으로 트래픽이 분산되는 방법을 정의하는 데 사용됩니다.

[Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig)를 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제는 `myLoadBalancerRule`이라는 부하 분산 장치 규칙을 만든 후 `80` 포트에 대한 트래픽 부하를 분산합니다.

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

[Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer)를 사용하여 부하 분산 장치를 업데이트합니다.

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>4단계 - 네트워킹 구성

각 VM에는 가상 네트워크에 연결되는 하나 이상의 가상 NIC(네트워크 인터페이스 카드)가 있습니다. 이 가상 네트워크는 정의된 액세스 규칙에 따라 트래픽을 필터링함으로써 보안이 유지됩니다.

### <a name="create-virtual-network"></a>가상 네트워크 만들기

먼저 [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig)를 사용하여 서브넷을 구성합니다. 다음 예제는 `mySubnet`이라는 서브넷을 만듭니다.

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

VM에 네트워크 연결을 제공하려면 [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)를 사용하여 가상 네트워크를 만듭니다. 다음 예제는 `mySubnet`를 사용하여 `myVnet`이라는 가상 네트워크를 만듭니다.

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>네트워크 보안 구성

Azure NSG([네트워크 보안 그룹](../../virtual-network/virtual-networks-nsg.md))는 하나 또는 여러 가상 컴퓨터에 대한 인바운드 및 아웃바운드 트래픽을 제어합니다. 네트워크 보안 그룹 규칙은 특정 포트 또는 포트 범위에 대한 네트워크 트래픽을 허용하거나 거부합니다. 이러한 규칙에는 미리 정의된 원본에서 시작되는 트래픽만 가상 컴퓨터와 통신할 수 있도록 원본 주소 접두사가 포함될 수도 있습니다.

웹 트래픽이 앱에 도달하게 하려면 [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)를 사용하여 네트워크 보안 그룹 규칙을 만듭니다. 다음 예제에서는 `myNetworkSecurityGroupRule`이라는 네트워크 보안 그룹 규칙을 만듭니다.

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)을 사용하여 네트워크 보안 그룹을 만듭니다. 다음 예제는 `myNetworkSecurityGroup`이라는 NSG를 만듭니다.

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)를 사용하여 네트워크 보안 그룹을 서브넷에 추가합니다.

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)를 사용하여 가상 네트워크를 업데이트합니다.

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>가상 네트워크 인터페이스 카드 만들기

부하 분산 장치는 실제 VM보다는 가상 NIC 리소스에 작동합니다. 가상 NIC는 부하 분산 장치에 연결된 후 VM에 연결됩니다.

[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface)를 사용하여 가상 NIC를 만듭니다. 다음 예제에서는 3개의 가상 NIC를 만듭니다. (다음 단계에서는 앱에 대한 가상 NIC를 각 VM에 대해 하나씩 만듭니다.)


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>5단계: 가상 컴퓨터 만들기

모든 기본 구성 요소가 구축되면 이제 앱을 실행하기 위한 고가용성 VM을 만들 수 있습니다. 

[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential)를 사용하여 가상 컴퓨터의 관리자 계정에 대한 사용자 이름 및 암호를 가져옵니다.

```powershell
$cred = Get-Credential
```

[New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) 및 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm)을 사용하여 VM을 만듭니다. 다음 예제에서는 3개의 가상 VM을 만듭니다.

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

3개의 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다. 부하 분산 장치 상태 프로브는 각 VM에서 앱을 실행될 경우를 자동으로 검색합니다. 앱이 실행되면 부하 분산 장치 규칙은 트래픽을 분산하기 시작합니다.

### <a name="install-the-app"></a>앱 설치 

Azure 가상 컴퓨터 확장은 응용 프로그램 설치 및 운영 체제 구성 등 가장 컴퓨터 구성 작업을 자동화하는 데 사용됩니다. [Windows용 사용자 지정 스크립트 확장](./../virtual-machines-windows-extensions-customscript.md)은 가상 컴퓨터에서 PowerShell 스크립트를 실행하는 데 사용됩니다. 스크립트를 Azure Storage, 액세스 가능한 HTTP 끝점에 저장하거나 사용자 지정 스크립트 확장 구성에 포함할 수 있습니다. 사용자 지정 스크립트 확장을 사용하는 경우 Azure VM 에이전트가 스크립트 실행을 관리합니다.

[Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension)을 사용하여 사용자 지정 스크립트 확장을 설치합니다. 이 확장이 `powershell Add-WindowsFeature Web-Server`을 실행하여 IIS 웹 서버를 설치합니다.

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>앱 테스트

[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 `myPublicIP`의 IP 주소를 가져옵니다.

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

웹 브라우저에 공용 IP 주소를 입력합니다. NSG 규칙이 설정되면 기본 IIS 웹 사이트가 표시됩니다. 

![IIS 기본 사이트](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>6단계 - 관리 작업

앱이 실행되는 동안 OS 업데이트와 같은 유지 관리 작업을 VM에서 수행해야 할 수 있습니다. 앱에 대해 증가된 트래픽을 처리하기 위해 VM을 더 추가해야 할 수 있습니다. 이 섹션에서는 부하 분산 장치에서 VM을 추가 또는 제거하는 방법을 보여 줍니다. 

### <a name="remove-a-vm-from-the-load-balancer"></a>부하 분산 장치에서 VM 제거

네트워크 인터페이스 카드의 LoadBalancerBackendAddressPools 속성을 재설정하여 백 엔드 주소 풀에서 VM을 제거합니다.

[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface)를 사용하여 네트워크 인터페이스 카드를 가져옵니다.

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

네트워크 인터페이스 카드의 LoadBalancerBackendAddressPools 속성을 $null로 설정합니다.

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

네트워크 인터페이스 카드 업데이트:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>부하 분산 장치에 VM 추가

VM 유지 관리를 수행한 이후 또는 용량을 확장해야 할 경우 VM의 NIC를 부하 분산 장치의 백 엔드 주소 풀에 추가합니다.

부하 분산 장치를 가져옵니다.

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

부하 분산 장치의 백 엔드 주소 풀을 네트워크 인터페이스 카드에 추가합니다.

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

네트워크 인터페이스 카드 업데이트:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>다음 단계

샘플 – [Azure Virtual Machine PowerShell 샘플 스크립트](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

