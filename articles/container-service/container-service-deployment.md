<properties
   pageTitle="Azure 컨테이너 서비스 클러스터 배포 | Microsoft Azure"
   description="Azure 포털, Azure CLI 또는 PowerShell을 사용하여 Azure 컨테이너 서비스 클러스터를 배포합니다."
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="rogardle"/>

# Azure 컨테이너 서비스 클러스터 배포

Azure 컨테이너 서비스는 인기 있는 오픈 소스 컨테이너 클러스터링 및 오케스트레이션 솔루션의 신속한 배포를 제공합니다. Azure 컨테이너 서비스를 사용하면 Azure Resource Manager 템플릿 또는 Azure 포털에서 DC/OS 및 Docker Swarm 클러스터를 배포할 수 있습니다. Azure 가상 컴퓨터 규모 집합을 사용하여 이러한 클러스터를 배포하면 클러스터가 Azure 네트워킹 및 저장소 기능을 활용합니다. Azure 컨테이너 서비스에 액세스하려면 Azure 구독이 필요합니다. 아직 구독하지 않은 경우 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)에 등록할 수 있습니다.

이 문서에서는 [Azure 포털](#creating-a-service-using-the-azure-portal), [Azure CLI(명령줄 인터페이스I)](#creating-a-service-using-the-azure-cli) 및 [Azure PowerShell 모듈](#creating-a-service-using-powershell)을 사용하여 Azure 컨테이너 서비스 클러스터를 배포하는 방법을 안내합니다.

## Azure 포털을 사용하여 서비스 만들기

Azure 포털에 로그인하여 **새로 만들기**를 선택하고, Azure 마켓플레이스에서 **Azure 컨테이너 서비스**를 검색합니다.

![배포 만들기 1](media/acs-portal1.png) <br />

**Azure 컨테이너 서비스**를 선택하고 **만들기**를 클릭합니다.

![배포 만들기 2](media/acs-portal2.png) <br />

다음 정보를 입력합니다.

- **사용자 이름**: Azure 컨테이너 서비스 클러스터의 가상 컴퓨터 규모 집합 및 각 가상 컴퓨터의 계정에 사용할 사용자 이름입니다.
- **구독**: Azure 구독을 선택합니다.
- **리소스 그룹**: 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
- **위치**: Azure 컨테이너 서비스 배포를 위한 Azure 지역을 선택합니다.
- **SSH 공개 키**: Azure 컨테이너 서비스 가상 컴퓨터에 대한 인증에 사용할 공개 키를 추가합니다. 키에 줄 바꿈이 없고 'ssh-rsa' 접두사와 'username@domain' 접미사를 포함해야 합니다. **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**와 같은 형태여야 합니다. SSH(보안 셸) 키를 만드는 방법에 대한 지침은 [Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) 및 [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) 문서를 참조하세요.

진행할 준비가 되면 **확인**을 클릭합니다.

![배포 만들기 3](media/acs-portal3.png) <br />

오케스트레이션 유형을 선택합니다. 옵션은 다음과 같습니다.

- **DC/OS**: DC/OS 클러스터를 배포합니다.
- **Swarm**: Docker Swarm 클러스터를 배포합니다.

진행할 준비가 되면 **확인**을 클릭합니다.

![배포 만들기 4](media/acs-portal4.png) <br />

다음 정보를 입력합니다.

- **마스터 수**: 클러스터의 마스터 수입니다.
- **에이전트 수**: Docker Swarm의 경우, 에이전트 규모 집합의 초기 에이전트 수입니다. DC/OS의 경우, 사설 규모 집합의 초기 에이전트 수입니다. 또한, 사전에 지정된 수의 에이전트를 포함하는 공개 규모 집합이 생성됩니다. 이 공개 규모 집합의 에이전트 수는 클러스터에 생성된 마스터의 수를(1개의 마스터에 대한 공개 에이전트 1개, 3 또는 5개의 마스터에 대한 공개 에이전트 2개)를 결정합니다.
- **에이전트 가상 컴퓨터 크기**: 에이전트 가상 컴퓨터의 크기입니다.
- **DNS 접두사**: 서비스의 정규화된 도메인 이름의 주요 부분에 접두사로 사용될 세계적으로 고유 이름입니다.

진행할 준비가 되면 **확인**을 클릭합니다.

![배포 만들기 5](media/acs-portal5.png) <br />

서비스 유효성 검사가 완료되면 **확인**을 클릭합니다.

![배포 만들기 6](media/acs-portal6.png) <br />

**만들기**를 클릭하여 배포 프로세스를 시작합니다.

![배포 만들기 7](media/acs-portal7.png) <br />

Azure 포털에 배포를 고정하도록 선택한 경우 배포 상태를 볼 수 있습니다.

![배포 만들기 8](media/acs-portal8.png) <br />

배포가 완료되면 Azure 컨테이너 서비스 클러스터를 사용할 준비가 됩니다.

## Azure CLI를 사용하여 서비스 만들기

명령줄을 사용하여 Azure 컨테이너 서비스의 인스턴스를 만들려면 Azure 구독이 필요합니다. 아직 구독하지 않은 경우 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)에 등록할 수 있습니다. 또한 Azure CLI를 [설치](../xplat-cli-install.md) 및 [구성](../xplat-cli-connect.md)해야 합니다.

DC/OS 또는 Docker Swarm 클러스터를 배포하려면 GitHub에서 다음 템플릿 중 하나를 선택합니다. 이러한 두 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.

* [DC/OS 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

다음으로, Azure CLI가 Azure 구독에 연결되어 있는지 확인합니다. 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

```bash
azure account show
```
Azure 계정이 반환되지 않으면 다음 명령을 사용하여 CLI를 Azure에 로그인합니다.

```bash
azure login -u user@domain.com
```

다음으로, Azure Resource Manager를 사용하도록 Azure CLI 도구를 구성해야 합니다.

```bash
azure config mode arm
```

다음 명령을 통해 Azure 리소스 그룹 및 컨테이너 서비스 클러스터를 만듭니다.

- **RESOURCE\_GROUP**은 이 서비스에 사용할 리소스 그룹의 이름입니다.
- **LOCATION**은 리소스 그룹 및 Azure 컨테이너 서비스 배포를 만들 Azure 지역입니다.
- **TEMPLATE\_URI**는 배포 파일의 위치입니다. GitHub UI에 대한 포인터가 아닌 원시 파일이어야 합니다. 이 URL을 찾으려면 GitHub에서 azuredeploy.json 파일을 선택하고 **원시** 단추를 클릭합니다.

> [AZURE.NOTE] 이 명령을 실행하면 셸에서 배포 매개 변수 값을 묻는 메시지가 표시됩니다.

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### 템플릿 매개 변수 제공

이 버전의 명령에서는 사용자가 대화형으로 매개 변수를 정의해야 합니다. JSON 형식 문자열 같은 매개 변수를 제공하려는 경우 `-p` 스위치로 수행할 수 있습니다. 예:

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

또는 `-e` 스위치를 사용하여 JSON 형식 매개 변수를 제공할 수 있습니다.

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

`azuredeploy.parameters.json`이라는 이름의 예제 매개 변수를 보려면 GitHub의 Azure 컨테이너 서비스 템플릿을 사용하여 해당 매개 변수를 찾아보세요.

## PowerShell을 사용하여 서비스 만들기

PowerShell 사용하여 Azure 컨테이너 서비스 클러스터를 배포할 수도 있습니다. 이 문서는 [Azure PowerShell 모듈](https://azure.microsoft.com/blog/azps-1-0/) 버전 1.0을 기반으로 합니다.

DC/OS 또는 Docker Swarm 클러스터를 배포하려면 다음 템플릿 중 하나를 선택합니다. 이러한 두 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.

* [DC/OS 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Azure 구독에서 클러스터를 만들기 전에 PowerShell 세션이 Azure에 로그인했는지 확인합니다. 이렇게 하려면 `Get-AzureRMSubscription` 명령을 사용합니다.

```powershell
Get-AzureRmSubscription
```

Azure에 로그인해야 할 경우는 `Login-AzureRMAccount` 명령을 사용합니다.

```powershell
Login-AzureRmAccount
```

새 리소스 그룹에 배포하는 경우 먼저 리소스 그룹을 만들어야 합니다. 새 리소스 그룹을 만들려면 `New-AzureRmResourceGroup` 명령을 사용하고 리소스 그룹 이름 및 대상 지역을 지정합니다.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

리소스 그룹을 만든 후에는 다음 명령을 사용하여 클러스터를 만들 수 있습니다. 원하는 템플릿의 URI는 `-TemplateUri` 매개 변수에 대해 지정됩니다. 이 명령을 실행하면 PowerShell에서 배포 매개 변수 값을 묻는 메시지가 표시됩니다.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### 템플릿 매개 변수 제공

PowerShell에 익숙한 경우 빼기 기호(-)를 입력하고 TAB 키를 눌러 Cmdlet에 사용할 수 있는 매개 변수를 순환시켜 볼 수 있습니다. 이 기능은 템플릿에 정의하는 매개 변수에 대해서도 작동합니다. 템플릿 이름을 입력하자마자 cmdlet이 템플릿을 인출하고 매개 변수의 구문을 분석한 다음 템플릿 매개 변수를 명령에 동적으로 추가합니다. 따라서 템플릿 매개 변수 값을 매우 쉽게 지정할 수 있습니다. 필수 매개 변수 값을 잊은 경우 PowerShell이 값을 묻는 메시지를 표시합니다.

다음은 매개 변수가 포함된 전체 명령입니다. 리소스의 이름에 대한 고유한 값을 제공할 수 있습니다.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## 다음 단계

이제 클러스터가 작동하기 시작했으니 연결 및 관리 정보는 다음 문서를 참조하세요.

- [Azure 컨테이너 서비스 클러스터에 연결](container-service-connect.md)
- [Azure 컨테이너 서비스 및 DC/OS로 작업](container-service-mesos-marathon-rest.md)
- [Azure 컨테이너 서비스 및 Docker Swarm으로 작업](container-service-docker-swarm.md)

<!---HONumber=AcomDC_0914_2016-->