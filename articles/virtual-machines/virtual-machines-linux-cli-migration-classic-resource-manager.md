<properties
	pageTitle="Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션 | Microsoft Azure"
	description="이 문서에서는 플랫폼 지원 방식의 Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 리소스를 마이그레이션하는 과정을 안내합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cynthn"/>

# Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션

이러한 단계에서는 Azure CLI(명령줄 인터페이스) 명령을 사용하여 클래식 배포 모델의 laaS(Infrastructure as a Service) 리소스를 Azure Resource Manager 배포 모델로 마이그레이션하는 방법을 보여 줍니다. 이 문서는 [Azure CLI](../xplat-cli-install.md)가 필요합니다.

>[AZURE.NOTE] 여기에 설명된 모든 작업은 idempotent 방식입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼에서 작업을 다시 시도합니다.

## 1단계: 마이그레이션 준비

클래식에서 Resource Manager로 IaaS 리소스 마이그레이션을 평가하는 몇 가지 모범 사례가 있습니다.

- [지원되지 않는 구성 또는 기능 목록](virtual-machines-windows-migration-classic-resource-manager.md)을 읽어보세요. 지원되지 않는 구성 또는 기능을 사용하는 가상 컴퓨터가 있다면, 해당 기능/구성 지원이 발표되기를 기다리는 것이 좋습니다. 아니면, 필요에 맞을 경우 마이그레이션이 가능하도록 해당 기능을 제거하거나 해당 구성을 사용하지 않을 수 있습니다.
-	현재 인프라 및 응용 프로그램을 배포하는 스크립트를 자동화한 경우 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설정을 만들어봅니다. 또는 Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.

## 2 단계: 구독 설정 및 공급자 등록

마이그레이션 시나리오의 경우 클래식 및 Resource Manager에 대한 환경을 설정해야 합니다. [Azure CLI를 설치](../xplat-cli-install.md)하고 [구독을 선택](../xplat-cli-connect.md)합니다.

계정에 로그인합니다.
	
	azure login

다음 명령을 사용하여 Azure 구독을 선택합니다.

	azure account set "<azure-subscription-name>"

>[AZURE.NOTE] 등록은 일회용 단계이지만 마이그레이션 전에 한 번 수행해야 합니다. 등록하지 않으면 다음과 같은 오류 메시지가 표시됩니다.

>	*BadRequest : Subscription is not registered for migration.* 

다음 명령을 사용하여 마이그레이션 리소스 공급자에 등록합니다. 일부 경우에 이 명령이 시간 초과됩니다. 그러나 등록은 성공적으로 수행됩니다.

	azure provider register Microsoft.ClassicInfrastructureMigrate

등록이 완료될 때까지 5분 정도 기다려 주세요. 다음 명령을 사용하여 승인 상태를 확인할 수 있습니다. 계속 진행하기 전에 RegistrationState가 `Registered`인지 확인합니다.

	azure provider show Microsoft.ClassicInfrastructureMigrate

이제 CLI를 `asm` 모드로 전환합니다.

	azure config mode asm


## 3 단계: 옵션 1 - 클라우드 서비스에서 가상 컴퓨터 마이그레이션 

다음 명령을 사용하여 클라우드 서비스 목록을 가져와서 마이그레이션할 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 가상 네트워크이거나 VM에 웹/작업자 역할이 있으면, 오류 메시지가 표시됩니다.

	azure service list

다음 명령을 실행하여 자세하게 출력된 정보에서 클라우드 서비스에 대한 배포 이름을 확보합니다. 대부분의 경우, 배포 이름은 클라우드 서비스 이름과 같습니다.

	azure service show <serviceName> -vv

마이그레이션을 위해 클라우드 서비스의 가상 컴퓨터를 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

VM을 플랫폼에서 만든 가상 네트워크에 마이그레이션하려면, 다음 명령을 사용합니다.

	azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Resource Manager 배포 모델에서 기존 가상 네트워크로 마이그레이션하려면, 다음 명령을 사용합니다.

	azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

준비 작업이 완료되면, 자세하게 출력된 정보를 검토하여 VM의 마이그레이션 상태를 확보하고 VM이 `Prepared` 상태인지 확인합니다.

	azure vm show <vmName> -vv

CLI 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

	azure service deployment abort-migration <serviceName> <deploymentName>

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

	azure service deployment commit-migration <serviceName> <deploymentName>


	
## 3 단계: 옵션 2 - 가상 네트워크에서 가상 컴퓨터 마이그레이션

마이그레이션할 가상 네트워크를 선택합니다. 가상 네트워크에 웹/작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면, 유효성 검사 오류 메시지가 표시됩니다.

다음 명령을 사용하여 구독의 가상 네트워크를 모두 가져옵니다.

	azure network vnet list
	
출력은 다음과 같이 표시됩니다.

![전체 가상 네트워크 이름이 강조 표시된 명령줄의 스크린샷.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

위의 예에서 **virtualNetworkName**은 **"그룹 classicubuntu16"**의 전체 이름입니다.

다음 명령을 사용하여 마이그레이션을 위해 선택한 가상 네트워크를 준비합니다.

	azure network vnet prepare-migration <virtualNetworkName>

CLI 또는 Azure 포털을 사용하여 준비된 가상 컴퓨터에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

	azure network vnet abort-migration <virtualNetworkName>

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

	azure network vnet commit-migration <virtualNetworkName>

## 4 단계: 저장소 계정 마이그레이션

가상 컴퓨터 마이그레이션이 완료되면 저장소 계정을 마이그레이션하는 것이 좋습니다.

다음 명령을 사용하여 마이그레이션을 위한 저장소 계정을 준비합니다.

	azure storage account prepare-migration <storageAccountName>

CLI 또는 Azure 포털을 사용하여 준비된 저장소 계정에 대한 구성을 확인합니다. 마이그레이션할 준비가 되지 않았으며 이전 상태로 되돌아가려면 다음 명령을 사용합니다.

	azure storage account abort-migration <storageAccountName>

준비된 구성이 양호하면 계속 진행하고 다음 명령을 사용하여 리소스를 커밋합니다.

	azure storage account commit-migration <storageAccountName>

## 다음 단계

- [클래식에서 Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](virtual-machines-windows-migration-classic-resource-manager.md)
- [클래식에서 Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0720_2016-->