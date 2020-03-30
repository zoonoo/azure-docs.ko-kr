---
title: Azure 사이트 복구 및 Azure PowerShell을 사용 하 고 복제에서 Azure VM 디스크 제외
description: Azure PowerShell을 사용하여 Azure 사이트 복구 중에 Azure 가상 시스템의 디스크를 제외하는 방법을 알아봅니다.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973671"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Azure VM의 PowerShell 복제에서 디스크 제외

이 문서에서는 Azure VM을 복제할 때 디스크를 제외하는 방법을 설명합니다. 사용된 복제 대역폭 또는 해당 디스크가 사용하는 대상 리소스를 최적화하기 위해 디스크를 제외할 수 있습니다. 현재 이 기능은 Azure PowerShell을 통해서만 사용할 수 있습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 수행합니다.

- 재해 복구 아키텍처 [및 구성 요소를](azure-to-azure-architecture.md)이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항을](azure-to-azure-support-matrix.md) 검토합니다.
- AzureRm PowerShell "Az" 모듈이 있는지 확인합니다. PowerShell을 설치하거나 업데이트하려면 [Azure PowerShell 설치 모듈을](https://docs.microsoft.com/powershell/azure/install-az-ps)참조하십시오.
- 복구 서비스 자격 증명 모음을 만들고 가상 컴퓨터를 한 번 이상 보호했는지 확인합니다. 이러한 작업을 수행하지 않은 경우 [Azure PowerShell을 사용하여 Azure 가상 시스템에 대한 재해 복구 설정에서](azure-to-azure-powershell.md)프로세스를 따릅니다.
- 복제를 위해 사용하도록 설정된 Azure VM에 디스크를 추가하는 방법에 대한 정보를 찾고 있다면 [이 문서를 검토하십시오.](azure-to-azure-enable-replication-added-disk.md)

## <a name="why-exclude-disks-from-replication"></a>디스크를 복제에서 제외하는 이유
다음과 같은 이유로 디스크를 복제에서 제외해야 할 수 있습니다.

- 가상 시스템이 [Azure 사이트 복구 제한에 도달하여 데이터 변경 률을 복제합니다.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- 제외된 디스크에서 휘젓는 데이터는 중요하지 않거나 복제할 필요가 없습니다.

- 데이터를 복제하지 않음으로써 저장소 및 네트워크 리소스를 저장하려고 합니다.

## <a name="how-to-exclude-disks-from-replication"></a>복제에서 디스크를 제외하는 방법

이 예제에서는 미국 동부 지역에 있는 하나의 OS와 3개의 데이터 디스크가 있는 가상 컴퓨터를 미국 서부 2 지역으로 복제합니다. 가상 시스템의 이름은 *AzureDemoVM*입니다. 디스크 1은 제외하고 디스크 2와 3을 유지합니다.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>복제할 가상 시스템의 세부 정보 가져오기

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

가상 컴퓨터의 디스크에 대한 세부 정보를 가져옵니다. 이 정보는 나중에 VM 복제를 시작할 때 사용됩니다.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Azure 가상 시스템 복제

다음 예제에서는 캐시 저장소 계정, 복제 정책 및 매핑이 이미 있다고 가정합니다. 이러한 것들이 없는 경우 [Azure PowerShell을 사용하여 Azure 가상 시스템에 대한 재해 복구 설정에서](azure-to-azure-powershell.md)프로세스를 따릅니다.

관리 디스크를 가진 Azure 가상 컴퓨터를 *복제합니다.*

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication.

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

시작 복제 작업이 성공하면 VM 데이터가 복구 영역으로 복제됩니다.

Azure 포털로 이동하여 "복제된 항목" 아래에 복제된 VM을 볼 수 있습니다.

복제 프로세스는 복구 영역에서 가상 시스템의 복제 디스크 복사본을 시드하는 것으로 시작합니다. 이 단계를 초기 복제 단계라고 합니다.

초기 복제가 완료되면 복제가 차동 동기화 단계로 이동합니다. 이 시점에는 가상 머신이 보호됩니다. 보호된 가상 컴퓨터를 선택하여 디스크가 제외되는지 확인합니다.

## <a name="next-steps"></a>다음 단계

테스트 [장애 조치(failover) 실행에](site-recovery-test-failover-to-azure.md)대해 자세히 알아봅니다.
