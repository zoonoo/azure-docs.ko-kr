---
title: Kubernetes에서 기존 Helm 차트를 사용하여 애플리케이션 개발
services: azure-dev-spaces
ms.date: 04/21/2020
ms.topic: quickstart
description: 이 빠른 시작에서는 Azure Dev Spaces 및 명령줄을 사용하여 Azure Kubernetes Service의 기존 Helm 차트로 애플리케이션을 개발하는 방법을 보여줍니다.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Helm, 서비스 메시, 서비스 메시 라우팅, kubectl, k8s
manager: gwallace
ms.openlocfilehash: e767b1ade2a80882ee33ff1fdd718c691dcefcf3
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025288"
---
# <a name="quickstart-develop-an-application-with-an-existing-helm-chart-on-kubernetes---azure-dev-spaces"></a>빠른 시작: Kubernetes에서 기존 Helm 차트를 사용하여 애플리케이션 개발 - Azure Dev Spaces
이 가이드에서는 다음을 수행하는 방법을 배우게 됩니다.

- Azure에서 관리되는 Kubernetes 클러스터를 사용하여 Azure Dev Spaces를 설정합니다.
- 명령줄에서 Azure Dev Spaces를 사용하여 AKS의 기존 Helm 차트로 애플리케이션을 실행합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
- [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

[지원되는 지역][supported-regions]에서 AKS 클러스터를 만들어야 합니다. 아래 명령은 *MyResourceGroup*이라는 리소스 그룹과 *MyAKS*라는 AKS 클러스터를 만듭니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS 클러스터에서 Azure Dev Spaces를 사용하도록 설정

`use-dev-spaces` 명령을 사용하여 AKS 클러스터에서 Dev Spaces를 사용하도록 설정하고 프롬프트의 지시를 따릅니다. 아래 명령은 *MyResourceGroup* 그룹의 *MyAKS* 클러스터에서 Dev Spaces를 사용하도록 설정하고 *dev*라는 개발 공간을 만듭니다.

> [!NOTE]
> `use-dev-spaces` 명령은 Azure Dev Spaces CLI가 아직 설치되지 않은 경우에도 설치합니다. Azure Cloud Shell에는 Azure Dev Spaces CLI를 설치할 수 없습니다.

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS --space dev --yes
```

## <a name="get-sample-application-code"></a>샘플 애플리케이션 코드 받기

이 문서에서는 [Azure Dev Spaces 샘플 애플리케이션](https://github.com/Azure/dev-spaces)을 사용하여 Azure Dev Spaces 사용법을 시연합니다.

GitHub에서 애플리케이션을 복제하고 *dev-spaces/samples/python/getting-started/webfrontend* 디렉터리로 이동합니다.

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/python/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>애플리케이션 준비

Azure Dev Spaces에서 애플리케이션을 실행하려면 Dockerfile 및 Helm 차트가 필요합니다. [Java][java-quickstart], [.NET core][netcore-quickstart] 및 [Node.js][nodejs-quickstart]와 같은 일부 언어의 경우 Azure Dev Spaces 클라이언트 도구에서 필요한 모든 자산을 생성할 수 있습니다. Go, PHP 및 Python과 같은 다른 여러 언어의 경우 유효한 Dockerfile을 제공할 수 있는 한 클라이언트 도구에서 Helm 차트를 생성할 수 있습니다. 이 경우 샘플 애플리케이션에는 기존 Dockerfile 및 Helm 차트가 있습니다.

`azds prep` 명령을 통해 기존 Helm 차트 및 Dockerfile과 함께 Azure Dev Spaces를 사용하여 애플리케이션을 실행하기 위한 구성을 생성합니다.

```cmd
azds prep --enable-ingress --chart webfrontend/
```

*dev-spaces/samples/python/getting-started/webfrontend* 디렉터리에서 `prep` 명령을 실행하고 `--chart`를 사용하여 Helm 차트의 위치를 지정해야 합니다.

> [!NOTE]
> 다음과 같은 경고가 표시될 수 있습니다. *경고: 지원되지 않는 언어로 인해 Dockerfile을 생성할 수 없습니다.* `azds prep`를 실행하는 경우 `azds prep` 명령은 프로젝트에 대해 [Dockerfile 및 Helm 차트](how-dev-spaces-works-prep.md#prepare-your-code)를 생성하려고 하지만 기존 Dockerfiles 또는 Helm 차트를 덮어쓰지 않습니다.

## <a name="build-and-run-code-in-kubernetes"></a>Kubernetes에서 코드 빌드 및 실행

`azds up` 명령을 사용하여 AKS에서 코드를 빌드하고 실행합니다.

```cmd
$ azds up
Using dev space 'dev' with target 'MyAKS'
Synchronizing files...14s
Installing Helm chart...2s
Waiting for container image build...3s
Building container image...
Step 1/7 : FROM python:3
Step 2/7 : WORKDIR /python/webfrontend
Step 3/7 : RUN pip install flask
Step 4/7 : COPY webfrontend.py webfrontend.py
Step 5/7 : COPY public/ public/
Step 6/7 : EXPOSE 80
Step 7/7 : CMD ["python", "./webfrontend.py"]
Built container image in 45s
Waiting for container...25s
Service 'azds-543eae-dev-webfrontend' port 'http' is available at http://dev.service.1234567890abcdef1234.eus.azds.io/
Service 'azds-543eae-dev-webfrontend' port 80 (http) is available via port forwarding at http://localhost:52382
Press Ctrl+C to detach
...
```

`azds up` 명령의 출력에 표시되는 공용 URL을 열어서 실행 중인 서비스를 볼 수 있습니다. 이 예제에서 공용 URL은 *http://dev.service.1234567890abcdef1234.eus.azds.io/* 입니다.

> [!NOTE]
> `azds up`을 실행하는 동안 서비스로 이동하면 HTTP 요청 추적도 `azds up` 명령의 출력에 표시됩니다. 이 추적은 서비스의 문제를 해결하고 디버그하는 데 도움이 될 수 있습니다. `azds up`을 실행할 때 `--disable-http-traces`를 사용하면 이러한 추적을 사용하지 않도록 설정할 수 있습니다.

*Ctrl+c*를 사용하여 `azds up` 명령을 중지하면 서비스가 AKS에서 계속 실행되고, 공용 URL은 사용 가능한 상태로 유지됩니다.

## <a name="update-code"></a>코드 업데이트

서비스의 업데이트된 버전을 배포하려면 프로젝트의 파일을 업데이트하고 `azds up` 명령을 다시 실행하면 됩니다. 다음은 그 예입니다.

1. `azds up`가 계속 실행 중인 경우 *Ctrl+c*를 누르세요.
1. [`webfrontend.py`의 13줄](https://github.com/Azure/dev-spaces/blob/master/samples/python/getting-started/webfrontend/webfrontend.py#L13)을 다음으로 업데이트합니다.
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 변경 내용을 저장합니다.
1. `azds up` 명령을 다시 실행합니다.

    ```cmd
    $ azds up
    Using dev space 'dev' with target 'MyAKS'
    Synchronizing files...11s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 실행 중인 서비스로 이동하고 변경 내용을 살펴봅니다.
1. *Ctrl+c*를 눌러서 `azds up` 명령을 중지합니다.

## <a name="clean-up-your-azure-resources"></a>Azure 리소스 정리

```azurecli
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
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service