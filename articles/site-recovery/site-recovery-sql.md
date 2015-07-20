<properties 
	pageTitle="SQL Server 및 Azure Site Recovery를 사용한 재해 복구" 
	description="Azure Site Recovery는 보조 온-프레미스 사이트 또는 Azure에 SQL Server의 복제, 장애 조치 및 복구를 조정합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2015" 
	ms.author="raynew"/>


# SQL Server 및 Azure Site Recovery를 사용한 재해 복구 

사이트 복구는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여하는 Azure 서비스입니다. 사이트 복구는 일관된 보호를 위해 다양한 복제 메커니즘을 지원하며, Azure 또는 보조 데이터센터로 컴퓨터를 복제 및 장애 조치합니다. [Azure Site Recovery 개요](site-recovery-overview.md)에서 모든 배포 시나리오의 개요를 참조하세요.

 이 문서에서는 SQL Server BCDR 기술 및 사이트 복구를 조합하 여 응용프로그램의 SQL Server 백엔드를 보호하는 방법을 설명합니다. 이 문서에 설명된 시나리오를 배포하기 전에 SQL Server BCDR 기능(장애 조치 클러스터링, AlwaysOn 가용성 그룹, 데이터베이스 미러링, 로그 전달) 및 사이트 복구를 제대로 이해해야 합니다.



## 개요

다양한 작업에서 SQL Server를 기본으로 사용합니다. SharePoint, Dynamics 및 SAP과 같은 응용프로그램은 SQL Server를 사용하여 데이터 서비스를 구현합니다. SQL Server 가용성 그룹과 같은 SQL Server의 고가용성 및 재해 복구 기능은 SQL Server 데이터베이스를 보호 및 복구합니다.

사이트 복구는 Hyper-V 가상 컴퓨터, VMware 가상 컴퓨터 또는 물리적 서버로 실행하여 SQL Server를 보호할 수 있습니다.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td colspan = "2"><b>Hyper-V</b></td>
		<td colspan = "2"><b>VMware</b></td>
		<td colspan = "2"><b>물리적 서버</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>온-프레미스 간</td>
		<td>온-프레미스와 Azure 간</td>
		<td>온-프레미스 간</td>
		<td>온-프레미스와 Azure 간</td>
		<td>온-프레미스 간</td>
		<td>온-프레미스와 Azure 간</td>
    </tr>
	<tr align="left" valign="top">
		<td>예</td>
		<td>예</td>
		<td>예</td>
		<td>서비스 예정</td>
		<td>예</td>
		<td>서비스 예정</td>
    </tr>
    </tbody>
    </table>

## 지원 및 통합

사이트 복구는 재해 복구 솔루션을 제공하기 위해 표에 요약된 네이티브 SQL Server BCDR 기술과 함께 통합할 수 있습니다.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>기능</b></td>
		<td><b>세부 정보</b></td>
		<td><b>SQL Server 버전</b></td>
    </tr>
    </tr><tr align="left" valign="top">
		<td><b>AlwaysOn 가용성 그룹</b></td>
		<td><p>SQL Server의 여러 독립 실행형 인스턴스는 여러 노드가 있는 장애 조치 클러스터에서 실행됩니다.</p> <p>SQL Server 인스턴스에서 복사(미러링)할 수 있는 장애 조치 그룹으로 데이터베이스를 그룹화하여 공유 저장소가 필요하지 않습니다.</p> <p>기본 사이트 및 하나 이상의 보조 사이트 간에 재해 복구를 제공합니다. 동기 복제 및 자동 장애 조치를 사용하여 가용성 그룹에서 구성된 SQL Server 데이터베이스로 공유되지 않은 클러스터에서 두 노드를 설정할 수 있습니다.</p></td>
		<td>SQL Server 2014/2012 Enterprise 버전</td>
    </tr>
	<tr align="left" valign="top">
		<td><b>장애 조치 클러스터링(AlwaysOn FCI)</b></td>
		<td><p>SQL Server는 온-프레미스 SQL Server 작업의 고가용성을 위해 Windows 장애 조치 클러스터링을 활용합니다.</p><p>공유 디스크를 사용하는 SQL Server 서버의 인스턴스를 실행하는 노드는 장애 조치 클러스터에서 구성됩니다. 인스턴스가 다운되는 경우 클러스터는 다른 것을 장애 조치합니다.</p> <p>클러스터는 공유 저장소의 오류 또는 중단을 보호하지 않습니다. 공유 디스크는 iSCSI, 파이버 채널 또는 VHDX와 함께 구현할 수 있습니다.</p></td>
		<td><p>SQL Server Enterprise 버전</p> <p>SQL Server Standard 버전(두 노드로만 제한됨)</p></td>
	<tr align="left" valign="top">
		<td><b>높은 보안 모드에서 데이터베이스 미러링</b></td>
		<td>하나의 보조 복사본으로 단일 데이터베이스를 보호합니다. 높은 보안(동기) 및 고성능(비동기) 복제 모드에서 모두 사용할 수 있습니다. 장애 조치 클러스터가 필요하지 않습니다.</td>
		<td><p>SQL Server 2008 R2</p><p>SQL Server Enterprise 모든 버전</p></td>
    </tr>
    </tr>
	<tr align="left" valign="top">
		<td><b>독립 실행형 SQL Server</b></td>
		<td>SQL Server 및 데이터베이스는 단일 서버(실제 또는 가상)에서 호스팅됩니다. 호스트 클러스터링은 가상 서버인 경우 고가용성을 위해 사용됩니다. 게스트 수준의 고가용성은 없습니다.</td>
		<td>Enterprise 또는 Standard 에디션</td>
 
    </tbody>
    </table>

다음 표에서는 사이트 복구 배포에 SQL Server BCDR 기술을 통합하기 위한 권장 사항을 요약합니다.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>버전</b></td>
		<td><b>에디션</b></td>
		<td><b>개발</b></td>
		<td><b>온-프레미스 간</b></td>
		<td><b>온-프레미스와 Azure 간</b>&lt;/td
    </tr>
    <tr align="left" valign="top">
		<td rowspan = "3">SQL Server 2014 또는 2012</td>
		<td rowspan = "2">Enterprise</td>
		<td>장애 조치 클러스터 인스턴스</td>
		<td>AlwaysOn 가용성 그룹</td>
		<td>AlwaysOn 가용성 그룹</td>
    </tr>
	<tr align="left" valign="top">
		<td>고가용성을 위한 AlwaysOn 가용성 그룹</td>
		<td>AlwaysOn 가용성 그룹</td>
		<td>AlwaysOn 가용성 그룹</td>
    </tr>
	<tr align="left" valign="top">
		<td>표준</td>
		<td>장애 조치 클러스터 인스턴스</td>
		<td>로컬 미러를 사용하는 사이트 복구 복제</td>
		<td>로컬 미러를 사용하는 사이트 복구 복제</td>
    </tr>
	<tr align="left" valign="top">
		<td>Enterprise 또는 Standard</td>
		<td>독립 실행형</td>
		<td>로컬 미러를 사용하는 사이트 복구 복제</td>
		<td>로컬 미러를 사용하는 사이트 복구 복제</td>
    </tr>
	<tr align="left" valign="top">
		<td>SQL Server 2008 R2</td><td>Enterprise 또는 Standard</td>
		<td>독립 실행형</td>
		<td>로컬 미러를 사용하는 사이트 복구 복제</td>
		<td>로컬 미러를 사용하는 사이트 복구 복제</td>
    </tr>
    </tbody>
    </table>


## 배포 필수 조건

시작하기 전에 필요한 사항:


- 지원되는 SQL Server 버전을 실행하는 온-프레미스 SQL Server 배포입니다. 일반적으로 SQL server에 대한 Active Directory도 필요합니다.
- 배포하려는 시나리오를 위한 필수 조건입니다. 각 배포 문서에서 필수 조건을 찾을 수 있습니다. [사이트 복구 개요](site-recovery-overview.md)에 나열 및 연결되어 있습니다.
- Azure에서 복구를 설정하려는 경우,SQL Server 가상 컴퓨터의 [Azure 가상 컴퓨터 준비 평가](http://www.microsoft.com/download/details.aspx?id=40898)를 실행하여 Azure 및 사이트 복구와 호환되도록 해야 합니다.

## 보호 설정

다음과 같은 몇 가지 단계를 수행해야 합니다.

- Active Directory 설정
- SQL Server 클러스터 또는 독립 실행형 서버에 대한 보호 설정

### Active Directory 설정

제대로 실행 하려면 SQL Server에 대해 보조 복구 사이트에서 Active Directory가 필요합니다. 두 가지 선택 사항이 있습니다.

- **소형 엔터프라이즈**—온-프레미스 사이트에 대한 소량의 응용프로그램 및 단일 도메인 컨트롤러가 있고 전체 사이트를 장애 조치하려는 경우, 사이트 복구 복제를 사용하여 도메인 컨트롤러를 보조 데이터센터 또는 Azure로 복제하는 것이 좋습니다.

- **중형 및 대형 엔터프라이즈**—다량의 응용프로그램이 있고 Active Directory 포리스트를 실행하며 응용프로그램 또는 워크로드에 대한 장애 조치를 하려는 경우, 보조 데이터센터 또는 Azure에서 추가 도메인 컨트롤러를 설정하는 것이 좋습니다. AlwaysOn 가용성 그룹을 사용하여 원격 사이트로 복구하는 경우, 복구된 SQL Server 인스턴스에 대해 사용하기 위해 보조 사이트 또는 Azure에서 다른 추가 도메인 컨트롤러를 설정하는 것이 좋습니다.

이 문서의 지침에서는 도메인 컨트롤러를 보조 위치에서 사용할 수 있다고 가정합니다.

### 독립 실행형 SQL Server에 대한 보호 설정


이 구성에서 SQL Server 컴퓨터를 보호하기 위해 사이트 복구 복제를 사용하는 것이 좋습니다. 정확한 단계는 SQL Server가 가상 컴퓨터 또는 물리적 서버로 설정되었는지에 따라, Azure 또는 보조 온-프레미스 사이트로 복제하려는지에 따라 달라집니다. [사이트 복구 개요](site-recovery-overview.md)에서 모든 배포 시나리오에 대한 지침을 참조하세요.


### SQL Server 클러스터(2012 또는 2014 Enterprise)에 대한 보호 설정

SQL Server가 고가용성을 위해 고가용성 그룹 또는 장애 조치 클러스터 인스턴스를 사용하는 경우, 복구 사이트에서도 고가용성 그룹을 사용하는 것이 좋습니다. 이 지침은 분산된 트랜잭션을 사용하지 않는 응용프로그램에 대한 것입니다.

##### 온-프레미스 간

1. 가용성 그룹으로 [데이터베이스를 구성](https://msdn.microsoft.com/library/hh213078.aspx)합니다.
2. 보조 사이트에서 새 가상 네트워크를 만듭니다.
3. 새 가상 네트워크 및 기본 사이트 사이에서 사이트 간 VPN을 설정합니다.
4. 복구 사이트에서 가상 컴퓨터를 만들고 SQL Server를 설치합니다.
5. 기존의 AlwaysOn 가용성 그룹을 새 SQL Server 가상 컴퓨터로 확장합니다. 이 SQL Server 인스턴스를 비동기 복제 복사본으로 구성합니다.
6. 가용성 그룹 수신기를 만들거나 비동기 복제 가상 컴퓨터를 포함하도록 기존 수신기를 업데이트합니다.
7. 수신기를 사용하여 응용프로그램 팜이 설정되었는지 확인합니다. 데이터베이스 서버 이름을 사용하여 설정하는 경우, 수신기를 사용하도록 업데이트하여 장애 조치 후에 이를 다시 인식할 필요가 없도록 합니다.

#### 온-프레미스와 Azure 간

Azure으로 복제할 때 여러 가용성 그룹 구성은 각 가용성 그룹에는 전용 수신기가 필요하기 때문에 어렵습니다. 또한 각 수신기 구성에는 개별 클라우드 서비스가 필요합니다. 포함되는 모든 데이터베이스와 함께 하나의 가용성 그룹을 구성하는 것이 좋습니다.

1. Azure 가상 네트워크를 만듭니다.
2. 온-프레미스 사이트와 이 네트워크 사이에서 사이트 간 VPN을 설정합니다.
3. 네트워크에 새 SQL Server Azure 가상 컴퓨터를 만들고 비동기 가용성 그룹 복제본으로 이를 구성합니다. Azure에 장애 조치 후 SQL Server 계층에 대해 고가용성이 필요한 경우 Azure에 두 개의 비동기 복제 복사본을 구성합니다.
4. 가상 네트워크에 도메인 컨트롤러의 복제본을 설정합니다.
5. 가상 컴퓨터에서 가상 컴퓨터 확장을 사용하고 있는지 확인합니다. 복구 계획에서 SQL Server 특정 스크립트를 넣어야 합니다.
6. Azure의 내부 부하 분산 장치를 사용하여 가용성 그룹에 대한 SQL Server 수신기를 구성합니다.
7. 데이터베이스 계층에 액세스하는 데 수신기를 사용하도록 응용프로그램 계층을 구성합니다. 분산된 트랜잭션을 사용하는 응용프로그램의 경우, SAN 복제 또는 VMWare 사이트간 복제로 사이트 복구를 사용하는 것이 좋습니다.

### SQL Server 클러스터(Standard 또는 2008 R2)에 대한 보호 설정

SQL Server Standard 에디션 또는 SQL Server 2008 R2를 실행하는 클러스터의 경우 SQL Server를 보호하기 위해 사이트 복구 복제를 사용하는 것이 좋습니다.

#### 온-프레미스 간

- Hyper-V 환경의 경우, 응용프로그램이 분산된 트랜잭션을 사용하는 경우 [SAN 복제로 사이트 복구](site-recovery-vmm-san.md)를 배포하는 것이 좋습니다.

- VMware 환경의 경우, [VMware간](site-recovery-vmware-to-vmware.md) 보호를 배포하는 것이 좋습니다.

#### 온-프레미스와 Azure 간

Azure로 복제할 때 사이트 복구는 게스트 클러스터 지원을 지원하지 않습니다. 또한 SQL Server는 Standard 에디션에 대해 저비용 재해 복구 솔루션을 제공하지 않습니다. 독립 실행형 SQL Server에 온-프레미스 SQL Server 클러스터를 보호하고 이를 Azure에서 복구하는 것이 좋습니다.


1. 온-프레미스 사이트에 추가 독립 실행형 SQL Server 인스턴스를 구성합니다.
2. 보호가 필요한 데이터베이스에 대한 미러로 제공하기 위해 이 인스턴스를 구성합니다. 높은 보안 모드에서 미러링을 구성합니다.
3.	환경([Hyper-V](site-recovery-hyper-v-site-to-azure.md) 또는 [VMware](site-recovery-vmware-to-azure.md))에 따라 온-프레미스 사이트에서 사이트 복구를 구성합니다.
4.	Azure에 새 SQL Server 인스턴스를 복제하도록 사이트 복구 복제를 사용합니다. 높은 보안으로 미러를 복사하여 기본 클러스터와 동기화할 수 있지만 사이트 복구 복제를 사용하여 Azure에 복제됩니다.

다음 그래픽에서는 이러한 설정을 보여줍니다.

![표준 클러스터](./media/site-recovery-sql/BCDRStandaloneClusterLocal.png)



##복구 계획 만들기

복구 계획은 함께 장애 조치해야 하는 컴퓨터를 그룹화합니다. 시작하기 전에 [복구 계획](site-recovery-create-recovery-plans.md) 및 [장애 조치](site-recovery-failover.md)에 대해 알아봅니다.


### SQL Server 클러스터(SQL Server 2012/2014 Enterprise)에 대한 복구 계획 만들기

#### Azure에 장애 조치를 위한 SQL Server 스크립트 구성

이 시나리오에서는 SQL Server 가용성 그룹의 스크립트된 장애 조치를 구성하기 위해 사용자 지정 스크립트 및 복구 계획을 위한 Azure 자동화를 활용합니다.

1.	가용성 그룹을 장애 조치하기 위해 스크립트용 로컬 파일을 만듭니다. 이 샘플 스크립트는 Azure 복제본에서 가용성 그룹에 대한 경로를 지정하고 해당 복제본 인스턴스에 장애 조치를 합니다. 이 스크립트는 사용자 지정 스크립트 확장으로 전달을 통해 SQL Server 복제본 가상 컴퓨터에서 실행됩니다.



    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2.	Azure 저장소 계정에서 Blob으로 스크립트를 업로드합니다. 이 예제를 사용하여:

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Azure의 SQL Server 복제본 가상 컴퓨터에서 스크립트를 호출하는 Azure 자동화 runbook을 만듭니다. 이 예제 스크립트를 사용하여 이 작업을 수행합니다. 복구 계획에서 자동화 runbook 사용에 대해 [자세히 알아봅니다](site-recovery-runbook-automation.md).

    	workflow SQLAvailabilityGroupFailover
    	{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    		$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    		#Connect to Azure
    		$AzureAccount = Add-AzureAccount -Credential $Cred
    		$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    		Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    		InLineScript
    		{
     		#Update the script with name of your storage account, key and blob name
     		$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     		$sasuri = New-AzureStorageBlobSASToken -Container "script-container"- Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     		Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     		if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       			{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       			}
     		else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
    	}

4.	응용프로그램에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 자동화 runbook을 호출하는 "pre-Group 1 boot" 스크립트 단계를 추가합니다.

#### 보조 사이트에 장애 조치를 위한 SQL Server 스크립트 구성

1. 이 샘플 스크립트를 기본 및 보조 사이트의 VMM 라이브러리에 추가합니다.

    	Param(
    	[string]$SQLAvailabilityGroupPath
    	)
    	import-module sqlps
    	Switch-SqlAvailabilityGroup -Path $SQLAvailabilityGroupPath -AllowDataLoss -force

2. 응용프로그램에 대한 복구 계획을 만들 때 가용성 그룹을 장애 조치하기 위해 스크립트를 호출하는 "pre-Group 1 boot" 스크립트 단계를 추가합니다.

### SQL Server 클러스터(Standard)에 대한 복구 계획 만들기

#### Azure에 장애 조치를 위한 SQL Server 스크립트 구성

1.	SQL Server 데이터베이스 미러링을 장애 조치하기 위해 스크립트용 로컬 파일을 만듭니다. 이 샘플 스크립트를 사용합니다.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	Azure 저장소 계정에서 Blob으로 스크립트를 업로드합니다. 이 샘플 스크립트를 사용합니다.

    	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key"
    	Set-AzureStorageBlobContent -Blob "AGFailover.ps1" -Container "script-container" -File "ScriptLocalFilePath" -context $context

3.	Azure의 SQL Server 복제본 가상 컴퓨터에서 스크립트를 호출하는 Azure 자동화 runbook을 만듭니다. 이 예제 스크립트를 사용하여 이 작업을 수행합니다. 복구 계획에서 자동화 runbook 사용에 대해 [자세히 알아봅니다](site-recovery-runbook-automation.md). 이를 수행하기 전에 가상 컴퓨터 에이전트가 장애 조치된 SQL Server 가상 컴퓨터에서 실행 중인지 확인합니다.

    	workflow SQLAvailabilityGroupFailover
		{
    		param (
        		[Object]$RecoveryPlanContext
    		)

    	$Cred = Get-AutomationPSCredential -name 'AzureCredential'
	
    	#Connect to Azure
    	$AzureAccount = Add-AzureAccount -Credential $Cred
    	$AzureSubscriptionName = Get-AutomationVariable –Name ‘AzureSubscriptionName’
    	Select-AzureSubscription -SubscriptionName $AzureSubscriptionName
    
    	InLineScript
    	{
     	#Update the script with name of your storage account, key and blob name
     	$context = New-AzureStorageContext -StorageAccountName "Account" -StorageAccountKey "Key";
     	$sasuri = New-AzureStorageBlobSASToken -Container "script-container" -Blob "AGFailover.ps1" -Permission r -FullUri -Context $context;
     
     	Write-output "failovertype " + $Using:RecoveryPlanContext.FailoverType;
               
     	if ($Using:RecoveryPlanContext.FailoverType -eq "Test")
       		{
           		#Skipping TFO in this version.
           		#We will update the script in a follow-up post with TFO support
           		Write-output "tfo: Skipping SQL Failover";
       		}
     	else
       			{
           		Write-output "pfo/ufo";
           		#Get the SQL Azure Replica VM.
           		#Update the script to use the name of your VM and Cloud Service
           		$VM = Get-AzureVM -Name "SQLAzureVM" -ServiceName "SQLAzureReplica";     
       
           		Write-Output "Installing custom script extension"
           		#Install the Custom Script Extension on teh SQL Replica VM
           		Set-AzureVMExtension -ExtensionName CustomScriptExtension -VM $VM -Publisher Microsoft.Compute -Version 1.3| Update-AzureVM; 
                    
           		Write-output "Starting AG Failover";
           		#Execute the SQL Failover script
           		#Pass the SQL AG path as the argument.
       
           		$AGArgs="-SQLAvailabilityGroupPath sqlserver:\sql\sqlazureVM\default\availabilitygroups\testag";
       
           		Set-AzureVMCustomScriptExtension -VM $VM -FileUri $sasuri -Run "AGFailover.ps1" -Argument $AGArgs | Update-AzureVM;
       
           		Write-output "Completed AG Failover";

       			}
        
    		}
		}



4. SQL Server 계층 장애 조치를 위해 복구 계획에 이러한 단계를 추가합니다.

	- 계획된 장애 조치의 경우 “그룹 종료” 후에 기본 클러스터를 종료하도록 기본 쪽 스크립트를 추가합니다.
	- 복구 계획에, 가급적이면 첫 번째 부팅 그룹에서 SQL Server 데이터베이스 미러 가상 컴퓨터를 추가합니다.
3.	위의 자동화 스크립트를 사용하여 이 가상 컴퓨터 내의 미러 복사본을 장애 조치하도록 사후 장애 조치 스크립트를 추가합니다. 데이터베이스 인스턴스 이름이 변경되기 때문에 응용프로그램 계층은 새 데이터베이스를 사용하도록 다시 구성해야 합니다.


#### 보조 사이트에 장애 조치를 위한 SQL Server 스크립트 구성

1.	이 샘플 스크립트를 기본 및 보조 사이트의 VMM 라이브러리에 추가합니다.

    	Param(
    	[string]$database
    	)
    	Import-module sqlps
    	Invoke-sqlcmd –query “ALTER DATABASE $database SET PARTNER FORCE_SERVICE_ALLOW_DATA_LOSS”

2.	복구 계획에, 가급적이면 첫 번째 부팅 그룹에서 SQL Server 데이터베이스 미러 가상 컴퓨터를 추가합니다.
3.	위의 VMM 스크립트를 사용하여 이 가상 컴퓨터 내의 미러 복사본을 장애 조치하도록 사후 장애 조치 스크립트를 추가합니다. 데이터베이스 인스턴스 이름이 변경되기 때문에 응용프로그램 계층은 새 데이터베이스를 사용하도록 다시 구성해야 합니다.





## 테스트 장애 조치 시 고려 사항

AlwaysOn 가용성 그룹을 사용하는 경우 SQL Server 계층의 테스트 장애 조치를 수행할 수 없습니다. 대신 다음과 같은 옵션을 고려합니다.

- 옵션 1

	1. 응용프로그램 및 프런트엔드 계층의 테스트 장애 조치를 수행합니다.
	2. 읽기 전용 모드에서 복제 복사본에 액세스하도록 응용프로그램 계층을 업데이트하고 응용프로그램의 읽기 전용 테스트를 수행합니다.

- 옵션 2
1.	복제본 SQL Server 가상 컴퓨터 인스턴스(사이트간 또는 Azure 백업용 VMM 복제 사용)를 만들고 이를 테스트 네트워크로 가져옵니다.
2.	복구 계획을 사용하여 테스트 장애 조치를 수행합니다.

## 장애 복구 고려 사항

SQL 표준 클러스터의 경우, 계획되지 않은 장애 조치 후의 장애 복구는 SQL Server 백업이 필요하며 미러 인스턴스에서 원래 클러스터로 복구한 다음 미러를 다시 설정해야 합니다.





 

<!---HONumber=July15_HO2-->