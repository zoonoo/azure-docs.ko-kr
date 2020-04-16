---
title: 초안이 있는 AZURE Kubernetes 서비스(AKS)에서 개발
description: AKS 및 Azure 컨테이너 레지스트리가 있는 초안을 사용하여 클러스터에서 응용 프로그램 컨테이너를 패키징하고 실행합니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: 2d69676121fcb26f7d2f796e6de42435447084cd
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392744"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>빠른 시작: 초안이 있는 AZURE Kubernetes 서비스(AKS)에서 개발

초안은 Kubernetes 클러스터에서 응용 프로그램 컨테이너를 패키징하고 실행하는 데 도움이 되는 오픈 소스 도구입니다. 초안을 사용하면 버전 제어에 변경 내용을 커밋하지 않고도 코드 변경이 발생할 때 Kubernetes에 응용 프로그램을 신속하게 다시 배포할 수 있습니다. 초안에 대한 자세한 내용은 [GitHub의 초안 설명서를][draft-documentation]참조하십시오.

이 문서에서는 초안을 사용하여 AKS에서 응용 프로그램을 패키징하고 실행하는 방법을 보여 주며 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [무료 계정을](https://azure.microsoft.com/free)만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker가 설치되고 구성되었습니다. Docker는 모든 [Mac][docker-for-mac], [Windows][docker-for-windows] 또는 [Linux][docker-for-linux] 시스템에서 Docker를 구성하는 패키지를 제공합니다.
* [투구 v2 가 설치되었습니다.][helm-install]
* [드래프트가 설치되었습니다.][draft-documentation]

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

AKS 클러스터를 만듭니다. 아래 명령은 MyResourceGroup이라는 리소스 그룹과 MyAKS라는 AKS 클러스터를 만듭니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기
초안을 사용하여 AKS 클러스터에서 응용 프로그램을 실행하려면 컨테이너 이미지를 저장하려면 Azure 컨테이너 레지스트리가 필요합니다. 아래 예제에서는 [az acr create를][az-acr-create] 사용하여 *기본* SKU를 사용하여 *MyResourceGroup* 리소스 그룹에서 *MyDraftACR이라는* ACR을 만듭니다. 고유한 레지스트리 이름을 제공해야 합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. *기본* SKU는 개발을 위해 비용에 최적화된 진입점으로, 스토리지와 처리량의 균형을 적절하게 맞추었습니다.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

다음 예제와 유사하게 출력됩니다. 이후 단계에서 사용되므로 ACR에 대한 *로그인Server* 값을 기록해 둡을 확인합니다. 아래 예제에서 *mydraftacr.azurecr.io* *MyDraftACR에*대한 *로그인 서버입니다.*

```console
{
  "adminUserEnabled": false,
  "creationDate": "2019-06-11T13:35:17.998425+00:00",
  "id": "/subscriptions/<ID>/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerRegistry/registries/MyDraftACR",
  "location": "eastus",
  "loginServer": "mydraftacr.azurecr.io",
  "name": "MyDraftACR",
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


초안이 ACR 인스턴스를 사용하려면 먼저 로그인해야 합니다. az [acr 로그인][az-acr-login] 명령을 사용하여 로그인합니다. 아래 예제는 *MyDraftACR이라는*ACR에 로그인합니다.

```azurecli
az acr login --name MyDraftACR
```

이 명령이 완료되면 *로그인했습니다.* 라는 메시지를 반환합니다.

## <a name="create-trust-between-aks-cluster-and-acr"></a>AKS 클러스터와 ACR 사이의 트러스트 만들기

또한 AKS 클러스터는 컨테이너 이미지를 끌어와 실행하기 위해 ACR에 액세스해야 합니다. 트러스트를 설정하여 AKS에서 ACR에 대한 액세스를 허용합니다. AKS 클러스터와 ACR 레지스트리 간에 트러스트를 설정하려면 AKS 클러스터에서 ACR 레지스트리에 액세스하는 데 사용하는 Azure Active Directory 서비스 주체에 대한 권한을 부여합니다. 다음 명령은 *MyResourceGroup의* *MyAkS* 클러스터의 서비스 주체에게 *MyResourceGroup의* *MyDraftACR* ACR에 대한 권한을 부여합니다.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

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

## <a name="create-a-service-account-for-helm"></a>Helm에 대한 서비스 계정 만들기

RBAC 지원 AKS 클러스터에서 Helm을 배포하려면 먼저 서비스 계정과 Tiller 서비스에 대한 역할 바인딩이 필요합니다. RBAC 지원 클러스터에서 Helm/Tiller를 보호하는 방법에 대한 자세한 내용은 [Tiller, 네임스페이스 및 RBAC][tiller-rbac]를 참조하세요. AKS 클러스터가 RBAC를 사용할 수 없는 경우 이 단계를 건너뜁니다.

`helm-rbac.yaml`이라는 파일을 만들고 다음 YAML에 복사합니다.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl apply` 명령을 사용하여 서비스 계정 및 역할 바인딩을 만듭니다.

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="configure-helm"></a>Helm 구성
기본 Tiller를 AKS 클러스터에 배포하려면 [helm init][helm-init] 명령을 사용합니다. 클러스터가 RBAC를 사용할 수 없는 `--service-account` 경우 인수및 값을 제거합니다.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Draft 구성

로컬 컴퓨터에서 초안을 구성하지 않은 경우 다음을 실행합니다. `draft init`

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

또한 ACR의 *로그인 Server를* 사용하도록 초안을 구성해야 합니다. 다음 명령은 `draft config set` 레지스트리로 `mydraftacr.azurecr.io` 사용하는 데 사용됩니다.

```console
draft config set registry mydraftacr.azurecr.io
```

ACR을 사용하도록 초안을 구성했으며, Draft는 컨테이너 이미지를 ACR에 푸시할 수 있습니다. 초안이 AKS 클러스터에서 응용 프로그램을 실행하는 경우 ACR 레지스트리를 푸시하거나 가져올 때 암호나 암호가 필요하지 않습니다. AKS 클러스터와 ACR 간에 트러스트가 만들어졌기 때문에 Azure Active Directory를 사용하여 Azure 리소스 관리자 수준에서 인증이 수행됩니다.

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 [시작은 초안 GitHub 리포지토리의 예제 Java 응용 프로그램을][example-java]사용합니다. GitHub에서 응용 프로그램을 복제하고 `draft/examples/example-java/` 디렉터리로 이동합니다.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>초안으로 샘플 응용 프로그램 실행

`draft create` 명령을 사용하여 응용 프로그램을 준비합니다.

```console
draft create
```

이 명령은 Kubernetes 클러스터에서 애플리케이션을 실행하는 데 사용되는 아티팩트를 만듭니다. 이러한 항목에는 Dockerfile, Helm 차트 포함 및 Draft 구성 파일인 *draft.toml* 파일이 포함됩니다.

```console
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

AKS 클러스터에서 샘플 애플리케이션을 실행하려면 `draft up` 명령을 사용합니다.

```console
draft up
```

이 명령은 Dockerfile을 빌드하여 컨테이너 이미지를 만들고 이미지를 ACR로 푸시한 다음 Helm 차트를 설치하여 AKS에서 응용 프로그램을 시작합니다. 이 명령을 처음 실행하면 컨테이너 이미지를 밀고 당기는 데 다소 시간이 걸릴 수 있습니다. 기본 계층이 캐시되면 애플리케이션을 배포하는 데 걸리는 시간이 크게 줄어듭니다.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>로컬 컴퓨터에서 실행 중인 샘플 응용 프로그램에 연결

애플리케이션을 테스트하려면 `draft connect` 명령을 사용합니다.

```console
draft connect
```

이 명령은 Kubernetes Pod로의 보안 연결을 프록시합니다. 완료되면 제공된 URL에서 애플리케이션에 액세스할 수 있습니다.

```console
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

`localhost` URL을 사용하여 브라우저에서 응용 프로그램으로 이동하여 샘플 응용 프로그램을 확인합니다. 위의 예에서 URL은 `http://localhost:49804`. 을 사용하여 `Ctrl+c`연결을 중지합니다.

## <a name="access-the-application-on-the-internet"></a>인터넷의 애플리케이션 액세스

이전 단계에서는 AKS 클러스터에 애플리케이션 pod에 대한 프록시 연결을 만들었습니다. 애플리케이션을 개발하고 테스트할 때는 인터넷에서 애플리케이션을 사용할 수 있도록 하는 것이 좋습니다. 인터넷에서 응용 프로그램을 노출하려면 [LoadBalancer][kubernetes-service-loadbalancer]의 유형으로 Kubernetes 서비스를 만들 수 있습니다.

로드밸슬러 서비스를 만들려면 `charts/example-java/values.yaml` *업데이트합니다.* *Service.type* 값을 *ClusterIP에서* *로드밸런서로*변경합니다.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

변경 내용을 저장하고 파일을 닫은 다음 실행하여 `draft up` 응용 프로그램을 다시 실행합니다.

```console
draft up
```

서비스가 공인 IP 주소를 반환하는 데 몇 분이 걸릴 수 있습니다. 진행률을 모니터링하려면 `kubectl get service` 명령과 *watch* 매개 변수를 함께 사용합니다.

```console
$ kubectl get service --watch

NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
...
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

*외부 IP를* 사용하여 브라우저에서 응용 프로그램의 로드 밸러터로 이동하여 샘플 응용 프로그램을 확인합니다. 위의 예에서 IP는 `52.175.224.118`.

## <a name="iterate-on-the-application"></a>애플리케이션 반복

로컬에서 변경하고 다시 실행하여 `draft up`응용 프로그램을 반복할 수 있습니다.

[SRC/메인/자바/helloworld/Hello.java의 7호선에][example-java-hello-l7] 반환된 메시지를 업데이트합니다.

```java
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
```

`draft up` 명령을 실행하여 애플리케이션을 재배포합니다.

```console
$ draft up

Draft Up Started: 'example-java': 01CMZC9RF0TZT7XPWGFCJE15X4
example-java: Building Docker Image: SUCCESS ⚓  (25.0202s)
example-java: Pushing Docker Image: SUCCESS ⚓  (7.1457s)
example-java: Releasing Application: SUCCESS ⚓  (3.5773s)
Inspect the logs with `draft logs 01CMZC9RF0TZT7XPWGFCJE15X4`
```

업데이트된 응용 프로그램을 보려면 로드 밸러터의 IP 주소로 다시 이동하여 변경 사항이 나타나는지 확인합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

클러스터가 더 이상 필요하지 않으면 [az 그룹 삭제][az-group-delete] 명령을 사용하여 리소스 그룹, AKS 클러스터, 컨테이너 레지스트리, 거기에 저장된 컨테이너 이미지 및 모든 관련 리소스를 제거합니다.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리되는 ID를 사용한 경우 ID는 플랫폼에서 관리되며 제거할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

Draft 사용에 대한 자세한 내용은 Github의 Draft 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Draft 설명서][draft-documentation]


[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-create]: /cli/azure/acr#az-acr-login
[az-group-delete]: /cli/azure/group#az-group-delete
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[kubernetes-ingress]: ./ingress-basic.md

[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[example-java]: https://github.com/Azure/draft/tree/master/examples/example-java
[example-java-hello-l7]: https://github.com/Azure/draft/blob/master/examples/example-java/src/main/java/helloworld/Hello.java#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
