---
title: Windows VM에서 여러 NIC 구성 | Microsoft Docs
description: Azure PowerShell 또는 Resource Manager 템플릿을 사용하여 여러 NIC가 연결된 VM을 만드는 방법에 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: timlt
editor: ''

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/04/2016
ms.author: iainfou

---
# 여러 NIC를 사용하여 VM 만들기
Azure에서 여러 가상 NIC(네트워크 인터페이스)가 연결된 VM(가상 컴퓨터)을 만들 수 있습니다. 일반적인 시나리오는 프런트 엔드 및 백 엔드 연결에 다른 서브넷을 사용하거나 모니터링 또는 백업 솔루션 전용 네트워크를 두는 것입니다. 이 문서에서는 여러 NIC가 연결된 VM을 만드는 빠른 명령을 제공합니다. 자체 PowerShell 스크립트 내에서 여러 NIC를 만드는 방법을 비롯한 자세한 내용은 [다중 NIC VM 배포](../virtual-network/virtual-network-deploy-multinic-arm-ps.md)에 대해 자세히 읽어보세요. [VM 크기](virtual-machines-windows-sizes.md)가 다르면 다양한 NIC가 지원되므로 그에 따라 VM 크기를 지정하도록 합니다.

> [!WARNING]
> VM을 만들 때 여러 NIC를 연결해야 합니다. 기존 VM에 NIC를 추가할 수 없습니다. [가상 디스크에 따라 새 VM을 만들고](virtual-machines-windows-specialized-image.md) VM을 배포할 때 여러 NIC를 만들 수 있습니다.
> 
> 

## 코어 리소스 만들기
먼저 [최신 Azure PowerShell을 설치 및 구성](../powershell-install-configure.md)했는지 확인합니다.

먼저 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

VM을 보유할 저장소 계정을 만듭니다.

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## 가상 네트워크 및 서브넷 만들기
두 개의 가상 네트워크 서브넷(프런트 엔드 트래픽용 및 백 엔드 트래픽용) 정의 다음 서브넷으로 가상 네트워크를 만듭니다.

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## 여러 NIC 만들기
2개의 NIC를 만들고 한 NIC를 프런트 엔드 서브넷에, 다른 NIC를 백 엔드 서브넷에 연결합니다.

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

일반적으로 [네트워크 보안 그룹](../virtual-network/virtual-networks-nsg.md) 또는 [부하 분산 장치](../load-balancer/load-balancer-overview.md)를 만들어 VM에서 트래픽을 관리하고 분산할 수 있습니다. [좀 더 자세한 다중 NIC VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) 문서에서 네트워크 보안 그룹 생성 및 NIC 할당에 대해 안내합니다.

## 가상 컴퓨터 만들기
이제 VM 구성 빌드를 시작합니다. VM 크기마다 VM에 추가할 수 있는 NIC의 총수가 제한되어 있습니다. [Windows VM 크기](virtual-machines-windows-sizes.md)에 대해 자세히 읽어보세요. 다음 예제에서는 최대 2개의 NIC를 지원하는 VM 크기를 사용합니다(`Standard_DS2_v2`).

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

이전에 만든 2개의 NIC를 연결합니다.

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

새 VM에 대한 저장소 및 가상 디스크를 구성합니다.

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

마지막으로 VM을 만듭니다.

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Resource Manager 템플릿을 사용하여 여러 NIC 만들기
Azure Resource Manager 템플릿은 선언적 JSON 파일을 사용하여 환경을 정의합니다. [Azure Resource Manager 개요](../resource-group-overview.md)에 대해 읽어볼 수 있습니다. Resource Manager 템플릿은 여러 NIC를 만드는 것과 같이 배포하는 동안 리소스의 여러 인스턴스를 만드는 방법을 제공합니다. *복사*를 사용하여 만들 인스턴스 수를 지정합니다.

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

[*복사*를 사용하여 여러 인스턴스 만들기](../resource-group-create-multiple.md)에 대해 자세히 읽어보세요.

`copyIndex()`를 사용하여 리소스 이름에 번호를 추가할 수도 있습니다. 이와 같이 `NIC1`, `NIC2` 등을 만들 수 있습니다. 다음은 인덱스 값을 추가하는 예를 보여 줍니다.

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

[Resource Manager 템플릿을 사용하여 여러 NIC 만들기](../virtual-network/virtual-network-deploy-multinic-arm-template.md)의 전체 예제를 읽어볼 수 있습니다.

## 다음 단계
여러 NIC를 사용하여 VM을 만들려고 할 때 [Windows VM 크기](virtual-machines-windows-sizes.md)를 검토해야 합니다. 각 VM 크기가 지원하는 NIC의 최대 수에 유의합니다.

기존 VM에 NIC를 더 추가할 수 없으므로 VM을 배포할 때 모든 NIC를 만들어야 합니다. 배포를 계획할 때 처음부터 필요한 모든 네트워크 연결이 있는지 확인합니다.

<!---HONumber=AcomDC_0817_2016-->