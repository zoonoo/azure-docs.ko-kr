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
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# Azure 컨테이너 서비스 클러스터 배포

Azure 컨테이너 서비스는 인기 있는 오픈 소스 컨테이너 클러스터링 및 오케스트레이션 솔루션의 신속한 배포를 제공합니다. Azure 컨테이너 서비스를 사용하면 Azure 리소스 관리자 템플릿 또는 포털에서 Marathon Mesos 및 Docker Swarm 클러스터를 배포할 수 있습니다. 이러한 클러스터는 Azure 가상 컴퓨터 규모 집합을 사용하여 배포되고 Azure 네트워킹 및 저장소 기능을 활용합니다. Azure 컨테이너 서비스에 액세스하려면 Azure 구독이 필요합니다. 없는 경우 [무료 평가판에 등록](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)하세요.

이 문서는 [Azure 포털](#creating-a-service-using-the-azure-portal), [Azure CLI](#creating-a-service-using-the-azure-cli) 및 [Azure PowerShell 모듈](#creating-a-service-using-powershell)을 사용하여 Azure 컨테이너 서비스를 배포하는 과정을 안내합니다.
   
## Azure 포털을 사용하여 서비스 만들기
 
Mesos 또는 Docker Swarm 클러스터를 배포하려면 다음 템플릿 중 하나를 선택합니다. **참고** - 이러한 두 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
각 템플릿 페이지에는 'Azure에 배포' 단추가 있으며 이 단추를 클릭하면 다음과 같은 양식의 Azure 포털이 시작됩니다. <br />

![배포 만들기](media/create-mesos-params.png) <br />

이 지침에 따라 양식을 작성하고 완료하면 확인을 선택합니다. <br />

필드 | 설명
----------------|-----------
DNSNAMEPREFIX | 전 세계에서 고유한 값이어야 합니다. 서비스의 각 주요 부분에 대한 DNS 이름을 만들기 위해 사용됩니다. 자세한 정보는 아래에 설명됩니다.
AGENTCOUNT | ACS 에이전트 규모 집합에 생성할 가상 컴퓨터 수입니다.
AGENTVMSIZE | 에이전트 가상 컴퓨터의 크기를 지정합니다. 가장 큰 컨테이너를 호스트하기에 충분한 리소스를 제공하는 크기를 선택해야 합니다.
ADMINUSERNAME | ACS 클러스터의 가상 컴퓨터 규모 집합 및 각 가상 컴퓨터의 계정에 사용할 사용자 이름입니다.
ORCHESTRATORTYPE| ACS 클러스터에서 사용할 Orchestrator를 선택합니다.
MASTERCOUNT | 클러스터에 대한 마스터로 구성할 가상 컴퓨터 수입니다. 1을 선택할 수 있지만 이 경우 클러스터에 어떠한 복원력도 제공하지 않으며 테스트 용도로만 권장됩니다. 프로덕션 클러스터에 권장되는 수는 3 또는 5입니다. 
SSHRSAPUBLICKEY | 가상 컴퓨터에 대한 인증에 SSH를 사용할 때 필요합니다. 공개 키를 추가하는 위치입니다. 키 값을 이 상자에 붙여넣을 때는 매우 신중해야 합니다. 일부 편집기는 내용에 줄 바꿈을 삽입하여 키가 끊길 수 있습니다. 키에 줄 바꿈이 없고 'ssh-rsa' 접두사와 'username@domain' 접미사를 포함하는지 확인합니다. 'ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'와 같은 형태여야 합니다. SSH 키를 만들어야 하는 경우 Azure 설명서 사이트에서 [windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) 및 [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md)에 대한 지침을 찾을 수 있습니다.
  
매개 변수로 적절한 값을 설정했으면 확인을 클릭합니다. 다음으로, 리소스 그룹 이름을 제공하고 하위 지역을 선택하며 약관을 검토 및 동의합니다.

> 미리 보기 중에는 Azure 컨테이너 서비스에 대해 요금이 청구되지 않으며 가상 컴퓨터, 저장소, 네트워킹 등 표준 계산 비용만 청구됩니다.
 
![리소스 그룹 선택](media/resourcegroup.png)
 
마지막으로 "만들기"를 클릭합니다. 대시보드로 돌아가고 배포 블레이드에서 "대시보드에 고정"을 선택하지 않았다고 가정하면 다음과 같은 애니메이션 타일이 표시됩니다.

![배포](media/deploy.png)
 
이제 클러스터가 만들어지는 동안 가만히 기다립니다. 완료되면 ACS 클러스터를 구성하는 리소스를 보여 주는 몇 가지 블레이드가 표시됩니다.
 
![Finished](media/final.png)

## Azure CLI를 사용하여 서비스 만들기

명령줄을 사용하여 Azure 컨테이너 서비스의 인스턴스를 만들려면 Azure 구독이 필요합니다. 아직 구독하지 않은 경우 무료 평가판에 등록할 수 있습니다. 또한 Azure CLI를 설치 및 구성해야 합니다.
 
Mesos 또는 Docker Swarm 클러스터를 배포하려면 다음 템플릿 중 하나를 선택합니다. **참고** - 이러한 두 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)
 
다음으로, Azure CLI가 Azure 구독에 연결되어 있는지 확인합니다. 이는 다음 명령을 사용하여 수행할 수 있습니다.

```bash
Azure account show
```
Azure 계정이 반환되지 않으면 다음을 사용하여 CLI를 Azure에 로그인합니다.
 
```bash
azure login -u user@domain.com
```

다음으로, Azure 리소스 관리자를 사용하도록 Azure CLI 도구를 구성해야 합니다.
 
```bash
azure config mode arm
```
 
새 리소스 그룹에 클러스터를 만들려면 먼저 리소스 그룹을 만들어야 합니다. 이 명령을 사용하며 여기서 `GROUP_NAME`은 만들려는 리소스 그룹의 이름이고 `REGION`은 리소스 그룹을 만들 하위 지역입니다.
 
```bash
azure group create GROUP_NAME REGION
```
 
리소스 그룹이 있으면 이 명령을 사용하여 클러스터를 만들 수 있습니다. 여기서,

- **RESOURCE\_GROUP**은 이 서비스에 사용할 리소스 그룹의 이름입니다.
- **DEPLOYMENT\_NAME**은 이 배포의 이름입니다.
- **TEMPLATE\_URI**는 배포 파일의 위치입니다. **참고** - GitHub UI에 대한 포인터가 아닌 원시 파일이어야 합니다. 이 URL을 찾으려면 GitHub에서 azuredeploy.json 파일을 선택하고 원시 단추를 클릭합니다.

> 참고 - 이 명령을 실행하는 경우 셸에서 배포 매개 변수 값을 묻는 메시지가 표시됩니다.
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### 템플릿 매개 변수 제공
 
이 버전의 명령에서는 사용자가 대화형으로 매개 변수를 정의해야 합니다. json 형식 문자열로 매개 변수를 제공하려는 경우 `-p` 스위치로 작업을 수행할 수 있습니다. 예:
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

또는 `-e` 스위치를 사용하여 json 형식 매개 변수를 제공할 수 있습니다.

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
`azuredeploy.parameters.json`으로 명명된 예제 매개 변수 파일은 GitHub에서 ACS 템플릿으로 찾을 수 있습니다.
 
## PowerShell을 사용하여 서비스 만들기

ACS 클러스터는 PowerShell로 배포할 수도 있습니다. 이 문서는 버전 1.0 이상 Azure [PowerShell 모듈](https://azure.microsoft.com/blog/azps-1-0/)을 기반으로 합니다.

Mesos 또는 Docker Swarm 클러스터를 배포하려면 다음 템플릿 중 하나를 선택합니다. **참고** - 이러한 두 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/acs-swarm)

Azure 구독에서 클러스터를 만들기 전에 PowerShell 세션이 Azure에 로그인했는지 확인합니다. `Get-AzureRMSubscription` 명령으로 이 작업을 완료할 수 있습니다.

```powershell
Get-AzureRmSubscription
```

Azure에 로그인해야 할 경우는 `Login-AzureRMAccount` 명령을 사용합니다.

```powershell
Login-AzureRmAccount
```
 
새 리소스 그룹에 배포하는 경우 먼저 리소스 그룹을 만들어야 합니다. 새 리소스 그룹을 만들려면 `New-AzureRmResourceGroup` 명령을 사용하고 리소스 그룹 및 대상 하위 지역을 지정합니다.

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

리소스 그룹이 있으면 다음 명령을 사용하여 클러스터를 만들 수 있습니다. 원하는 템플릿의 URI는 `-TemplateUri` 매개 변수에 대해 지정됩니다. 이 명령을 실행하는 경우 PowerShell에서 배포 매개 변수 값을 묻는 메시지가 표시됩니다.

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### Provide template parameters
 
If you are familiar with PowerShell, you know that you can cycle through the available parameters for a cmdlet by typing a minus sign (-) and then pressing the TAB key. This same functionality also works with parameters that you define in your template. As soon as you type the template name, the cmdlet fetches the template, parses the parameters, and adds the template parameters to the command dynamically. This makes it very easy to specify the template parameter values. And, if you forget a required parameter value, PowerShell prompts you for the value.
 
Below is the full command with parameters included. You can provide your own values for the names of the resources.

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## 다음 단계
 
이제 클러스터가 작동하며 연결 및 관리 정보가 나와 있는 다음 문서를 방문합니다.
 
- [ACS 클러스터를 사용하여 연결](./container-service-connect.md)
- [ACS 및 Mesos 작업](./container-service-mesos-marathon-rest.md)

 

<!-----HONumber=AcomDC_0218_2016-->