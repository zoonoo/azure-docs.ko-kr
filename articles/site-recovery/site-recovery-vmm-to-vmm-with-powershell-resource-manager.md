<properties
	pageTitle="PowerShell 및 Microsoft Azure 리소스 관리자를 사용하여 VMM 사이트 간에 VM 보호"
	description="PowerShell 및 Azure 리소스 관리자를 사용하여 두 온-프레미스 VMM 사이트와 Azure 간에 보호 자동화"
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/26/2015"
	ms.author="raynew"/>
	

#  PowerShell 및 Azure 리소스 관리자를 사용하여 VMM 사이트에 VMM 사이트


## 개요

Azure Site Recovery는 여러 배포 시나리오에서 가상 컴퓨터의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 배포 시나리오의 전체 목록은 [Azure Site Recovery 개요](site-recovery-overview.md)를 참조하세요.

Azure PowerShell은 Windows PowerShell을 통해 Azure를 관리하기 위한 cmdlet을 제공하는 모듈입니다. Azure 프로필 모듈 또는 Azure 리소스 관리자(ARM) 모듈 등 두 유형의 모듈로 작업할 수 있습니다.

이 문서에서는 두 VMM 사이트 간에 가상 컴퓨터 보호를 구성하고 오케스트레이션하기 위해 ARM과 함께 Windows PowerShell ®를 사용하여 Azure Site Recovery를 배포하는 방법을 설명합니다. 기본 사이트의 VMM 사설 클라우드에 있는 Hyper-v 호스트 서버에서 실행되는 가상 컴퓨터는 Hyper-V 복제본을 사용하여 보조 VMM 사이트로 복제하고 장애 조치합니다.

이 문서를 사용하기 위해 PowerShell 전문가가 될 필요는 없지만 모듈, cmdlet 및 세션과 같은 기본 개념을 이해한다고 가정합니다. Windows PowerShell에 대한 자세한 내용은 [Azure PowerShell을 사용하여 시작](http://technet.microsoft.com/library/hh857337.aspx)을 참조하세요. 자세한 내용은 [Azure 리소스 관리자를 사용하여 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 읽어보세요.

문서에는 시나리오에 대한 필수 조건이 포함되어 있으며, 사이트 복구로 작업하기 위해 Azure PowerShell을 설정하고, 사이트 복구 자격 증명 모음을 만들며, VMM 서버에 Azure Site Recovery 공급자를 가져오고, 자격 증명 모음에 등록하는 방법을 설명하고, 보호된 모든 가상 컴퓨터에 적용되는 VMM 클라우드에 대한 보호 설정을 구성하고, 해당 가상 컴퓨터에 대해 보호를 사용하도록 설정합니다. 끝으로, 장애 조치(Failover)를 테스트하여 모두 예상대로 작동하는지 확인합니다.


## 시작하기 전에

다음 필수 조건이 충족되었는지 확인합니다.

- [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [Microsoft Azure](http://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](pricing/free-trial/)으로 시작할 수 있습니다. 또한 [Azure Site Recovery Manager 가격 책정](http://azure.microsoft.com/pricing/details/site-recovery/)에 대해 알아볼 수 있습니다.
- System Center 2012 R2에서 실행되는 기본 및 보조 사이트에 VMM 서버가 필요합니다.
- 각 VMM 서버는 다음을 포함하는 클라우드가 하나 이상 있어야 합니다.
	- 하나 이상의 VMM 호스트 그룹.
	- 각 호스트 그룹에 있는 하나 이상의 Hyper-V 호스트 서버 또는 클러스터.
	- 원본 Hyper-V 서버에 있는 하나 이상의 가상 컴퓨터.
	- VMM 클라우드 설정에 대해 자세히 알아봅니다.

		- [System Center 2012 R2 VMM을 사용하는 사설 클라우드의 새로운 기능](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=dfgvHAmYryA) 및 [VMM 2012와 클라우드의 새로운 기능](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
		- [VMM 클라우드 패브릭 구성](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
		- [VMM에서 사설 클라우드 만들기](https://technet.microsoft.com/library/jj860425.aspx) 및 [연습: System Center 2012 SP1 VMM을 사용하는 사설 클라우드 만들기](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
- 최신 업데이트가 설치된 Hyper-V 역할로 Windows Server 2012 이상을 실행하는 하나 이상의 Hyper-V 서버입니다. 서버 또는 클러스터는 VMM 클라우드에 포함되어야 합니다.
- 클러스터에서 Hyper-V를 실행하는 경우 고정 IP 주소 기반 클러스터가 있으면 클러스터 브로커가 자동으로 만들어지지 않습니다. 클러스터 브로커를 수동으로 구성해야 합니다. 자세한 내용은 [Hyper-V 복제본 브로커 구성](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)을 참조하세요.

	- Azure PowerShell이 필요합니다. Azure PowerShell 버전 0.9.6 이상을 실행하는지 확인합니다. [Azure PowerShell 설치 및 구성 방법](powershell-install-configure.md)을 읽습니다. 
	- Azure PowerShell을 설치하면 사용자 지정 콘솔도 설치됩니다. 이 콘솔 또는 Windows PowerShell ISE와 같은 다른 호스트 프로그램에서 PowerShell 명령을 실행할 수 있습니다.

## 1단계: PowerShell 설정


1. PowerShell 콘솔을 열고 이 명령을 실행하여 ARM 모듈로 전환합니다.

    `Switch-AzureMode AzureResourceManager`

3. 이제 이 명령을 실행하여 PowerShell 세션에 Azure 계정을 추가합니다. Cmdlet가 계정에 대한 로그인 자격 증명을 유도합니다.

    `Add-AzureAccount`

	사용자가 테넌트를 대신하여 작업 중인 CSP 파트너인 경우 Azure 계정을 추가할 때 고객을 테넌트로 지정해야 합니다.

    `Add-AzureAccount-Tenant "customer"`

5. 계정에 여러 구독을 있을 수 있으므로 계정을 사용하려는 구독을 연결해야 합니다.

    `Select-AzureSubscription -SubscriptionName $SubscriptionName`

6. 구독에서 처음으로 사이트 복구 cmdlet을 사용하는 경우 사이트 복구에 Azure 공급자를 등록해야 합니다.

    `Register-AzureProvider -ProviderNamespace Microsoft.SiteRecovery`

## 2단계: 사이트 복구 자격 증명 모음 설정

1. 현재 사이트 복구 자격 증명 모음이 없는 경우 [New-AzureSiteRecoveryVault](https://msdn.microsoft.com/library/azure/dn954225.aspx) cmdlet을 실행하여 만들어야 합니다.

    `New-AzureSiteRecoveryVault -Location $VaultGeo -Name $VaultName;
    $vault = Get-AzureSiteRecoveryVault -Name $VaultName;`

## 3단계: 자격 증명 모음 등록 키 생성

1. [Get AzureSiteRecoveryVaultSettingsFile](https://msdn.microsoft.com/library/azure/dn850404.aspx) cmdlet을 실행하여 자격 증명 모음 등록 키를 가져옵니다. 이 키는 자격 증명 모음에서 VMM 서버를 등록하는 데 필요합니다.

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Location $VaultGeo -Name $VaultName -Path $OutputPathForSettingsFile;`

## 4단계: Azure Site Recovery 공급자 설치

1. 사이트 복구 자격 증명 모음의 빠른 시작 페이지에서 공급자 설치 파일을 다운로드합니다.
2. 각 VMM 서버는 이 명령을 사용하여 폴더를 만듭니다.

    `pushd C:\ASR`

3. 다운로드한 공급자에서 파일을 추출하려면 이 명령을 실행합니다.

    `AzureSiteRecoveryProvider.exe /x:. /q`

4. 다음을 실행하여 공급자를 설치합니다.

    `.\SetupDr.exe /i` `$installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
	do
	{
                $isNotInstalled = $true;
                if(Test-Path $installationRegPath)
                {
                                $isNotInstalled = $false;
                }
	}While($isNotInstalled)`

5. 설치가 완료되기를 기다린 다음 자격 증명 모음에 서버를 등록합니다.

    `$BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
	pushd $BinPath
	$encryptionFilePath = "C:\temp"
	.\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice`

## 5단계: 자격 증명 모음 컨텍스트 설정

1. Get AzureSiteRecoveryVault cmdlet을 실행하여 모든 명령이 특정 자격 증명 모음에서 실행하도록 합니다.

    `$Vault = Get-AzureSiteRecoveryVault -ResouceGroupName $ResourceGroupName | where { $_.Name -eq $VaultName}`

2. 자격 증명 모음 설정을 다운로드합니다.

    `$VaultSettingsFile = Get-AzureSiteRecoveryVaultSettingsFile -Vault $Vault -Path $OutputPathForSettingsFile`

3. cmdlet이 자격 증명 모음 실행에서 실행되는지 확인하려면

    `Import-AzureSiteRecoveryVaultSettingsFile -Path $VaultSetingsFile.FilePath`

## 3단계: 클라우드 보호 설정 구성

VMM 서버를 자격 증명 모음에 등록한 후에 등록된 VMM 서버의 클라우드에 있는 Hyper-V 호스트에서 모든 가상 컴퓨터에 적용될 클라우드 보호 설정을 구성할 수 있습니다.

1. 기본 및 보조 클라우드에 대한 자격 증명 모음에서 컨테이너를 만듭니다.

    `$PrimaryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $PrimaryCloudName` `$$RecoveryContainer = Get-AzureSiteRecoveryProtectionContainer -FriendlyName  $RecoveryCloudName`

2. 클라우드에 적용할 보호 설정을 구성합니다.

    `New-AzureSiteRecoveryProtectionProfile -Name $ProtectionProfileName -ReplicationProvider HyperVReplica -ReplicationMethod Online -ReplicationFrequencyInSeconds 30 -RecoveryPoints 1 -ApplicationConsistentSnapshotFrequencyInHours 0 -ReplicationPort 8083 -Authentication Kerberos -AllowReplicaDeletion`

3. 클라우드 보호 설정을 사용하여 클라우드 컨테이너를 연결하는 작업을 시작합니다.

    `Start-AzureSiteRecoveryProtectionProfileAssociationJob -ProtectionProfile $ProtectionProfile -PrimaryProtectionContainer $PrimaryContainer -RecoveryProtectionContainer $RecoveryContainer`


## 단계4: VM 보호 사용

VMM 클라우드에서 VM에 대한 보호를 사용하도록 설정합니다.

1. 보호하려는 VM을 가져옵니다.

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName $VMName `

2. VM에 대한 보호를 사용하도록 설정합니다.

    `Set-AzureSiteRecoveryProtectionEntity -ProtectionEntity $VM -Protection Enable`


## 5단계: 테스트 장애 조치 실행

1.	장애 조치를 수행하려는 VM을 선택합니다.

    `$VM = Get-AzureSiteRecoveryProtectionEntity -ProtectionContainer $PrimaryContainer -FriendlyName  $VMName`

2. 테스트 장애 조치 작업을 실행합니다.

    `$ currentJob = Start-AzureSiteRecoveryTestFailoverJob -ProtectionEntity $VM -Direction PrimaryToRecovery`

3. 실패한 VM이 보조 사이트에 표시되 장애 조치를 완료하는지 확인합니다.

    `Resume-AzureSiteRecoveryJob -Id $currentJob.Name`


## 다음 단계

이 시나리오에 대한 의견 및 질문은 [사이트 복구 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr/)을 방문하세요.

<!---HONumber=August15_HO9-->