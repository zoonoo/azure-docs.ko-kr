---
title: 근접 배치 그룹에서 실행되는 Azure VM 복제
description: Azure Site Recovery를 사용하여 근접 배치 그룹에서 실행되는 Azure VM을 복제하는 방법을 알아봅니다.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 4d079a8b6254fc70ca641380ce68aac12eed28d9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95805615"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>근접 배치 그룹에서 실행되는 Azure 가상 머신을 다른 지역에 복제

이 문서에서는 근접 배치 그룹에서 실행되는 가상 머신을 보조 지역으로 복제, 장애 조치(failover), 장애 복구(failback)하는 방법을 설명합니다.

[근접 배치 그룹](../virtual-machines/windows/proximity-placement-groups-portal.md)은 애플리케이션과 관련된 VM 간 네트워크 대기 시간을 줄이는 데 사용할 수 있는 Azure 가상 머신 논리 그룹화 기능입니다. 동일한 근접 배치 그룹에 배포되는 VM은 물리적으로 서로 가까운 위치에 배치됩니다. 근접 배치 그룹은 대기 시간이 중요한 워크로드의 요구 사항을 해결하는 데 특히 유용합니다.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>근접 배치 그룹을 사용한 재해 복구

일반적인 시나리오에서는 애플리케이션의 다양한 계층 간 네트워크 대기 시간을 방지하기 위해 가상 머신을 근접 배치 그룹에서 실행할 수 있습니다. 이렇게 하면 애플리케이션의 네트워크 대기 시간을 최적화할 수 있지만 모든 지역 수준 장애에 대해 Site Recovery를 사용하여 이러한 애플리케이션을 보호할 수 있습니다. Site Recovery는 데이터를 한 지역에서 다른 Azure 지역으로 복제하고 장애 조치(failover) 시 재해 복구 지역에서 머신을 시작합니다.

## <a name="considerations"></a>고려 사항

- 최선의 노력은 가상 머신을 근접 배치 그룹으로 장애 조치(failover)/장애 복구(failback)하는 것입니다. 그러나 장애 조치(failover)/장애 복구(failback) 중에 근접 배치 내부에서 VM을 시작할 수 없는 경우 장애 조치(failover)/장애 복구(failback)가 계속 발생하고, 가상 머신이 근접 배치 그룹 외부에 생성됩니다.
-  가용성 집합이 근접 배치 그룹에 고정되어 있고 장애 조치(failover)/장애 복구(failback) 중 가용성 집합의 VM에 할당 제약 조건이 있는 경우, 가상 머신은 가용성 집합 및 근접 배치 그룹의 외부에 생성됩니다.
-  비관리 디스크는 근접 배치 그룹에 대한 Site Recovery가 지원되지 않습니다.

> [!NOTE]
> Azure Site Recovery는 Hyper-v에서 Azure로 시나리오에 대해 managed disks에서 장애 복구 (failback)를 지원 하지 않습니다. 따라서 Azure의 근접 배치 그룹에서 Hyper-v로 장애 복구 (failback)가 지원 되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

1. Azure PowerShell Az 모듈이 있어야 합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.
2. 최소 Azure PowerShell Az version은 4.1.0 여야 합니다. 현재 버전을 확인 하려면 아래 명령을 사용 합니다.
    ```
    Get-InstalledModule -Name Az
    ```

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>근접 배치 그룹 내 가상 머신에 Site Recovery 설정

> [!NOTE]
> 대상 근접 배치 그룹의 고유 ID가 유용한 지 확인 합니다. 새 근접 배치 그룹을 만드는 경우 [여기](../virtual-machines/windows/proximity-placement-groups.md#create-a-proximity-placement-group) 에서 명령을 확인 하 고 기존 근접 배치 그룹을 사용 하는 경우 [여기](../virtual-machines/windows/proximity-placement-groups.md#list-proximity-placement-groups)에서 명령을 사용 합니다.

### <a name="azure-to-azure"></a>Azure 간

1. 계정에 [로그인](./azure-to-azure-powershell.md#sign-in-to-your-microsoft-azure-subscription)하고 구독을 설정합니다.
2. [여기](./azure-to-azure-powershell.md#get-details-of-the-virtual-machine-to-be-replicated) 설명된 대로 복제할 가상 머신의 세부 정보를 가져옵니다.
3. Recovery Services 자격 증명 모음을 [만들고](./azure-to-azure-powershell.md#create-a-recovery-services-vault) 자격 증명 모음 컨텍스트를 [설정](./azure-to-azure-powershell.md#set-the-vault-context)합니다.
4. 복제 가상 머신을 시작하도록 자격 증명 모음을 준비합니다. 여기에는 기본 및 복구 지역에 대한 [Service Fabric 개체](./azure-to-azure-powershell.md#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region)를 만드는 작업이 포함됩니다.
5. 기본 및 복구 패브릭에 대해 Site Recovery 보호 컨테이너를 [만듭니다](./azure-to-azure-powershell.md#create-a-site-recovery-protection-container-in-the-primary-fabric).
6. 복제 정책을 [만듭니다](./azure-to-azure-powershell.md#create-a-replication-policy).
7. [이러한](./azure-to-azure-powershell.md#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) 단계를 사용하여 기본 보호 컨테이너와 복구 보호 컨테이너 간의 보호 컨테이너 매핑을 만들고 [여기](./azure-to-azure-powershell.md#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)에 설명된 대로 장애 복구(failback)에 대한 보호 컨테이너 매핑을 만듭니다.
8. [이러한](./azure-to-azure-powershell.md#create-cache-storage-account-and-target-storage-account) 단계를 사용하여 캐시 스토리지 계정을 만듭니다.
9. [여기](./azure-to-azure-powershell.md#create-network-mappings) 설명된 대로 필요한 네트워크 매핑을 만듭니다.
10. 관리 디스크를 사용 하 여 Azure 가상 머신을 복제 하려면 아래 PowerShell cmdlet을 사용 합니다.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)
#Make sure to replace the variables $OSdiskName with OS disk name.

#OS Disk
$OSdisk = Get-AzDisk -DiskName $OSdiskName -ResourceGroupName "A2AdemoRG"
$OSdiskId = $OSdisk.Id
$RecoveryOSDiskAccountType = $OSdisk.Sku.Name
$RecoveryReplicaDiskAccountType = $OSdisk.Sku.Name

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $OSdiskId -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

#Make sure to replace the variables $datadiskName with data disk name.

#Data disk
$datadisk = Get-AzDisk -DiskName $datadiskName -ResourceGroupName "A2AdemoRG"
$datadiskId1 = $datadisk[0].Id
$RecoveryReplicaDiskAccountType = $datadisk[0].Sku.Name
$RecoveryTargetDiskAccountType = $datadisk[0].Sku.Name

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id
```

PPG를 사용 하 여 영역에 영역 복제를 사용 하도록 설정 하면 복제를 시작 하는 명령이 PowerShell cmdlet과 교환 됩니다.

```azurepowershell
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $targetPpg.Id -RecoveryAvailabilityZone "2"
```

복제 시작 작업이 성공하면 가상 머신 데이터가 복구 지역으로 복제됩니다.

복제 프로세스는 처음에 복구 영역에 있는 가상 머신의 복제 디스크의 복사본을 시드하면서 시작됩니다. 이 단계를 초기 복제 단계라고 합니다.

초기 복제가 완료되면 복제는 차등 동기화 단계로 이동합니다. 이 시점에서 가상 머신이 보호되고 테스트 장애 조치(failover) 작업을 수행할 수 있습니다. 초기 복제가 완료된 후 가상 머신을 나타내는 복제된 항목의 복제 상태는 보호됨 상태로 변경됩니다.

해당하는 복제 보호된 항목의 세부 정보를 가져와서 복제 상태 및 가상 머신의 복제 상태를 모니터링합니다.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. 테스트 장애 조치(failover)를 수행하고, 유효성을 검사하고, 정리하려면 [이러한](./azure-to-azure-powershell.md#do-a-test-failover-validate-and-cleanup-test-failover) 단계를 수행합니다.
12. 장애 조치(failover)하려면 [여기](./azure-to-azure-powershell.md#fail-over-to-azure) 설명된 단계를 따릅니다.
13. 원본 지역을 다시 보호하고 장애 복구(failback)하려면 아래 PowerShell cmdlet을 사용합니다.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```

14. 복제를 사용하지 않도록 설정하려면 [여기](./azure-to-azure-powershell.md#disable-replication)의 단계를 따릅니다.

### <a name="vmware-to-azure"></a>VMware에서 Azure로

1. Azure로 재해 복구를 위해 [온-프레미스 VMware 서버를 준비](./vmware-azure-tutorial-prepare-on-premises.md)해야 합니다.
2. 계정에 로그인하고 [여기](./vmware-azure-disaster-recovery-powershell.md#log-into-azure)에 지정된 대로 구독을 설정합니다.
3. Recovery Services 자격 증명 모음을 [설정](./vmware-azure-disaster-recovery-powershell.md#set-up-a-recovery-services-vault)하고 [자격 증명 모음 컨텍스트를 설정](./vmware-azure-disaster-recovery-powershell.md#set-the-vault-context)합니다.
4. 자격 증명 모음 등록의 [유효성을 검사](./vmware-azure-disaster-recovery-powershell.md#validate-vault-registration)합니다.
5. 복제 정책을 [만듭니다](./vmware-azure-disaster-recovery-powershell.md#create-a-replication-policy).
6. vCenter 서버를 [추가](./vmware-azure-disaster-recovery-powershell.md#add-a-vcenter-server-and-discover-vms)하고, 가상 머신을 검색하고, 복제를 위한 스토리지 계정을 [만듭니다](./vmware-azure-disaster-recovery-powershell.md#create-storage-accounts-for-replication).
7. VMware 가상 머신을 복제하려면 여기에서 세부 정보를 확인하고 아래 PowerShell cmdlet을 따릅니다.

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg"

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $targetPpg.Id
```

8. 가상 머신의 복제 상황 및 복제 상태는 Get-ASRReplicationProtectedItem cmdlet을 사용하여 확인할 수 있습니다.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

9. [여기](./vmware-azure-disaster-recovery-powershell.md#configure-failover-settings)의 단계를 수행하여 장애 조치(failover) 설정을 구성합니다.
10. 테스트 장애 조치(failover)를 [실행](./vmware-azure-disaster-recovery-powershell.md#run-a-test-failover)합니다.
11. [이러한](./vmware-azure-disaster-recovery-powershell.md#fail-over-to-azure) 단계를 사용하여 Azure로 장애 조치(Failover)합니다.

### <a name="hyper-v-to-azure"></a>Hyper-V에서 Azure로

1. Azure로 재해 복구를 위해 [온-프레미스 Hyper-V 서버를 준비](./hyper-v-prepare-on-premises-tutorial.md)해야 합니다.
2. Azure에 [로그인](./hyper-v-azure-powershell-resource-manager.md#step-1-sign-in-to-your-azure-account)합니다.
3. Recovery Services 자격 증명 모음을 [설정](./hyper-v-azure-powershell-resource-manager.md#step-2-set-up-the-vault)하고 자격 증명 모음 컨텍스트를 [설정](./hyper-v-azure-powershell-resource-manager.md#step-3-set-the-recovery-services-vault-context)합니다.
4. Hyper-V 사이트를 [만듭니다](./hyper-v-azure-powershell-resource-manager.md#step-4-create-a-hyper-v-site).
5. 공급자 및 에이전트를 [설치](./hyper-v-azure-powershell-resource-manager.md#step-5-install-the-provider-and-agent)합니다.
6. 복제 정책을 [만듭니다](./hyper-v-azure-powershell-resource-manager.md#step-6-create-a-replication-policy).
7. 아래 단계를 사용하여 복제를 사용하도록 설정합니다. 
    
    a. 다음과 같이 보호하려는 VM에 해당하는 보호 항목을 검색합니다.

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. VM을 보호합니다. 보호하는 VM에 둘 이상의 디스크가 연결된 경우 OSDiskName 매개 변수를 사용하여 운영 체제 디스크를 지정합니다.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $targetPpg.Id
    ```
    다. VM이 초기 복제 후 보호되는 상태에 도달할 때까지 기다립니다. 시간이 걸릴 수 있으며 복제될 데이터의 양과 Azure에 사용 가능한 업스트림 대역폭 등의 요인에 따라 걸리는 시간이 달라질 수 있습니다. 보호된 상태가 되면 작업 상태와 작업 설명이 다음과 같이 업데이트됩니다. 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. 장애 조치 후 VM NIC를 연결할 Azure 네트워크 및 복구 속성(예: VM 역할 크기)을 업데이트합니다.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. 테스트 [장애 조치(failover)](./hyper-v-azure-powershell-resource-manager.md#step-8-run-a-test-failover)를 실행합니다.


## <a name="next-steps"></a>다음 단계

VMware에서 Azure로 다시 보호 및 장애 복구(failback)를 수행하려면 [여기](./vmware-azure-prepare-failback.md) 설명된 단계를 따릅니다.

Hyper-v에서 Azure로 장애 조치(failover)를 수행하려면 [여기](./site-recovery-failover.md)에 설명된 단계를 따르고, 장애 복구(failback)를 수행하려면 [여기](./hyper-v-azure-failback.md)에 설명된 단계를 따릅니다.

자세한 내용은 [Site Recovery에서 장애 조치(failover)](site-recovery-failover.md)를 참조하세요.