---
title: PowerShell을 사용 하 여 Azure 스팟 Virtual Machines 배포
description: Azure PowerShell를 사용 하 여 비용을 절감 하기 위해 Azure 스팟 Virtual Machines를 배포 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a2ad2eb197af613919efa4414da1759cd47e2e7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802746"
---
# <a name="deploy-azure-spot-virtual-machines-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 스팟 Virtual Machines 배포


[Azure 지점 Virtual Machines](../spot-vms.md) 를 사용 하면 비용을 크게 절약할 수 있는 사용 되지 않는 용량을 활용할 수 있습니다. Azure에서 용량을 다시 필요로 하는 경우 언제 든 지 azure 인프라가 azure point Virtual Machines를 제거 합니다. 따라서 Azure 스팟 Virtual Machines는 일괄 처리 작업, 개발/테스트 환경, 큰 계산 워크 로드 등의 중단을 처리할 수 있는 워크 로드에 적합 합니다.

Azure 스폿 Virtual Machines의 가격은 지역 및 SKU에 따라 가변적입니다. 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)에 대 한 VM 가격 책정을 참조 하세요. 최대 가격을 설정 하는 방법에 대 한 자세한 내용은 [Azure 스폿 Virtual Machines-가격 책정](../spot-vms.md#pricing)을 참조 하세요.

VM에 대해 시간당 요금을 지불할 최대 가격을 설정 하는 옵션이 있습니다. Azure 스폿 가상 머신의 최대 가격은 미국 달러 (USD)로 설정 하 여 최대 5 개의 소수 자릿수를 사용할 수 있습니다. 예를 들어 값은 `0.98765` 시간당 $0.98765 USD의 최대 가격이 됩니다. 최대 가격을로 설정 하는 경우 `-1` 가격에 따라 VM이 제거 되지 않습니다. VM의 가격은 사용 가능한 용량과 할당량을 초과 하는 경우 더 작은 표준 VM의 현재 가격 또는 가격입니다.


## <a name="create-the-vm"></a>VM 만들기

[AzVmConfig](/powershell/module/az.compute/new-azvmconfig) 를 사용 하 여 spotVM를 만들어 구성을 만듭니다. 를 포함 하 `-Priority Spot` 고 `-MaxPrice` 를 다음 중 하나로 설정 합니다.
- `-1` 따라서 VM은 가격에 따라 제거 되지 않습니다.
- 최대 5 자리로 된 달러 금액입니다. 예를 들어은 `-MaxPrice .98765` spotVM에 대 한 가격이 시간당 $. 98765에 대 한 것이 되 면 VM의 할당이 취소 됩니다.


이 예제에서는 가격 책정에 따라 할당 취소 되지 않는 spotVM을 만듭니다 (Azure에서 용량을 다시 사용 해야 하는 경우에만). 제거 정책은 나중에 다시 시작할 수 있도록 VM의 할당을 취소 하도록 설정 됩니다. Vm을 제거할 때 VM 및 기본 디스크를 삭제 하려면 `-EvictionPolicy` 에서을로 설정 `Delete` `New-AzVMConfig` 합니다.


```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be an Azure Spot Virtual Machine

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1 -EvictionPolicy Deallocate | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

VM을 만든 후에 쿼리 하 여 리소스 그룹의 모든 Vm에 대 한 최대 가격을 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="simulate-an-eviction"></a>제거 시뮬레이션

REST, PowerShell 또는 CLI를 사용 하 여 Azure 스폿 가상 머신의 제거를 시뮬레이트하여 응용 프로그램이 갑작스러운 제거에 얼마나 잘 대응 하는지 테스트할 수 있습니다.

대부분의 경우 REST API [Virtual Machines 시뮬레이트 제거](/rest/api/compute/virtualmachines/simulateeviction) 를 사용 하 여 응용 프로그램의 자동화 된 테스트에 도움을 받을 수 있습니다. REST의 경우는 `Response Code: 204` 시뮬레이트된 제거를 성공적으로 완료 했음을 의미 합니다. 시뮬레이트된 제거를 [예약 된 이벤트 서비스](scheduled-events.md)와 결합 하 여 VM이 제거 될 때 앱이 응답 하는 방식을 자동화할 수 있습니다.

작동 중인 예약 된 이벤트를 보려면 azure [Scheduled Events를 사용 하 여 VM 유지 관리를 준비](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)합니다.


### <a name="quick-test"></a>빠른 테스트

시뮬레이션 된 제거의 작동 방식을 보여 주는 빠른 테스트를 위해 PowerShell을 사용 하 여 제거를 시뮬레이션 하는 경우 예약 된 이벤트 서비스를 쿼리하여 어떻게 되는지 확인 하는 방법을 살펴보겠습니다.

예약 된 이벤트 서비스는 이벤트에 대 한 요청을 처음 수행할 때 서비스에 대해 사용 하도록 설정 됩니다. 

VM에 원격으로 연결한 다음 명령 프롬프트를 엽니다. 

VM의 명령 프롬프트에서 다음을 입력 합니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

이 첫 번째 응답은 최대 2 분 정도 걸릴 수 있습니다. 지금부터 출력이 거의 즉시 표시 되어야 합니다.

Az PowerShell 모듈이 설치 된 컴퓨터 (예: 로컬 컴퓨터)에서 [new-azvm](https://docs.microsoft.com/powershell/module/az.compute/set-azvm)를 사용 하 여 제거를 시뮬레이션 합니다. 리소스 그룹 이름 및 VM 이름을 사용자 고유의 이름으로 바꿉니다. 

```azurepowershell-interactive
Set-AzVM -ResourceGroupName "mySpotRG" -Name "mySpotVM" -SimulateEviction
```

요청이 성공적으로 수행 되 면 응답 출력에이 포함 됩니다 `Status: Succeeded` .

신속 하 게 지점 가상 머신으로 원격 연결로 돌아가서 Scheduled Events 끝점을 다시 쿼리 합니다. 추가 정보를 포함 하는 출력을 가져올 때까지 다음 명령을 반복 합니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

예약 된 이벤트 서비스에서 제거 알림을 가져오면 다음과 유사한 응답이 표시 됩니다.

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

이를 확인할 수 `"EventType":"Preempt"` 있으며 리소스는 VM 리소스입니다 `"Resources":["myspotvm"]` . 

값을 확인 하 여 VM이 제거 되는 시기를 확인할 수도 있습니다 `"NotBefore"` . VM은에 지정 된 시간 전에는 제거 되지 않으므로 `NotBefore` 응용 프로그램이 정상적으로 종료 될 수 있는 창이 됩니다.


## <a name="next-steps"></a>다음 단계

[Azure CLI](../linux/spot-cli.md), [포털](../spot-portal.md) 또는 [템플릿을](../linux/spot-template.md)사용 하 여 Azure 스팟 가상 머신을 만들 수도 있습니다.

Azure 스팟 가상 머신 가격 책정에 대 한 자세한 내용은 [azure 소매 가격 API](/rest/api/cost-management/retail-prices/azure-retail-prices) 를 사용 하 여 현재 가격 정보를 쿼리 하세요. 및에는 `meterName` `skuName` 둘 다 포함 됩니다 `Spot` .

오류가 발생 하는 경우 [오류 코드](../error-codes-spot.md)를 참조 하세요.
