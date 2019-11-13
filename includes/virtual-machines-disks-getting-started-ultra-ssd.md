---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3525edb2a73811254b2a4dce70ce3edb58988492
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012465"
---
Azure ultra disks는 Azure IaaS Vm (가상 머신)에 대 한 높은 처리량, 높은 IOPS 및 일관 된 짧은 대기 시간 디스크 저장소를 제공 합니다. 이 새 제품은 당사의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. 울트라 디스크의 주요 장점 중 하나는 Vm을 다시 시작할 필요 없이 워크 로드와 함께 SSD의 성능을 동적으로 변경 하는 기능입니다. Ultra disks는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적인 워크 로드와 같은 데이터를 많이 사용 하는 워크 로드에 적합 합니다.

## <a name="ga-scope-and-limitations"></a>GA 범위 및 제한 사항

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>VM 크기 및 지역 가용성 확인

Ultra disks를 활용 하려면 사용 중인 가용성 영역을 확인 해야 합니다. 모든 지역이 ultra disks를 사용 하는 모든 VM 크기를 지 원하는 것은 아닙니다. 영역, 영역 및 VM 크기가 ultra disks를 지원 하는지 확인 하려면 다음 명령 중 하나를 실행 하 여 **지역**, **vmsize**및 **구독** 값을 먼저 바꾸어야 합니다.

CLI:

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

응답은 아래와 유사 합니다. 여기서 X는 선택한 지역에서 배포 하는 데 사용 하는 영역입니다. X는 1, 2 또는 3이 될 수 있습니다.

**영역** 값을 유지 하 고, 가용성 영역을 나타내며, Ultra disk를 배포 하기 위해이 값이 필요 합니다.

|ResourceType  |이름  |Location  |영역  |제한  |기능  |값  |
|---------|---------|---------|---------|---------|---------|---------|
|디스크     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> 명령에서 응답이 없는 경우 선택한 VM 크기는 선택한 지역의 ultra disks에서 지원 되지 않습니다.

이제 배포할 영역을 알고 있으므로이 문서의 배포 단계에 따라 ultra 디스크가 연결 된 VM을 배포 하거나 기존 VM에 울트라 디스크를 연결 합니다.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 ultra disk 배포

먼저, 배포할 VM 크기를 결정 합니다. 지원 되는 VM 크기 목록은 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조 하세요.

여러 개의 ultra disks를 사용 하 여 VM을 만들려면 샘플 [단일 ultra disks를 사용 하 여 Vm 만들기](https://aka.ms/ultradiskArmTemplate)를 참조 하세요.

사용자 고유의 템플릿을 사용 하려는 경우 `Microsoft.Compute/virtualMachines` 및 `Microsoft.Compute/Disks`에 대 한 **apiVersion** `2018-06-01` 이상으로 설정 되어 있는지 확인 합니다.

디스크 sku를 **UltraSSD_LRS**설정 하 고 디스크 용량, IOPS, 가용성 영역 및 처리량을 MBps 단위로 설정 하 여 울트라 디스크를 만듭니다.

VM을 프로비전한 후 데이터 디스크를 분할 및 포맷하고 워크로드용 데이터 디스크를 구성할 수 있습니다.

## <a name="deploy-an-ultra-disk-using-cli"></a>CLI를 사용 하 여 ultra disk 배포

먼저, 배포할 VM 크기를 결정 합니다. 지원 되는 VM 크기 목록은 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조 하세요.

울트라 디스크를 연결 하기 위해 ultra disks를 사용할 수 있는 VM을 만들어야 합니다.

**$Vmname**, **$rgname**, **$diskname**, **$location**, **$password**, **$user** 변수를 고유한 값으로 바꾸거나 설정 합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 설정 합니다. 그런 다음, 다음 CLI 명령을 실행 하 여 ultra enabled VM을 만듭니다.

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>CLI를 사용 하 여 ultra disk 만들기

이제 ultra disks를 연결할 수 있는 VM이 있으므로 ultra 디스크를 만들어 연결할 수 있습니다.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>CLI를 사용 하 여 VM에 ultra disk 연결

또는 ultra disks를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 ultra disks를 사용할 수 있습니다.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>CLI를 사용 하 여 ultra disk의 성능 조정

울트라 디스크는 성능을 조정 하는 데 사용할 수 있는 고유한 기능을 제공 합니다. 다음 명령에서는이 기능을 사용 하는 방법을 보여 줍니다.

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>PowerShell을 사용 하 여 ultra disk 배포

먼저, 배포할 VM 크기를 결정 합니다. 지원 되는 VM 크기 목록은 [GA 범위 및 제한 사항](#ga-scope-and-limitations) 섹션을 참조 하세요. 이러한 VM 크기에 대 한 자세한 내용은을 참조 하세요.

Ultra disks를 사용 하려면 ultra disks를 사용할 수 있는 VM을 만들어야 합니다. **$Resourcegroup** 및 **$vmName** 변수를 고유한 값으로 바꾸거나 설정 합니다. [이 문서의 시작 부분](#determine-vm-size-and-region-availability)에서 가져온 가용성 영역 값으로 **$zone** 설정 합니다. 그런 후에 다음 [new-azvm](/powershell/module/az.compute/new-azvm) 명령을 실행 하 여 ULTRA enabled VM을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-an-ultra-disk-using-powershell"></a>PowerShell을 사용 하 여 ultra disk 만들기

이제 ultra disks를 사용할 수 있는 VM이 있으므로 ultra 디스크를 만들어 연결할 수 있습니다.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>PowerShell을 사용 하 여 VM에 ultra disk 연결

또는 ultra disks를 사용할 수 있는 지역/가용성 영역에 기존 VM이 있는 경우 새 VM을 만들지 않고도 ultra disks를 사용할 수 있습니다.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>PowerShell을 사용 하 여 ultra disk의 성능 조정

Ultra disks에는 성능을 조정할 수 있는 고유한 기능이 있습니다. 다음 명령은 디스크를 분리 하지 않고도 성능을 조정 하는 예제입니다.

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>다음 단계

새 디스크 유형을 사용 하려는 경우 [이 설문 조사를 통해 액세스를 요청](https://aka.ms/UltraDiskSignup)하세요.