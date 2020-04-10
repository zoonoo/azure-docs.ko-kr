---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008644"
---
Azure 울트라 디스크는 Azure IaaS 가상 시스템(VM)에 대해 높은 처리량, 높은 IOPS 및 일관된 낮은 대기 시간 디스크 저장소를 제공합니다. 이 새 제품은 Microsoft의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. 울트라 디스크의 주요 이점 중 하나는 VM을 다시 시작할 필요 없이 워크로드와 함께 SSD의 성능을 동적으로 변경할 수 있다는 것입니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다.

## <a name="ga-scope-and-limitations"></a>GA 범위 및 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>VM 크기 및 지역 가용성 결정

### <a name="vms-using-availability-zones"></a>가용성 영역을 사용하는 VM

울트라 디스크를 활용하려면 사용 중인 가용성 영역을 결정해야 합니다. 모든 리전이 울트라 디스크로 모든 VM 크기를 지원하는 것은 아닙니다. 지역, 영역 및 VM 크기가 울트라 디스크를 지원하는지 확인하려면 다음 명령 중 하나를 실행하려면 **먼저 지역,** **vmSize**및 **구독** 값을 바꿔야 합니다.

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

응답은 선택한 지역에 배포하는 데 사용할 영역인 아래 양식과 유사합니다. X는 1, 2 또는 3이 될 수 있습니다.

**영역** 값을 보존하면 가용성 영역을 나타내며 Ultra 디스크를 배포하려면 해당 영역이 필요합니다.

|ResourceType  |이름  |위치  |영역  |제한 사항  |기능  |값  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 명령에서 응답이 없는 경우 선택한 VM 크기는 선택한 영역의 울트라 디스크에서 지원되지 않습니다.

배포할 영역을 알 수 있으므로 이 문서의 배포 단계를 따라 초디스크가 연결된 VM을 배포하거나 기존 VM에 울트라 디스크를 연결합니다.

### <a name="vms-with-no-redundancy-options"></a>중복 성 옵션이 없는 VM

미국 서부에 배포된 Ultra 디스크는 현재 중복 옵션 없이 배포해야 합니다. 그러나 울트라 디스크를 지원하는 모든 디스크 크기가 이 영역에 있는 것은 아닙니다. 미국 서부에서 울트라 디스크를 지원하는 디스크를 확인하려면 다음 코드 조각 중 하나를 사용할 수 있습니다. `vmSize` 먼저 및 `subscription` 값을 바꿔야 합니다.

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

응답은 다음 양식과 유사하며 `UltraSSDAvailable   True` VM 크기가 이 영역에서 울트라 디스크를 지원하는지 여부를 나타냅니다.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Azure 리소스 관리자를 사용하여 울트라 디스크 배포

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록은 GA [범위 및 제한](#ga-scope-and-limitations) 섹션을 참조하십시오.

여러 개의 울트라 디스크가 있는 VM을 만들려면 여러 [개의 울트라 디스크가 있는 VM 만들기](https://aka.ms/ultradiskArmTemplate)샘플을 참조하십시오.

사용자 고유의 템플릿을 사용하려는 경우 **해당 apiVersion에** 대해 `Microsoft.Compute/virtualMachines` `Microsoft.Compute/Disks` 설정하고 (또는 그 이상)으로 `2018-06-01` 설정되어 있는지 확인하십시오.

디스크 스쿠를 **UltraSSD_LRS**설정한 다음 MBps에서 디스크 용량, IOPS, 가용성 영역 및 처리량을 설정하여 울트라 디스크를 만듭니다.

VM을 프로비전한 후 데이터 디스크를 분할 및 포맷하고 워크로드용 데이터 디스크를 구성할 수 있습니다.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Azure 포털을 사용하여 울트라 디스크 배포

이 섹션에서는 데이터 디스크로 울트라 디스크가 장착된 가상 컴퓨터를 배포하는 것을 다룹니다. 가상 컴퓨터를 배포하는 데 익숙하지 않은 경우 [빠른 시작: Azure Portal에서 Windows 가상 컴퓨터 만들기를](../articles/virtual-machines/windows/quick-create-portal.md)참조하십시오.

- [Azure 포털에](https://portal.azure.com/) 로그인하고 탐색하여 VM(가상 시스템)을 배포합니다.
- [지원되는 VM 크기 및 지역을](#ga-scope-and-limitations)선택해야 합니다.
- **가용성 옵션에서** **가용성 영역을** 선택합니다.
- 나머지 항목을 선택한 항목으로 채웁니다.
- **디스크**를 선택합니다.

![생성 울트라 디스크 지원 vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- 디스크 블레이드에서 Ultra Disk **호환성을 사용하려면** **예를** 선택합니다.
- 지금 울트라 **디스크를 연결하려면 새 디스크 만들기를 선택하고 첨부합니다.**

![인에이블 및 어태치먼트 울트라 디스크.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- 새 디스크 블레이드 **만들기에서** 이름을 입력한 다음 **크기 변경을**선택합니다.
- 계정 유형을 **울트라 디스크로** **변경합니다.**
- **GiB(사용자 지정 디스크 크기)** 값, **디스크 IOPS**및 **디스크 처리량값을** 선택한 값으로 변경합니다.
- 두 블레이드에서 **확인을** 선택합니다.
- VM 배포를 계속하면 다른 VM을 배포하는 것과 동일합니다.

![생성 울트라 디스크.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Azure 포털을 사용하여 울트라 디스크 연결

또는 기존 VM이 울트라 디스크를 사용할 수 있는 영역/가용성 영역에 있는 경우 새 VM을 만들지 않고도 울트라 디스크를 사용할 수 있습니다. 기존 VM에서 울트라 디스크를 사용하도록 설정한 다음 데이터 디스크로 연결합니다.

- VM으로 이동하여 **디스크를 선택합니다.**
- **편집**을 선택합니다.

![옵션 선택기 울트라 디스크.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- **울트라 디스크 호환성을 사용하려면** **예를** 선택합니다.

![울트라 옵션 - 예 - 활성화.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- **저장**을 선택합니다.
- **이름** 에 대한 드롭다운에서 **데이터 디스크 추가를** 선택합니다. **Create disk**

![새로 연결된 울트라 disk.png 생성](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- 새 디스크의 이름을 입력한 다음 **크기 변경을**선택합니다.
- 계정 유형을 **울트라 디스크로** **변경합니다.**
- **GiB(사용자 지정 디스크 크기)** 값, **디스크 IOPS**및 **디스크 처리량값을** 선택한 값으로 변경합니다.
- **확인을** 선택한 다음 **을**선택합니다.

![새로운 울트라 디스크 를 만들기.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- 디스크 블레이드로 돌아온 후 **저장을**선택합니다.

![새로운 울트라 disk.png 를 절약하고 부착](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>Azure 포털을 사용하여 울트라 디스크의 성능 조정

울트라 디스크는 성능을 조정할 수 있는 고유한 기능을 제공합니다. 디스크 자체에서 Azure 포털에서 이러한 조정을 수행할 수 있습니다.

- VM으로 이동하여 **디스크를 선택합니다.**
- 성능을 수정할 울트라 디스크를 선택합니다.

![울트라 디스크-수정.png 선택](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- **구성을** 선택한 다음 수정합니다.
- **저장**을 선택합니다.

![구성-울트라 디스크 성능 및 크기.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>CLI를 사용하여 울트라 디스크 배포

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록은 [GA 범위 및 제한](#ga-scope-and-limitations) 섹션을 참조하십시오.

울트라 디스크를 연결하려면 울트라 디스크를 사용할 수 있는 VM을 만들어야 합니다.

**$vmname**, **$rgname**, **$diskname $location**, **$password,** **$user** 변수를 자신의 값으로 바꾸거나 설정합니다. **$password** **$zone** 이 문서의 시작 부터 얻은 가용성 [영역의](#determine-vm-size-and-region-availability)값으로 설정합니다. 그런 다음 다음 CLI 명령을 실행하여 울트라 사용 가능한 VM을 만듭니다.

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>기존 VM에서 울트라 디스크 호환성 지원

VM이 [GA 범위 및 제한 사항에](#ga-scope-and-limitations) 설명된 요구 사항을 충족하고 계정에 적합한 [영역에](#determine-vm-size-and-region-availability)있는 경우 VM에서 울트라 디스크 호환성을 활성화할 수 있습니다.

울트라 디스크 호환성을 사용하려면 VM을 중지해야 합니다. VM을 중지한 후 호환성을 활성화하고 울트라 디스크를 연결한 다음 VM을 다시 시작할 수 있습니다.

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>CLI를 사용하여 울트라 디스크 만들기

이제 울트라 디스크를 연결할 수 있는 VM을 통해 울트라 디스크를 만들고 연결할 수 있습니다.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>CLI를 사용하여 VM에 울트라 디스크 연결

또는 기존 VM이 울트라 디스크를 사용할 수 있는 영역/가용성 영역에 있는 경우 새 VM을 만들지 않고도 울트라 디스크를 사용할 수 있습니다.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>CLI를 사용하여 울트라 디스크의 성능 조정

울트라 디스크는 성능을 조정할 수있는 고유 한 기능을 제공하며 다음 명령은이 기능을 사용하는 방법을 설명합니다.

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>PowerShell을 사용하여 울트라 디스크 배포

먼저 배포할 VM 크기를 결정합니다. 지원되는 VM 크기 목록은 [GA 범위 및 제한](#ga-scope-and-limitations) 섹션을 참조하십시오.

울트라 디스크를 사용하려면 울트라 디스크를 사용할 수 있는 VM을 만들어야 합니다. **$resourcegroup** **변수를** 사용자 고유의 값으로 $vmName 바꾸거나 설정합니다. **$zone** 이 문서의 시작 부터 얻은 가용성 [영역의](#determine-vm-size-and-region-availability)값으로 설정합니다. 그런 다음 다음 [New-AzVm](/powershell/module/az.compute/new-azvm) 명령을 실행하여 울트라 사용 가능한 VM을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>기존 VM에서 울트라 디스크 호환성 지원

VM이 [GA 범위 및 제한 사항에](#ga-scope-and-limitations) 설명된 요구 사항을 충족하고 계정에 적합한 [영역에](#determine-vm-size-and-region-availability)있는 경우 VM에서 울트라 디스크 호환성을 활성화할 수 있습니다.

울트라 디스크 호환성을 사용하려면 VM을 중지해야 합니다. VM을 중지한 후 호환성을 활성화하고 울트라 디스크를 연결한 다음 VM을 다시 시작할 수 있습니다.

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>PowerShell을 사용하여 울트라 디스크 만들기

울트라 디스크를 사용할 수 있는 VM이 되었으므로 이제 울트라 디스크를 만들고 연결할 수 있습니다.

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>PowerShell을 사용하여 VM에 울트라 디스크 연결

또는 기존 VM이 울트라 디스크를 사용할 수 있는 영역/가용성 영역에 있는 경우 새 VM을 만들지 않고도 울트라 디스크를 사용할 수 있습니다.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>PowerShell을 사용하여 울트라 디스크의 성능 조정

울트라 디스크는 성능을 조정할 수있는 고유 한 기능을 가지고 있으며, 다음 명령은 디스크를 분리할 필요없이 성능을 조정하는 예입니다.

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```