---
title: Azure managed disks에 대 한 중복성 옵션
description: Azure managed disks에 대 한 영역 중복 저장소 및 로컬 중복 저장소에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043646"
---
# <a name="redundancy-options-for-managed-disks"></a>관리 디스크에 대 한 중복성 옵션

Azure managed disks는 ZRS (영역 중복 저장소)를 미리 보기 및 로컬 중복 저장소로 두 개의 저장소 중복성 옵션을 제공 합니다. ZRS는 LRS (로컬 중복 저장소) 보다 관리 디스크에 대 한 고가용성을 제공 합니다. 그러나 LRS 디스크는 단일 데이터 센터의 3 개 복사본에 데이터를 동기적으로 쓰기 때문에 LRS 디스크에 대 한 쓰기 대기 시간은 ZRS 디스크 보다 좋습니다.

## <a name="locally-redundant-storage-for-managed-disks"></a>관리 디스크에 대 한 로컬 중복 저장소

LRS (로컬 중복 저장소)는 선택한 지역의 단일 데이터 센터 내에서 데이터를 세 번 복제 합니다. LRS는 서버 랙 및 드라이브 오류로부터 데이터를 보호합니다. 

자연 재해 또는 하드웨어 문제로 인해 발생할 수 있는 전체 영역 오류에서 LRS 디스크를 사용 하 여 응용 프로그램을 보호할 수 있는 몇 가지 방법이 있습니다.
- SQL Server AlwaysOn과 같은 응용 프로그램을 사용 합니다 .이 응용 프로그램은 두 영역에 동기적으로 데이터를 쓸 수 있으며 재해 발생 시 다른 영역으로 자동으로 장애 조치 (failover)
- ZRS 스냅숏으로 LRS 디스크의 백업을 자주 수행 합니다.
- [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)를 통해 LRS 디스크에 대 한 영역 간 재해 복구를 사용 하도록 설정 합니다. 그러나 영역 간 재해 복구는 0 개의 RPO (복구 지점 목표)를 제공 하지 않습니다.

워크플로에서 응용 프로그램 수준 동기 쓰기를 영역 간에 지원 하지 않거나 응용 프로그램이 0 개의 RPO를 충족 해야 하는 경우 ZRS 디스크가 적합 합니다.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>관리 디스크에 대 한 영역 중복 저장소 (미리 보기)

ZRS (영역 중복 저장소)는 선택한 지역에 있는 세 개의 Azure 가용성 영역에서 Azure 관리 디스크를 동기적으로 복제 합니다. 각 가용성 영역은 독립적인 전원, 냉각 및 네트워킹을 갖춘 별도의 물리적 위치입니다. 

ZRS 디스크를 사용 하면 가용성 영역에서 오류를 복구할 수 있습니다. 전체 영역 작동이 중단 되 면 다른 영역의 VM에 ZRS 디스크를 연결할 수 있습니다. ZRS 디스크를 공유 디스크로 사용 하 여 SQL FCI, SAP ASCS/SCS 또는 GFS2와 같은 클러스터 된 응용 프로그램 또는 배포 된 응용 프로그램의 가용성을 향상 시킬 수도 있습니다. ZRS와 [가용성 영역](../availability-zones/az-overview.md)를 모두 활용 하기 위해 공유 된 ZRS 디스크를 다른 영역의 기본 및 보조 vm에 연결할 수 있습니다. 주 영역에 오류가 발생 하면 [SCSI 영구 예약](disks-shared-enable.md#supported-scsi-pr-commands)을 사용 하 여 보조 VM으로 신속 하 게 장애 조치할 수 있습니다.

### <a name="limitations"></a>제한 사항

미리 보기 중에 managed disks에 대 한 ZRS에는 다음과 같은 제한 사항이 있습니다.

- 프리미엄 SSD (반도체 드라이브) 및 표준 Ssd 에서만 지원 됩니다.
- 현재 EastUS2EUAP 지역 에서만 사용할 수 있습니다.
- ZRS 디스크는 API를 사용 하 여 Azure Resource Manager 템플릿만 만들 수 있습니다 `2020-12-01` .

[여기](https://aka.ms/ZRSDisksPreviewSignUp)에서 미리 보기에 등록 하세요.

### <a name="billing-implications"></a>요금 청구 영향

자세한 내용은 [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조 하세요.

### <a name="comparison-with-other-disk-types"></a>다른 디스크 유형과 비교

쓰기 대기 시간이 ZRS, 디스크를 사용 하는 디스크는 LRS를 사용 하는 디스크와 동일 합니다. 성능 목표는 동일 합니다.

### <a name="create-zrs-managed-disks"></a>ZRS managed disks 만들기

`2020-12-01`Azure Resource Manager 템플릿에 API를 사용 하 여 ZRS 디스크를 만듭니다.

#### <a name="create-a-vm-with-zrs-disks"></a>ZRS 디스크를 사용 하 여 VM 만들기

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>다른 영역의 Vm에 연결 된 공유 ZRS 디스크를 사용 하 여 Vm 만들기

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS 디스크를 사용 하 여 가상 머신 확장 집합 만들기

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>다음 단계

- 이러한 샘플 [Azure Resource Manager 템플릿을 사용 하 여 ZRS 디스크를 사용 하는 VM을 만듭니다](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).