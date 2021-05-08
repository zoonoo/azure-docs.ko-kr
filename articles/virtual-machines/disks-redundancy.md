---
title: Azure 관리 디스크에 대한 중복 옵션
description: Azure 관리 디스크에 대한 영역 중복 스토리지 및 로컬 중복 스토리지에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 0882efeccfc8dc83686d75ab39b8364219c3b5f1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588091"
---
# <a name="redundancy-options-for-managed-disks"></a>관리 디스크에 대한 중복성 옵션

Azure 관리 디스크는 ZRS(영역 중복 스토리지)를 미리 보기 및 로컬 중복 스토리지로 두 개의 스토리지 중복성 옵션을 제공합니다. ZRS는 LRS(로컬 중복 스토리지)보다 관리 디스크에 대한 고가용성을 제공합니다. 그러나 LRS 디스크는 단일 데이터 센터의 3개 복사본에 데이터를 동기식으로 쓰기 때문에 LRS 디스크의 쓰기 대기 시간이 ZRS 디스크보다 좋습니다.

## <a name="locally-redundant-storage-for-managed-disks"></a>관리 디스크에 대한 로컬 중복 스토리지

LRS(로컬 중복 스토리지)는 선택된 지역의 단일 데이터 센터 내에서 데이터를 세 번 복제합니다. LRS는 서버 랙 및 드라이브 오류로부터 데이터를 보호합니다. 

자연 재해 또는 하드웨어 문제로 인해 발생할 수 있는 전체 영역 오류에서 LRS 디스크를 사용하여 애플리케이션을 보호할 수 있는 몇 가지 방법이 있습니다.
- SQL Server AlwaysOn과 같은 애플리케이션을 사용하면 두 영역에 데이터를 동기적으로 쓸 수 있고 재해 발생 시 다른 영역으로 자동 장애 조치(failover)할 수 있습니다.
- ZRS 스냅샷을 사용하여 LRS 디스크를 자주 백업합니다.
- [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)를 통해 LRS 디스크에 대한 영역 간 재해 복구를 사용합니다. 그러나 영역 간 재해 복구는 0개의 RPO(복구 지점 목표)를 제공하지 않습니다.

워크플로가 영역 전체에서 애플리케이션 수준의 동기 쓰기를 지원하지 않거나 애플리케이션이 0 RPO를 충족해야 하는 경우 ZRS 디스크가 이상적입니다.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>관리 디스크에 대한 영역 중복 스토리지(미리 보기)

ZRS(영역 중복 스토리지)는 선택된 지역에 있는 3개의 Azure 가용성 영역에서 Azure 관리 디스크를 동기적으로 복제합니다. 각 가용성 영역은 독립적인 전원, 냉각 및 네트워킹을 갖춘 별도의 물리적 위치입니다. 

ZRS 디스크를 사용하면 가용성 영역의 오류를 복구할 수 있습니다. 전체 영역의 작동이 중단되면 다른 영역의 VM에 ZRS 디스크를 연결할 수 있습니다. 또한 ZRS 디스크를 공유 디스크로 사용하여 SQL FCI, SAP ASCS/SCS 또는 GFS2와 같은 클러스터 또는 분산 애플리케이션에 향상된 가용성을 제공할 수 있습니다. 공유 ZRS 디스크를 서로 다른 영역의 기본 및 보조 VM에 연결하여 ZRS 및 [가용성 영역](../availability-zones/az-overview.md)을 모두 활용할 수 있습니다. 주 영역에 오류가 발생하면 [SCSI 영구 예약](disks-shared-enable.md#supported-scsi-pr-commands)을 사용하여 보조 VM으로 신속하게 장애 조치(failover)할 수 있습니다.

### <a name="limitations"></a>제한 사항

미리 보기 중에 관리 디스크에 대한 ZRS에는 다음과 같은 제한 사항이 있습니다.

- 프리미엄 SSD(반도체 드라이브) 및 표준 SSD에서만 지원됩니다.
- 현재 EastUS2EUAP 지역에서만 사용할 수 있습니다.
- ZRS 디스크는 `2020-12-01` API를 사용하는 Azure Resource Manager 템플릿으로만 만들 수 있습니다.

[여기](https://aka.ms/ZRSDisksPreviewSignUp)에서 미리 보기를 등록합니다.

### <a name="billing-implications"></a>요금 청구 영향

자세한 내용은 [Azure 가격 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

### <a name="comparison-with-other-disk-types"></a>다른 디스크 유형과 비교

더 많은 쓰기 대기 시간을 제외하고 ZRS를 사용하는 디스크는 LRS를 사용하는 디스크와 동일합니다. 동일한 성능 목표를 가지고 있습니다. [디스크 벤치마킹](disks-benchmarks.md)을 수행하여 LRS와 ZRS 디스크 간의 대기 시간을 비교하기 위해 애플리케이션의 워크로드를 시뮬레이션하는 것이 좋습니다. 

### <a name="create-zrs-managed-disks"></a>ZRS 관리 디스크 만들기

Azure Resource Manager 템플릿과 함께 `2020-12-01` API를 사용하여 ZRS 디스크를 만듭니다.

#### <a name="create-a-vm-with-zrs-disks"></a>ZRS 디스크로 VM 만들기

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

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>다른 영역의 VM에 연결된 공유 ZRS 디스크를 사용하여 VM 만들기

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

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS 디스크를 사용하여 가상 머신 확장 집합 만들기

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

- 이 샘플 [Azure Resource Manager 템플릿을 사용하여 ZRS 디스크가 있는 VM을 만듭니다](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).
