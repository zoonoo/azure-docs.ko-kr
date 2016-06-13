<properties
	pageTitle="Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션 | Microsoft Azure"
	description="이 문서에서는 플랫폼 지원 방식의 PowerShell 스크립트를 사용하여 클래식에서 Azure Resource Manager로 리소스를 마이그레이션하는 과정을 안내합니다."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션

이러한 단계에서는 Azure PowerShell 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다. 다음 단계에서는 빈칸을 채우는 방식에 따라 사용자 지정 환경을 마이그레이션합니다. 명령을 가져와서 변수를 사용자 고유의 값으로 대체하면 됩니다("$"로 시작하는 줄).

## 1단계: 마이그레이션 준비

클래식에서 Resource Manager로 IaaS 리소스 마이그레이션을 평가하는 몇 가지 모범 사례가 있습니다.

- [지원되지 않는 구성 또는 기능 목록](virtual-machines-windows-migration-classic-resource-manager.md)을 읽어보세요. 지원되지 않는 구성 또는 기능을 사용하는 가상 컴퓨터가 있다면, 해당 구성/기능 지원이 발표되기를 기다리는 것이 좋습니다. 아니면, 필요에 맞을 경우 마이그레이션이 가능하도록 해당 기능을 제거하거나 해당 구성을 사용하지 않을 수 있습니다.
-	현재 인프라 및 응용 프로그램을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.
- 서비스가 공개 미리 보기에 포함되므로, 마이그레이션을 위한 테스트 환경이 프로덕션 환경과 반드시 격리되도록 해야 합니다. 테스트와 프로덕션 환경 사이에 저장소 계정, 가상 네트워크 또는 기타 리소스를 혼합하지 마세요.

## 2단계: Azure PowerShell 최신 버전 설치

설치에 대한 두 가지 주요 옵션으로 [PowerShell 갤러리](https://www.powershellgallery.com/profiles/azure-sdk/)와 [WebPI(웹 플랫폼 설치 관리자)](http://aka.ms/webpi-azps)가 있습니다. WebPI는 월별 업데이트를 받습니다. PowerShell 갤러리는 지속적으로 업데이트를 받습니다.

자세한 내용은 [Azure PowerShell 1.0](https://azure.microsoft.com//blog/azps-1-0/)을 참조하세요.

## 3단계: 마이그레이션 공개 미리 보기용 구독 및 등록 설정

먼저, PowerShell 프롬프트를 시작합니다. 마이그레이션 시나리오의 경우 클래식 및 Resource Manager에 대한 환경을 설정해야 합니다.

Resource Manager 모델에 대한 계정으로 로그인합니다.

	Login-AzureRmAccount

다음 명령으로 사용 가능한 구독을 가져옵니다.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

현재 세션에 대한 Azure 구독을 설정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

다음 명령을 사용하여 공개 미리 보기에 등록합니다.

	Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

등록이 완료될 때까지 5분 정도 기다려 주세요. 다음 명령을 사용하여 승인 상태를 확인할 수 있습니다. 계속 진행하기 전에 RegistrationState가 `Registered`인지 확인합니다.

	Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate

이제 클래식 모델에 대한 계정으로 로그인합니다.

	Add-AzureAccount

다음 명령으로 사용 가능한 구독을 가져옵니다.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

현재 세션에 대한 Azure 구독을 설정합니다. < and > 문자를 포함하여 따옴표 안의 모든 항목을 올바른 이름으로 바꿉니다.

	$subscr="<subscription name>"
	Get-AzureSubscription –SubscriptionName $subscr | Select-AzureSubscription

## 4단계: IaaS 리소스를 마이그레이션하는 명령 실행

>[AZURE.NOTE] 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼에서 작업을 다시 시도합니다.

### 클라우드 서비스에서(가상 네트워크가 아닌) 가상 컴퓨터 마이그레이션

다음 명령을 사용하여 클라우드 서비스 목록을 가져와서 마이그레이션할 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 가상 네트워크이거나 VM에 웹/작업자 역할이 있으면, 오류 메시지가 표시됩니다.

	Get-AzureService | ft Servicename

다음 명령을 사용하여 클라우드 서비스에 대한 배포 이름을 가져옵니다.

	$serviceName = "<service name>"
	$deployment = Get-AzureDeployment -ServiceName $serviceName
	$deploymentName = $deployment.DeploymentName

마이그레이션을 위해 클라우드 서비스의 가상 컴퓨터를 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

VM을 플랫폼에서 만든 가상 네트워크에 마이그레이션하려면, 다음 명령을 사용합니다.

	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork

Resource Manager 배포 모델에서 기존 가상 네트워크로 마이그레이션하려면, 다음 명령을 사용합니다.

	$existingVnetRGName = "<Existing VNET's Resource Group Name>"
	$vnetName = "<Virtual Network Name>"
	$subnetName = "<Subnet name>"
	Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName 		-VirtualNetworkName $vnetName -SubnetName $subnetName

준비 작업이 완료되면, VM의 마이그레이션 상태를 쿼리하고 `Prepared` 상태인지 확인합니다.

	$vmName = "<vm-name>"
	$vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
	$migrationState = $vm.VM.MigrationState

PowerShell 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

	Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

	Move-AzureService -Commit -ServiceName docmigtest1 -DeploymentName docmigtest1

### 가상 네트워크에서 가상 컴퓨터 마이그레이션

마이그레이션할 가상 네트워크를 선택합니다. 가상 네트워크에 웹/작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면, 유효성 검사 오류 메시지가 표시됩니다.

다음 명령을 사용하여 마이그레이션을 위한 가상 네트워크를 준비합니다.

	$vnetName = "VNET-Name"
	Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName

PowerShell 또는 Azure 포털을 사용하여 준비된 가상 컴퓨터에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

	Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

	Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName

## 다음 단계

- [클래식에서 Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](virtual-machines-windows-migration-classic-resource-manager.md)
- [클래식에서 Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [커뮤니티 PowerShell 스크립트를 사용하여 클래식 가상 컴퓨터를 Azure Resource Manager로 복제](virtual-machines-windows-migration-scripts.md)

<!---HONumber=AcomDC_0601_2016-->