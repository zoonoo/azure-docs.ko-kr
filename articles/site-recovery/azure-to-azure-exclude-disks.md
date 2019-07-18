---
title: Azure Site Recovery-Azure PowerShell을 사용 하 여 Azure 가상 머신의 복제 하는 동안 디스크 제외 | Microsoft Docs
description: Azure PowerShell을 사용 하 여 Azure 사이트 복구 중 Azure virtual machines의 디스크를 제외 하는 방법에 알아봅니다.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160310"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Azure Vm의 PowerShell 복제에서 디스크 제외

이 문서에서는 Azure Vm을 복제 하는 경우 디스크를 제외 하는 방법을 설명 합니다. 소비 된 복제 대역폭 또는 해당 디스크를 사용 하는 대상 쪽 리소스를 최적화 하기 위해 디스크를 제외할 수 있습니다. 현재이 기능은 Azure PowerShell을 통해서만 사용할 수 있습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 수행합니다.

- 파악 해야 합니다 [재해 복구 아키텍처 및 구성 요소](azure-to-azure-architecture.md)합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](azure-to-azure-support-matrix.md)을 검토합니다.
- 했는지는 "Az" AzureRm PowerShell 모듈. 를 설치 하거나 PowerShell 업데이트를 참조 하세요 [Azure PowerShell 모듈을 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)합니다.
- Recovery services 자격 증명 모음을 생성 하 고 한 번 이상 보호 된 가상 컴퓨터를 확인 합니다. 이 프로세스에 따라 이러한 작업을 수행 하지 않았다면 [Azure PowerShell을 사용 하 여 Azure virtual machines에 대 한 재해 복구 설정](azure-to-azure-powershell.md)합니다.

## <a name="why-exclude-disks-from-replication"></a>복제에서 디스크를 제외 이유
때문에 복제에서 디스크를 제외할 수 해야 합니다.

- 가상 컴퓨터에 도달 했습니다 [데이터를 복제 하도록 Azure Site Recovery 제한 변경률이](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)합니다.

- 제외 된 디스크에서 변동 되는 데이터는 중요 하지 않은 또는 복제할 필요가 없습니다.

- 복제 하지 않고도 데이터 저장소 및 네트워크 리소스를 저장 해야 합니다.

## <a name="how-to-exclude-disks-from-replication"></a>복제에서 디스크를 제외 하는 방법

예제에서는 하나의 OS가 있는 가상 컴퓨터 및 미국 서 부 2 지역으로 미국 동부 지역에 있는 세 개의 데이터 디스크가 복제 했습니다. 가상 컴퓨터의 이름은 *AzureDemoVM*합니다. 1 디스크를 제외 하 고 디스크 2 및 3 유지 합니다.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>복제할 가상 머신의 세부 정보 가져오기

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

가상 머신의 디스크에 대 한 세부 정보를 가져옵니다. VM의 복제를 시작할 때이 정보를 나중에 사용 됩니다.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Azure 가상 머신 복제

다음 예를 들어 이미 캐시 저장소 계정, 복제 정책 및 매핑을 가정 합니다. 이러한 작업에 없는 경우에 프로세스를 따릅니다 [Azure PowerShell을 사용 하 여 Azure virtual machines에 대 한 재해 복구 설정](azure-to-azure-powershell.md)합니다.

사용 하 여 Azure 가상 머신 복제 *관리 디스크*합니다.

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

복제 시작 작업이 성공 하면 VM 데이터를 복구 지역에 복제 됩니다.

Azure portal로 이동 하 고 "복제 된 항목입니다." 아래에서 복제 된 Vm을 볼 수 있습니다.

복구 지역에서 가상 컴퓨터의 복제 디스크의 복사본을 시드하 여 복제 프로세스를 시작 합니다. 이 단계는 초기 복제 단계 라고 합니다.

초기 복제가 완료 되 면 복제 차등 동기화 단계를 이동 합니다. 이 시점에는 가상 머신이 보호됩니다. 모든 디스크는 제외 되는지 확인 하려면 보호 된 가상 컴퓨터를 선택 합니다.

## <a name="next-steps"></a>다음 단계

에 대 한 자세한 [테스트 장애 조치를 실행](site-recovery-test-failover-to-azure.md)합니다.
