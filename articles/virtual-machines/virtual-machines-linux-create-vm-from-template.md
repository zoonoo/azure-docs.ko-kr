<properties
	pageTitle="Azure 템플릿을 사용하여 Linux VM 만들기 | Microsoft Azure"
	description="Azure Resource Manager 템플릿을 사용하여 Azure에 Linux VM을 만듭니다."
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
	ms.topic="hero-article"
	ms.date="04/29/2016"
	ms.author="v-livech"/>

# Azure 템플릿을 사용하여 Linux VM 만들기

이 문서에서는 Azure 템플릿을 사용하여 Azure에서 Linux 가상 컴퓨터를 신속하게 배포하는 방법을 보여 줍니다. 이 문서에는 Azure 계정([무료 평가판 가져오기](https://azure.microsoft.com/pricing/free-trial/))과, 리소스 관리자 모드(`azure config mode arm`)로 로그인(`azure login`)된 [Azure CLI](../xplat-cli-install.md)가 필요합니다. [Azure 포털](virtual-machines-linux-quick-create-portal.md) 또는 [Azure CLI](virtual-machines-linux-quick-create-cli.md)를 사용하여 Linux VM을 신속히 배포할 수도 있습니다.


## 빠른 명령

다음 명령 예제에서 &lt;와 &gt; 사이의 값을 사용자 환경의 값으로 바꿉니다.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## 자세한 연습

템플릿을 사용하면 사용자 이름 및 호스트 이름과 같은 시작 작업 중에 사용자 지정하려는 설정으로 Azure에서 VM을 만들 수 있습니다. 이 문서는 하나의 열린 포트(SSH의 경우 22)를 가진 NSG(네트워크 보안 그룹)를 만들고 로그인에 대해 SSH 키가 필요한 Azure 템플릿을 사용하여 Ubuntu VM을 시작하는 방법에 대해 알아봅니다.

Azure Resource Manager 템플릿은 이 문서에서처럼 Ubuntu VM을 시작하는 등 간단한 일회성 작업에 사용할 수 있거나 또는 네트워킹에서 응용 프로그램 스택 배포에 대한 OS까지 테스트, 개발 또는 프로덕션 배포와 같은 전체 환경의 복잡한 Azure 구성을 생성하는 데 사용할 수 있는 JSON 파일입니다.

## Linux VM 만들기

다음 코드 예제에서는 [이 Azure Resource Manager 템플릿](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)을 사용하여 동일한 시간에 리소스 그룹을 만들고 SSH 보안 Linux VM을 배포하기 위해 `azure group create`을 호출하는 방법을 보여줍니다. 예제에서 환경에 고유한 이름을 사용해야 합니다. 이 예에서는 `quicksecuretemplate`을 리소스 그룹 이름으로, `securelinux`를 VM 이름으로, `quicksecurelinux`를 하위 도메인 이름으로 사용합니다.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

출력

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

`--template-uri` 매개 변수를 사용하여 새 리소스 그룹을 만들고 VM을 배포할 수 있습니다. 또는 인수인 템플릿 파일에 대한 경로가 있는 `--template-file` 매개 변수를 사용하여 템플릿을 다운로드하거나 로컬로 만들고 전달할 수 있습니다. Azure CLI는 템플릿에서 필요한 매개 변수에 대한 메시지를 표시합니다.

## 다음 단계

템플릿을 사용하여 Linux VM을 만들면 템플릿과 함께 배포할 수 있는 다른 앱 프레임워크를 확인하려고 합니다. [템플릿 갤러리](https://azure.microsoft.com/documentation/templates/)를 검색하여 다음에 배포하는 앱 프레임워크를 검색합니다.

<!---HONumber=AcomDC_0504_2016-->