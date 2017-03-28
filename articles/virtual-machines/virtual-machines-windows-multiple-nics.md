---
title: "여러 NIC를 사용하여 Windows VM 만들기 | Microsoft Docs"
description: "Azure PowerShell 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 연결된 Windows VM을 만드는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 95b2820d2f68be34cca7b8d414c581ba44a29804
ms.lasthandoff: 03/17/2017


---
# <a name="create-a-windows-vm-with-multiple-nics"></a>여러 NIC를 사용하여 Windows VM 만들기
Azure에서 여러 가상 NIC(네트워크 인터페이스)가 연결된 VM(가상 컴퓨터)을 만들 수 있습니다. 일반적인 시나리오는 프런트 엔드 및 백 엔드 연결에 다른 서브넷을 사용하거나 모니터링 또는 백업 솔루션 전용 네트워크를 두는 것입니다. 이 문서에서는 여러 NIC가 연결된 VM을 만드는 빠른 명령을 제공합니다. 자체 PowerShell 스크립트 내에서 여러 NIC를 만드는 방법을 비롯한 자세한 내용은 [다중 NIC VM 배포](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)에 대해 자세히 읽어보세요. [VM 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다.

## <a name="create-core-resources"></a>코어 리소스 만들기
먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azureps-cmdlets-docs)했는지 확인합니다. Azure 계정에 로그인합니다.

```powershell
Login-AzureRmAccount
```

다음 예제에서 매개 변수 이름을 고유한 값으로 바꿉니다. 예제 매개 변수 이름에 `myResourceGroup`, `mystorageaccount` 및 `myVM`가 포함됩니다.

먼저 리소스 그룹을 만듭니다. 다음 예제에서는 `WestUs` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

VM을 보유할 저장소 계정을 만듭니다. 다음 예제에서는 `mystorageaccount`라는 저장소 계정을 만듭니다.

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>가상 네트워크 및 서브넷 만들기
두 개의 가상 네트워크 서브넷(프런트 엔드 트래픽용 및 백 엔드 트래픽용) 정의 다음 예제에서는 `mySubnetFrontEnd` 및 `mySubnetBackEnd` 서브넷을 정의합니다.

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

가상 네트워크 및 서브넷을 만듭니다. 다음 예제에서는 `myVnet`이라는 가상 네트워크를 만듭니다.

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>여러 NIC 만들기
2개의 NIC를 만들고 한 NIC를 프런트 엔드 서브넷에, 다른 NIC를 백 엔드 서브넷에 연결합니다. 다음 예제에서는 `myNic1` 및 `myNic2`라고 하는 NIC 두 개를 만듭니다.

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

또한 일반적으로 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md) 또는 [부하 분산 장치](../load-balancer/load-balancer-overview.md)를 만들어 VM에서 트래픽을 관리하고 분산합니다. [좀 더 자세한 다중 NIC VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) 문서에서 네트워크 보안 그룹 생성 및 NIC 할당에 대해 안내합니다.

## <a name="create-the-virtual-machine"></a>가상 컴퓨터 만들기
이제 VM 구성 빌드를 시작합니다. VM 크기마다 VM에 추가할 수 있는 NIC의 총수가 제한되어 있습니다. [Windows VM 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 대해 자세히 읽어보세요. 

먼저 다음과 같이 `$cred` 변수에 VM 자격 증명을 설정합니다.

```powershell
$cred = Get-Credential
```

다음 예제에서는 `myVM`이라는 VM을 정의하고 최대 2개의 NIC를 지원하는 VM 크기(`Standard_DS2_v2`)를 사용합니다.

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

VM 구성의 나머지 부분을 만듭니다. 다음 예제에서는 Windows Server 2012 R2 VM을 만듭니다.

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

이전에 만든 2개의 NIC를 연결합니다.

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

새 VM에 대한 저장소 및 가상 디스크를 구성합니다.

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

마지막으로 VM을 만듭니다.

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="add-a-nic-to-an-existing-vm"></a>기존 VM에 NIC 추가

이제 기존 VM에 NIC를 추가할 수 있습니다. 이 기능을 사용하려면 먼저 아래의 Stop-AzureRmVM cmdlet을 사용하여 VM을 할당 취소해야 합니다.

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

다음으로, Get-AzureRmVM cmdlet을 사용하여 VM의 기존 구성을 가져옵니다.

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

이 문서의 시작 부분에 나와 있는 것처럼 **VM과 동일한 VNET**에서 새 NIC를 만들거나, 기존 NIC를 연결할 수 있습니다. VNET에서 기존 NIC `MyNic3`을 연결한다고 가정해 보겠습니다. 

```powershell
$nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId -Primary | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
```

> [!NOTE]
> 다중 NIC VM의 NIC 중 하나는 기본 NIC여야 하므로 새 NIC를 기본으로 설정합니다. VM의 이전 NIC가 기본 NIC인 경우에는 -Primary 스위치를 지정할 필요가 없습니다. VM에서 기본 NIC를 전환하려면 다음 단계를 수행하세요.

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"

# Find out all the NICs on the VM and find which one is Primary
$vm.NetworkProfile.NetworkInterfaces

# Set the NIC 0 to be primary
$vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
$vm.NetworkProfile.NetworkInterfaces[1].Primary = $false

# Update the VM state in Azure
Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
```

## <a name="remove-a-nic-from-an-existing-vm"></a>기존 VM에서 NIC 제거

VM에서 NIC를 제거할 수도 있습니다. 이 기능을 사용하려면 먼저 아래의 Stop-AzureRmVM cmdlet을 사용하여 VM을 할당 취소해야 합니다.

```powershell
Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
```

다음으로, Get-AzureRmVM cmdlet을 사용하여 VM의 기존 구성을 가져옵니다.

```powershell
$vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
```

이제 VM의 모든 NIC를 표시하고 제거할 NIC의 이름을 복사합니다.

```powershell
$vm.NetworkProfile.NetworkInterfaces

Remove-AzureRmNetworkInterface -Name "myNic3" -ResourceGroupName "myResourceGroup"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 여러 NIC 만들기
Azure Resource Manager 템플릿은 선언적 JSON 파일을 사용하여 환경을 정의합니다. [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)에 대해 읽어볼 수 있습니다. Resource Manager 템플릿은 여러 NIC를 만드는 것과 같이 배포하는 동안 리소스의 여러 인스턴스를 만드는 방법을 제공합니다. *복사* 를 사용하여 만들 인스턴스 수를 지정합니다.

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

[*복사*를 사용하여 여러 인스턴스 만들기](../azure-resource-manager/resource-group-create-multiple.md)에 대해 자세히 읽어보세요. 

`copyIndex()`를 사용하여 리소스 이름에 번호를 추가할 수도 있습니다. 이와 같이 `myNic1`, `MyNic2` 등을 만들 수 있습니다. 다음은 인덱스 값을 추가하는 예를 보여 줍니다.

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Resource Manager 템플릿을 사용하여 여러 NIC 만들기](../virtual-network/virtual-network-deploy-multinic-arm-template.md)의 전체 예제를 읽어볼 수 있습니다.

## <a name="next-steps"></a>다음 단계
여러 NIC를 사용하여 VM을 만들려고 할 때 [Windows VM 크기](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 를 검토해야 합니다. 각 VM 크기가 지원하는 NIC의 최대 수에 유의합니다. 



