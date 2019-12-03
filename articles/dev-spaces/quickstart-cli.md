---
title: Kubernetes에서 애플리케이션 개발
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: Azure Dev Spaces를 사용하여 AKS에서 마이크로서비스 배포
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 932b25e277936a2efe6a3e037458fabd67b024c2
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325652"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>빠른 시작: Azure Dev Spaces에서 애플리케이션 개발 - Azure Dev Spaces
이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

- Azure에서 관리되는 Kubernetes 클러스터를 사용하여 Azure Dev Spaces를 설정합니다.
- 명령줄을 사용하여 컨테이너에서 코드를 개발하고 실행합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
- [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

[지원되는 지역][supported-regions]에서 AKS 클러스터를 만들어야 합니다. 아래 명령은 *MyResourceGroup*이라는 리소스 그룹과 *MyAKS*라는 AKS 클러스터를 만듭니다.

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --disable-rbac --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다. 아래 명령은 *MyResourceGroup* 그룹의 *MyAKS* 클러스터에서 Dev Spaces를 사용하도록 설정하고 *기본* 개발 공간을 만듭니다.

> [!NOTE]
> `use-dev-spaces` 명령은 Azure Dev Spaces CLI가 아직 설치되지 않은 경우에도 설치합니다. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>샘플 애플리케이션 코드 받기

이 문서에서는 [Azure Dev Spaces 샘플 애플리케이션](https://github.com/Azure/dev-spaces)을 사용하여 Azure Dev Spaces 사용법을 시연합니다.

GitHub의 애플리케이션을 복제하고 *dev-spaces/samples/nodejs/getting-started/webfrontend* 디렉터리로 이동합니다.

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>애플리케이션 준비

Azure Dev Spaces에서 애플리케이션을 실행하려면 Dockerfile 및 Helm 차트가 필요합니다. [Java][java-quickstart], [.NET core][netcore-quickstart] 및 [Node.js][nodejs-quickstart]와 같은 일부 언어의 경우 Azure Dev Spaces 클라이언트 도구에서 필요한 모든 자산을 생성할 수 있습니다. Go, PHP 및 Python과 같은 다른 여러 언어의 경우 유효한 Dockerfile을 제공할 수 있는 한 클라이언트 도구에서 Helm 차트를 생성할 수 있습니다.

`azds prep` 명령을 사용하여 Kubernetes에서 애플리케이션을 실행하는 데 필요한 Docker 및 Helm 차트 자산을 생성합니다.

```cmd
azds prep --public
```

*dev-spaces/samples/nodejs/getting-started/webfrontend* 디렉터리에서 `prep` 명령을 실행하여 Docker 및 Helm 차트 자산을 올바르게 생성해야 합니다.

## <a name="build-and-run-code-in-kubernetes"></a>Kubernetes에서 코드 빌드 및 실행

`azds up` 명령을 사용하여 AKS에서 코드를 빌드하고 실행합니다.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

`azds up` 명령의 출력에 표시되는 공용 URL을 열어서 실행 중인 서비스를 볼 수 있습니다. 이 예제에서 공용 URL은 *http://webfrontend.1234567890abcdef1234.eus.azds.io/* 입니다.

> [!NOTE]
> `azds up`을 실행하는 동안 서비스로 이동하면 HTTP 요청 추적도 `azds up` 명령의 출력에 표시됩니다. 이 추적은 서비스의 문제를 해결하고 디버그하는 데 도움이 될 수 있습니다. `azds up`을 실행할 때 `--disable-http-traces`를 사용하면 이러한 추적을 사용하지 않도록 설정할 수 있습니다.

*Ctrl+c*를 사용하여 `azds up` 명령을 중지하면 서비스가 AKS에서 계속 실행되고, 공용 URL은 사용 가능한 상태로 유지됩니다.

## <a name="update-code"></a>코드 업데이트

서비스의 업데이트된 버전을 배포하려면 프로젝트의 파일을 업데이트하고 `azds up` 명령을 다시 실행하면 됩니다. 예:

1. `azds up`가 계속 실행 중인 경우 *Ctrl+c*를 누르세요.
1. [`server.js`의 13줄](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)을 다음으로 업데이트합니다.
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 변경 내용을 저장합니다.
1. `azds up` 명령을 다시 실행합니다.

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 실행 중인 서비스로 이동하고 변경 내용을 살펴봅니다.
1. *Ctrl+c*를 눌러서 `azds up` 명령을 중지합니다.

## <a name="clean-up-your-azure-resources"></a>Azure 리소스 정리

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces를 통해 여러 컨테이너에서 더 복잡한 애플리케이션을 개발할 수 있는 방법 및 사용자가 다양한 환경에서 다양한 코드 버전이나 분기로 작업하여 공동 개발을 간소화하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Dev Spaces에서 팀 개발][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: about.md#supported-regions-and-configurations