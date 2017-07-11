---
title: "PowerShell을 사용하여 보조 사이트에 VMM의 Hyper-V VM 복제(Azure Resource Manager) | Microsoft Docs"
description: "Azure Site Recovery를 배포하고 PowerShell을 사용하여 VMM 클라우드의 Hyper-V VM을 보조 VMM 사이트에 복제, 장애 조치(Failover) 및 복구하는 작업을 어떻게 조정해야 하는지 설명합니다(Resource Manager)."
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: sutalasi
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 118dd3a69f140d57a3779e86c658982d58dbb718
ms.contentlocale: ko-kr
ms.lasthandoff: 04/27/2017


---
<a id="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager" class="xliff"></a>

# PowerShell을 사용하여 보조 VMM 사이트에 VMM 클라우드의 Hyper-V 가상 컴퓨터 복제(Resource Manager)
> [!div class="op_single_selector"]
> * [Azure 포털](site-recovery-vmm-to-vmm.md)
> * [클래식 포털](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Azure Site Recovery에 오신 것을 환영합니다! System Center VMM(Virtual Machine Manager) 클라우드에서 관리되는 온-프레미스 Hyper-V 가상 컴퓨터를 보조 사이트에 복제하려는 경우 이 문서를 사용하세요.

이 문서에서는 System Center VMM 클라우드의 Hyper-V 가상 컴퓨터를 보조 사이트의 System Center VMM 클라우드로 복제하도록 Azure Site Recovery를 설정할 때 수행해야 하는 일반적인 작업을 PowerShell을 사용하여 자동화하는 방법을 보여 줍니다.

이 문서는 시나리오의 필수 조건을 포함하고 있으며, 다음 내용을 보여 줍니다.

* 복구 서비스 자격 증명 모음을 설정하는 방법
* 원본 VMM 서버 및 대상 VMM 서버에 Azure Site Recovery 공급자를 설치합니다.
* VMM 서버를 자격 증명 모음에 등록합니다.
* VMM 클라우드에 대한 복제 정책을 구성합니다. 정책의 복제 설정이 보호된 모든 가상 컴퓨터에 적용됩니다.
* 가상 컴퓨터에 대한 보호를 사용하도록 설정합니다.
* VM의 장애 조치를 개별적으로 또는 복구 계획의 일부로 테스트하여 모든 것이 예상대로 작동하는지 확인합니다.
* VM의 계획된 또는 계획되지 않은 장애 조치를 개별적으로 또는 복구 계획의 일부로 수행하여 모든 것이 예상대로 작동하는지 확인합니다.

이 시나리오를 설정하는 동안 문제가 발생할 경우 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 문의 사항을 게시하세요.

> [!NOTE]
> Azure에는 리소스를 만들고 작업하기 위한 두 가지 [배포 모델](../azure-resource-manager/resource-manager-deployment-model.md) 인 Azure Resource Manager 모델과 클래식 모델이 있습니다. 또한 Azure에는 두 가지 포털이 있는데, 하나는 클래식 배포 모델을 지원하는 Azure 클래식 포털이고 다른 하나는 두 가지 배포 모델을 모두 지원하는 Azure 포털입니다. 이 문서에서는 리소스 관리자 배포 모델에 대해 설명합니다.
>
>

<a id="on-premises-prerequisites" class="xliff"></a>

## 온-프레미스 필수 조건
이 시나리오를 배포하기 위해 기본 및 보조 온-프레미스 사이트에서 필요한 사항은 다음과 같습니다.

| **필수 구성 요소** | **세부 정보** |
| --- | --- |
| **VMM** |기본 사이트에서 VMM 서버 1개, 보조 사이트에서 VMM 서버 1개를 배포할 것을 권장합니다.<br/><br/> [단일 VMM 서버의 클라우드 간에 복제](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment)에 문의 사항을 게시하세요. 이렇게 하려면 VMM 서버에 둘 이상의 클라우드가 구성되어 있어야 합니다.<br/><br/> VMM 서버는 최신 업데이트를 설치한 System Center 2012 SP1 이상을 실행해야 합니다.<br/><br/> 각 VMM 서버에는 하나 이상의 클라우드가 구성되어 있어야 하고 모든 클라우드에 Hyper-V 용량 프로필이 설정되어 있어야 합니다. <br/><br/>클라우드에 하나 이상의 VMM 호스트 그룹이 있어야 합니다.<br/><br/>[VMM 패브릭 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) 및 [연습: System Center 2012 SP1 VMM으로 사설 클라우드 만들기](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)에서 VMM 클라우드 설정에 대해 자세히 알아봅니다.<br/><br/> VMM 서버는 인터넷에 연결되어야 합니다. |
| **Hyper-V** |Hyper-V 서버는 Hyper-V 역할로 Windows Server 2012 이상을 실행해야 하며 최신 업데이트가 설치되어 있어야 합니다.<br/><br/> Hyper-V 서버에 VM이 하나 이상 있어야 합니다.<br/><br/>  Hyper-V 호스트 서버는 기본 및 보조 VMM 클라우드의 호스트 그룹에 있어야 합니다.<br/><br/> Windows Server 2012 R2의 클러스터에서 Hyper-V를 실행하는 경우 [업데이트 2961977](https://support.microsoft.com/kb/2961977)을 설치해야 합니다.<br/><br/> Windows Server 2012의 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. [자세히 알아보기](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **공급자** |사이트 복구 배포 중에 VMM 서버에 Azure Site Recovery 공급자를 설치합니다. 공급자는 HTTPS 443을 통해 Site Recovery와 통신하여 복제를 오케스트레이션합니다. LAN 또는 VPN 연결을 통해 기본 및 보조 Hyper-V 서버 간에 데이터 복제가 발생합니다.<br/><br/> VMM 서버에서 실행되는 공급자는 다음 URL에 액세스할 수 있어야 합니다. *.hypervrecoverymanager.windowsazure.com; *.accesscontrol.windows.net; *.backup.windowsazure.com; *.blob.core.windows.net; *.store.core.windows.net.<br/><br/> 또한 VMM 서버에서 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/confirmation.aspx?id=41653)로의 방화벽 통신을 허용하고 HTTPS(443) 프로토콜을 허용합니다. |

<a id="network-mapping-prerequisites" class="xliff"></a>

### 네트워크 매핑 필수 조건
네트워크 매핑 기능은 다음을 위해 기본 및 보조 VMM 서버의 VMM VM 네트워크 간을 매핑합니다.

* 장애 조치(Failover) 후 보조 Hyper-V 호스트에 복제본 VM을 최적으로 배치합니다.
* 복제본 VM을 해당 VM 네트워크에 연결합니다.
* 네트워크 매핑을 구성하지 않으면 장애 조치 후에 복제본 VM이 어떤 네트워크에도 연결되지 않습니다.
* Site Recovery를 배포하는 동안 네트워크 매핑을 설정하려면 다음이 필요합니다.

  * 원본 Hyper-V 호스트 서버의 VM이 VMM VM 네트워크에 연결되어야 합니다. 해당 네트워크가 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.
  * 복구에 사용할 보조 클라우드에 해당 VM 네트워크가 구성되어 있는지 확인합니다. 해당 VM 네트워크는 보조 클라우드와 연결된 논리 네트워크에 연결되어야 합니다.

아래 문서에서 VMM 네트워크를 구성하는 방법에 대해 자세히 알아보세요.

* [VMM에서 논리 네트워크를 구성하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [VMM에서 VM 네트워크 및 게이트웨이를 구성하는 방법](http://go.microsoft.com/fwlink/p/?LinkId=386308)

[알아보세요](site-recovery-vmm-to-vmm.md#prepare-for-network-mapping) .

<a id="powershell-prerequisites" class="xliff"></a>

### PowerShell 필수 구성 요소
Azure PowerShell을 사용할 준비가 되었는지 확인하세요. 이미 PowerShell을 사용하고 있는 경우 버전 0.8.10 이상으로 업그레이드해야 합니다. PowerShell 설치에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요. PowerShell을 설정 및 구성하면 [여기](/powershell/azure/overview)에서 서비스에 사용 가능한 모든 cmdlet을 볼 수 있습니다.

Azure PowerShell에서 매개 변수 값, 입력, 출력이 일반적으로 처리되는 방법 등 cmdlet를 사용하는 데 도움이 되는 팁을 보려면 [Azure Cmdlet 시작하기](/powershell/azure/get-started-azureps)를 참조하세요.

<a id="step-1-set-the-subscription" class="xliff"></a>

## 1단계: 구독 설정
1. Azure powershell에서 다음 cmdlet을 사용하여 Azure 계정에 로그인합니다.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. 구독 목록을 가져옵니다. 각 구독의 subscriptionID 목록도 함께 표시될 것입니다. 복구 서비스 자격 증명 모음을 만들려는 구독의 subscriptionID를 메모합니다.    

        Get-AzureRmSubscription
3. 구독 ID를 알려주어 복구 서비스 자격 증명 모음을 만들 구독을 설정합니다.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

<a id="step-2-create-a-recovery-services-vault" class="xliff"></a>

## 2단계: 복구 서비스 자격 증명 모음 만들기
1. 또한 Azure Resource Manager 리소스 그룹이 없는 경우 만듭니다.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. 새 복구 서비스 자격 증명 모음을 만들고, 만든 ASR 자격 증명 모음 개체를 변수에 저장합니다(나중에 사용됨). Get-AzureRMRecoveryServicesVault cmdlet을 사용하여 ASR 자격 증명 모음 개체 게시 만들기를 검색할 수도 있습니다.

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

<a id="step-3-set-the-recovery-services-vault-context" class="xliff"></a>

## 3단계: 복구 서비스 자격 증명 모음 설정
1. 자격 증명 모음을 이미 만든 경우 아래 명령을 실행하여 자격 증명 모음을 가져옵니다.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. 다음 명령을 실행하여 자격 증명 모음 컨텍스트를 설정합니다.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

<a id="step-4-install-the-azure-site-recovery-provider" class="xliff"></a>

## 4단계: Azure Site Recovery 공급자 설치
1. VMM 컴퓨터에서 다음 명령을 실행하여 디렉터리를 만듭니다.

       New-Item c:\ASR -type directory
2. 다음 명령을 실행하고 다운로드한 공급자를 사용하여 파일을 추출합니다.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. 다음 명령을 사용하여 공급자를 설치합니다.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

   설치가 완료될 때까지 기다립니다.
4. 다음 명령을 사용하여 자격 증명 모음에 서버를 등록합니다.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

<a id="step-5-create-and-associate-a-replication-policy" class="xliff"></a>

## 5단계: 복제 정책 만들기 및 연결
1. 다음 명령을 실행하여 Hyper-V 2012 R2 복제 정책을 만듭니다.

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > VMM 클라우드는 서로 다른 버전의 Windows Server에서 실행되는 Hyper-V 호스트를 포함할 수 있으나(Hyper-V 필수 구성 요소에 나와 있음) 복제 정책은 OS 버전마다 다릅니다. 운영 체제 버전마다 다른 호스트가 실행되고 있는 경우 각 형식의 OS 버전에 대해 별도의 복제 정책을 만듭니다. 예를 들어 Windows Server 2012에서 5개의 호스트가 실행되고 있고 Windows Server 2012 R2에서 3개의 호스트가 실행되고 있는 경우 각 운영 체제 버전 형식에 대해 하나씩 2개의 복제 정책을 만듭니다.

1. 다음 명령을 실행하여 기본 보호 컨테이너(기본 VMM 클라우드) 및 복구 보호 컨테이너(복구 VMM 클라우드)를 가져옵니다.

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. 정책의 이름을 사용하여 1단계에서 만든 정책을 검색합니다.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. 복제 정책을 사용하여 보호 컨테이너(VMM 클라우드) 연결을 시작합니다.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. 정책 연결 작업이 완료될 때까지 기다립니다. 다음 PowerShell 코드 조각을 사용하여 작업이 완료되었는지 확인할 수 있습니다.

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   작업에서 처리를 완료하면 다음 명령을 실행합니다.

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

작동 완료 여부를 확인하려면 [작업 모니터](#monitor)의 단계를 따릅니다.

<a id="step-6-configure-network-mapping" class="xliff"></a>

## 6단계: 네트워크 매핑 구성
1. 첫 번째 명령은 현재 Azure Site Recovery 자격 증명 모음의 서버를 가져옵니다. 이 명령은 $Servers 어레이 변수에 Microsoft Azure Site Recovery 서버를 저장합니다.

        $Servers = Get-AzureRmSiteRecoveryServer
2. 아래 명령은 원본 VMM 서버 및 대상 VMM 서버에 대한 사이트 복구 네트워크를 가져옵니다.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > 원본 VMM 서버는 서버 배열의 첫 번째 또는 두 번째 서버일 수 있습니다. VMM 서버의 이름을 확인하고 네트워크를 적절히 가져옵니다.


1. 최종 cmdlet는 기본 네트워크와 복구 네트워크 사이에 매핑을 만듭니다. 이 cmdlet는 $PrimaryNetworks의 첫 번째 요소로 기본 네트워크를 지정하고, $RecoveryNetworks의 첫 번째 요소로 복구 네트워크를 지정합니다.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

<a id="step-7-configure-storage-mapping" class="xliff"></a>

## 7단계: 저장소 매핑 구성
1. 아래 명령으로 저장소 분류의 목록을 $storageclassifications 변수로 가져옵니다.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. 아래 명령으로 원본 분류를 $SourceClassificaion 변수로 가져오고, 대상 분류를 $TargetClassification 변수로 가져옵니다.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > 원본 및 대상 분류는 배열의 임의 요소일 수 있습니다. $storageclassifications 배열에서 원본 및 대상 분류의 인덱스를 알아보려면 아래 명령의 출력을 참조하세요.

    > Get-AzureRmSiteRecoveryStorageClassification | Select-Object -Property FriendlyName, Id | Format-Table


1. 아래 cmdlet은 원본 분류와 대상 분류 간에 매핑을 만듭니다.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

<a id="step-8-enable-protection-for-virtual-machines" class="xliff"></a>

## 8단계: 가상 컴퓨터의 보호 활성화
서버, 클라우드 및 네트워크가 제대로 구성되었으면 클라우드에서 가상 컴퓨터에 대한 보호를 설정할 수 있습니다.

1. 보호를 활성화하려면 다음 명령을 실행하여 보호 컨테이너를 가져옵니다.

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. 다음 명령을 실행하여 VM(보호 엔터티)을 가져옵니다.

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. 다음 명령을 실행하여 VM에 복제를 사용하도록 설정합니다.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

<a id="test-your-deployment" class="xliff"></a>

## 배포 테스트
배포를 테스트하려면 단일 가상 컴퓨터에 대한 테스트 장애 조치(Failover)를 실행하거나, 여러 개의 가상 컴퓨터로 구성된 복구 계획을 만들고 이 계획에 대한 테스트 장애 조치(Failover)를 실행하면 됩니다. 테스트 장애 조치(Failover)에서는 격리된 네트워크에서 장애 조치(Failover) 및 복구 메커니즘을 시뮬레이션합니다.

> [!NOTE]
> Azure 포털에서 응용 프로그램에 대한 복구 계획을 만들 수 있습니다.
>
>

작동 완료 여부를 확인하려면 [작업 모니터](#monitor)의 단계를 따릅니다.

<a id="run-a-test-failover" class="xliff"></a>

### 테스트 장애 조치(Failover) 실행
1. 아래 cmdlet를 실행하여 VM에 대해 테스트 장애 조치(Failover)를 수행하려는 VM 네트워크를 가져옵니다.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. 다음을 수행하여 VM의 테스트 장애 조치(Failover)를 수행합니다.

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. 다음을 수행하여 복구 계획의 테스트 장애 조치(Failover)를 수행합니다.

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

<a id="run-a-planned-failover" class="xliff"></a>

### 계획된 장애 조치(Failover) 실행
1. 다음을 수행하여 VM의 계획된 장애 조치(Failover)를 수행합니다.

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. 다음을 수행하여 복구 계획의 계획된 장애 조치(Failover)를 수행합니다.

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

<a id="run-an-unplanned-failover" class="xliff"></a>

### 계획되지 않은 장애 조치 실행
1. 다음을 수행하여 VM의 계획되지 않은 장애 조치(Failover)를 수행합니다.

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2.다음을 수행하여 복구 계획의 계획되지 않은 장애 조치(Failover)를 수행합니다.

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a> 작업 모니터
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



<a id="next-steps" class="xliff"></a>

## 다음 단계
[자세히 알아보세요](/powershell/module/azurerm.recoveryservices.backup/#recovery) .

