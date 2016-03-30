<properties
	pageTitle="Azure 템플릿을 사용하여 보안 Linux VM 만들기 | Microsoft Azure"
	description="Azure Resource Manager 템플릿을 사용하여 Azure에 보안 Linux VM을 만듭니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/08/2016"
	ms.author="vlivech"/>

# Azure 템플릿을 사용하여 보안 Linux VM 만들기

템플릿을 사용하면 사용자 이름 및 호스트 이름과 같은 시작 작업 중에 사용자 지정하려는 설정으로 Azure에서 VM을 만들 수 있습니다. 이 문서는 하나의 열린 포트(SSH의 경우 22)를 가진 NSG(네트워크 보안 그룹)를 만들고 로그인에 대해 SSH 키가 필요한 Azure 템플릿을 사용하여 Ubuntu VM을 시작하는 방법에 대해 알아봅니다.

Azure Resource Manager 템플릿은 이 문서에서처럼 Ubuntu VM을 시작하는 등 간단한 일회성 작업에 사용할 수 있거나 또는 네트워킹에서 응용 프로그램 스택 배포에 대한 OS까지 테스트, 개발 또는 프로덕션 배포와 같은 전체 환경의 복잡한 Azure 구성을 생성하는 데 사용할 수 있는 JSON 파일입니다.

## 목표

- Azure에서 Azure 템플릿을 사용하여 SSH 보안 Linux VM 만들기

## 필수 조건

- Azure 계정([무료 평가판 받기!](https://azure.microsoft.com/pricing/free-trial/)), [SSH 공개 키 파일](virtual-machines-linux-mac-create-ssh-keys.md), [Azure CLI](../xplat-cli-install.md) 다음에 `azure config mode arm`를 입력하여 리소스 모드에 CLI를 배치합니다. `azure login`를 입력하여 CLI로 Azure에 로그인합니다.

## 빠른 명령 요약

이 배포는 Azure Resource Manager 템플릿이 있는 위치를 나타내는 옵션을 선택하는 작업과 함께 하나의 명령을 필요로 합니다. 이 항목에는 예를 들어 Azure 빠른 시작 템플릿 Github 리포지토리에서 직접 템플릿을 사용합니다. 아래에서 다양한 옵션은 단순히 템플릿 및 매개 변수 파일이 위치한 곳을 나타냅니다.

1. `azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]`

## Linux VM 만들기

다음 코드 예제에서는 [이 Azure Resource Manager 템플릿](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)사용하여 동일한 시간에 리소스 그룹을 만들고 SSH 보안 Linux VM을 배포하기 위해 `azure group create`을 호출하는 방법을 보여줍니다. 예제에서 환경에 고유한 이름을 사용해야 합니다. 이 예에서는 `quicksecuretemplate`을 리소스 그룹 이름으로, `securelinux`를 VM 이름으로, `quicksecurelinux`를 하위 도메인 이름으로 사용합니다.

```bash
azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

## 자세한 연습

`--template-uri` 매개 변수를 사용하여 새 리소스 그룹을 만들고 VM을 배포할 수 있습니다. 또는 인수인 템플릿 파일에 대한 경로가 있는 `--template-file` 매개 변수를 사용하여 템플릿을 다운로드하거나 로컬로 만들고 전달할 수 있습니다. Azure CLI는 템플릿에서 필요한 매개 변수에 대한 메시지를 표시합니다.

## 다음 단계

템플릿을 사용하여 Linux VM을 만들면 템플릿과 함께 사용할 수 있는 다른 앱 프레임워크를 확인하려고 합니다. [템플릿 갤러리](https://azure.microsoft.com/documentation/templates/)를 검색하여 다음에 배포하는 앱 프레임워크를 검색합니다.

<!---HONumber=AcomDC_0323_2016-->