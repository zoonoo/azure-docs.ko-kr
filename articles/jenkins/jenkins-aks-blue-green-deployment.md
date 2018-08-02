---
title: Jenkins와 파란색/녹색 배포 패턴을 사용하여 AKS(Azure Kubernetes Service) 배포
description: Jenkins와 파란색/녹색 배포 패턴을 사용하여 AKS(Azure Kubernetes Service)를 배포하는 방법 알아보기
services: app-service\web
documentationcenter: ''
author: tomarcher
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 07/23/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 472622f78303593b7a4d5d5136aa47f34a1f44b1
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227930"
---
# <a name="deploy-to-azure-kubernetes-service-aks-using-jenkins-and-bluegreen-deployment-pattern"></a>Jenkins와 파란색/녹색 배포 패턴을 사용하여 AKS(Azure Kubernetes Service) 배포

AKS(Azure Kubernetes Service)는 호스팅된 Kubernetes 환경을 관리하므로 컨테이너 오케스트레이션에 대한 전문 지식 없이 컨테이너화된 응용 프로그램을 빠르고 쉽게 배포하고 관리할 수 있습니다. AKS는 또한 응용 프로그램을 오프라인으로 변경하지 않고 주문형 리소스를 프로비전하고, 업그레이드하고, 크기 조정하여 진행 중인 작업 및 유지 관리 부담을 제거합니다. AKS에 대한 자세한 내용은 [AKS 설명서](/azure/aks/)를 참조하세요.

파란색/녹색 배포는 새로운(녹색) 버전이 배포되는 동안 기존(파란색) 버전을 유지하도록 하는 DevOps CD(지속적인 업데이트) 패턴입니다. 일반적으로 이 패턴은 부하 분산을 사용하여 증가하는 트래픽 양을 녹색 배포로 전달합니다. 모니터링을 통해 인시던트가 발견되면 트래픽은 계속 실행 중인 파란색 배포로 경로 전환될 수 있습니다. 지속적인 업데이트에 대한 자세한 내용은 [지속적인 업데이트란](/azure/devops/what-is-continuous-delivery) 문서를 참조하세요.

이 자습서에서는 Jenkins와 파란색/녹색 배포 패턴을 사용하여 AKS에 배포하는 방법을 학습하면서 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * 파란색/녹색 배포 패턴에 대해 알아보기
> * 관리되는 Kubernetes 클러스터 만들기
> * Kubernetes 클러스터를 구성하는 샘플 스크립트 실행
> * Kubernetes 클러스터를 수동으로 구성
> * Jenkins 작업 만들기 및 실행

## <a name="prerequisites"></a>필수 조건
- [GitHub 계정](https://github.com): 샘플 리포지토리를 복제하려면 GitHub 계정이 필요합니다.
- [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest): Azure CLI 2.0은 Kubernetes 클러스터를 만드는 데 사용됩니다.
- [Chocolatey](https://chocolatey.org): kubectl을 설치하는 데 사용된 패키지 관리자입니다.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): Kubernetes 클러스터에 대한 명령을 실행하기 위한 명령줄 인터페이스입니다.
- [jq](https://stedolan.github.io/jq/download/): 간단한 명령줄 JSON 프로세서입니다.

## <a name="clone-the-sample-app-from-github"></a>GitHub에서 샘플 앱 복제

Jenkins와 파란색/녹색 배포 패턴을 사용하여 AKS에 배포하는 방법을 보여주는 샘플 앱은 GitHub의 Microsoft 리포지토리에 있습니다. 이 섹션에서는 GitHub에서 해당 리포지토리의 포크를 만들고 앱을 로컬 시스템에 복제합니다.

1. [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git) 샘플 앱에 대한 GitHub 리포지토리로 이동합니다.

    ![Microsoft GitHub 리포지토리의 샘플 앱.](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. 페이지 오른쪽 상단의 **포크**를 선택하여 리포지토리를 포크하고 안내에 따라 GitHub 계정에서 리포지토리를 포크합니다.

    ![GitHub 계정으로 샘플 앱을 포크합니다.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. 일단 리포지토리를 포크하면 계정 이름이 사용자의 계정 이름으로 변경되고 리포지토리가 포크된 원본 위치가 표시됩니다(Microsoft).

    ![다른 GitHub 계정으로 포크된 후 샘플 앱.](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. **복제 또는 다운로드**를 선택합니다.

    ![GitHub를 사용하면 리포지토리를 신속하게 복제 또는 다운로드할 수 있습니다.](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. **HTTPS를 사용하여 복제** 창에서 복사 아이콘을 선택합니다.

    ![복제 URL을 클립보드에 복사합니다.](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. 터미널 또는 Bash 창을 엽니다.

1. 리포지토리의 로컬 복사본(복제본)을 저장할 위치로 디렉터리를 변경합니다.

1. `git clone` 명령을 사용하여 이전에 복사한 URL을 복제합니다.

    !["git clone" 및 복제 URL을 입력하여 리포지토리의 복제를 만듭니다.](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. &lt;Enter> 키를 선택하여 복제 프로세스를 시작합니다.

    !["git clone" 명령을 사용하면 테스트할 수 있는 리포지토리의 개인 복사본이 만들어집니다.](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. 디렉터리를 앱 소스의 복제본을 포함하는 새로 만든 디렉터리로 변경합니다.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>관리되는 Kubernetes 클러스터 만들기 및 구성

이 섹션에서는 다음 단계를 수행합니다.

- Azure CLI 2.0을 사용하여 관리되는 Kubernetes 클러스터를 만듭니다.
- 설치 스크립트를 사용하거나 수동으로 클러스터를 설정하는 방법에 대해 알아봅니다.
- Azure Container Registry를 만듭니다.

> [!NOTE]   
> AKS는 현재 미리 보기로 제공되고 있습니다. Azure 구독의 미리 보기를 사용하도록 설정하는 방법에 대한 자세한 내용은 [빠른 시작: AKS(Azure Kubernetes Service) 클러스터 배포](/azure/aks/kubernetes-walkthrough#enabling-aks-preview-for-your-azure-subscription)를 참조하세요.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Azure CLI 2.0을 사용하여 관리되는 Kubernetes 클러스터 만들기
[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)으로 관리되는 Kubernetes 클러스터를 만들려면 Azure CLI 버전 2.0.25 이상을 사용 중이어야 합니다.

1. Azure 계정에 로그인합니다. 다음 `az login` 명령을 입력하면 로그인을 완료하는 방법을 설명하는 지침이 제공됩니다. 
    
    ```bash
    az login
    ```

1. 이전 단계에서 `az login` 명령을 실행하면 모든 Azure 구독 목록이 구독 ID와 함께 표시됩니다. 이 단계에서는 기본 Azure 구독을 설정합니다. &lt;your-subscription-id> 자리 표시자를 원하는 Azure 구독 ID로 바꿉니다. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. 리소스 그룹을 만듭니다. &lt;your-resource-group-name> 자리 표시자를 새 리소스 그룹의 이름으로 바꾸고 &lt;your-location> 자리 표시자를 위치로 바꿉니다. `az account list-locations` 명령은 모든 Azure 위치를 표시합니다. AKS 미리 보기 상태에서는 일부 위치만 사용할 수 있습니다. 현재 유효하지 않은 위치를 입력하면 오류 메시지에 사용 가능한 위치가 나열됩니다.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Kubernetes 클러스터를 만듭니다. &lt;your-resource-group-name>을 이전 단계에서 만든 리소스 그룹의 이름으로 바꾸고 &lt;you-kubernetes-cluster-name>을 새 클러스터의 이름으로 바꿉니다. 이 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Kubernetes 클러스터 설정

AKS에서 파란색/녹색 배포를 설정하는 작업은 이전에 복제된 샘플에 있는 설정 스크립트를 사용하거나 수동으로 수행할 수 있습니다. 이 섹션에서는 두 가지 다 수행하는 방법을 보여 줍니다.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>설정 스크립트 샘플을 통해 Kubernetes 클러스터 설정
1. **deploy/aks/setup/setup.sh** 파일을 편집하고 다음 자리 표시자를 사용자 환경에 맞는 값으로 바꿉니다. 

    - **&lt;your-resource-group-name>**
    - **&lt;your-kubernetes-cluster-name>**
    - **&lt;your-location>**
    - **&lt;your-dns-name-suffix>**

    ![Setup.sh 스크립트에는 사용자 환경에 맞게 수정할 수 있는 다양한 자리 표시자가 있습니다.](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. 설정 스크립트를 실행합니다.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Kubernetes 클러스터를 수동으로 설정 
1. Kubernetes 구성을 프로필 폴더에 다운로드합니다.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. 디렉터리를 **deploy/aks/setup** 디렉터리로 변경합니다. 

1. 다음 **kubectl** 명령을 실행하여 공용 엔드포인트와 두 개의 테스트 엔드포인트에 대한 서비스를 설정합니다.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. 공용 및 테스트 엔드포인트에 대한 DNS 이름을 업데이트합니다. Kubernetes 클러스터가 만들어지면 [추가 리소스 그룹](https://github.com/Azure/AKS/issues/3)이 **MC_&lt;your-resource-group-name>_&lt;your-kubernetes-cluster-name>_&lt;your-location>** 의 이름 지정 패턴을 사용하여 만들어집니다.

    리소스 그룹에서 공용 IP 찾기

    ![리소스 그룹에서 공용 IP](./media/jenkins-aks-blue-green-deployment/publicip.png)

    각 서비스에 대해 다음 명령을 실행하여 외부 IP 주소를 찾습니다.
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    다음 명령을 사용하여 해당 IP 주소에 대한 DNS 이름을 업데이트합니다.

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    `todoapp-test-blue` 및 `todoapp-test-green`에 대한 호출을 반복합니다.

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    DNS 이름은 각 구독에 대해 고유해야 합니다. 고유성을 보장하기 위해 `<your-dns-name-suffix>`를 사용할 수 있습니다.

### <a name="create-azure-container-registry"></a>Azure Container Registry 만들기

1. `az acr create` 명령을 실행하여 Azure Container Registry를 만듭니다. Azure Container Registry를 생성한 후에는 다음 섹션에서 Docker 레지스트리 URL로 `login server`를 사용합니다.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. `az acr credential` 명령을 실행하면 Azure Container Registry 자격 증명이 표시됩니다. Docker 레지스트리 사용자 이름과 암호는 다음 섹션에서 사용되므로 적어두세요.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Jenkins 서버 준비

이 섹션에서는 테스트하기 좋은 Jenkins 서버를 준비하여 빌드를 실행하는 방법을 살펴봅니다. 그러나 [마스터에서 빌드의 보안 관련 문제](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master) 문서에서 설명한 것처럼 [Azure VM 에이전트](https://plugins.jenkins.io/azure-vm-agents) 또는 [Azure 컨테이너 에이전트](https://plugins.jenkins.io/azure-container-agents)를 사용하여 빌드를 실행할 Azure에서 에이전트를 스핀업하는 것이 좋습니다. 

1. [Azure에 Jenkins 마스터](https://aka.ms/jenkins-on-azure)를 배포합니다.

1. SSH 통해 서버에 연결하고 빌드를 실행할 서버에서 빌드 도구를 설치합니다.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Docker를 설치](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce)합니다. `jenkins` 사용자에게 `docker` 명령을 실행할 권한이 있는지 확인합니다.

1. [kubectl을 설치](https://kubernetes.io/docs/tasks/tools/install-kubectl/)합니다.

1. [jq를 다운로드](https://stedolan.github.io/jq/download/)합니다.

1. 다음 명령을 사용하여 jq를 설치합니다.

   ```bash
   sudo apt-get install jq
   ```
   
1. Jenkins 대시보드 내에서 다음 단계를 수행하여 Jenkins에서 플러그 인을 설치합니다.

    1. **Jenkins 관리 > 플러그 인 관리 > 사용 가능**을 차례로 선택합니다.
    1. Azure Container Service 플러그 인을 검색하여 설치합니다.

1. Azure에서 리소스를 관리하는 데 사용할 자격 증명을 추가해야 합니다. 플러그 인이 아직 없는 경우 **Azure 자격 증명** 플러그 인을 설치합니다.

1. Azure 서비스 주체 자격 증명을 **Microsoft Azure 서비스 주체** 형식/종류로 추가합니다.

1. Azure Docker Registry 사용자 이름과 암호(**Azure Container Registry 만들기** 섹션에서 가져옴)를 **사용자 이름 및 암호** 형식/종류로 추가합니다.

## <a name="edit-the-jenkinsfile"></a>Jenkinsfile 편집

1. 사용자 고유의 리포지토리에서 `/deploy/aks/`로 이동하고 `Jenkinsfile`을 엽니다.

2. 파일을 다음과 같이 업데이트합니다.

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    그리고 ACR 자격 증명 ID를 업데이트합니다.
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>작업 만들기
1. **파이프라인** 형식에 새 작업을 추가합니다.

1. **파이프라인 > 정의 > SCM의 파이프라인 스크립트**를 선택합니다.

1. &lt;your-forked-repo>로 SCM 리포지토리 URL을 입력합니다.

1. 스크립트 경로를 `deploy/aks/Jenkinsfile`로 입력합니다.

## <a name="run-the-job"></a>작업 실행

1. 로컬 환경에서 프로젝트를 성공적으로 실행할 수 있는지 확인합니다. [로컬 컴퓨터에서 프로젝트 실행](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it)

1. Jenkins 작업을 실행합니다. Jenkins 작업을 처음 실행할 때 Jenkins는 기본적으로 비활성 환경인 파란색 환경에 할 일 앱을 배포합니다. 

1. 작업이 실행되었는지 확인하려면 URL로 이동합니다.
    - 공용 엔드포인트: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - 파란색 엔드포인트 - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - 녹색 엔드포인트 - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

공용 및 파란색 테스트 엔드포인트는 녹색 엔드포인트가 기본 tomcat 이미지를 표시하는 동안 동일한 업데이트를 포함합니다. 

빌드를 한 번을 초과하여 실행하는 경우 파란색 및 녹색 배포가 순환 실행됩니다. 즉, 현재 환경이 파란색인 경우 작업은 녹색 환경에 배포/테스트된 다음, 테스트에서 모든 항목이 정상이라면 응용 프로그램 공용 엔드포인트를 업데이트하여 트래픽을 녹색 환경으로 라우팅합니다.

## <a name="additional-information"></a>추가 정보

가동 중단 없는 배포에 대한 자세한 내용은 [빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment)을 참조하세요. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 이 자습서에서 만든 Azure 리소스를 삭제합니다.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>문제 해결

Jenkins 플러그 인에서 버그가 발생하면 [Jenkins JIRA](https://issues.jenkins-ci.org/)에서 특정 구성 요소에 대한 문제를 제출해 주세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Jenkins와 파란색/녹색 배포 패턴을 사용하여 AKS(Azure Kubernetes Service)를 배포하는 방법을 알아보았습니다. Azure Jenkins 공급자에 대한 자세한 내용은 Azure 사이트에서 Jenkins를 참조하세요.

> [!div class="nextstepaction"]
> [Azure의 Jenkins](/azure/jenkins/)