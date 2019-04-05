---
title: Azure Site Recovery - Azure PowerShell을 사용하여 Azure 가상 머신을 복제하는 중 디스크 제외 | Microsoft Docs
description: Azure PowerShell을 사용한 Azure Site Recovery에서 Azure 가상 머신의 디스크를 제외하는 방법을 알아봅니다.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 1c278d810df7e5ba8701529a59987c9bb16fa40c
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044126"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure VM을 Azure로 복제하는 중 디스크 제외

이 문서에서는 Azure VM을 복제할 때 디스크를 제외하는 방법을 설명합니다. 이 제외는 그러한 디스크가 활용하는 대상 쪽 리소스를 최적화하거나 소비된 복제 대역폭을 최적화할 수 있습니다. 현재 이 기능은 Azure PowerShell을 통해서만 공개됩니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음을 수행합니다.

- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](azure-to-azure-support-matrix.md)을 검토합니다.
- Azure PowerShell을 사용할 `Az` 모듈입니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.
- 복구 서비스 자격 증명 모음을 이미 만들었으며 가상 머신을 한 번 이상 보호했습니다. 그렇지 않으면 언급 한 설명서를 사용 하 여 [여기](azure-to-azure-powershell.md)합니다.

## <a name="why-exclude-disks-from-replication"></a>복제에서 디스크를 제외하는 이유는?
다음과 같은 이유로 복제에서 디스크를 제외해야 하는 경우가 자주 있습니다.

- 가상 머신이 [복제 데이터 변경률에 대한 Azure Site Recovery 제한](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)에 도달했습니다.

- 제외되는 디스크에서 변동된 데이터는 중요하지 않거나 복제할 필요가 없습니다.

- 저장소 및 네트워크 리소스를 이 변동을 복제하지 않고 저장하고 싶습니다.


## <a name="how-to-exclude-disks-from-replication"></a>복제에서 디스크를 제외하는 방법은?

이 문서의 예제에서는 미국 동부 지역에 OS 1개와 데이터 디스크 3개가 있는 가상 머신이 미국 서부 2 지역에 복제됩니다. 이 예제에서 사용되는 가상 머신의 이름은 AzureDemoVM입니다. 디스크 1을 제외하고 디스크 2와 3을 유지하겠습니다.

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>복제할 가상 머신의 세부 정보 가져오기

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


가상 머신의 디스크에 대한 디스크 세부 정보를 가져옵니다. 디스크 세부 정보는 나중에 가상 머신에 대한 복제를 시작할 때 사용됩니다.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Azure Virtual Machine 복제

아래 예제에서는 캐시 스토리지 계정, 복제 정책 및 매핑이 이미 있다고 가정합니다. 그러지 않은 경우 [여기](azure-to-azure-powershell.md)에 언급된 문서를 사용하여 해당 작업을 수행합니다. 


**관리 디스크**를 사용하여 Azure Virtual Machine을 복제합니다.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

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


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

복제 시작 작업이 성공하면 가상 머신 데이터가 복구 지역으로 복제됩니다.

Azure Portal로 이동하면 복제된 항목 아래에서 가상 머신이 복제되는 것을 확인할 수 있습니다.
복제 프로세스는 처음에 복구 영역에 있는 가상 머신의 복제 디스크의 복사본을 시드하면서 시작됩니다. 이 단계를 초기 복제 단계라고 합니다.

초기 복제가 완료되면 복제는 차등 동기화 단계로 이동합니다. 이 시점에는 가상 머신이 보호됩니다. 보호된 가상 머신> 디스크를 클릭하여 디스크가 제외되었는지 여부를 확인합니다.

## <a name="next-steps"></a>다음 단계

테스트 장애 조치(failover) 실행에 대해 [자세히 알아보세요](site-recovery-test-failover-to-azure.md).
