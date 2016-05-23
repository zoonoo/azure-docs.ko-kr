<properties
	pageTitle="Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션"
	description="이 문서는 플랫폼 지원 마이그레이션 서비스 기능 즉, Azure CLI를 사용한 Azure Resource Manager에 대한 서비스 관리에 대해 설명합니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Azure CLI를 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션

이 단계는 Azure CLI 명령을 사용하여 클래식에서 Resource Manager로 IaaS 리소스를 마이그레이션하는 방법을 보여줍니다. 이 문서는 [Azure CLI](../xplat-cli-install.md) 로그인(`azure login`)이 필요합니다.

## 1 단계: 마이그레이션 준비

클래식에서 Resource Manager로 IaaS 리소스 마이그레이션을 평가하는 몇 가지 모범 사례가 있습니다.

- 지원되지 않는 구성 또는 기능 목록은 [여기](virtual-machines-windows-migration-classic-resource-manager.md)에서 읽어보세요. 지원되지 않는 구성 또는 기능을 사용하는 가상 컴퓨터가 있다면, 해당 기능/구성 지원이 발표되기를 기다리는 것이 좋습니다. 아니면, 필요에 맞을 경우 마이그레이션이 가능하도록 해당 기능을 제거하거나 해당 구성을 사용하지 않을 수 있습니다.
-	인프라와 응용 프로그램을 배포하는 자동화된 스크립트가 있다면, 마이그레이션을 위해 해당 스크립트를 사용하여 유사한 테스트 설치를 만들어 보세요. 아니면, Azure 포털을 사용하여 샘플 환경을 설정할 수도 있습니다.
- 서비스가 공개 미리 보기에 포함되므로, 마이그레이션을 위한 테스트 환경이 프로덕션 환경과 반드시 격리되도록 해야 합니다. 테스트와 프로덕션 환경 사이에 저장소 계정, VNET 또는 기타 리소스를 혼합하지 마세요.

## 2단계: 마이그레이션 공개 미리 보기용 구독 및 등록을 설정합니다.

마이그레이션 시나리오에 대해, 클래식 및 Resource Manager에 대한 환경을 설정해야 합니다. [Azure CLI를 설치](../xplat-cli-install.md)하고 [구독을 선택](../xplat-cli-connect.md)합니다.

다음 명령을 사용하여 Azure 구독을 선택합니다.

	azure account set "azure-subscription-name"

다음 명령을 사용하여 공개 미리 보기에 등록합니다. 경우에 따라, 이 명령의 시간이 초과될 수 있지만, 등록은 성공적으로 수행됩니다. 다음 단계에 따라 등록 상태를 확인하세요.

	azure provider register Microsoft.ClassicInfrastructureMigrate

등록이 완료되기까지 5분 동안 기다려 주세요. 다음 명령을 사용하여 승인 상태를 확인할 수 있습니다. 계속하기 전에 RegistrationState가 'Registered'인지 확인해야 합니다.

	azure provider show Microsoft.ClassicInfrastructureMigrate

이제 CLI를 asm 모드로 전환합니다.

	azure config mode asm

## 3단계: IaaS 리소스를 마이그레이션하는 명령

>[AZURE.NOTE] 아래에 설명된 모든 작업은 idempotent입니다. 지원되지 않는 기능 또는 구성 오류 이외의 항목을 실행할 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. 그러면 플랫폼에서 작업을 다시 시도합니다.

### 클라우드 서비스에서(가상 네트워크가 아닌) 가상 컴퓨터 마이그레이션

다음 명령을 사용하여 클라우드 서비스 목록을 가져와서 마이그레이션할 클라우드 서비스를 선택합니다. 클라우드 서비스의 VM이 VNET이거나 VM에 웹/작업자 역할이 있으면, 오류 메시지가 표시됩니다.

	azure service list

다음 명령을 실행하여 자세하게 출력된 정보에서 클라우드 서비스에 대한 배포 이름을 확보합니다. 대부분의 경우, 배포 이름은 클라우드 서비스 이름과 같습니다.

	azure service show servicename -vv

마이그레이션을 위해 클라우드 서비스의 가상 컴퓨터를 준비합니다. 두 가지 옵션 중 선택할 수 있습니다.

VM을 플랫폼에서 만든 가상 네트워크에 마이그레이션하려면, 아래 명령을 사용합니다.

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Resource Manager 배포 모델에서 기존 가상 네트워크로 마이그레이션하려면, 아래 명령을 사용합니다.

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

준비 작업이 완료되면, 자세하게 출력된 정보를 검토하여 VM의 마이그레이션 상태를 확보하고 VM이 'Prepared' 상태인지 확인합니다.

	azure vm show vmName -vv

CLI 또는 Azure 포털을 사용하여 준비된 리소스에 대한 구성을 확인합니다. 마이그레이션 준비가 되지 않았다면, 이전 상태로 돌아가서 아래 명령을 사용합니다.

	azure service deployment abort-migration serviceName deploymentName

준비된 구성이 양호하면, 계속 진행하고 아래 명령을 사용하여 리소스를 커밋합니다.

	azure service deployment commit-migration serviceName deploymentName

### 가상 네트워크에서 가상 컴퓨터 마이그레이션

마이그레이션할 가상 네트워크를 선택합니다. 가상 네트워크에 웹/작업자 역할이 포함되어 있거나 지원되지 않는 구성을 포함하는 VM이 있으면, 유효성 검사 오류 메시지가 표시됩니다.

다음 명령을 사용하여 구독의 가상 네트워크를 모두 가져옵니다.

	azure network vnet list

아래 명령을 사용하여 마이그레이션할 가상 네트워크를 준비합니다.

	azure network vnet prepare-migration virtualnetworkname

CLI 또는 Azure 포털을 사용하여 준비된 가상 컴퓨터에 대한 구성을 확인합니다. 마이그레이션 준비가 되지 않았다면, 이전 상태로 돌아가서 아래 명령을 사용합니다.

	azure network vnet abort-migration virtualnetworkname

준비된 구성이 양호하면, 계속 진행하고 아래 명령을 사용하여 리소스를 커밋합니다.

	azure network vnet commit-migration virtualnetworkname

## 참조

- [클래식에서 Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](virtual-machines-windows-migration-classic-resource-manager.md)
- [클래식에서 Resource Manager로의 플랫폼 지원 마이그레이션에 대한 기술 정보](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0511_2016-->