---
title: 투구를 사용 하 여 AKS (Azure Kubernetes Service)에서 개발
description: AKS 및 Azure Container Registry와 함께 투구를 사용 하 여 클러스터에서 응용 프로그램 컨테이너를 패키지 하 고 실행 합니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: 0ca2d7ccc863e2208db1212ef3d3f10fa709d069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87407118"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-helm"></a>빠른 시작: 투구를 사용 하 여 AKS (Azure Kubernetes Service)에서 개발

[투구][helm] 는 Kubernetes 응용 프로그램의 수명 주기를 설치 하 고 관리 하는 데 도움이 되는 오픈 소스 패키징 도구입니다. *APT* , *Yum*등의 Linux 패키지 관리자와 마찬가지로, 투구는 미리 구성 된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리 하는 데 사용 됩니다.

이 문서에서는 AKS에서 응용 프로그램을 패키지 하 고 실행 하는 데 투구를 사용 하는 방법을 보여 줍니다. 투구를 사용 하 여 기존 응용 프로그램을 설치 하는 방법에 대 한 자세한 내용은 [AKS에서 투구로 기존 응용 프로그램 설치][helm-existing]를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)
* [투구 v3이 설치][helm-install]되었습니다.

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기
AKS 클러스터에서 응용 프로그램을 실행 하는 데 투구를 사용 하려면 컨테이너 이미지를 저장 하는 Azure Container Registry 필요 합니다. 아래 예제에서는 [az acr create][az-acr-create] 를 사용 하 여 *기본* SKU를 사용 하 여 *Myresourcegroup* 리소스 그룹에 *myaz macr* 이라는 acr을 만듭니다. 고유한 레지스트리 이름을 제공 해야 합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. *기본* SKU는 개발을 위해 비용에 최적화된 진입점으로, 스토리지와 처리량의 균형을 적절하게 맞추었습니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az acr create --resource-group MyResourceGroup --name MyHelmACR --sku Basic
```

다음 예제와 유사하게 출력됩니다. ACR에 대 한 *loginServer* 값은 이후 단계에서 사용 되므로 기록해 둡니다. 아래 예제에서 *myhelmacr.azurecr.io* 는 *MyloginServer Macr*의 *loginServer* 입니다.

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

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

AKS 클러스터를 만듭니다. 아래 명령은 MyAKS 라는 AKS 클러스터를 만들고 My Macr을 연결 합니다.

```azurecli
az aks create -g MyResourceGroup -n MyAKS --location eastus  --attach-acr MyHelmACR --generate-ssh-keys
```

AKS 클러스터는 컨테이너 이미지를 끌어와 실행 하기 위해 ACR에 액세스 해야 합니다. 위의 명령은 *MyAKS* 클러스터에 *My macr* ACR에 대 한 액세스 권한도 부여 합니다.

## <a name="connect-to-your-aks-cluster"></a>AKS 클러스터에 연결

로컬 컴퓨터에서 Kubernetes 클러스터에 연결하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용합니다.

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. [az aks install-cli][] 명령을 사용하여 kubectl을 로컬로 설치할 수도 있습니다.

```azurecli
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][] 명령을 사용합니다. 다음 예제에서는 *Myresourcegroup*에서 *MyAKS* 라는 AKS 클러스터에 대 한 자격 증명을 가져옵니다.

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 [시작에서는 Azure Dev Spaces 샘플 리포지토리의 예제 Node.js 응용 프로그램][example-nodejs]을 사용 합니다. GitHub에서 응용 프로그램을 복제 하 고 `dev-spaces/samples/nodejs/getting-started/webfrontend` 디렉터리로 이동 합니다.

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="create-a-dockerfile"></a>Dockerfile 만들기

다음을 사용 하 여 새 *Dockerfile* 파일을 만듭니다.

```dockerfile
FROM node:latest

WORKDIR /webfrontend

COPY package.json ./

RUN npm install

COPY . .

EXPOSE 80
CMD ["node","server.js"]
```

## <a name="build-and-push-the-sample-application-to-the-acr"></a>샘플 응용 프로그램을 빌드하여 ACR에 푸시합니다.

[Az acr build][az-acr-build] 명령을 사용 하 여 앞의 Dockerfile을 사용 하 여 이미지를 빌드하고 레지스트리에 푸시합니다. 명령 끝부분에 있는 `.`는 Dockerfile의 위치(이 예에서는 현재 디렉터리)를 설정합니다.

```azurecli
az acr build --image webfrontend:v1 \
  --registry MyHelmACR \
  --file Dockerfile .
```

## <a name="create-your-helm-chart"></a>투구 차트 만들기

명령을 사용 하 여 투구 차트를 생성 `helm create` 합니다.

```console
helm create webfrontend
```

*Webfrontend 엔드/값*으로 다음 업데이트를 수행 합니다. yaml. 이전 단계에서 기록한 레지스트리의 loginServer (예: *myhelmacr.azurecr.io*)를 대체 합니다.

* `image.repository`를 `<loginServer>/webfrontend`로 변경
* `service.type`를 `LoadBalancer`로 변경

예를 들면 다음과 같습니다.

```yml
# Default values for webfrontend.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

replicaCount: 1

image:
  repository: *myhelmacr.azurecr.io*/webfrontend
  pullPolicy: IfNotPresent
...
service:
  type: LoadBalancer
  port: 80
...
```

`appVersion` `v1` *Webfrontend 엔드/차트에서로 업데이트 합니다. yaml*. 예를 들면 다음과 같습니다.

```yml
apiVersion: v2
name: webfrontend
...
# This is the version number of the application being deployed. This version number should be
# incremented each time you make changes to the application.
appVersion: v1
```

## <a name="run-your-helm-chart"></a>투구 차트 실행

이 명령을 사용 하 여 `helm install` 투구 차트를 사용 하 여 응용 프로그램을 설치 합니다.

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

을 사용 하 여 브라우저에서 응용 프로그램의 부하 분산 장치로 이동 하 여 `<EXTERNAL-IP>` 샘플 응용 프로그램을 확인 합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요 하지 않은 경우 [az group delete][az-group-delete] 명령을 사용 하 여 리소스 그룹, AKS 클러스터, 컨테이너 레지스트리, 거기에 저장 된 컨테이너 이미지 및 모든 관련 리소스를 제거 합니다.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

투구를 사용 하는 방법에 대 한 자세한 내용은 투구 설명서를 참조 하세요.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[example-nodejs]: https://github.com/Azure/dev-spaces/tree/master/samples/nodejs/getting-started/webfrontend
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[helm]: https://helm.sh/
[helm-documentation]: https://helm.sh/docs/
[helm-existing]: kubernetes-helm.md
[helm-install]: https://helm.sh/docs/intro/install/
[sp-delete]: kubernetes-service-principal.md#additional-considerations
