---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 495326c172f900dc8bcff78b0df38f2cb64ed27e
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546537"
---
# <a name="enable-and-deploy-azure-ultra-ssds-preview"></a>사용 하도록 설정 하 고 Azure ultra Ssd (미리 보기) 배포

Azure ultra 반도체 (SSD) (미리 보기) 제품에 대 한 높은 처리량, 높은 IOPS 및 Azure IaaS 가상 머신 (Vm)에 대 한 일관 된 짧은 대기 시간 디스크 저장소 드라이브입니다. 이 새 제품은 Microsoft의 기존 디스크 제품과 동일한 가용성 수준에서 최상의 성능을 제공합니다. Ultra SSDs의 주요 이점 중 하나는 Vm을 다시 시작 하지 않고 워크 로드와 함께 SSD의 성능을 동적으로 변경 하려면 기능입니다. Ultra SSDs는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 작업이 많은 워크 로드와 같은 데이터 집약적인 워크 로드에 적합 합니다.

현재 ultra Ssd 미리 보기 상태 이며 해야 [등록](https://aka.ms/UltraSSDPreviewSignUp) 액세스 하기 위해 미리 보기 상태에서입니다.

## <a name="determine-your-availability-zone"></a>가용성 영역 확인

승인 되 면 ultra Ssd를 사용 하려면,은 현재 가용성 영역을 결정 해야 합니다. 미국 동부 2에서 ultra 디스크를 배포 하는 영역을 확인 하려면 다음 명령 중 하나를 실행 합니다.

PowerShell: `Get-AzComputeResourceSku | where {$_.ResourceType -eq "disks" -and $_.Name -eq "UltraSSD_LRS" }`

CLI: `az vm list-skus --resource-type disks --query “[?name==UltraSSD_LRS]”`

응답을 여기서 X는 미국 동부 2에에서 배포 하기 위해 사용 하는 영역 아래 폼 비슷할 것입니다. X는 1, 2 또는 3이 될 수 있습니다.

유지 된 **영역** 값 가용성 영역을 나타내므로 및 울트라 SSD를 배포 하기 위해 필요 합니다.

|ResourceType  |이름  |Location  |영역  |제한  |기능  |값  |
|---------|---------|---------|---------|---------|---------|---------|
|디스크     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

명령에서 응답이 없는 경우 기능에 등록 계속 중 하나는 아직 승인 되지 않음 또는 보류 합니다.

배포할 지역을 파악했으므로 이 문서의 배포 단계를 따라 울트라 SSD로 첫 번째 VM을 배포합니다.

## <a name="deploy-an-ultra-ssd-using-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 ultra SSD를 배포 합니다.

먼저, 배포 하는 VM 크기를 결정 합니다. 이 미리 보기의 일부인 DsV3 및 EsV3 VM 제품군만 지원됩니다. 이러한 VM 크기에 대 한 자세한 내용은 이 [블로그](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)의 두 번째 표를 참조하세요.

여러 ultra Ssd를 사용 하 여 VM 만들기를 원하는 경우 샘플을 참조 하세요 [여러 ultra SSD를 사용 하 여 VM 만들기](https://aka.ms/UltraSSDTemplate)합니다.

사용자 고유의 템플릿을 사용 하려는 경우 했는지 **apiVersion** 에 대 한 `Microsoft.Compute/virtualMachines` 하 고 `Microsoft.Compute/Disks` 으로 설정 됩니다 `2018-06-01` (또는 이상).

디스크 sku로 **UltraSSD_LRS**, 디스크 용량, IOPS, 가용성 영역 및 처리량 (mbps)는 초소형 디스크 만들기에 설정 합니다.

VM을 프로비전한 후 데이터 디스크를 분할 및 포맷하고 워크로드용 데이터 디스크를 구성할 수 있습니다.

## <a name="deploy-an-ultra-ssd-using-cli"></a>CLI를 사용 하 여 ultra SSD를 배포 합니다.

먼저, 배포 하는 VM 크기를 결정 합니다. 이 미리 보기의 일부인 DsV3 및 EsV3 VM 제품군만 지원됩니다. 이러한 VM 크기에 대 한 자세한 내용은 이 [블로그](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)의 두 번째 표를 참조하세요.

Ultra Ssd를 사용 하려면 ultra Ssd를 사용 하 여 수 있는 VM을 만들어야 합니다.

바꾸거나 설정 합니다 **$vmname**, **$rgname**를 **$diskname**를 **$location**를 **$password**, **$user** 고유한 값을 사용 하 여 변수입니다. 설정할 **$zone** 가용성 영역에서 가져온의 값에는 [이 문서의 시작](#determine-your-availability-zone)합니다. Ultra 설정 된 VM을 만드는 다음 CLI 명령을 실행 합니다.

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled --zone $zone --authentication-type password --admin-password $password --admin-username $user --attach-data-disks $diskname --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-ssd-using-cli"></a>CLI를 사용 하 여 ultra SSD 만들기

했으므로 ultra Ssd를 사용 하 여 수 있는 VM을 만들 수 있으며 ultra SSD에 연결.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an Ultra SSD disk
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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-cli"></a>CLI를 사용 하 여 ultra SSD의 성능을 조정합니다

Ultra Ssd 성능을 조정할 수 있도록 하는 고유 기능을 제공, 다음 명령은이 기능을 사용 하는 방법을 보여 줍니다.

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-ssd-using-powershell"></a>PowerShell을 사용 하 여 ultra SSD를 배포 합니다.

먼저, 배포 하는 VM 크기를 결정 합니다. 이 미리 보기의 일부인 DsV3 및 EsV3 VM 제품군만 지원됩니다. 이러한 VM 크기에 대 한 자세한 내용은 이 [블로그](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)의 두 번째 표를 참조하세요.

Ultra Ssd를 사용 하려면 ultra Ssd를 사용 하 여 수 있는 VM을 만들어야 합니다. 바꾸거나 설정 합니다 **$resourcegroup** 하 고 **$vmName** 고유한 값을 사용 하 여 변수입니다. 설정할 **$zone** 가용성 영역에서 가져온의 값에는 [이 문서의 시작](#determine-your-availability-zone)합니다. 다음 실행 [New-azvm](/powershell/module/az.compute/new-azvm) 를 크게 만들기 명령을 사용 합니다.

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

### <a name="create-an-ultra-ssd-using-powershell"></a>PowerShell을 사용 하 여 ultra SSD 만들기

Ultra Ssd를 사용 하 여 수 있는 VM을 설정 했으므로 만들고 하 ultra SSD 연결할:

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

### <a name="adjust-the-performance-of-an-ultra-ssd-using-powershell"></a>PowerShell을 사용 하 여 ultra SSD의 성능을 조정합니다

Ultra Ssd 성능을 조정할 수 있도록 하는 고유한 기능이 다음 명령은 디스크를 분리 하지 않고도 성능을 조정 하는 예제입니다.

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

## <a name="next-steps"></a>다음 단계

새 디스크 유형을 시도 하려는 경우 [이 설문 조사를 사용 하 여 미리 보기에 대 한 액세스를 요청](https://aka.ms/UltraSSDPreviewSignUp)합니다.