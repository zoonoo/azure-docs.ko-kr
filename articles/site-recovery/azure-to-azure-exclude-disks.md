---
title: Azure Site Recovery 및 Azure PowerShell를 사용 하 여 복제에서 Azure VM 디스크 제외
description: Azure PowerShell를 사용 하 여 Azure Site Recovery 중에 Azure 가상 컴퓨터의 디스크를 제외 하는 방법에 대해 알아봅니다.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75973671"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Azure Vm의 PowerShell 복제에서 디스크 제외

이 문서에서는 Azure Vm을 복제할 때 디스크를 제외 하는 방법을 설명 합니다. 사용 중인 복제 대역폭 또는 해당 디스크에서 사용 하는 대상 쪽 리소스를 최적화 하기 위해 디스크를 제외할 수 있습니다. 현재이 기능은 Azure PowerShell 통해서만 사용할 수 있습니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 수행합니다.

- [재해 복구 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해 하 고 있는지 확인 합니다.
- 모든 구성 요소에 대 한 [지원 요구 사항을](azure-to-azure-support-matrix.md) 검토 합니다.
- AzureRm PowerShell "Az" 모듈이 있는지 확인 합니다. PowerShell을 설치 하거나 업데이트 하려면 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조 하세요.
- Recovery services 자격 증명 모음 및 보호 된 가상 컴퓨터를 한 번 이상 만들었는지 확인 합니다. 이러한 작업을 수행 하지 않은 경우 [Azure PowerShell를 사용 하 여 Azure 가상 머신에 대 한 재해 복구 설정](azure-to-azure-powershell.md)의 프로세스를 따르세요.
- 복제용으로 사용 하도록 설정 된 Azure VM에 디스크를 추가 하는 방법에 대 한 정보를 찾고 있는 경우 [이 문서를 검토](azure-to-azure-enable-replication-added-disk.md)하세요.

## <a name="why-exclude-disks-from-replication"></a>복제에서 디스크를 제외 하는 이유
다음 이유로 인해 복제에서 디스크를 제외 해야 할 수 있습니다.

- 가상 머신이 [데이터 변경 비율을 복제 하는 Azure Site Recovery 제한](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)에 도달 했습니다.

- 제외 된 디스크에 변동 된 데이터는 중요 하지 않거나 복제할 필요가 없습니다.

- 데이터를 복제 하지 않고 저장소 및 네트워크 리소스를 저장 하려고 합니다.

## <a name="how-to-exclude-disks-from-replication"></a>복제에서 디스크를 제외 하는 방법

이 예제에서는 하나의 OS가 있는 가상 머신과 미국 동부 지역에 있는 세 개의 데이터 디스크를 미국 서 부 2 지역에 복제 합니다. 가상 컴퓨터의 이름은 *Azuredemovm*입니다. 디스크 1을 제외 하 고 2와 3 디스크를 유지 합니다.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>복제할 가상 컴퓨터의 세부 정보를 가져옵니다.

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

가상 컴퓨터의 디스크에 대 한 세부 정보를 가져옵니다. 이 정보는 나중에 VM의 복제를 시작할 때 사용 됩니다.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Azure 가상 머신 복제

다음 예에서는 캐시 저장소 계정, 복제 정책 및 매핑이 이미 있다고 가정 합니다. 이러한 항목이 없는 경우 [Azure PowerShell를 사용 하 여 Azure 가상 머신에 대 한 재해 복구 설정](azure-to-azure-powershell.md)의 프로세스를 따르세요.

*관리 디스크*를 사용 하 여 Azure 가상 머신을 복제 합니다.

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

복제 시작 작업이 성공 하면 VM 데이터가 복구 지역에 복제 됩니다.

Azure Portal로 이동 하 여 "복제 된 항목" 아래의 복제 된 Vm을 확인할 수 있습니다.

복제 프로세스는 복구 지역에서 가상 컴퓨터를 복제 하는 디스크의 복사본을 시드 하 여 시작 합니다. 이 단계를 초기 복제 단계 라고 합니다.

초기 복제가 완료 되 면 복제는 차등 동기화 단계로 이동 합니다. 이 시점에는 가상 머신이 보호됩니다. 보호 된 가상 컴퓨터를 선택 하 여 디스크가 제외 되는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

[테스트 장애 조치 (failover) 실행](site-recovery-test-failover-to-azure.md)에 대해 알아봅니다.
