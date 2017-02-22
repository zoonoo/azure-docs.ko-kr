---
title: "Azure Container Service 및 DC/OS를 사용한 CI/CD | Microsoft Docs"
description: "다중 컨테이너 Docker 앱의 빌드 및 DC/OS를 실행하는 Azure Container Service 클러스터로의 배포를 완전하게 자동화하는 방법"
services: container-service
documentationcenter: 
author: spboyer
manager: wpickett
editor: 
tags: acs, azure-container-service
keywords: "Docker, 컨테이너, 마이크로 서비스, Mesos, Azure"
ms.assetid: b16b767a-2eaa-418a-becc-8c032713a1f2
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: johnsta
translationtype: Human Translation
ms.sourcegitcommit: 831f585a9591338c2f404f7ec031d40937731eab
ms.openlocfilehash: dcf4c0b67bc7a6596070cdf44644a6c451e3afc1


---

# <a name="continuous-integration-and-deployment-of-multi-container-docker-applications-to-azure-container-service"></a>Azure Container Service로의 다중 컨테이너 Docker 응용 프로그램의 지속적인 통합 및 배포 
이 자습서에서는 다중 컨테이너 Docker 앱의 빌드 및 DC/OS를 실행하는 Azure Container Service 클러스터로의 배포를 완전하게 자동화하는 방법을 소개합니다. CI/CD(지속적인 통합 및 배포)의 장점은 잘 알려져 있지만 워크플로에 컨테이너를 통합할 때는 새롭게 고려해야 할 사항이 있습니다. 새 Azure 컨테이너 레지스트리 및 CLI 명령을 사용하여 사용자 지정이 가능한 종단 간 흐름을 설정했습니다.

## <a name="get-started"></a>시작
OS X, Windows 또는 Linux에서 이 연습을 실행할 수 있습니다.
- Azure 구독이 필요합니다. 계정이 없는 경우 [계정을 등록](https://azure.microsoft.com/)할 수 있습니다.
- [Azure 명령줄 도구](https://github.com/Azure/azure-cli#microsoft-azure-cli-20---preview)를 설치합니다.

## <a name="what-well-create"></a>만들 항목
설정한 앱 및 해당 배포 흐름의 몇 가지 핵심 측면을 살펴보겠습니다.
* **응용 프로그램은 여러 서비스로 구성되어 있습니다**. 앱에서 서비스를 정의하기 위한 Docker 자산, Dockerfile 및 docker-compose.yml은 각각 별도의 컨테이너에서 실행됩니다. 따라서 앱 부분을 개별적으로 확장할 수 있으며 각 서비스를 다른 프로그래밍 언어 및 프레임워크로 작성할 수 있습니다. 앱의 코드는 하나 이상의 Git 소스 리포지토리에서 호스트될 수 있습니다(현재 도구는 GitHub 또는 Visual Studio Team Services를 지원함).

* 앱은 **DC/OS로 구성된 ACS 클러스터**에서 실행됩니다. 컨테이너 orchestrator는 클러스터의 상태를 관리할 수 있으며 필요한 수의 컨테이너 인스턴스가 계속 실행되도록 합니다. 

* **컨테이너 이미지 빌드 및 배포 프로세스는 가동 중단 없이 완전히 자동화됩니다**. 팀의 개발자가 분기에 대해 'git push'를 수행하여 통합 프로세스가 자동으로 트리거되게 하려고 합니다. 즉, 컨테이너 이미지를 빌드하고 태그를 지정하고, 각 컨테이너에 대해 테스트를 실행하고, Docker 개인 레지스트리에 해당 이미지를 푸시하는 과정이 진행됩니다. 여기에서 새 이미지는 추가 테스트를 위해 ACS 클러스터의 공유 프로덕션 이전 환경에 자동으로 배포됩니다.

* **한 환경에서 다음 환경으로 릴리스를 승격합니다**(예: 개발 -> 테스트 -> 스테이징 -> 프로덕션). 다운스트림 환경으로 승격할 때마다 이전 환경에서 테스트된 동일한 이미지를 배포하기 위해 컨테이너 이미지를 다시 작성할 필요는 없습니다. 이 프로세스는 *변경할 수 없는 서비스* 개념에 해당하며, 프로덕션에서 검색되지 않은 오류가 발생할 가능성을 줄여줍니다.

* ACS 클러스터의 계산 리소스를 가장 효과적으로 활용하기 위해 같은 클러스터를 사용하여 빌드 작업을 실행하고 빌드 및 배포 단계를 완전히 컨테이너화합니다. 또한 클러스터는 여러 개발/테스트/프로덕션 환경을 호스트합니다.


## <a name="create-an-azure-container-service-cluster-configured-with-dcos"></a>DC/OS로 구성된 Azure Container Service 클러스터 만들기

>[!IMPORTANT]
> 보안 클러스터를 만들려면 `az acs create`를 호출할 때 SSH 공개 키 파일을 전달합니다. `--generate-ssh-keys` 옵션을 사용하여 Azure CLI 2.0에서 키를 자동으로 생성하고 동시에 전달하게 하거나 `--ssh-key-value` 옵션을 사용하여 키에 대한 경로를 제공할 수 있습니다(기본 위치는 Linux에서 `~/.ssh/id_rsa.pub`, Windows에서 `%HOMEPATH%\.ssh\id_rsa.pub`이지만 변경할 수 있음).
<!---Loc Comment: What do you mean by "you pass your SSH public key file to pass"? Thank you.--->
> Linux에서 SSH 공용 및 개인 키 파일을 만들려면 [Linux 및 Mac에서 SSH 키 만들기](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fcontainer-services%2ftoc.json)를 참조하세요. 
> Windows에서 SSH 공용 및 개인 키 파일을 만들려면 [Windows에서 SSH 키 만들기](../virtual-machines/virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fcontainer-services%2ftoc.json)를 참조하세요. 

1. 먼저 터미널 창에 [az login](/cli/azure/#login) 명령을 입력하여 Azure CLI를 사용하여 Azure 구독에 로그인합니다. 

    `az login`

1. [az group create](/cli/azure/group#create)를 사용하여 클러스터를 배치할 리소스 그룹을 만듭니다.
    
    `az group create --name myacs-rg --location westus`

    본인에게 가장 가까운 [Azure 데이터 센터 지역](https://azure.microsoft.com/regions)을 지정할 수 있습니다. 

1. [az acs create](/cli/azure/acs#create)를 사용하고 공개 SSH 키 파일 경로를 제공하여 기본 설정으로 ACS 클러스터를 만듭니다. 

    ```azurecli
    az acs create \
    --resource-group myacs-rg 
    --name myacs \
    --dns-prefix myacs \
    --ssh-key-value ~/.ssh/id_rsa.pub
    ```
    
이 단계는 몇 분 정도 걸리므로 천천히 기다리세요.  `acs create` 명령은 새로 만든 클러스터에 대한 정보를 반환합니다(`az acs list`를 사용하여 구독의 ACS 클러스터를 나열할 수도 있음). 기타 ACS 구성 옵션에 대해서는 [ACS 클러스터 생성 및 읽기에 대해 읽어보세요](container-service-deployment.md).

## <a name="set-up-sample-code"></a>샘플 코드 설정
클러스터를 만드는 동안 ACS에 배포하는 샘플 코드를 설정할 수 있습니다.

1. 자체 복사본이 생성되도록 샘플 GitHub 리포지토리를 [분기](https://help.github.com/articles/fork-a-repo/)합니다. [https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git](https://github.com/azure-samples/container-service-dotnet-continuous-integration-multi-container.git) 이 앱은 기본적으로 "hello world"의 다중 컨테이너 버전입니다.
1. 사용자 고유의 GitHub 계정에 분기를 만든 경우 컴퓨터에 리포지토리를 로컬로 복제합니다.

    ```bash
    git clone  https://github.com/your-github-account/container-service-dotnet-continuous-integration-multi-container.git
    cd container-service-dotnet-continuous-integration-multi-container
    ```
    
코드를 좀 더 자세히 살펴보겠습니다.
* `/service-a`는 Node.js 백 엔드가 있는 Angular.js 기반 웹앱입니다.
* `/service-b`는 .NET Core 서비스이며 REST를 통해 `service-a`에 의해 호출됩니다.
* `service-a` 및 `service-b`의 각 디렉터리에는 Node.js 및.NET Core 기반 컨테이너 이미지를 각각 설명하는 `Dockerfile`이 포함되어 있습니다. 
* `docker-compose.yml`은 빌드 및 배포되는 서비스 집합을 선언합니다.
* `service-a` 및 `service-b` 외에도, `cache`라는 세 번째 서비스가 `service-a`가 사용할 수 있는 Redis 캐시를 실행합니다. `cache`는 소스 리포지토리에 해당 코드가 없다는 측면에서 처음 두 서비스와 다릅니다. 대신, Docker Hub에서 미리 만든 `redis:alpine` 이미지를 가져온 후 ACS에 배포합니다.
* `/service-a/server.js`에는 `service-a`가 `service-b` 및 `cache`를 둘 다 호출하는 코드가 포함되어 있습니다. `service-a` 코드는 `docker-compose.yml`에서 명명되는 방식을 `service-b` 및 `cache`에서 참조합니다. `docker-compose`를 통해 로컬 컴퓨터에서 이러한 서비스를 실행하는 경우 Docker는 서비스를 모두 적절히 네트워크로 연결하여 이름으로 찾을 수 있도록 합니다. 일반적으로 네트워크가 부하 분산된 클러스터 환경에서 서비스를 실행하는 것은 로컬로 실행하는 것보다 훨씬 더 복잡합니다. 다행스럽게도 Azure CLI 명령은 이러한 직관적인 서비스 검색 코드가 ACS에서 있는 그대로 계속 실행되도록 하는 CI/CD 흐름을 설정합니다. 

    ![다중 컨테이너 샘플 앱 개요](media/container-service-setup-ci-cd/multi-container-sample-app-overview.PNG)

## <a name="set-up-continuous-integration-and-deployment"></a>지속적인 통합 및 배포 설정
1. ACS 클러스터가 준비되어 있는지 확인합니다. 이를 위해 [az acs list](/cli/azure/acs#list)를 실행하고 ACS 클러스터가 나열되어 있는지 확인합니다. (참고: ACS에서 DC/OS 1.8 이상이 실행되어야 합니다.)

1. [GitHub 개인 액세스 토큰을 만들고](https://help.github.com/articles/creating-an-access-token-for-command-line-use/) 적어도 `repo` 범위를 허가합니다.  다음 명령에서 사용하게 되므로 토큰을 클립보드에 복사해야 합니다(이 명령을 통해 GitHub 리포지토리에서 [webhook](https://help.github.com/articles/about-webhooks/)이 설정됨). 

1. 현재 디렉터리를 복제된 소스 리포지토리의 루트로 설정하고 방금 만든 _&lt;GitHubPersonalAccessToken&gt;을 사용하여 빌드 및 릴리스 파이프라인을 만듭니다.
    
    `cd container-service-dotnet-continuous-integration-multi-container`

    ```azurecli
    az container release create \
    --target-name myacs \
    --target-resource-group myacs-rg \
    --remote-access-token <GitHubPersonalAccessToken>
    ```

    여기서 `--target-name`은 ACS 클러스터의 이름이고 `--target-resource-group`은 ACS 클러스터의 리소스 그룹 이름입니다.

처음 실행 시 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 완료되면 만든 빌드 및 릴리스 파이프라인과 관련해서 다음과 같은 중요 정보가 반환됩니다.
* `sourceRepo`: [webhook](https://help.github.com/articles/about-webhooks/)은 소스 코드 적용할 때마다 빌드 및 릴리스 파이프라인이 자동으로 트리거되도록 소스 리포지토리에 대해 구성됩니다.  
* `vstsProject`: VSTS([Visual Studio Team Services](https://www.visualstudio.com/team-services/))는 워크플로를 *구동*하도록 구성됩니다(ACS의 컨테이너 내에서 실행되는 실제 빌드 및 배포 작업). 특정 VSTS 계정 및 프로젝트를 사용하려는 경우 `--vsts-account-name` 및 `--vsts-project-name` 매개 변수를 사용하여 정의할 수 있습니다.
* `buildDefinition`: 각 빌드에 대해 실행되는 작업을 정의합니다. docker-compose.yml에 정의된 각 서비스에 대해 컨테이너 이미지가 생성된 후 Docker 컨테이너 레지스트리로 푸시됩니다. 
* `containerRegistry`: Azure Container Registry는 Docker 컨테이너 레지스트리를 실행하는 관리되는 서비스입니다. 기본 이름으로 새 Azure Container Registry가 생성되거나 `--registry-name` 매개 변수를 통해 Azure Container Registry 이름을 지정할 수도 있습니다.
* `releaseDefinition`: 각 배포에 대해 실행되는 작업을 정의합니다. Docker-compose.yml에 정의된 서비스에 대한 컨테이너 이미지는 컨테이너 레지스트리에서 가져온 후 ACS 클러스터에 배포됩니다. 기본적으로 *개발*, *테스트* 및 *프로덕션*의 세 가지 환경이 생성됩니다. 릴리스 정의는 빌드가 성공적으로 완료될 때마다 *개발* 환경에 자동으로 배포되도록 구성됩니다. 릴리스는 다시 빌드를 요구하지 않고도 수동으로 *테스트* 또는 *프로덕션*으로 승격할 수 있습니다. VSTS에서 기본 흐름을 사용자 지정할 수 있습니다. 
* `containerService`: 대상 ACS 클러스터(DC/OS 1.8을 실행해야 함)


다음 코드 조각은 `myregistry`라는 기존 Azure Container Registry가 이미 있는 경우 입력하는 예제 명령입니다. `myvstsaccount.visualstudio.com`에서 VSTS 계정으로 릴리스 정의를 만들고 빌드하고 기존 VSTS 프로젝트 `myvstsproject`를 빌드합니다.
        
```azurecli
az container release create \
--target-name myacs \
--target-resource-group myacs-rg \
--registry-name myregistry \
--vsts-account-name myvstsaccount \
--vsts-project-name myvstsproject \
--remote-access-token <GitHubPersonalAccessToken>
```

## <a name="view-deployment-pipeline-progress"></a>배포 파이프라인 진행률 보기
파이프라인이 만들어지면 첫 번째 빌드 및 배포가 자동으로 시작됩니다. 코드가 소스 리포지토리로 푸시될 때마다 후속 빌드가 트리거됩니다. 빌드 정의 또는 릴리스 정의 URL에 대해 브라우저를 열어 빌드 및/또는 릴리스 진행 상태를 확인할 수 있습니다.

항상 이 명령을 실행하여 ACS 클러스터와 연결된 릴리스 정의 URL을 찾을 수 있습니다.

```azurecli
az container release list \
--target-name myacs \
--target-resource-group myacs-rg
``` 

![VSTS 빌드](media/container-service-setup-ci-cd/vsts-build.PNG)

*다중 컨테이너 앱의 CI 결과를 보여 주는 VSTS 스크린샷*

![VSTS 릴리스](media/container-service-setup-ci-cd/vsts-release.PNG)

*여러 환경을 포함하는 VSTS docker-compose 릴리스*

## <a name="view-the-application"></a>응용 프로그램 보기
이때 응용 프로그램이 공유 개발 환경에 배포되며 공개적으로 노출되지는 않습니다. 이제 DC/OS 대시보드를 사용하여 서비스를 보고 관리하며 [DC/OS 관련 끝점에 대한 SSH 터널을 만들거나](https://azure.microsoft.com/documentation/articles/container-service-connect/) Azure CLI에서 제공하는 간편 명령을 실행합니다.

> [!IMPORTANT]
> 첫 번째 배포에서 계속하기 전에 VSTS 릴리스가 배포되었는지 확인합니다.

> [!NOTE]
> Windows에만 해당: [Pageant](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 설정하여 이 섹션을 완료합니다.
> 
>* *PuttyGen*을 시작하고 ACS 클러스터를 만드는 데 사용되는 개인 SSH 키를 로드합니다(%homepath%\id_rsa).
>* 개인 SSH 키를 같은 폴더에 `id_rsa.ppk`로 저장합니다.
>* *Pageant*를 시작합니다. 그러면 실행되기 시작하고 오른쪽 아래 시스템 트레이에 아이콘이 표시됩니다.
>* 시스템 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 *Add Key*를 선택합니다.
>* `id_rsa.ppk` 파일을 추가합니다.
> 
> 

1. Azure CLI 간편 명령을 사용하여 ACS 클러스터의 DC/OS 대시보드를 엽니다.
    
    `az acs dcos browse -g myacs-rg -n myacs`

    * `-g`는 대상 ACS 작업의 리소스 그룹 이름입니다.
    * `-n`은 대상 ACS 클러스터의 이름입니다.
    * 이 명령에는 관리자 권한이 필요하므로 로컬 계정 암호를 묻는 메시지가 표시됩니다. 이 명령은 DC/OS 끝점에 대한 SSH 터널을 만들고, 해당 끝점에 대해 기본 브라우저를 열고, 브라우저의 웹 프록시를 일시적으로 구성합니다. 

    > [!TIP]
    > ACS 클러스터의 이름을 조회해야 할 경우 `az acs list`를 실행하여 구독의 모든 ACS 클러스터를 나열할 수 있습니다. 

1. DC/OS 대시보드의 왼쪽 탐색 메뉴에서 **Services**([http://localhost/#/services](http://localhost/#/services))를 클릭합니다. 파이프라인을 통해 배포된 서비스는 *dev*(VSTS 릴리스 정의의 환경 이름) 루트 폴더에 그룹화됩니다. 

![Marathon UI](media/container-service-setup-ci-cd/marathon-ui.png)

DC/OS 대시보드에서 많은 유용한 작업을 수행할 수 있습니다.

* 각 서비스에 대한 배포 상태 추적
* CPU 및 메모리 요구 사항 보기
* 로그 보기
* 각 서비스의 인스턴스 수 확장

**service-a에 대한 웹 응용 프로그램을 보려면**: *dev* 루트 폴더에서 시작한 다음 `service-a`에 도달할 때까지 폴더 계층 구조를 드릴 다운합니다. 이 뷰에는 `service-a`에 대한 실행 중인 작업(또는 컨테이너 인스턴스)이 표시됩니다.

![service a](media/container-service-setup-ci-cd/service-a.png)

작업을 클릭하여 해당 뷰를 열고 사용 가능한 끝점 중 하나를 클릭합니다.

![service a 작업](media/container-service-setup-ci-cd/service-a-task.PNG)

이 간단한 웹앱은 `service-a`를 호출합니다. 그러면 이 작업은 `service-b`를 호출한 다음 hello world 메시지를 반환합니다. 요청이 수행될 때마다 Redis에서 카운터가 증가합니다.

![service a 웹앱](media/container-service-setup-ci-cd/service-a-web-app.PNG)

### <a name="optional-reaching-a-service-from-the-command-line"></a>(선택 사항) 명령줄에서 서비스에 도달
명령줄에서 curl을 통해 서비스에 도달하려면:

1. `az acs dcos browse --verbose -g myacs-rg -n myacs`를 실행하고 암호를 입력한 후 "Proxy running on <ip-address>:<port>" 줄의 내용을 확인합니다.
1. 새 터미널 창에서 다음을 입력합니다.

    `export http_proxy=http://<web-proxy-service-ip>:<portnumber>`

    예: `export http_proxy=http://127.0.0.1:55405`

1. 이제 서비스 끝점에 대해 curl을 실행할 수 있습니다. `curl http://service-url`. 여기서 `service-url`은 Marathon UI에서 서비스 끝점으로 이동할 때 표시되는 주소입니다. 명령줄에서 http_proxy 변수를 설정 해제하려면 `unset http_proxy`를 입력합니다.
 

## <a name="scale-services"></a>서비스 크기 조정
DC/OS 대시보드에서 서비스를 확장해 보겠습니다.
1. *dev* 하위 폴더의 응용 프로그램으로 이동합니다.
1. `service-b` 위로 마우스를 가져간 후 기어 아이콘을 클릭하고 **Scale**을 선택합니다.

    ![동작 메뉴](media/container-service-setup-ci-cd/marathon-ui-action-menu.PNG)

1. 이 수를 3으로 늘리고 **Scale Service**를 클릭합니다.

    ![서비스 크기 조정](media/container-service-setup-ci-cd/marathon-ui-scale-service.png)

1. 실행 중인 웹앱으로 다시 이동하고 *Say It Again* 단추를 반복해서 클릭합니다. `service-b` 호출은 `service-a`의 단일 인스턴스가 동일한 호스트를 계속 보고하는 동안 호스트 이름 컬렉션에 대해 라운드 로빈을 시작합니다.   

## <a name="promote-a-release-to-downstream-environments-without-rebuilding-container-images"></a>컨테이너 이미지를 다시 작성하지 않고 릴리스를 다운스트림 환경으로 승격
VSTS 릴리스 파이프라인는 기본적으로 *Dev*, *Test* 및 *Production*의 세 가지 환경을 설정합니다. 지금까지는 *Dev*로 배포했습니다. 컨테이너 이미지를 다시 작성하지 않고 릴리스를 다음 다운스트림 환경인 *Test*로 승격하는 방법을 살펴보겠습니다. 이 워크플로는 이전 환경에서 테스트했던 것과 정확히 동일한 이미지를 배포하도록 하고, *변경할 수 없는 서비스* 개념에 해당하며, 프로덕션에서 검색되지 않은 오류가 발생할 가능성을 줄여줍니다.

1. VSTS 웹 UI에서 **Releases**로 이동합니다.

    ![VSTS 릴리스 메뉴](media/container-service-setup-ci-cd/vsts-releases-menu.PNG)

1. 최신 릴리스를 엽니다.

1. 릴리스 정의 메뉴 모음에서 **Deploy**를 클릭하고 새 배포를 시작할 다음 환경으로 **Test**를 선택합니다. 이때 이전에 *Dev*에 배포한 것과 같은 이미지를 다시 사용합니다. 배포를 좀 더 자세히 살펴보려면 **Logs**를 클릭합니다.

    ![VSTS 릴리스 승격](media/container-service-setup-ci-cd/vsts-promote-release.PNG)

*Test*로의 배포가 성공하면 Marathon UI에는 해당 환경에 대해 실행 중인 서비스를 포함하는 *test*라는 새 루트 폴더가 생성됩니다. 

![DC/OS의 각 환경에 대한 하위 폴더](media/container-service-setup-ci-cd/marathon-ui-dev-test-environments.png)

## <a name="trigger-a-new-build-and-deployment"></a>새 빌드 및 배포 트리거
개발자가 소스 리포지토리에 코드 변경을 적용할 경우 어떤 결과가 나타나는지를 시뮬레이트해 보겠습니다.

1. 코드 편집기로 돌아가 `service-a/public/index.html`을 엽니다. 
1. 다음 코드 줄을

    `<h2>Server Says</h2>`

    다음과 같이 수정합니다.

    `<h2>Server Says Hello</h2>`

1. 파일을 저장한 후 커밋하고 코드 변경 내용을 소스 리포지토리로 푸시합니다.

    ```bash
    git commit -am 'updated title'
    git push
    ```

커밋에 의해 새 빌드가 자동으로 시작되고 *Dev*에 새 릴리스가 배포됩니다. 해당 환경으로 특정 릴리스로 승격하기로 결정할 때까지 다운스트림 환경(*Test* 또는 *Production*)의 서비스는 변경되지 않습니다.

VSTS에서 빌드 정의를 열면 다음과 유사한 결과가 나타납니다. 

![git push에 의해 트리거되는 새 빌드](media/container-service-setup-ci-cd/new-build.png)



## <a name="expose-public-endpoint-for-production"></a>프로덕션에 대한 공용 끝점 노출

1. 소스 리포지토리의 루트 폴더에 있는 `docker-compose.env.production.yml`이라는 새 파일에 다음 yaml 코드를 추가합니다. 이렇게 하면 레이블이 추가되어 `service-a`에 대해 공용 끝점이 노출됩니다. 
    
    ```yaml
    version: "2"
    services:
      service-a:
        labels:
          com.microsoft.acs.dcos.marathon.vhost: "<FQDN, or custom domain>"
    ```

    * 레이블 값에 대해 ACS 에이전트의 FQDN(정규화된 도메인 이름) 또는 사용자 지정 도메인(예: app.contoso.com)의 URL을 지정할 수 있습니다. ACS 에이전트의 FQDN을 찾으려면 명령 `az acs list`를 실행하고 `agentPoolProfiles.fqdn`에 대한 속성을 확인합니다. 예: `myacsagents.westus.cloudapp.azure.com`
    * 파일 이름 규칙 docker-compose.env.*environment-name*.yml에 따라, 이러한 설정은 명명된 환경에만 영향을 줍니다(이 경우에 *프로덕션* 환경). VSTS의 릴리스 정의를 살펴보면 각 환경 배포 작업은 이 규칙의 이름을 딴 docker-compose 파일에서 읽어오도록 설정되어 있습니다.

1. 파일을 커밋하고 마스터 소스 리포지토리에 푸시하여 다른 빌드를 시작합니다.

    ```bash
    git add .
    git commit -am "expose public port for service-a"
    git push
    ```

1. 업데이트가 빌드되고 *개발*로 배포될 때까지 기다렸다가 *테스트*로 승격한 후 *프로덕션*으로 승격합니다. (이 자습서의 목적에 따라, *프로덕션*으로 직접 배포할 수 있지만 그 다음 다운스트림 환경으로 배포하는 과정만 연습하는 것이 좋습니다.)

1. (선택 사항) vhost에 대해 **사용자 지정 도메인을 지정한 경우**(예: app.contoso.com) 도메인 공급자의 설정에 DNS 레코드를 추가합니다. 도메인 공급자의 관리 UI에 로그인하고 DNS 레코드를 다음과 같이 추가합니다.

    * 형식: CNAME
    * 호스트: 사용자 지정 도메인(예: app.contoso.com)
    * 대답: ACS 에이전트 FQDN(예: myacsagents.westus.cloudapp.azure.com)
    * TTL(선택 사항): 경우에 따라 도메인 공급자가 TTL 편집 기능을 제공합니다. 값이 낮을수록 DNS 레코드 업데이트가 더 신속하게 전파됩니다.   

1. 릴리스가 *프로덕션*으로 배포되면 누구나 해당 버전에 액세스할 수 있습니다. `com.microsoft.acs.dcos.marathon.vhost` 레이블에 대해 지정한 URL로 브라우저를 엽니다. (참고: 프로덕션 이전 환경에 대한 릴리스는 계속 비공개입니다.)

## <a name="summary"></a>요약
축하합니다. DC/OS에서 ACS 클러스터를 만들고, 다중 컨테이너 앱에 대해 완전히 자동화되고 컨테이너화된 빌드 및 배포 파이프라인을 설정하는 방법을 알아보았습니다.

살펴볼 다음 단계:
* **VSTS 에이전트를 확장합니다.** 빌드 및 릴리스 작업을 실행하기 위해 더 많은 처리량이 필요한 경우 VSTS 에이전트 인스턴스 수를 늘릴 수 있습니다. DC/OS 대시보드에서 **Services**로 이동하고 vsts-agents 폴더를 열고 VSTS 에이전트 인스턴스 수를 확장하는 과정을 실험해봅니다.
* **단위 테스트를 통합합니다.** 이 GitHub 리포지토리에서는 컨테이너에서 단위 테스트 및 통합 테스트 실행을 만든 다음 빌드 작업에 포함하는 방법을 보여 줍니다. [https://github.com/mindaro/sample-app](https://github.com/mindaro/sample-app) 
    * 힌트: 리포지토리에서 `service-a/unit-tests.js`, `service-a/service-tests.js`, `docker-compose.ci.unit-tests.yml` 및 `docker-compose.ci.service-tests.yml` 파일을 확인합니다.

## <a name="clean-up"></a>정리
이 자습서와 관련된 계산 비용을 최소화하려면 다음 명령을 실행하고 ACS 클러스터에 관련된 배포 파이프라인 리소스를 적어둡니다.

```azurecli 
az container release list --resource-name myacs --resource-group myacs-rg
```

ACS 클러스터 삭제:
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. ACS 클러스터를 포함하는 리소스 그룹을 찾습니다.
1. 리소스 그룹의 블레이드 UI를 열고 블레이드의 명령 모음에서 **삭제**를 클릭합니다.

Azure Container Registry 삭제: Azure Portal에서 Azure Container Registry를 검색한 후 삭제합니다. 

[Visual Studio Team Services 계정은 처음&5;명의 사용자에게 무료 기본 액세스 수준을 제공하지만](https://azure.microsoft.com/en-us/pricing/details/visual-studio-team-services/) 빌드 및 릴리스 정의를 삭제할 수 있습니다.

VSTS 빌드 정의 삭제:
        
1. 브라우저에서 빌드 정의 URL을 연 다음 **빌드 정의** 링크(현재 보고 있는 빌드 정의 이름 옆에 있음)를 클릭합니다.
2. 삭제하려는 빌드 정의 옆에 있는 작업 메뉴를 클릭하고 **정의 삭제**를 선택합니다.

`![VSTS 빌드 정의 삭제](media/container-service-setup-ci-cd/vsts-delete-build-def.png) 

VSTS 릴리스 정의 삭제:

1. 브라우저에서 릴리스 정의 URL을 엽니다.
2. 왼쪽의 릴리스 정의 목록에서 삭제하려는 릴리스 정의 옆에 있는 드롭다운을 클릭하고 **삭제**를 선택합니다.

`![VSTS 릴리스 정의 삭제](media/container-service-setup-ci-cd/vsts-delete-release-def.png)



<!--HONumber=Jan17_HO4-->


