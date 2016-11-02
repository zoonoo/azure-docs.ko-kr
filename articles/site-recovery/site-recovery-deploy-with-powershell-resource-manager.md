<properties
    pageTitle="Azure Resource Manager로 Azure PowerShell을 사용하여 Azure에 서버 보호 | Microsoft Azure"
    description="PowerShell 및 Azure Resource Manager를 사용하여 Azure Site Recovery로 Azure에 대한 서버 보호를 자동화합니다."
    services="site-recovery"
    documentationCenter=""
    authors="bsiva"
    manager="abhiag"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="09/27/2016"
    ms.author="bsiva"/>


# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>PowerShell 및 Azure Resource Manager를 사용하여 온-프레미스 Hyper-V 가상 컴퓨터와 Azure 간 복제

> [AZURE.SELECTOR]
- [Azure 포털](site-recovery-hyper-v-site-to-azure.md)
- [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
- [클래식 포털](site-recovery-hyper-v-site-to-azure-classic.md)



## <a name="overview"></a>개요

Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 비즈니스 연속성 및 재해 복구 전략에 기여합니다. 배포 시나리오의 전체 목록은 [Azure Site Recovery 개요](site-recovery-overview.md)를 참조하세요.

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. Azure 프로필 모듈 또는 Azure Resource Manager 모듈 등 두 유형의 모듈로 작업할 수 있습니다.

Azure Resource Manager에 대한 Azure PowerShell과 함께 사용할 수 있는 사이트 복구 PowerShell cmdlet을 사용하여 Azure에서 서버를 보호하고 복구할 수 있습니다.

이 문서에서는 Azure에 서버 보호를 구성하고 오케스트레이션하기 위해 Azure Resource Manager와 함께 Windows PowerShell을 사용하여 사이트 복구를 배포하는 방법을 설명합니다. 이 문서에 사용된 예제는 Azure Resource Manager로 Azure PowerShell을 사용하여 Azure에 대한 Hyper-V 호스트의 가상 컴퓨터를 보호, 장애 조치하고 복구하는 방법을 보여 줍니다.

> [AZURE.NOTE] 사이트 복구 PowerShell cmdlet을 사용하여 한 가상 컴퓨터 관리자 사이트에서 다른 가상 컴퓨터 관리자 사이트로, 가상 컴퓨터 관리자 사이트에서 Azure로, Hyper-V 사이트에서 Azure로 구성할 수 있습니다.

이 문서를 사용하기 위해 PowerShell 전문가가 될 필요는 없지만 모듈, cmdlet 및 세션과 같은 기본 개념을 이해해야 합니다. Windows PowerShell에 대한 자세한 내용은 [Windows PowerShell 시작](http://technet.microsoft.com/library/hh857337.aspx)(영문)을 참조하십시오.

자세한 내용은 [Azure Resource Manager에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.

> [AZURE.NOTE] CSP(클라우드 솔루션 공급자) 프로그램의 일부인 Microsoft 파트너를 통해 고객의 각 CSP 구독(테넌트 구독)에 대한 고객의 서버 보호를 구성하고 관리할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

다음 필수 조건이 충족되었는지 확인합니다.

- [Microsoft Azure](https://azure.microsoft.com/) 계정. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. [Azure Site Recovery Manager 가격](https://azure.microsoft.com/pricing/details/site-recovery/)에 대해 알아볼 수도 있습니다.
- Azure PowerShell 1.0 이 릴리스에 대한 정보 및 설치하는 방법은 [Azure PowerShell 1.0.](https://azure.microsoft.com/)을 참조하세요.
- [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) 및 [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) 모듈.  [PowerShell 갤러리](https://www.powershellgallery.com/)

이 문서에서는 Azure Resource Manager로 Azure Powershell을 사용하여 서버 보호를 구성하고 관리하는 방법을 보여 줍니다. 이 문서에 사용된 예제에서는 Azure에 대한 Hyper-V 호스트에서 실행 중인 가상 컴퓨터를 보호하는 방법을 보여 줍니다. 다음에 나오는 필수 구성 요소는 이 예제에 해당됩니다. 다양한 사이트 복구 시나리오에 대한 포괄적인 요구 사항 집합은 해당 시나리오와 관련된 설명서를 참조하세요.

- 하나 이상의 가상 컴퓨터가 포함된 Windows Server 2012 R2를 실행하는 Hyper-V 호스트.
- 직접 또는 프록시를 통해 인터넷에 연결된 Hyper-V 서버.
- 보호할 가상 컴퓨터는 [가상 컴퓨터 필수 조건](site-recovery-best-practices.md#virtual-machines)에 맞아야 합니다.


## <a name="step-1:-sign-in-to-your-azure-account"></a>1단계: Azure 계정에 로그인


1. PowerShell 콘솔을 열고 이 명령을 실행하여 Azure 계정에 로그인합니다. cmdlet은 계정 자격 증명에 대한 프롬프트를 표시하는 웹 페이지를 엽니다.

        Login-AzureRmAccount

    또는 `-Credential` 매개 변수를 사용하여 `Login-AzureRmAccount` cmdlet에 대한 매개 변수로 계정 자격 증명을 포함할 수도 있습니다.

    사용자가 테넌트를 대신하여 작업 중인 CSP 파트너인 경우 tenantID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다.

        Login-AzureRmAccount -Tenant "fabrikam.com"

2. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결해야 합니다.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName

3.  다음 명령을 사용하여 Recovery Services와 Site Recovery에 대해 Azure 공급자를 사용하도록 구독이 등록되었는지 확인합니다.

    - `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
    -  `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

    이러한 명령의 출력에서 **RegistrationState**가 **등록됨**으로 설정된 경우에 2단계를 진행할 수 있습니다. 그렇지 않은 경우 구독에서 누락된 공급자를 등록해야 합니다.

    사이트 복구 및 복구 서비스에 대해 Azure 공급자를 등록하려면 다음 명령을 수행합니다.

        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` 및 `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery` 명령을 사용하여 공급자가 성공적으로 등록되었는지 확인합니다.



## <a name="step-2:-set-up-the-recovery-services-vault"></a>2단계: 복구 서비스 자격 증명 모음 설정

1. 자격 증명 모음을 만들거나 기존 리소스 그룹을 사용하는 Azure Resource Manager 리소스 그룹을 만듭니다. 다음 명령을 사용하여 새로운 리소스 그룹을 만들 수 있습니다.

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    여기서 $ResourceGroupName 변수는 만들려는 리소스 그룹의 이름을 포함하고 $Geo 변수는 리소스 그룹을 만들 Azure 지역(예: "브라질 남부")을 포함합니다.

    `Get-AzureRmResourceGroup` cmdlet을 사용하여 구독에 리소스 그룹의 목록을 가져올 수 있습니다.

2. 다음과 같이 새 Azure 복구 서비스 자격 증명 모음을 만듭니다.

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    `Get-AzureRmRecoveryServicesVault` cmdlet을 사용하여 기존 자격 증명 모음의 목록을 검색할 수 있습니다.

> [AZURE.NOTE] 클래식 포털 또는 Azure 서비스 관리 PowerShell 모듈을 사용하여 만든 사이트 복구 자격 증명 모음에서 작업을 수행하려는 경우 `Get-AzureRmSiteRecoveryVault` cmdlet을 사용하여 이러한 자격 증명 모음 목록을 검색할 수 있습니다. 모든 새 작업에 대해 새 복구 서비스 자격 증명 모음을 만들어야 합니다. 앞서 만든 사이트 복구 자격 증명 모음은 지원되지만 최신 기능이 없습니다.

## <a name="step-3:-set-the-recovery-services-vault-context"></a>3단계: 복구 서비스 자격 증명 모음 설정

1.  다음 명령을 실행하여 자격 증명 모음 컨텍스트를 설정합니다.

        Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4:-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site."></a>4단계: Hyper-V 사이트를 만들고 사이트에 대한 새 자격 증명 모음 등록 키를 생성합니다.

1. 다음과 같이 새 Hyper-V 사이트를 만듭니다.

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    이 cmdlet은 사이트 복구 작업을 시작하여 사이트를 만들고 사이트 복구 작업 개체를 반환합니다. 작업이 완료될 때까지 기다리고 작업이 성공적으로 완료되었는지 확인합니다.

    작업 개체를 검색할 수 있으므로 Get-AzureRmSiteRecoveryJob cmdlet을 사용하여 작업의 현재 상태를 확인합니다.
2. 다음과 같이 사이트에 대한 등록 키를 생성하고 다운로드합니다.

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Hyper-V 호스트에 다운로드한 키를 복사합니다. 사이트에 Hyper-V 호스트를 등록하는 키가 필요합니다.

## <a name="step-5:-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>5단계: Hyper-V 호스트에 Azure Site Recovery 공급자와 Azure 복구 서비스 에이전트 설치

1. [Microsoft](https://aka.ms/downloaddra)에서 공급자의 최신 버전을 위한 설치 관리자를 다운로드합니다.

2. Hyper-V 호스트에서 설치 관리자를 실행하고 설치 끝부분에서 등록 단계를 계속 진행합니다.

3. 메시지가 표시되면 다운로드한 사이트 등록 키를 제공하고 사이트에 Hyper-V 호스트의 등록을 완료합니다.

4. 다음 명령을 사용하여 Hyper-V 호스트가 사이트에 등록되었는지 확인합니다.

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6:-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>6단계: 복제 정책을 만들고 보호 컨테이너와 연결

1. 다음과 같이 복제 정책을 만듭니다.

        $ReplicationFrequencyInSeconds = "300";     #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                 #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    반환된 작업을 확인하여 복제 정책 만들기 성공했는지 확인합니다.

    >[AZURE.IMPORTANT] 지정된 저장소 계정은 복구 서비스 자격 증명 모음과 같은 Azure 지역에 있어야 하고 지역에서 복제를 활성화해야 합니다.
    >
    > - 지정된 복구 저장소 계정이 Azure 저장소 형식(클래식)인 경우 보호된 컴퓨터의 장애 조치는 Azure IaaS(클래식)에 대한 컴퓨터를 복구합니다.
    > - 지정된 복구 저장소 계정이 Azure 저장소 형식(Azure Resource Manager)인 경우 보호된 컴퓨터의 장애 조치는 Azure IaaS(Azure Resource Manager)에 대한 컴퓨터를 복구합니다.

2. 다음과 같이 사이트에 해당하는 보호 컨테이너를 가져옵니다.

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3.  다음과 같이 복제 정책을 사용하여 보호 컨테이너 연결을 시작합니다.

        $Policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $PolicyName
        $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy $Policy -PrimaryProtectionContainer $protectionContainer

    연결 작업이 완료될 때까지 기다리고 성공적으로 완료되었는지 확인합니다.

##<a name="step-7:-enable-protection-for-virtual-machines"></a>7단계: 가상 컴퓨터의 보호 활성화

1. 다음과 같이 보호하려는 VM에 해당하는 보호 엔터티를 가져옵니다.

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

2. 다음과 같이 가상 컴퓨터의 보호를 시작합니다.

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

    >[AZURE.IMPORTANT] 지정된 저장소 계정은 복구 서비스 자격 증명 모음과 같은 Azure 지역에 있어야 하고 지역에서 복제를 활성화해야 합니다.
    >
    > - 지정된 복구 저장소 계정이 Azure 저장소 형식(클래식)인 경우 보호된 컴퓨터의 장애 조치는 Azure IaaS(클래식)에 대한 컴퓨터를 복구합니다.
    > - 지정된 복구 저장소 계정이 Azure 저장소 형식(Azure Resource Manager)인 경우 보호된 컴퓨터의 장애 조치는 Azure IaaS(Azure Resource Manager)에 대한 컴퓨터를 복구합니다.

    > 보호하는 VM에 둘 이상의 디스크가 연결된 경우 *OSDiskName* 매개 변수를 사용하여 운영 체제 디스크를 지정합니다.

3. 가상 컴퓨터가 초기 복제 이후의 보호되는 상태에 도달할 때까지 기다립니다. 복제될 데이터의 양과 Azure에 사용 가능한 업스트림 대역폭 등의 요인에 따라 시간이 걸릴 수 있습니다. 작업 상태와 작업 설명은 VM이 보호되는 상태에 도달할 때 다음과 같이 업데이트됩니다.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed

4. 장애 조치 시에 가상 컴퓨터의 네트워크 인터페이스 카드를 연결할 VM 역할 크기 및 Azure 네트워크와 같은 복구 속성을 업데이트합니다.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob


        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8:-run-a-test-failover"></a>8단계: 테스트 장애 조치 실행

1. 다음과 같이 테스트 장애 조치 작업을 실행합니다.

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id

2. 테스트 VM이 Azure에서 만들어졌는지 확인합니다. (Azure에서 테스트 VM을 만든 후 테스트 장애 조치 작업이 중지됩니다. 다음 단계에 설명된 대로 작업 다시 시작 시 생성된 아티팩트를 정리하여 작업이 완료됩니다.)

3. 다음과 같이 테스트 장애 조치(Failover)를 완료합니다.

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob


##<a name="next-steps"></a>다음 단계

[자세히 알아보세요](https://msdn.microsoft.com/library/azure/mt637930.aspx) .




<!--HONumber=Oct16_HO2-->


