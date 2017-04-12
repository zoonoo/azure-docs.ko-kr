---
title: "Azure Container Service에서 Kubernetes와 Jenkins CI/CD | Microsoft Docs"
description: "Jenkins로 CI/CD 프로세스를 자동화하여 Azure Container Service의 Kubernetes에서 컨테이너화된 앱을 배포 및 업그레이드하는 방법입니다."
services: container-service
documentationcenter: 
author: chzbrgr71
manager: johny
editor: 
tags: acs, azure-container-service, jenkins
keywords: "Docker, 컨테이너, Kubernetes, Azure, Jenkins"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: briar
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 3d206ebb6deeaa40f8e792ec12304c99c0abe684
ms.lasthandoff: 03/29/2017


---

# <a name="jenkins-integration-with-azure-container-service-and-kubernetes"></a>Azure Container Service 및 Kubernetes로 Jenkins 통합 
이 자습서에서는 Jenkins 플랫폼을 사용하여 다중 컨테이너 응용 프로그램을 Azure Container Service Kubernetes로 연속 통합을 설정하는 과정을 안내합니다. 이 워크플로는 Docker Hub에서 컨테이너 이미지를 업데이트하고 배포 롤아웃을 사용하여 Kubernetes 포드를 업그레이드합니다. 

## <a name="high-level-process"></a>상위 수준 프로세스
이 문서에서 자세히 설명하는 기본 단계는 다음과 같습니다. 
- Container Service에서 Kubernetes 클러스터 설치
- Jenkins 설정 및 Container Service에 대한 액세스 구성
- Jenkins 워크플로 만들기
- CI/CD 프로세스 종단 간 테스트

## <a name="install-a-kubernetes-cluster"></a>Kubernetes 클러스터 설치
    
다음 단계를 사용하여 Azure Container Service에서 Kubernetes 클러스터를 배포합니다. 전체 설명서는 [여기](container-service-kubernetes-walkthrough.md)에 있습니다.

### <a name="step-1-create-a-resource-group"></a>1단계: 리소스 그룹 만들기
```azurecli
RESOURCE_GROUP=my-resource-group
LOCATION=westus

az group create --name=$RESOURCE_GROUP --location=$LOCATION
```

### <a name="step-2-deploy-the-cluster"></a>2단계: 클러스터 배포
> [!NOTE]
> 다음 단계를 수행하려면 로컬 SSH 공개 키가 ~/.ssh 폴더에 저장되어 있어야 합니다.
>

```azurecli
RESOURCE_GROUP=my-resource-group
DNS_PREFIX=some-unique-value
CLUSTER_NAME=any-acs-cluster-name

az acs create \
--orchestrator-type=kubernetes \
--resource-group $RESOURCE_GROUP \ 
--name=$CLUSTER_NAME \
--dns-prefix=$DNS_PREFIX \ 
--ssh-key-value ~/.ssh/id_rsa.pub \
--admin-username=azureuser \
--master-count=1 \
--agent-count=5 \
--agent-vm-size=Standard_D1_v2
```

## <a name="set-up-jenkins-and-configure-access-to-container-service"></a>Jenkins 설정 및 Container Service에 대한 액세스 구성

### <a name="step-1-install-jenkins"></a>1단계: Jenkins 설치
1. Ubuntu 16.04 LTS로 Azure VM을 만듭니다 
2. 다음 [지침](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu)을 통해 Jenkins를 설치합니다.
3. 자세한 자습서는 [howtoforge.com](https://www.howtoforge.com/tutorial/how-to-install-jenkins-with-apache-on-ubuntu-16-04)에 있습니다.
4. 포트 8080을 허용하도록 Azure 네트워크 보안 그룹을 업데이트한 후 포트 8080에서 공용 IP를 검색하여 브라우저에서 Jenkins를 관리합니다.
5. 초기 Jenkins 관리자 암호는 /var/lib/jenkins/secrets/initialAdminPassword에 저장됩니다.
6. 이러한 [지침](https://docs.docker.com/cs-engine/1.13/#install-on-ubuntu-1404-lts-or-1604-lts)을 통해 Jenkins 컴퓨터에 Docker를 설치합니다. 그러면 Jenkins 작업에 Docker 명령을 실행할 수 있습니다.
7. Jenkins에서 끝점에 액세스할 수 있도록 Docker 권한을 구성합니다.

    ```bash
    sudo chmod 777 /run/docker.sock
    ```
8. Jenkins에 `kubectl` CLI를 설치합니다. 자세한 내용은 [kubectl 설치 및 설정](https://kubernetes.io/docs/tasks/kubectl/install/)에 나와 있습니다.

    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl

    chmod +x ./kubectl

    sudo mv ./kubectl /usr/local/bin/kubectl
    ```

### <a name="step-2-set-up-access-to-the-kubernetes-cluster"></a>2단계: Kubernetes 클러스터에 대한 액세스 설정

> [!NOTE]
> 다음 단계를 수행하는 여러 가지 방법이 있습니다. 가장 쉽게 여겨지는 방법을 사용하면 됩니다.
>

1. `kubectl` 구성 파일을 Jenkins 컴퓨터에 복사합니다.

    ```bash
    export KUBE_MASTER=<your_cluster_master_fqdn>
        
    sudo scp -3 -i ~/.ssh/id_rsa azureuser@$KUBE_MASTER:.kube/config user@<your_jenkins_server>:~/.kube/config
        
    sudo ssh user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config

    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo chmod 777 /home/user/.kube/config
        
    sudo ssh -i ~/.ssh/id_rsa user@<your_jenkins_server> sudo cp /home/user/.kube/config /var/lib/jenkins/config
    ```
        
2. Jenkins에서 Kubernetes 클러스터에 액세스할 수 있는지 확인합니다.
    

## <a name="create-a-jenkins-workflow"></a>Jenkins 워크플로 만들기

### <a name="prerequisites"></a>필수 조건

- 코드 리포지토리용 GitHub 계정.
- 이미지를 저장 및 업데이트할 Docker Hub 계정
- 다시 작성하여 업데이트할 수 있는 컨테이너화된 응용 프로그램. Golang으로 작성된 다음 샘플 컨테이너를 사용할 수 있습니다. https://github.com/chzbrgr71/go-web 

> [!NOTE]
> 사용자 고유의 GitHub 계정에서 다음 단계를 수행해야 합니다. 위의 리포지토리를 자유롭게 복제할 수 있지만 사용자 고유의 계정을 사용하여 웹후크 및 Jenkins 액세스를 구성해야 합니다.
>

### <a name="step-1-deploy-initial-v1-of-application"></a>1단계: 응용 프로그램의 초기 v1 배포
1. 다음 명령으로 개발자 컴퓨터에서 앱을 빌드합니다. `myrepo`를 사용자 고유의 값으로 바꿉니다.
    
    ```bash
    git clone https://github.com/chzbrgr71/go-web.git
    cd go-web
    docker build -t myrepo/go-web .
    ```

2. 이미지를 Docker Hub에 푸시합니다.

    ```bash
    docker login
    docker push myrepo/go-web
    ```

3. Kubernetes 클러스터에 배포합니다.
    
    > [!NOTE] 
    > `go-web.yaml` 파일을 편집하여 컨테이너 이미지 및 리포지토리를 업데이트합니다.
    >
        
    ```bash
    kubectl create -f ./go-web.yaml --record
    ```
### <a name="step-2-configure-jenkins-system"></a>2단계: Jenkins 시스템 구성
1. **Jenkins 관리** > **시스템 구성**을 클릭합니다.
2. **GitHub**에서 **GitHub 서버 추가**를 선택합니다.
3. **API URL**은 기본값으로 둡니다.
4. **자격 증명** 아래에서 **비밀 텍스트**를 사용하여 Jenkins 자격 증명을 추가합니다. GitHub 사용자 계정 설정에 구성된 GitHub 개인 액세스 토큰을 사용하는 것이 좋습니다. 자세한 내용은 [여기](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)에 있습니다.
5. **연결 테스트**를 클릭하여 제대로 구성되었는지 확인합니다.
6. **전역 속성** 아래에서 환경 변수 `DOCKER_HUB`를 추가하고 Docker Hub 암호를 제공합니다. (이 데모에서는 유용하지만 프로덕션 시나리오에는 보다 안전한 방법을 사용해야 합니다.)
7. 저장합니다.

![Jenkins GitHub 액세스](media/container-service-kubernetes-jenkins/jenkins-github-access.png)

### <a name="step-3-create-the-jenkins-workflow"></a>3단계: Jenkins 워크플로 만들기
1. Jenkins 항목을 만듭니다.
2. 이름(예: "go-web")을 제공하고 **Freestyle Project**를 선택합니다. 
3. **GitHub 프로젝트**를 선택하고 GitHub 리포지토리에 대한 URL을 제공합니다.
4. **소스 코드 관리**에서 GitHub 리포지토리 URL 및 자격 증명을 제공합니다. 
5. **셸 실행** 형식의 **빌드 단계**를 추가하고 다음 텍스트를 사용합니다.

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME .
    docker login -u <your-dockerhub-username> -p ${DOCKER_HUB}
    docker push $WEB_IMAGE_NAME
    ```

6. **셸 실행** 형식의 다른 **빌드 단계**를 추가하고 다음 텍스트를 사용합니다.

    ```bash
    WEB_IMAGE_NAME="myrepo/go-web:kube${BUILD_NUMBER}"
    kubectl set image deployment/go-web go-web=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

![Jenkins 빌드 단계](media/container-service-kubernetes-jenkins/jenkins-build-steps.png)
    
7. Jenkins 항목을 저장하고 **Build Now**로 테스트합니다.

### <a name="step-4-connect-github-webhook"></a>4단계: GitHub 웹후크 연결
1. 사용자가 만든 Jenkins 항목에서 **구성**을 클릭합니다.
2. **트리거 빌드** 아래에서 **GITScm 폴링에 대한 GitHub 후크 트리거** 및 **저장**을 선택합니다. 그러면 GitHub 웹후크가 자동으로 구성됩니다.
3. go-web에 대한 GitHub 리포지토리에서 **설정 > 웹후크**를 클릭합니다.
4. Jenkins 웹후크 URL이 성공적으로 추가되었는지 확인합니다. URL은 "github-webhook"로 끝나야 합니다.

![Jenkins 웹후크 구성](media/container-service-kubernetes-jenkins/jenkins-webhook.png)

## <a name="test-the-cicd-process-end-to-end"></a>CI/CD 프로세스 종단 간 테스트

1. 리포지토리에 대한 코드를 업데이트하고 GitHub 리포지토리에 푸시/동기화합니다.
2. Jenkins 콘솔에서 **빌드 기록**을 확인하고 작업이 실행되었는지 확인합니다. 콘솔 출력에서 자세한 내용을 확인합니다.
3. Kubernetes에서 업그레이드된 배포의 세부 사항을 확인합니다.

    ```bash
    kubectl rollout history deployment/go-web
    ```

## <a name="next-steps"></a>다음 단계

- Azure Container Registry를 배포하고 안전한 리포지토리에 이미지를 저장합니다. [Azure Container Registry 문서](https://docs.microsoft.com/azure/container-registry)를 참조하세요.
- Jenkins에서 단계별 배포 및 자동화된 테스트를 포함하는 좀더 복잡한 워크플로를 빌드합니다.
- Jenkins 및 Kubernetes와 CI/CD에 대한 자세한 내용은 [Jenkins 블로그](https://jenkins.io/blog/2015/07/24/integrating-kubernetes-and-jenkins/)를 참조하세요.

