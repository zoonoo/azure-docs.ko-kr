---
title: "여러 IP 구성의 부하 분산 | Microsoft Docs"
description: "기본 및 보조 IP 구성에서 부하 분산."
services: load-balancer
documentationcenter: na
author: anavinahar
manager: narayan
editor: na
ms.assetid: 244907cd-b275-4494-aaf7-dcfc4d93edfe
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2017
ms.author: annahar
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: 555f43d6fca2ac6ba53eae94bff22426d667c444
ms.lasthandoff: 03/11/2017

---

# <a name="load-balancing-on-multiple-ip-configurations"></a>여러 IP 구성의 부하 분산

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
>

이 문서는 가상 NIC(네트워크 인터페이스)당 여러 IP 주소가 있는 경우 Azure Load Balancer를 사용하는 방법을 설명합니다. NIC의 여러 IP 주소에 대한 지원은 현재 미리 보기 릴리스에 포함된 기능입니다. 자세한 내용은 이 문서의 [제한 사항](#limitations) 섹션을 참조하세요. 다음 시나리오는 이 기능이 Load Balancer와 작동하는 방식을 보여줍니다.

이 시나리오에는 Windows를 실행하는 VM이 둘 있고 각각 단일 NIC가 있습니다. 각 NIC에는 여러 IP 구성이 있습니다. 각 VM은 contoso.com 및 fabrikam.com 사이트를 둘 다 호스트합니다. 각 웹 사이트는 NIC의 IP 구성 중 하나로 바인딩됩니다. Load Balancer를 사용하여 웹 사이트의 각 IP 구성에 트래픽을 분산하기 위해 각 웹 사이트에 하나씩 두 개의 프런트 엔드 IP 주소를 노출합니다. 이 시나리오는 양 쪽 프런트 엔드는 물론 양쪽 백 엔드 풀 IP 주소에 동일한 포트 번호를 사용합니다.

![LB 시나리오 이미지](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

## <a name="limitations"></a>제한 사항

현재 보조 IP 구성에 대한 부하 분산 구성은 Azure PowerShell 및 Azure CLI를 사용하는 경우에만 가능합니다. 이 제한은 일시적이며 언제든지 변경될 수 있습니다. 업데이트를 확인하려면 이 페이지에 다시 방문해 주세요.

[!INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

로그인하고 적절한 구독을 선택한 후에 PowerShell에서 다음 명령을 실행하여 미리 보기에 등록합니다.

```
Register-AzureRmProviderFeature -FeatureName AllowMultipleIpConfigurationsPerNic -ProviderNamespace Microsoft.Network

Register-AzureRmProviderFeature -FeatureName AllowLoadBalancingonSecondaryIpconfigs -ProviderNamespace Microsoft.Network

Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

```Get-AzureRmProviderFeature``` 명령을 실행하면 다음과 같은 출력이 표시될 때까지 나머지 단계를 완료하지 마세요.
        
```powershell
FeatureName                            ProviderName      RegistrationState
-----------                            ------------      -----------------      
AllowLoadBalancingOnSecondaryIpConfigs Microsoft.Network Registered       
AllowMultipleIpConfigurationsPerNic    Microsoft.Network Registered       
```
        
>[!NOTE] 
>몇 분이 걸릴 수 있습니다.

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>여러 IP 구성의 부하를 분산하는 단계

아래 단계에 따라 이 문서에 설명된 시나리오를 수행합니다.

1. Azure PowerShell을 설치합니다. 최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azureps-cmdlets-docs)을 참조하세요.
2. 다음 설정을 사용하여 리소스 그룹을 만듭니다.

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    자세한 내용은 [리소스 그룹 만들기](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)의 2단계를 참조하세요.

3. VM을 포함할 [가용성 집합을 만듭니다](../virtual-machines/virtual-machines-windows-create-availability-set.md?toc=%2fazure%2fload-balancer%2ftoc.json). 이 시나리오에서는 다음 명령을 사용합니다.

    ```powershell
    New-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. NIC가 하나인 VM 만들기를 준비하려면 [Windows VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)에 있는 3 ~ 5단계의 지침을 수행합니다. 6.1단계를 실행하고 6.2단계 대신 다음을 사용합니다.

    ```powershell
    $availset = Get-AzureRmAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzureRmVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    그런 다음 [Windows VM 만들기](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)의 6.3 ~ 6.8단계를 완료합니다.

5. 각각의 VM에 두 번째 IP 구성을 추가합니다. [가상 컴퓨터에 여러 IP 주소 할당](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) 문서의 지침을 따릅니다. 다음 구성 설정을 사용합니다.

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    이 자습서의 경우 보조 IP 구성을 공용 IP와 연결할 필요가 없습니다. 명령을 편집하여 공용 IP 연결 부분을 제거합니다.

6. VM2에 대해 4 ~ 6단계를 다시 완료합니다. 이 단계를 수행할 때 VM 이름을 VM2로 바꿔야 합니다. 두 번째 VM에 대해 가상 네트워크를 만들 필요가 없습니다. 사용 사례를 기반으로 새 서브넷을 만들거나 만들지 않을 수 있습니다.

7. 두 개의 공용 IP 주소를 만들고 다음과 같이 적절한 변수에 저장합니다.

    ```powershell
    $publicIP1 = New-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzureRmPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzureRmPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. 두 개의 프런트 엔드 IP 구성을 만듭니다.

    ```powershell
    $frontendIP1 = New-AzureRmLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. 백 엔드 주소 풀, 프로브, 부하 분산 규칙을 만듭니다.

    ```powershell
    $beaddresspool1 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzureRmLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzureRmLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. 이러한 리소스를 만들었으면 부하 분산 장치를 만듭니다.

    ```powershell
    $mylb = New-AzureRmLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. 두 번째 백 엔드 주소 풀 및 프런트 엔드 IP 구성을 새로 만든 부하 분산 장치에 추가합니다.

    ```powershell
    $mylb = Get-AzureRmLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzureRmLoadBalancer

    $mylb | Add-AzureRmLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzureRmLoadBalancer
    
    Add-AzureRmLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzureRmLoadBalancer
    ```

12. 아래 명령은 NIC를 가져온 다음 보조 NIC 각각의 IP 구성 두 개를 부하 분산 장치의 백 엔드 주소 풀에 추가합니다.

    ```powershell
    $nic1 = Get-AzureRmNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzureRmNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzureRmLoadBalancer

    $nic1 | Set-AzureRmNetworkInterface
    $nic2 | Set-AzureRmNetworkInterface
    ```

13. 마지막으로 DNS 리소스 레코드가 부하 분산 장치의 각 프런트 엔드 IP 주소를 가리키도록 구성해야 합니다. 도메인을 Azure DNS에 호스트할 수 있습니다. Load Balancer와 Azure DNS를 사용하는 방법에 대한 자세한 내용은 [다른 Azure 서비스와 함께 Azure DNS 사용](../dns/dns-for-azure-services.md)을 참조하세요.

