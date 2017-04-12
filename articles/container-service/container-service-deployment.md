---
title: "Azure에서 Docker 컨테이너 클러스터 배포 | Microsoft Docs"
description: "Azure Portal 또는 Resource Manager 템플릿을 사용하여 Azure Container Service에 Kubernetes, DC/OS 또는 Docker Swarm 솔루션을 배포합니다."
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure, DCOS, Swarm, Kubernetes, Azure Container Service, ACS"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2e8ff5304d4ccc63f92567b22c0508e3fc7b9bcf
ms.lasthandoff: 04/03/2017

---
# <a name="deploy-a-docker-container-hosting-solution-using-the-azure-portal"></a>Azure Portal을 사용하여 Docker 컨테이너 호스팅 솔루션 배포



Azure 컨테이너 서비스는 인기 있는 오픈 소스 컨테이너 클러스터링 및 오케스트레이션 솔루션의 신속한 배포를 제공합니다. 이 문서에서는 Azure portal 또는 Azure Resource Manager 빠른 시작 템플릿을 사용하여 Azure Container Service 클러스터를 배포하는 방법을 안내합니다. 

[Azure CLI 2.0](container-service-create-acs-cluster-cli.md) 또는 Azure Container Service API를 사용하여 Azure Container Service 클러스터를 배포할 수도 있습니다.

배경 지식은 [Azure Container Service 소개](container-service-intro.md)를 참조하세요.


## <a name="prerequisites"></a>필수 조건

* **Azure 구독**: 없는 경우 지금 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)에 등록하세요. 

* **SSH RSA 공개 키**: 포털 또는 Azure 빠른 시작 템플릿 중 하나를 통해 배포하는 경우 Azure Container Service 가상 컴퓨터에 대한 인증을 위해 공개 키를 제공해야 합니다. SSH(보안 셸) RSA 키를 만들려면 [OS X 및 Linux](../virtual-machines/linux/mac-create-ssh-keys.md) 또는 [Windows](../virtual-machines/linux/ssh-from-windows.md) 지침을 참조하세요. 

* **서비스 주체 클라이언트 ID 및 비밀**(Kubernetes에만 해당): Azure Active Directory 서비스 주체 만들기에 대한 정보 및 지침은 [Kubernetes 클러스터에 대한 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Azure Portal을 사용하여 클러스터 만들기
1. Azure Portal에 로그인하여 **새로 만들기**를 선택하고 Azure Marketplace에서 **Azure Container Service**를 검색합니다.

    ![Marketplace의 Azure Container Service](media/container-service-deployment/acs-portal1.png)  <br />

2. **Azure Container Service**를 클릭하고 **만들기**를 클릭합니다.

3. **기본** 블레이드에서 다음 정보를 입력합니다.

    * **Orchestrator**: 클러스터에 배포할 컨테이너 또는 Orchestrator 중 하나를 선택합니다.
        * **DC/OS**: DC/OS 클러스터를 배포합니다.
        * **Swarm**: Docker Swarm 클러스터를 배포합니다.
        * **Kubernetes**: Kubernetes 클러스터를 배포합니다.
    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: 배포를 위해 새 리소스 그룹의 이름을 입력합니다.
    * **위치**: Azure 컨테이너 서비스 배포를 위한 Azure 지역을 선택합니다. 가용성은 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.
    
    ![기본 설정](media/container-service-deployment/acs-portal3.png)  <br />
    
    진행할 준비가 되면 **확인** 을 클릭합니다.

4. **마스터 구성** 블레이드에서 Linux 마스터 노드 또는 클러스터의 노드(일부 설정은 각 orchestrator만 해당됨)에 대한 다음 설정을 입력합니다.

    * **마스터 DNS 이름**: 마스터에 대한 고유의 FQDN(정규화된 도메인 이름)을 만드는 데 사용되는 접두사입니다. 마스터 FQDN은 *prefix*mgmt.*location*.cloudapp.azure.com 형식입니다.
    * **사용자 이름**: 클러스터의 각 Linux 가상 컴퓨터에 있는 계정의 사용자 이름입니다.
    * **SSH RSA 공개 키**: Linux 가상 컴퓨터에 대한 인증에 사용할 공개 키를 추가합니다. 키에 줄 바꿈이 없고 `ssh-rsa` 접두사를 포함해야 합니다. `username@domain` 접미사는 선택 사항입니다. 키는 **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm**와 같은 형태여야 합니다. 
    * **서비스 주체**: Kubernetes orchestrator를 선택한 경우 Azure Active Directory **서비스 주체 클라이언트 ID**(appId라고도 함) 및 **서비스 주체 클라이언트 비밀**(암호)을 입력합니다. 자세한 내용은 [Kubernetes 클러스터의 서비스 주체 정보](container-service-kubernetes-service-principal.md)를 참조하세요.
    * **마스터 수**: 클러스터의 마스터 수입니다.
    * **VM 진단**: 일부 orchestrator의 경우 마스터에서 VM 진단을 활성화하도록 선택할 수 있습니다.

    ![마스터 구성](media/container-service-deployment/acs-portal4.png)  <br />

    진행할 준비가 되면 **확인** 을 클릭합니다.

5. **에이전트 구성** 블레이드에서 다음 정보를 입력합니다.

    * **에이전트 수**: Docker Swarm 및 Kubernetes의 경우, 이 값은 에이전트 크기 집합의 초기 에이전트 수입니다. DC/OS의 경우, 사설 규모 집합의 초기 에이전트 수입니다. 또한, 사전에 지정된 수의 에이전트를 포함하는 DC/OS에 대한 공개 규모 집합이 생성됩니다. 이 공용 크기 집합의 에이전트 수는 클러스터의 마스터 수에 의해 결정됩니다. 마스터 하나에 대해 공용 에이전트 하나이고, 마스터 셋 또는 다섯에 대해 공용 에이전트는 둘 입니다.
    * **에이전트 가상 컴퓨터 크기**: 에이전트 가상 컴퓨터의 크기입니다.
    * **운영 체제**: 이 설정은 현재 Kubernetes orchestrator를 선택한 경우에만 사용할 수 있습니다. 에이전트에서 실행하려면 Linux 배포 또는 Windows Server 운영 체제를 선택하세요. 이 설정은 클러스터가 Linux 또는 Windows 컨테이너 앱을 실행할 수 있을지 여부를 결정합니다. 

        > [!NOTE]
        > Windows 컨테이너 지원은 Kubernetes 클러스터에 대해 미리 보기 상태입니다. DC/OS 및 Swarm 클러스터의 경우 현재 Linux 에이전트만 Azure Container Service에서 지원됩니다.

    * **에이전트 자격 증명**: Windows 운영 체제를 선택한 경우 에이전트 VM에 대한 관리자의 **사용자 이름** 및 **암호**를 입력합니다. 

    ![에이전트 구성](media/container-service-deployment/acs-portal5.png)  <br />

    진행할 준비가 되면 **확인** 을 클릭합니다.

6. 서비스 유효성 검사가 완료되면 **확인**을 클릭합니다.

    ![유효성 검사](media/container-service-deployment/acs-portal6.png)  <br />

7. 약관을 검토합니다. 배포 프로세스를 시작하려면 **만들기**를 클릭합니다.

    Azure 포털에 배포를 고정하도록 선택한 경우 배포 상태를 볼 수 있습니다.

    ![배포 상태](media/container-service-deployment/acs-portal8.png)  <br />

배포를 완료하려면 몇 분이 걸립니다. 그런 다음 Azure Container Service 클러스터를 사용할 준비가 됩니다.


## <a name="create-a-cluster-by-using-a-quickstart-template"></a>빠른 시작 템플릿을 사용하여 클러스터 만들기
Azure Container Service에서 클러스터를 배포하는 데 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 추가 또는 고급 Azure 구성을 포함하도록 제공된 빠른 시작 템플릿을 수정할 수 있습니다. Azure 빠른 시작 템플릿을 사용하여 Azure Container Service 클러스터를 만들려면 Azure 구독이 필요합니다. 없는 경우 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)에 등록하세요. 

템플릿 및 Azure CLI 2.0을 사용하여 클러스터를 배포하려면 다음 단계를 수행합니다([설치 및 설정 지침](/cli/azure/install-az-cli2.md) 참조).

> [!NOTE] 
> Windows 시스템의 경우 Azure PowerShell을 사용하여 템플릿을 배포하는 데 비슷한 단계를 사용할 수 있습니다. 이 섹션의 뒷부분에 나오는 단계를 참조하세요. [포털](../azure-resource-manager/resource-group-template-deploy-portal.md) 또는 다른 방법을 통해 템플릿을 배포할 수도 있습니다.

1. DC/OS, Docker Swarm 또는 Kubernetes 클러스터를 배포하려면 GitHub에서 사용 가능한 빠른 시작 템플릿 중 하나를 선택합니다. 다음은 일부 목록입니다. DC/OS 및 Swarm 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.

    * [DC/OS 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Azure 계정에 로그인하고(`az login`) Azure CLI가 Azure 구독에 연결되어 있는지 확인합니다. 다음 명령을 사용하여 기본 구독을 확인할 수 있습니다.

    ```azurecli
    az account show
    ```
    
    둘 이상의 구독이 있고 다른 기본 구독을 설정해야 하는 경우 `az account set --subscription`을 실행하고 구독 ID 또는 이름을 지정합니다.

3. 모범 사례로 배포에 새 리소스 그룹을 사용합니다. 리소스 그룹을 만들려면 `az group create` 명령을 사용하고 리소스 그룹 이름 및 위치를 지정합니다. 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. 필수 템플릿 매개 변수가 포함된 JSON 파일을 만듭니다. GitHub에서 Azure Container Service 템플릿 `azuredeploy.json`과 함께 제공되는 `azuredeploy.parameters.json`이라는 매개 변수 파일을 다운로드합니다. 클러스터에 대한 필수 매개 변수 값을 입력합니다. 

    예를 들어 [DC/OS 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)을 사용하려면 `dnsNamePrefix` 및 `sshRSAPublicKey`에 대한 매개 변수 값을 제공합니다. `azuredeploy.json`의 설명 및 다른 매개 변수에 대한 옵션을 참조하세요.  
 

5. 다음 명령을 사용하여 배포 매개 변수 파일을 전달하여 컨테이너 서비스 클러스터를 만듭니다. 다음과 같습니다.

    * **RESOURCE_GROUP**은 이전 단계에서 만든 리소스 그룹의 이름입니다.
    * **DEPLOYMENT_NAME**(선택 사항)은 배포에 제공한 이름입니다.
    * **TEMPLATE_URI**는 배포 파일 `azuredeploy.json`의 위치입니다. 이 URI는 GitHub UI에 대한 포인터가 아닌 원시 파일이어야 합니다. 이 URI를 찾으려면 GitHub에서 `azuredeploy.json` 파일을 선택하고 **원시** 단추를 클릭합니다.  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    또한 명령줄에서 JSON 형식 문자열로 매개 변수를 제공할 수도 있습니다. 다음과 유사한 명령을 사용합니다.

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > 배포를 완료하려면 몇 분이 걸립니다.
    > 

### <a name="equivalent-powershell-commands"></a>동일한 PowerShell 명령
PowerShell 사용하여 Azure Container Service 클러스터 템플릿을 배포할 수도 있습니다. 이 문서는 [Azure PowerShell 모듈](https://azure.microsoft.com/blog/azps-1-0/)버전 1.0을 기반으로 합니다.

1. DC/OS, Docker Swarm 또는 Kubernetes 클러스터를 배포하려면 GitHub에서 사용 가능한 빠른 시작 템플릿 중 하나를 선택합니다. 다음은 일부 목록입니다. DC/OS 및 Swarm 템플릿은 기본 Orchestrator 선택을 제외하고 동일합니다.

    * [DC/OS 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Azure 구독에서 클러스터를 만들기 전에 PowerShell 세션이 Azure에 로그인했는지 확인합니다. 이렇게 하려면 `Get-AzureRMSubscription` 명령을 사용합니다.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Azure에 로그인해야 할 경우는 `Login-AzureRMAccount` 명령을 사용합니다.

    ```powershell
    Login-AzureRmAccount
    ```

4. 모범 사례로 배포에 새 리소스 그룹을 사용합니다. 리소스 그룹을 만들려면 `New-AzureRmResourceGroup` 명령을 사용하고 리소스 그룹 이름 및 대상 지역을 지정합니다.

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. 리소스 그룹을 만든 후에는 다음 명령을 사용하여 클러스터를 만들 수 있습니다. 원하는 템플릿의 URI는 `-TemplateUri` 매개 변수에 지정됩니다. 이 명령을 실행하면 PowerShell에서 배포 매개 변수 값을 묻는 메시지가 표시됩니다.

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>템플릿 매개 변수 제공
PowerShell에 익숙한 경우 빼기 기호(-)를 입력하고 TAB 키를 눌러 Cmdlet에 사용할 수 있는 매개 변수를 순환시켜 볼 수 있습니다. 이 기능은 템플릿에 정의하는 매개 변수에 대해서도 작동합니다. 템플릿 이름을 입력하자마자 cmdlet이 템플릿을 인출하고 매개 변수의 구문을 분석한 다음 템플릿 매개 변수를 명령에 동적으로 추가합니다. 따라서 템플릿 매개 변수 값을 쉽게 지정할 수 있습니다. 필수 매개 변수 값을 잊은 경우 PowerShell이 값을 묻는 메시지를 표시합니다.

다음은 매개 변수가 포함된 전체 명령입니다. 리소스의 이름에 대한 고유한 값을 제공하세요.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>다음 단계
이제 클러스터가 작동하기 시작했으니 연결 및 관리 정보는 다음 문서를 참조하세요.

* [Azure 컨테이너 서비스 클러스터에 연결](container-service-connect.md)
* [Azure 컨테이너 서비스 및 DC/OS로 작업](container-service-mesos-marathon-rest.md)
* [Azure 컨테이너 서비스 및 Docker Swarm으로 작업](container-service-docker-swarm.md)
* [Azure Container Service 및 Kubernetes로 작업](container-service-kubernetes-walkthrough.md)

