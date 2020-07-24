---
title: Azure Site Recovery/PowerShell을 사용 하 여 Hyper-v (VMM 포함)에서 보조 사이트로 재해 복구를 설정 합니다.
description: Azure Site Recovery 및 PowerShell을 사용하여 VMM 클라우드의 Hyper-V VM과 보조 VMM 사이트 간 재해 복구를 설정하는 방법을 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: a4f7d330db9a4a0d9b435ebe7527e55e37c254e2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086220"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>PowerShell을 사용하여 Hyper-V VM과 보조 사이트 간 재해 복구 설정(Resource Manager)

이 문서에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 System Center Virtual Machine Manager 클라우드에서 보조 온-프레미스 사이트의 Virtual Machine Manager 클라우드로 Hyper-V VM을 복제하기 위한 단계를 자동화하는 것을 보여 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건

- [시나리오 아키텍처 및 구성 요소](hyper-v-vmm-architecture.md)를 검토합니다.
- 모든 구성 요소에 대 한 [지원 요구 사항을](./vmware-physical-secondary-support-matrix.md) 검토 합니다.
- Virtual Machine Manager 서버 및 Hyper-V 호스트가 [지원 요구 사항](./vmware-physical-secondary-support-matrix.md)을 준수하는지 확인합니다.
- 복제하려는 VM에서 [복제된 컴퓨터 지원](./vmware-physical-secondary-support-matrix.md)을 준수하는지 확인합니다.

## <a name="prepare-for-network-mapping"></a>네트워크 매핑을 준비

[네트워크 매핑](hyper-v-vmm-network-mapping.md)에서는 원본 및 대상 클라우드의 온-프레미스 Virtual Machine Manager VM 네트워크 사이를 매핑합니다. 매핑은 다음을 수행합니다.

- 장애 조치 후 VM을 적절한 대상 VM 네트워크에 연결합니다.
- 복제본 VM을 대상 Hyper-V 호스트 서버에 최적으로 배치합니다.
- 네트워크 매핑을 구성하지 않으면 장애 조치(failover) 후 복제본 VM이 VM 네트워크에 연결되지 않습니다.

Virtual Machine Manager를 다음과 같이 준비합니다.

- [Virtual Machine Manager 논리 네트워크](/system-center/vmm/network-logical)가 원본 및 대상 Virtual Machine Manager 서버에 있는지 확인합니다.
  - 원본 서버의 논리 네트워크는 Hyper-V 호스트가 있는 원본 클라우드와 연결되어 있어야 합니다.
  - 대상 서버의 논리 네트워크는 대상 클라우드와 연결되어 있어야 합니다.
- [VM 네트워크](/system-center/vmm/network-virtual)가 원본 및 대상 Virtual Machine Manager 서버에 있는지 확인합니다. VM 네트워크는 각 위치의 논리적 네트워크에 연결되어야 합니다.
- 원본 Hyper-V 호스트의 VM을 원본 VM 네트워크에 연결합니다.

## <a name="prepare-for-powershell"></a>PowerShell에 대한 준비

Azure PowerShell을 사용할 준비가 되었는지 확인합니다.

- PowerShell을 이미 사용하는 경우 0.8.10 이상의 버전으로 업그레이드합니다. PowerShell 설정에 대해 [자세히 알아봅니다](/powershell/azure/).
- PowerShell을 설정 및 구성한 후 [서비스 cmdlet](/powershell/azure/)을 검토합니다.
- PowerShell에서 매개 변수 값, 입력 및 출력을 사용하는 방법에 대해 자세히 알아보려면 [시작](/powershell/azure/get-started-azureps) 가이드를 읽어보세요.

## <a name="set-up-a-subscription"></a>구독 설정

1. PowerShell에서 Azure 계정에 로그인합니다.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. 구독 ID를 사용하여 구독의 목록을 검색합니다. Recovery Services 자격 증명 모음을 만들려는 구독의 ID를 적어 둡니다.

   ```azurepowershell
   Get-AzSubscription
   ```

1. 자격 증명 모음에 대한 구독을 설정합니다.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

1. Azure Resource Manager 리소스 그룹이 없는 경우 만듭니다.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. 새 Recovery Services 자격 증명 모음을 만듭니다. 자격 증명 모음 개체를 나중에 사용할 변수에 저장합니다.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   Cmdlet을 사용 하 여 자격 증명 모음 개체를 만든 후 해당 개체를 검색할 수 있습니다 `Get-AzRecoveryServicesVault` .

## <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

1. 기존 자격 증명 모음을 검색합니다.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. 자격 증명 모음 컨텍스트를 설정합니다.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>Site Recovery 공급자 설치

1. Virtual Machine Manager 컴퓨터에서 다음 명령을 실행하여 디렉터리를 만듭니다.

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. 다운로드된 공급자 설정 파일을 사용하여 파일을 압축합니다.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 공급자를 설치하고, 설치가 완료될 때까지 대기합니다.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. 자격 증명 모음에 서버를 등록합니다.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>복제 정책 만들기 및 연결

1. 다음과 같이 복제 정책을 만듭니다(이 경우 Hyper-V 2012 R2용).

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > Virtual Machine Manager 클라우드는 서로 다른 버전의 Windows Server에서 실행되는 Hyper-V 호스트를 포함할 수 있으나 복제 정책은 운영 체제의 버전마다 다릅니다. 서로 다른 운영 체제에서 다른 호스트가 실행되고 있는 경우 각 시스템에 대해 별도의 복제 정책을 만듭니다. 예를 들어 Windows Server 2012에서 5개의 호스트가 실행되고 있고 Windows Server 2012 R2에서 3개의 호스트가 실행되고 있는 경우 2개의 복제 정책을 만듭니다. 운영 체제의 각 형식에 대해 하나씩 만듭니다.

1. 기본 보호 컨테이너(기본 Virtual Machine Manager 클라우드) 및 복구 보호 컨테이너(복구 Virtual Machine Manager 클라우드)를 검색합니다.

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. 친숙한 이름을 사용하여 만든 복제 정책을 검색합니다.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. 복제 정책을 사용하여 보호 컨테이너(Virtual Machine Manager 클라우드) 연결을 시작합니다.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. 정책 연결 작업이 완료될 때까지 기다립니다. 작업이 완료되었는지 확인하려면 다음 PowerShell 코드 조각을 사용합니다.

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. 작업에서 처리를 완료한 후 다음 명령을 실행합니다.

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

작동 완료 여부를 확인하려면 [작업 모니터](#monitor-activity)의 단계를 따릅니다.

##  <a name="configure-network-mapping"></a>네트워크 매핑 구성

1. 이 명령을 사용하여 현재 자격 증명 모음에 대한 서버를 검색합니다. 이 명령은 Site Recovery 서버를 `$Servers` 배열 변수에 저장 합니다.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. 이 명령을 실행하여 원본 Virtual Machine Manager 서버와 대상 Virtual Machine Manager 서버에 대한 네트워크를 검색합니다.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > 원본 Virtual Machine Manager 서버는 서버 배열의 첫 번째 또는 두 번째 서버일 수 있습니다. Virtual Machine Manager 서버 이름을 확인하고, 네트워크를 적절하게 검색합니다.

1. 이 cmdlet는 기본 네트워크와 복구 네트워크 사이에 매핑을 만듭니다. 기본 네트워크를의 첫 번째 요소로 지정 합니다 `$PrimaryNetworks` . 의 첫 번째 요소로 복구 네트워크를 지정 합니다 `$RecoveryNetworks` .

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>VM에 대한 보호 사용

서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 VM에 대한 보호를 활성화합니다.

1. 보호를 활성화하려면 다음 명령을 실행하여 보호 컨테이너를 검색합니다.

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. 다음과 같이 보호 엔터티(VM)를 가져옵니다.

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. VM에 대한 복제를 활성화합니다.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Azure에서 CMK를 사용 하는 관리 디스크에 복제 하려는 경우 Az PowerShell 3.3.0을 사용 하 여 다음 단계를 수행 합니다.
>
> 1. VM 속성을 업데이트 하 여 관리 디스크에 대 한 장애 조치 (failover) 사용
> 1. Cmdlet을 사용 `Get-AzRecoveryServicesAsrReplicationProtectedItem` 하 여 보호 된 항목의 각 디스크에 대 한 디스크 ID를 인출 합니다.
> 1. Cmdlet을 사용 하 여 디스크 `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` ID를 디스크 암호화 집합으로 매핑하는 사전을 포함 하는 사전 개체를 만듭니다. 이러한 디스크 암호화 집합은 대상 지역에서 미리 생성 됩니다.
> 1. `Set-AzRecoveryServicesAsrReplicationProtectedItem` **Diskidtodiskencryptionsetmap** 매개 변수에서 사전 개체를 전달 하 여 cmdlet을 사용 하 여 VM 속성을 업데이트 합니다.

## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

배포를 테스트하려면 단일 가상 머신에 대한 테스트 장애 조치(failover)를 실행합니다. 또한 여러 개의 VM이 포함된 복구 계획을 만들고 계획에 대한 테스트 장애 조치(failover)를 실행할 수 있습니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다.

1. 장애 조치(failover)를 수행할 VM으로 VM을 검색합니다.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. 테스트 장애 조치(failover)를 수행합니다.

   단일 VM의 경우:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   복구 계획의 경우:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

작동 완료 여부를 확인하려면 [작업 모니터](#monitor-activity)의 단계를 따릅니다.

## <a name="run-planned-and-unplanned-failovers"></a>계획되거나 계획되지 않은 장애 조치(failover) 실행

1. 계획된 장애 조치(failover)를 수행합니다.

   단일 VM의 경우:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   복구 계획의 경우:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. 계획되지 않은 장애 조치(failover)를 수행합니다.

   단일 VM의 경우:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   복구 계획의 경우:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>작업 모니터링

다음 명령을 사용하여 장애 조치(failover) 작업을 모니터링합니다. 작업 사이에 처리가 완료될 때까지 기다립니다.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>다음 단계

Resource Manager PowerShell cmdlet을 사용한 Site Recovery에 대해 [자세히 알아봅니다](/powershell/module/az.recoveryservices).
