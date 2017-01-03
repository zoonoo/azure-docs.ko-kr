---
title: "Azure Site Recovery 및 PowerShell을 사용하여 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제 | Microsoft Docs"
description: "Site Recovery 및 PowerShell을 사용하여 VMM 클라우드의 Hyper-V 가상 컴퓨터의 복제를 자동화하는 방법에 대해 알아봅니다."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: tysonn
ms.assetid: 9011f567-e0b4-4306-951a-b30da19f5db6
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 7455d6f99ed8ceb401224f98105f7b651f55c724
ms.openlocfilehash: 667f796ff6c411389847f5200ebdc28db1ee1973


---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell---classic"></a>Powershell - 클래식을 사용하여 Azure에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제
> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-vmm-to-azure.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [클래식 포털](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell - 클래식](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>개요
Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 배포 시나리오의 전체 목록은 [Azure Site Recovery 개요](site-recovery-overview.md)를 참조하세요.

이 문서에서는 System Center VMM 클라우드의 Hyper-V 가상 컴퓨터를 Azure 저장소로 복제하도록 Azure Site Recovery를 설정할 때 수행해야 하는 일반적인 작업을 PowerShell을 사용하여 자동화하는 방법을 보여 줍니다.

본 문서에는 시나리오에 대한 필수 조건이 포함되어 있으며, 사이트 복구 자격 증명 모음을 설정하고, 원본 VMM 서버에 Azure Site Recovery 공급자를 설치하며, 서버를 자격 증명 모음에 등록하고, Azure 저장소 계정을 추가하며, Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트를 설치하고, 보호된 모든 가상 컴퓨터에 적용되는 VMM 클라우드에 대한 보호 설정을 구성하고, 해당 가상 컴퓨터에 대해 보호를 사용하도록 설정하는 방법을 설명합니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.

이 시나리오를 설정하는 동안 문제가 발생할 경우 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의 사항을 게시하세요.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다.
>
>

## <a name="before-you-start"></a>시작하기 전에
다음 필수 조건이 충족되었는지 확인합니다.

### <a name="azure-prerequisites"></a>Azure 필수 조건
* [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다.
* 복제된 데이터를 저장하려면 Azure 저장소 계정이 있어야 합니다. 계정의 지역에서 복제 기능을 사용하도록 설정해야 합니다. 계정은 Azure Site Recovery 자격 증명 모음과 동일한 지역에 있고 동일한 구독과 연결되어야 합니다. [Azure 저장소에 대해 자세히 알아보세요](../storage/storage-introduction.md).
* 보호할 가상 컴퓨터가 [Azure 가상 컴퓨터 필수 조건](site-recovery-best-practices.md#azure-virtual-machine-requirements)을 준수하는지 확인해야 합니다.

### <a name="vmm-prerequisites"></a>VMM 필수 구성 요소
* System Center 2012 R2에서 실행되는 VMM 서버가 필요합니다.
* 보호할 VMM 서버에 클라우드가 하나 이상 있어야 합니다. 클라우드에는 다음이 포함되어야 합니다.
  * 하나 이상의 VMM 호스트 그룹.
  * 각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버 또는 클러스터.
  * 원본 Hyper-V 서버에 있는 하나 이상의 가상 컴퓨터.

### <a name="hyper-v-prerequisites"></a>Hyper-V 필수 조건
* 호스트 Hyper-V 서버는 **Windows Server 2012** 이상(Hyper-V 역할 수행) 또는 **Microsoft Hyper-V Server 2012**를 실행하고 최신 업데이트가 설치되어 있어야 합니다.
* 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. 이렇게 하려면 [서버 관리자] > [장애 조치(Failover) 클러스터 관리자]에서 클러스터에 연결하여 **역할 구성**을 클릭하고 고가용성 마법사의 **역할 선택** 화면에서 **Hyper-V 복제본 Broker**를 선택합니다.
* 보호를 관리할 Hyper-V 호스트 서버 또는 클러스터가 모두 VMM 클라우드에 포함되어야 합니다.

### <a name="network-mapping-prerequisites"></a>네트워크 매핑 필수 조건
Azure 네트워크에서 가상 컴퓨터를 보호하는 경우 매핑은 원본 VMM 서버의 VM 네트워크와 대상 Azure 네트워크 간을 매핑하여 다음을 가능하게 합니다.

* 속해 있는 복구 계획에 관계없이 동일한 네트워크에서 장애 조치(Failover)되는 모든 컴퓨터가 서로 연결할 수 있습니다.
* 네트워크 게이트웨이가 대상 Azure 네트워크에서 설정된 경우 가상 컴퓨터가 다른 온-프레미스 가상 컴퓨터에 연결할 수 있습니다.
* 네트워크 매핑을 구성하지 않으면 동일한 복구 계획에서 장애 조치(Failover)되는 가상 컴퓨터만 Azure로의 장애 조치(Failover) 후에 서로 연결할 수 있습니다.

네트워크 매핑을 배포하려면 다음이 필요합니다.

* 원본 VMM 서버에서 보호할 가상 컴퓨터가 VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
* 복제된 가상 컴퓨터가 장애 조치(Failover) 후 연결할 수 있는 Azure 네트워크. 이 네트워크는 장애 조치(Failover) 시 선택합니다. 네트워크는 Azure Site Recovery 구독과 동일한 지역에 있어야 합니다.
* [자세히 알아봅니다.](site-recovery-network-mapping.md) 

### <a name="powershell-prerequisites"></a>PowerShell 필수 구성 요소
Azure PowerShell을 사용할 준비가 되었는지 확인하세요. 이미 PowerShell을 사용하고 있는 경우 버전 0.8.10 이상으로 업그레이드해야 합니다. PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요. PowerShell을 설정 및 구성하면 [여기](https://msdn.microsoft.com/library/dn850420.aspx)에서 서비스에 사용 가능한 모든 cmdlet을 볼 수 있습니다.

Azure PowerShell에서 매개 변수 값, 입력, 출력이 일반적으로 처리되는 방법 등 cmdlet을 사용하는 데 도움이 되는 팁을 보려면 [Azure Cmdlet 시작하기](https://msdn.microsoft.com/library/azure/jj554332.aspx)를 참조하세요.

## <a name="step-1-set-the-subscription"></a>1단계: 구독 설정
PowerShell에서 다음 cmdlet을 실행합니다.

```
$UserName = "<user@live.com>"
$Password = "<password>"
$AzureSubscriptionName = "prod_sub1"

$SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
$Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $securePassword
Add-AzureAccount -Credential $Cred;
$AzureSubscription = Select-AzureSubscription -SubscriptionName $AzureSubscriptionName

```

“< >” 안의 요소를 자신의 특정 정보로 대체합니다.

## <a name="step-2-create-a-site-recovery-vault"></a>2단계: 사이트 복구 자격 증명 모음 만들기
PowerShell에서 “< >” 안의 요소를 자신의 특정 정보로 대체하고 다음 명령을 실행합니다.

```

$VaultName = "<testvault123>"
$VaultGeo  = "<Southeast Asia>"
$OutputPathForSettingsFile = "<c:\>"

```


```
New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
$vault = Get-AzureSiteRecoveryVault -Name $VaultName;

```

## <a name="step-3-generate-a-vault-registration-key"></a>3단계: 자격 증명 모음 등록 키 생성
자격 증명 모음에 등록 키를 생성합니다. Azure Site Recovery 공급자를 다운로드하고 VMM 서버에 설치한 후 이 키를 사용하여 VMM 서버를 자격 증명 모음에 등록합니다.

1. 자격 증명 모음 설정 파일을 가져오고 컨텍스트를 설정합니다.

   ```

   $VaultName = "<testvault123>"
   $VaultGeo  = "<Southeast Asia>"
   $OutputPathForSettingsFile = "<c:\>"

   $VaultSetingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;

   ```
2. 다음 명령을 실행하여 자격 증명 모음 컨텍스트를 설정합니다.

   ```

   $VaultSettingFilePath = $vaultSetingsFile.FilePath
   $VaultContext = Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSettingFilePath -ErrorAction Stop

   ```

## <a name="step-4-install-the-azure-site-recovery-provider"></a>4단계: Azure Site Recovery 공급자 설치
1. VMM 컴퓨터에서 다음 명령을 실행하여 디렉터리를 만듭니다.

   ```

   pushd C:\ASR\

   ```
2. 다음 명령을 실행하고 다운로드한 공급자를 사용하여 파일을 추출합니다.

   ```

   AzureSiteRecoveryProvider.exe /x:. /q

   ```
3. 다음 명령을 사용하여 공급자를 설치합니다.

   ```

   .\SetupDr.exe /i

   ```

   ```

   $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
         $isNotInstalled = $false;
     }
   }While($isNotInstalled)

   ```

   설치가 완료될 때까지 기다립니다.
4. 다음 명령을 사용하여 자격 증명 모음에 서버를 등록합니다.

   ```

   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\"
   .\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

   ```

## <a name="step-5-create-an-azure-storage-account"></a>5단계: Azure 저장소 계정 만들기
Azure 저장소 계정이 없는 경우 다음 명령을 실행하여 지리적 복제가 활성화된 계정을 만듭니다.

```

$StorageAccountName = "teststorageacc1"
$StorageAccountGeo  = "Southeast Asia"

New-AzureStorageAccount -StorageAccountName $StorageAccountName -Label $StorageAccountName -Location $StorageAccountGeo;

```

저장소 계정은 Azure Site Recovery 서비스와 같은 지역에 있고 같은 구독과 연결되어야 합니다.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>6단계: Azure 복구 서비스 에이전트 설치
Azure 포털에서 보호할 VMM 클라우드에 있는 각 Hyper-V 호스트 서버에 Azure 복구 서비스 에이전트를 설치합니다.

모든 VMM 호스트에 다음 명령을 실행합니다.

```

marsagentinstaller.exe /q /nu

```


## <a name="step-7-configure-cloud-protection-settings"></a>7단계: 클라우드 보호 설정 구성
1. 다음 명령을 실행하여 Azure에 대한 클라우드 보호 프로필을 만듭니다.

   ```

   $ReplicationFrequencyInSeconds = "300";
   $ProfileResult = New-AzureSiteRecoveryProtectionProfileObject -ReplicationProvider HyperVReplica -RecoveryAzureSubscription $AzureSubscriptionName -RecoveryAzureStorageAccount $StorageAccountName -ReplicationFrequencyInSeconds     $ReplicationFrequencyInSeconds;

   ```
2. 다음 명령을 실행하여 보호 컨테이너를 가져옵니다.

   ```

   $PrimaryCloud = "testcloud"
   $protectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $PrimaryCloud;

   ```
3. 클라우드와 보호 컨테이너 연결을 시작합니다.

   ```

   $associationJob = Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $profileResult -PrimaryProtectionContainer $protectionContainer;        

   ```
4. 작업이 완료되면 다음 명령을 실행합니다.

        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
        if($job -eq $null -or $job.StateDescription -ne "Completed")
        {
        $isJobLeftForProcessing = $true;
        }


1. 작업에서 처리를 완료하면 다음 명령을 실행합니다.

        Do
        {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



작동 완료 여부를 확인하려면 [작업 모니터](#monitor)의 단계를 따릅니다.

## <a name="step-8-configure-network-mapping"></a>8단계: 네트워크 매핑 구성
네트워크 매핑을 시작하기 전에 원본 VMM 서버의 가상 컴퓨터가 VM 네트워크에 연결되었는지 확인합니다. 또한 Azure 가상 네트워크를 하나 이상 만듭니다. 단일 Azure 네트워크에 여러 개의 VM 네트워크를 매핑할 수 있습니다.

대상 네트워크에 여러 서브넷이 있고 이 서브넷 중 하나의 이름이 원본 가상 컴퓨터가 있는 서브넷과 같으면 복제본 가상 컴퓨터가 장애 조치(Failover) 후에 대상 서브넷에 연결됩니다. 일치하는 이름을 가진 대상 서브넷이 없으면 가상 컴퓨터가 네트워크의 첫 번째 서브넷에 연결됩니다.

첫 번째 명령은 현재 Azure Site Recovery 자격 증명 모음의 서버를 가져옵니다. 이 명령은 $Servers 어레이 변수에 Microsoft Azure Site Recovery 서버를 저장합니다.

    $Servers = Get-AzureSiteRecoveryServer


두 번째 명령은 $Servers 어레이의 첫 번째 서버에 대한 사이트 복구 네트워크를 가져옵니다. 이 명령은 $Networks 변수에 네트워크를 저장합니다.

    $Networks = Get-AzureSiteRecoveryNetwork -Server $Servers[0]

세 번째 명령은 Get-AzureSubscription cmdlet을 사용하여 Azure 구독을 가져온 다음 해당 값을 $Subscriptions 변수에 저장합니다.

    $Subscriptions = Get-AzureSubscription



네 번째 명령은 Get-AzureVNetSite cmdlet을 사용하여 Azure 가상 네트워크를 가져온 다음 해당 값을 $AzureVmNetworks 변수에 저장합니다.

    $AzureVmNetworks = Get-AzureVNetSite



최종 cmdlet은 기본 네트워크와 Azure 가상 컴퓨터 네트워크 사이에 매핑을 만듭니다. cmdlet은 $Networks의 첫 번째 요소로 기본 네트워크를 지정합니다. cmdlet은 ID를 사용하여 $AzureVmNetworks의 첫 번째 요소로 가상 컴퓨터 네트워크를 지정합니다. 이 명령에는 Azure 구독 ID가 포함됩니다.

    New-AzureSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureSubscriptionId $Subscriptions[0].SubscriptionId -AzureVMNetworkId $AzureVmNetworks[0].Id


## <a name="step-9-enable-protection-for-virtual-machines"></a>9단계: 가상 컴퓨터의 보호 활성화
서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다. 다음 사항에 유의하세요.

가상 컴퓨터에서 [Azure 가상 컴퓨터 필수 조건](site-recovery-best-practices.md#azure-virtual-machine-requirements)을 충족해야 합니다.

보호를 사용하도록 설정하려면 가상 컴퓨터에 대해 운영 체제 및 운영 체제 디스크 속성을 설정해야 합니다. VMM에서 가상 컴퓨터 템플릿을 사용하여 가상 컴퓨터를 만들 때 속성을 설정할 수 있습니다. 가상 컴퓨터 속성의 **일반** 및 **하드웨어 구성** 탭에서 기존 가상 컴퓨터에 대해 이러한 속성을 설정할 수도 있습니다. 이러한 속성을 VMM에서 설정하지 않는 경우 Azure Site Recovery 포털에서 구성할 수 있습니다.

1. 보호를 활성화하려면 다음 명령을 실행하여 보호 컨테이너를 가져옵니다.

     $ProtectionContainer = Get-AzureSiteRecoveryProtectionContainer -Name $CloudName
2. 다음 명령을 실행하여 VM(보호 엔터티)을 가져옵니다.

        $protectionEntity = Get-AzureSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer



1. 다음 명령을 실행하여 VM에 DR을 사용하도록 설정합니다.

        $jobResult = Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity     -Protection Enable -Force



## <a name="test-your-deployment"></a>배포 테스트
배포를 테스트하려면 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나, 여러 개의 가상 컴퓨터로 구성된 복구 계획을 만들고 이 계획에 대한 테스트 장애 조치(Failover)를 실행하면 됩니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다. 다음 사항에 유의하세요.

* 장애 조치(Failover) 후에 원격 데스크탑을 사용하여 Azure의 가상 컴퓨터에 연결하려면 가상 컴퓨터에서 원격 데스크탑 연결을 사용하도록 설정하고 나서 테스트 장애 조치(Failover)를 실행합니다.
* 장애 조치(Failover) 후에 공개 IP 주소를 사용하여 원격 데스크탑을 통해 Azure의 가상 컴퓨터에 연결합니다. 이 작업을 하려면 공개 주소를 사용하여 가상 컴퓨터에 연결하지 못하도록 차단하는 도메인 정책이 없어야 합니다.

작동 완료 여부를 확인하려면 [작업 모니터](#monitor)의 단계를 따릅니다.

### <a name="create-a-recovery-plan"></a>복구 계획 만들기
1. 아래 데이터를 사용하여 복구 계획의 템플릿으로 .xml 파일을 만든 다음 "C:\RPTemplatePath.xml"로 저장합니다.
2. RecoveryPlan node Id, Name, PrimaryServerId, SecondaryServerId를 변경합니다.
3. ProtectionEntity 노드 PrimaryProtectionEntityId(VMM의 vmid)를 변경합니다.
4. ProtectionEntity 노드를 추가하여 VM을 추가할 수 있습니다.

        <#
        <?xml version="1.0" encoding="utf-16"?>
        <RecoveryPlan Id="d0323b26-5be2-471b-addc-0a8742796610" Name="rp-test"     PrimaryServerId="9350a530-d5af-435b-9f2b-b941b5d9fcd5"     SecondaryServerId="21a9403c-6ec1-44f2-b744-b4e50b792387" Description=""     Version="V2014_07">
          <Actions />
          <ActionGroups>
            <ShutdownAllActionGroup Id="ShutdownAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </ShutdownAllActionGroup>
            <FailoverAllActionGroup Id="FailoverAllActionGroup">
              <PreActionSequence />
              <PostActionSequence />
            </FailoverAllActionGroup>
            <BootActionGroup Id="DefaultActionGroup">
              <PreActionSequence />
              <PostActionSequence />
              <ProtectionEntity PrimaryProtectionEntityId="d4c8ce92-a613-4c63-9b03-    cf163cc36ef8" />
            </BootActionGroup>
          </ActionGroups>
          <ActionGroupSequence>
            <ActionGroup Id="ShutdownAllActionGroup" ActionId="ShutdownAllActionGroup"     Before="FailoverAllActionGroup" />
            <ActionGroup Id="FailoverAllActionGroup" ActionId="FailoverAllActionGroup"     After="ShutdownAllActionGroup" Before="DefaultActionGroup" />
            <ActionGroup Id="DefaultActionGroup" ActionId="DefaultActionGroup" After="FailoverAllActionGroup"/>
          </ActionGroupSequence>
        </RecoveryPlan>
        #>



1. 템플릿에 데이터를 입력합니다.

        $TemplatePath = "C:\RPTemplatePath.xml";



1. RecoveryPlan 만들기:

        $RPCreationJob = New-AzureSiteRecoveryRecoveryPlan -File $TemplatePath -WaitForCompletion;

### <a name="run-a-test-failover"></a>테스트 장애 조치(Failover) 실행
1. 다음 명령을 실행하여 RecoveryPlan 개체를 가져옵니다.

     $RPObject = Get-AzureSiteRecoveryRecoveryPlan -Name $RPName;
2. 다음 명령을 실행하여 테스트 장애 조치(Failover)를 시작합니다.

        $jobIDResult = Start-AzureSiteRecoveryTestFailoverJob -RecoveryPlan $RPObject -Direction PrimaryToRecovery;


## <a name="a-namemonitora-monitor-activity"></a><a name=monitor></a> 작업 모니터
다음 명령을 사용하여 작업을 모니터합니다. 처리가 완료될 때까지 기다린 후 다음 작업을 시작할 수 있습니다.

    Do
    {
            $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



## <a name="next-steps"></a>다음 단계
[자세히 알아보세요](https://msdn.microsoft.com/library/dn850420.aspx) . </a>



<!--HONumber=Nov16_HO4-->


