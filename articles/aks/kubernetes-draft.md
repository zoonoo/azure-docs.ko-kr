---
title: Draft 사용 하 여 Azure Kubernetes Service (AKS)에서 개발
description: AKS 및 Azure Container Registry에서 Draft 사용
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/20/2019
ms.author: zarhoads
ms.openlocfilehash: bd099b9d76e17eda36be1650ef5081e5aaa7e53a
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303539"
---
# <a name="quickstart-develop-on-azure-kubernetes-service-aks-with-draft"></a>빠른 시작: Draft 사용 하 여 Azure Kubernetes Service (AKS)에서 개발

Draft는 패키지를 사용 하면 Kubernetes 클러스터에서 응용 프로그램 컨테이너를 실행 하는 오픈 소스 도구입니다. Draft를 사용 하 여 변경 내용을 버전 제어에 커밋할 필요가 없는 코드가 변경 될 때 Kubernetes에 응용 프로그램을 신속 하 게 배포할 수 있습니다. Draft에 대 한 자세한 내용은 참조는 [GitHub의 Draft 문서][draft-documentation]합니다.

이 문서에서는 초안 패키지를 사용 하 여 AKS에서 응용 프로그램을 실행 하는 방법을 보여 줍니다.


## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free)을 만들 수 있습니다.
* [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)
* Docker 설치 및 구성 합니다. Docker에서 Docker를 구성 하는 패키지를 제공 된 [Mac][docker-for-mac], [Windows][docker-for-windows], 또는 [Linux][linux 용 docker] 시스템입니다.
* [Helm 설치](https://github.com/helm/helm/blob/master/docs/install.md)합니다.
* [Draft 설치][draft-documentation]합니다.

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service 클러스터 만들기

AKS 클러스터를 만듭니다. 아래 명령을 호출 MyResourceGroup 이라는 MyAKS AKS 클러스터 리소스 그룹을 만듭니다.

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --node-vm-size Standard_DS2_v2 --node-count 1 --generate-ssh-keys
```

## <a name="create-an-azure-container-registry"></a>Azure Container Registry 만들기
초안을 AKS 클러스터에 응용 프로그램을 실행 하는 데 사용 하려면 Azure Container Registry에 컨테이너 이미지를 저장 해야 합니다. 아래 예제에서는 [az acr 만들기][az-acr-create] 명명 된 ACR을 만들려면 *MyDraftACR* 에 *MyResourceGroup* 사용 하 여 리소스 그룹을 *기본* SKU입니다. 자신의 고유한 레지스트리 이름을 제공 해야 합니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. *기본* SKU는 개발을 위해 비용에 최적화된 진입점으로, 저장소와 처리량의 균형을 적절하게 맞추었습니다.

```azurecli
az acr create --resource-group MyResourceGroup --name MyDraftACR --sku Basic
```

다음 예제와 유사하게 출력됩니다. 기록해는 *loginServer* 이후 단계에서 사용 되므로 ACR에 대 한 값입니다. 에 아래 예제에서는 *mydraftacr.azurecr.io* 은 합니다 *loginServer* 에 대 한 *MyDraftACR*합니다.

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


초안 ACR 인스턴스를 사용 하려면, 먼저에 로그인 해야 합니다. 사용 합니다 [az acr login][az-acr-login] 명령에 로그인 합니다. 아래 예제는에 로그인 하 라는 ACR *MyDraftACR*합니다.

```azurecli
az acr login --name MyDraftACR
```

이 명령이 완료되면 *로그인했습니다.* 라는 메시지를 반환합니다.

## <a name="create-trust-between-aks-cluster-and-acr"></a>AKS 클러스터와 ACR 사이의 트러스트 만들기

AKS 클러스터에 컨테이너 이미지를 실행 하 여 ACR에 액세스를 해야 합니다. 여 액세스를 허용 하 여 ACR을 AKS에서 트러스트를 설정 합니다. AKS 클러스터와 ACR 레지스트리 간에 트러스트를 설정하려면 AKS 클러스터에서 ACR 레지스트리에 액세스하는 데 사용하는 Azure Active Directory 서비스 주체에 대한 권한을 부여합니다. 서비스 주체에 권한을 부여 하는 다음 명령을 *MyAKS* 클러스터에 *MyResourceGroup* 에 *MyDraftACR* 에서 ACR를  *MyResourceGroup*합니다.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group MyResourceGroup --name MyAKS --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group MyResourceGroup --name MyDraftACR --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

## <a name="connect-to-your-aks-cluster"></a>AKS 클러스터에 연결

로컬 컴퓨터에서 Kubernetes 클러스터에 연결 하려면 사용 [kubectl][kubectl], Kubernetes 명령줄 클라이언트입니다.

Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. [az aks install-cli][] 명령을 사용하여 kubectl을 로컬로 설치할 수도 있습니다.

```azurecli
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][] 명령을 사용합니다. 다음 예제에서는 명명 된 AKS 클러스터에 대 한 자격 증명을 가져옵니다 *MyAKS* 에 *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

## <a name="create-a-service-account-for-helm"></a>Helm 용 서비스 계정 만들기

RBAC 지원 AKS 클러스터에서 Helm을 배포하려면 먼저 서비스 계정과 Tiller 서비스에 대한 역할 바인딩이 필요합니다. Helm을 보호 하는 방법은 Tiller를 RBAC에서 클러스터를 사용 / 참조 하세요 [Tiller, 네임 스페이스 및 RBAC][tiller-rbac]합니다. AKS 클러스터에서 사용 하도록 설정 하는 RBAC를 없는 경우이 단계를 건너뜁니다.

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
기본 Tiller AKS 클러스터를 배포 하려면 사용 합니다 [helm init][helm-init] 명령입니다. 클러스터에서 사용 하도록 설정 하는 RBAC를 없는 경우 제거 된 `--service-account` 인수 및 값입니다.

```console
helm init --service-account tiller --node-selectors "beta.kubernetes.io/os"="linux"
```

## <a name="configure-draft"></a>Draft 구성

초안을 로컬 컴퓨터에 구성 하지 않은 경우 실행 `draft init`:

```console
$ draft init
Installing default plugins...
Installation of default plugins complete
Installing default pack repositories...
...
Happy Sailing!
```

Draft를 사용 하 여 구성 해야 합니다 *loginServer* ACR의 합니다. 다음 명령을 사용 하 여 `draft config set` 데 `mydraftacr.azurecr.io` 레지스트리로 합니다.

```console
draft config set registry mydraftacr.azurecr.io
```

ACR을 사용 하는 초안 구성한 및 초안 ACR를 컨테이너 이미지를 푸시할 수 있습니다. AKS 클러스터에 응용 프로그램을 실행 하는 초안, 없습니다 암호 또는 암호를 밀어넣거나 가져올 ACR 레지스트리에서 필요 않습니다. AKS 클러스터와 ACR 간에 트러스트를 만든 후 Azure Active Directory를 사용 하 여 Azure Resource Manager 수준에서 인증이 발생 합니다.

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작에서는 [초안 GitHub 리포지토리에서 예제 java 응용 프로그램][example-java]합니다. GitHub에서 응용 프로그램을 복제 하 고 이동할는 `draft/examples/example-java/` 디렉터리입니다.

```console
git clone https://github.com/Azure/draft
cd draft/examples/example-java/
```

## <a name="run-the-sample-application-with-draft"></a>Draft를 사용 하 여 샘플 응용 프로그램을 실행 합니다.

사용 된 `draft create` 응용 프로그램을 준비 하는 명령입니다.

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

이 명령은 컨테이너 이미지를 만드는 Dockerfile을 빌드합니다 ACR에 이미지를 푸시 하며 AKS에서 응용 프로그램을 시작한 Helm 차트를 설치 합니다. 처음으로이 명령을 실행 하면 컨테이너 이미지를 푸시하고 약간의 시간이 걸릴 수 있습니다. 기본 계층이 캐시되면 애플리케이션을 배포하는 데 걸리는 시간이 크게 줄어듭니다.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

## <a name="connect-to-the-running-sample-application-from-your-local-machine"></a>실행과 연결 샘플 로컬 컴퓨터에서 응용 프로그램

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

사용 하 여 브라우저에서 응용 프로그램으로 이동 합니다 `localhost` 샘플 응용 프로그램을 확인 하는 url입니다. 위의 예제에서 url은 `http://localhost:49804`합니다. 연결 사용을 중지 `Ctrl+c`합니다.

## <a name="access-the-application-on-the-internet"></a>인터넷의 애플리케이션 액세스

이전 단계에서는 AKS 클러스터에 애플리케이션 pod에 대한 프록시 연결을 만들었습니다. 애플리케이션을 개발하고 테스트할 때는 인터넷에서 애플리케이션을 사용할 수 있도록 하는 것이 좋습니다. 인터넷에서 응용 프로그램에 노출 하려면 유형의 Kubernetes 서비스를 만들 수 있습니다 [LoadBalancer][kubernetes-service-loadbalancer]합니다.

업데이트 `charts/example-java/values.yaml` 만들려면를 *LoadBalancer* 서비스입니다. 값을 변경 *service.type* 에서 *클러스터 Ip* 하 *LoadBalancer*합니다.

```yaml
...
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
...
```

변경 내용을 저장 하 고 파일을 닫고 실행 `draft up` 응용 프로그램을 다시 실행 합니다.

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

사용 하 여 브라우저에서 응용 프로그램의 부하 분산 장치로 이동 합니다 *EXTERNAL-IP* 샘플 응용 프로그램을 확인 합니다. 위의 예제에서 IP는 `52.175.224.118`합니다.

## <a name="iterate-on-the-application"></a>애플리케이션 반복

로컬로 변경 하 고 다시 실행 하 여 응용 프로그램을 반복할 수 `draft up`입니다.

반환 되는 메시지를 업데이트 [src/main/java/helloworld/Hello.java 7 줄][example-java-hello-l7]

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

업데이트 된 응용 프로그램을 확인 하려면 부하 분산 장치의 IP 주소로 다시 이동 하 고 변경 내용이 표시를 확인 합니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

사용 하 여 클러스터를 더 이상 필요한 경우는 [az 그룹 삭제][az-group-delete] 명령 관련 된 모든 리소스 및 리소스 그룹, AKS 클러스터에서 컨테이너 레지스트리, 컨테이너 이미지를 제거 하려면 여기에 저장 합니다.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거 하는 방법에 대 한 단계를 참조 하세요 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]합니다.

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
[helm-init]: https://docs.helm.sh/helm/#helm-init
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
