<properties 
	pageTitle="Azure에서 SQL Server 가상 컴퓨터 만들기(PowerShell)"
	description="SQL Server 가상 컴퓨터 갤러리 이미지를 사용하여 Azure VM을 만드는 단계 및 PowerShell 스크립트를 제공합니다."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/26/2015"
	ms.author="jroth"/>

# Azure에서 SQL Server 가상 컴퓨터 만들기(PowerShell)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)

## 개요

이 문서에서는 PowerShell cmdlet을 사용하여 Azure에서 SQL Server 가상 컴퓨터를 만드는 방법에 대한 단계를 제공합니다.

>[AZURE.NOTE]이 문서는 서비스 관리에 만든 가상 컴퓨터를 위한 것이며 이는 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md) 항목에 있는 보다 일반적인 단계의 SQL Server별 확장입니다. 서비스 관리 대신 PowerShell에서 리소스 관리자를 사용하여 SQL Server 가상 컴퓨터를 만들려면 [리소스 관리자 및 Azure PowerShell을 사용하여 Windows 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md) 항목의 리소스 관리자 VM에 대한 일반적인 지침을 참조하세요.

## PowerShell 설치 및 구성

1. Azure 계정이 없는 경우 [Azure 무료 평가판](https://azure.microsoft.com/ko-KR/pricing/free-trial/)을 방문하십시오. 
 
2. [최신 Azure PowerShell cmdlet 설치](../powershell-install-configure.md/#how-to-install-azure-powershell)

3. [Azure 구독과 PowerShell 연결](../powershell-install-configure.md/#how-to-connect-to-your-subscription)

## 대상 Azure 지역 확인

SQL Server 가상 컴퓨터를 특정 Azure 지역에 있는 클라우드 서비스에서 호스트합니다. 다음 단계에서 지역, 저장소 계정 및 클라우드 서비스를 확인할 수 있으며, 이는 자습서의 나머지 부분에 사용됩니다.

1. SQL Server VM을 호스트하기 위해 사용하려는 데이터 센터를 확인합니다. 다음 PowerShell 명령은 사용할 수 있는 지역을 자세히 표시하고 끝 부분에 요약 목록을 표시합니다.

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  원하는 위치를 식별했으면 **$dcLocation**이라는 변수를 해당 지역으로 설정합니다.

		$dcLocation = "<region name>"

## 구독 및 저장소 계정 설정

1. 새 가상 컴퓨터에 대해 사용할 Azure 구독을 확인합니다.

		(Get-AzureSubscription).SubscriptionName

1. 대상 Azure 구독을 **$subscr** 변수에 할당합니다. 그런 다음 이를 현재 Azure 구독으로 설정합니다.

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. 기존 저장소 계정을 확인합니다. 다음 스크립트는 선택한 지역에 있는 모든 저장소 계정을 표시합니다.

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE]새 저장소 계정이 필요한 경우 먼저 New-AzureStorageAccount 명령을 사용하여 저장소 계정 이름(모두 소문자)을 만듭니다(예: **New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation**).

1. 대상 저장소 계정 이름을 **$staccount**에 할당합니다. **Set-AzureSubscription**을 사용하여 구독 및 현재 저장소 계정을 설정합니다.

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## SQL Server 가상 컴퓨터 이미지를 선택합니다.

1. 갤러리에서 사용 가능한 SQL Server 가상 컴퓨터 이미지의 목록을 찾습니다. 이러한 모든 이미지에는 "SQL"로 시작하는 **ImageFamily** 속성이 있습니다. 다음 쿼리는 SQL Server를 미리 설치했을 때 사용 가능한 이미지 패밀리를 표시합니다.

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. 가상 컴퓨터 이미지 패밀리를 찾으면 해당 패밀리에 여러 개의 게시된 이미지가 있을 수 있습니다. 다음 스크립트를 사용하여 선택한 이미지 패밀리에 대한 최신 게시된 가상 컴퓨터 이미지 이름을 찾습니다(예: **Windows Server 2012 R2의 SQL Server 2014 SP1 Enterprise**).

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## 가상 컴퓨터 만들기

마지막으로, PowerShell을 사용하여 가상 컴퓨터를 만듭니다.

1. 새 VM을 호스트할 클라우드 서비스를 만듭니다. 기존 클라우드 서비스를 대신 사용할 수도 있습니다. 클라우드 서비스의 짧은 이름을 사용하여 새 변수 **$svcname**을 만듭니다.

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. 가상 컴퓨터 이름 및 크기를 지정합니다. 가상 컴퓨터 크기에 대한 자세한 내용은 [Azure에 대한 가상 컴퓨터 크기](virtual-machines-size-specs.md)를 참조하세요.

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. 로컬 관리자 계정 및 암호 지정

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. 다음 스크립트를 실행하여 가상 컴퓨터를 만듭니다.

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE]추가 설명과 구성 옵션은 [Azure PowerShell을 사용하여 Windows 기반 가상 컴퓨터 만들기 및 미리 구성](virtual-machines-ps-create-preconfigure-windows-vms.md)의 **명령 집합 빌드** 섹션을 참조하세요.

## PowerShell 스크립트 예

다음 스크립트는 **Windows Server 2012 R2의 SQL Server 2014 SP1 Enterprise** 가상 컴퓨터를 만드는 전체 스크립트 예입니다. 이 스크립트를 사용하는 경우 이 항목의 이전 단계를 기반으로 하여 초기 변수를 사용자 지정해야 합니다.

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5" 
	
	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
	
	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	
	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
	
	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
	
	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	
	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1
	 

## 원격 데스크톱을 사용하여 연결

1. 현재 사용자의 문서 폴더에 .RDP 파일을 만들고 이러한 가상 컴퓨터를 시작하여 설정을 완료합니다.

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. 문서 디렉터리에서 RDP 파일을 시작합니다. 앞에서 입력한 관리자의 사용자 이름 및 암호에 연결합니다(예: 사용자 이름이 VMAdmin이면 사용자로 "\\VMAdmin"을 지정하고 암호 입력).

		.\vm1.rdp

## 원격 액세스를 위한 SQL Server 컴퓨터 구성 완료

원격 데스크톱을 사용하여 컴퓨터에 로그온 한 후 [Azure VM에서 SQL Server 연결을 구성하기 위한 단계](virtual-machines-sql-server-connectivity.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)의 지침에 따라 SQL Server를 구성합니다.

## 다음 단계

PowerShell을 사용하여 가상 컴퓨터를 프로비전하는 추가 지침을 [가상 컴퓨터 설명서](virtual-machines-ps-create-preconfigure-windows-vms.md)에서 찾을 수 있습니다. SQL Server 및 프리미엄 저장소에 관련된 추가 스크립트는 [가상 컴퓨터에서 SQL Server와 함께 Azure 프리미엄 저장소 사용](virtual-machines-sql-server-use-premium-storage.md)을 참조하세요.

대부분의 경우 다음 단계는 이 새로운 SQL Server VM에 데이터베이스를 마이그레이션하는 것입니다. 데이터베이스 마이그레이션 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-migrate-onpremises-database.md)을 참조하세요.

또한 Azure 관리 포털에서 이러한 단계를 수행하는 방법을 알아보려면 [Azure에서 SQL Server 가상 컴퓨터 프로비전](virtual-machines-provision-sql-server.md)을 참조하세요.

이러한 리소스 외에도 [Azure 가상 컴퓨터에서 SQL Server 실행과 관련된 기타 항목](virtual-machines-sql-server-infrastructure-services.md)을 확인하는 것이 좋습니다.

<!---HONumber=August15_HO9-->