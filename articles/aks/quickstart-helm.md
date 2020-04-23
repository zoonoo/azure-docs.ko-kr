---
title: 헬름을 통해 AZURE Kubernetes 서비스(AKS)에서 개발
description: AKS 및 Azure 컨테이너 레지스트리가 있는 Helm을 사용하여 클러스터에서 응용 프로그램 컨테이너를 패키징하고 실행합니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 04/20/2020
ms.author: zarhoads
ms.openlocfilehash: 77627ab846999ea5ba42fde7a9c49b9cc7559fba
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873434"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>빠른 시작: 헬름을 사용 하 고 Azure Kubernetes 서비스 (AKS)에서 개발

[Helm][helm]은 Kubernetes 애플리케이션을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. *APT*, *Yum* 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다.

이 문서에서는 Helm을 사용하여 AKS에서 응용 프로그램을 패키징하고 실행하는 방법을 보여 주며 있습니다. Helm을 사용하여 기존 응용 프로그램 설치에 대한 자세한 내용은 [AKS의 Helm을 사용하여 기존 응용 프로그램 설치를][helm-existing]참조하십시오.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [무료 계정을](https://azure.microsoft.com/free)만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker가 설치되고 구성되었습니다. Docker는 모든 [Mac][docker-for-mac], [Windows][docker-for-windows] 또는 [Linux][docker-for-linux] 시스템에서 Docker를 구성하는 패키지를 제공합니다.
* [투구 v3 설치][helm-install].

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기
Helm을 사용하여 AKS 클러스터에서 응용 프로그램을 실행하려면 컨테이너 이미지를 저장하려면 Azure 컨테이너 레지스트리가 필요합니다. 아래 예제에서는 [az acr create를][az-acr-create] 사용하여 *기본* SKU를 사용하여 *MyResourceGroup* 리소스 그룹에서 *MyHelmACR이라는* ACR을 만듭니다. 고유한 레지스트리 이름을 제공해야 합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. *기본* SKU는 개발을 위해 비용에 최적화된 진입점으로, 스토리지와 처리량의 균형을 적절하게 맞추었습니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

다음 예제와 유사하게 출력됩니다. 이후 단계에서 사용되므로 ACR에 대한 *로그인Server* 값을 기록해 둡을 확인합니다. 아래 예제에서 *myhelmacr.azurecr.io* *MyHelmACR에*대한 *로그인 서버입니다.*

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyHelmACR",
  "location": "eastus",
  "loginServer": "myhelmacr.azurecr.io",
  "name": "MyHelmACR",
  "networkRuleSet": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

ACR 인스턴스를 사용하려면 먼저 로그인해야 합니다. az [acr 로그인][az-acr-login] 명령을 사용하여 로그인합니다. 아래 예제는 *MyHelmACR이라는*ACR에 로그인합니다.

```azurecli
az acr login --name MyHelmACR
```

이 명령이 완료되면 *로그인했습니다.* 라는 메시지를 반환합니다.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

AKS 클러스터를 만듭니다. 아래 명령은 MyAKS라는 AKS 클러스터를 만들고 MyHelmACR을 연결합니다.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

AKS 클러스터는 컨테이너 이미지를 끌어와 실행하기 위해 ACR에 액세스해야 합니다. 위의 명령은 *MyAkS* 클러스터 액세스 권한을 *MyHelmACR* ACR에 부여합니다.

## <a name="connect-to-your-aks-cluster"></a>AKS 클러스터에 연결

로컬 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다.

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. [az aks install-cli][] 명령을 사용하여 kubectl을 로컬로 설치할 수도 있습니다.

```azurecli
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][] 명령을 사용합니다. 다음 예제에서는 *MyResourceGroup에서* *MyAKS라는* AKS 클러스터에 대한 자격 증명을 가져옵니다.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작은 [Azure 개발자 공간 샘플 리포지토리의 예제 Node.js 응용 프로그램을][example-nodejs]사용합니다. GitHub에서 응용 프로그램을 복제하고 `dev-spaces/samples/nodejs/getting-started/webfrontend` 디렉터리로 이동합니다.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Dockerfile 만들기

다음을 사용하여 새 *Dockerfile* 파일을 만듭니다.

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>샘플 응용 프로그램을 빌드하고 ACR로 푸시

[az acr 목록][az-acr-list] 명령을 사용하여 로그인 서버 주소를 얻고 *loginServer에*대한 쿼리 :

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Docker를 사용하여 샘플 응용 프로그램 컨테이너를 빌드, 태그 및 ACR에 푸시합니다.

```console
docker build -t webfrontend:latest .
docker tag webfrontend <acrLoginServer>/webfrontend:v1
docker push <acrLoginServer>/webfrontend:v1
```

## <a name="create-your-helm-chart"></a>투구 차트 만들기

명령을 사용하여 투구 `helm create` 차트를 생성합니다.

```console
helm create webfrontend
```

*웹 프론트 엔드 / value.yaml에*대한 다음 업데이트를 합니다.

* `image.repository`를 `<acrLoginServer>/webfrontend`로 변경
* `service.type`를 `LoadBalancer`로 변경

예를 들어:

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: <acrLoginServer>/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

웹 `appVersion` `v1` 프론트 *엔드 / Chart.yaml에서*업데이트합니다. 예를 들면 다음과 같습니다.

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>투구 차트 실행

`helm create` 명령을 사용하여 Helm 차트를 사용하여 응용 프로그램을 설치합니다.

```console
helm install webfrontend webfrontend/
```

서비스가 공인 IP 주소를 반환하는 데 몇 분이 걸릴 수 있습니다. 진행률을 모니터링하려면 `kubectl get service` 명령과 *watch* 매개 변수를 함께 사용합니다.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
webfrontend         LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
webfrontend         LoadBalancer  10.0.141.72   <EXTERNAL-IP> 80:32150/TCP   7m
```

샘플 응용 프로그램을 보려면 브라우저에서 응용 프로그램의 `<EXTERNAL-IP>` 로드 밸러터로 이동합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않으면 [az 그룹 삭제][az-group-delete] 명령을 사용하여 리소스 그룹, AKS 클러스터, 컨테이너 레지스트리, 거기에 저장된 컨테이너 이미지 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

투구 사용에 대한 자세한 내용은 Helm 설명서를 참조하십시오.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
