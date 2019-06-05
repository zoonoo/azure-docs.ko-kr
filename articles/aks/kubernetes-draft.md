---
title: AKS 및 Azure Container Registry에서 Draft 사용
description: AKS 및 Azure Container Registry에서 Draft 사용
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 08/15/2018
ms.date: 05/13/2019
ms.author: v-yeche
ms.openlocfilehash: 462cfd6ec0a6b25f85dda0245dd4f5feed7cb712
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465162"
---
# <a name="use-draft-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Draft 사용

Draft는 Kubernetes 클러스터에 애플리케이션 컨테이너를 패키지하고 배포하도록 지원하면서 집중 개발의 "내부 루프" 곧 개발 주기에 자유롭게 집중하게 하는 오픈 소스 도구입니다. Draft는 코드가 개발되고 있을 때 그러나 버전 제어에 커밋하기 전에 작동합니다. Draft를 사용하면 코드가 변경될 때 Kubernetes에 애플리케이션을 신속하게 재배포할 수 있습니다. Draft에 대한 자세한 내용은 [GitHub의 Draft 설명서][draft-documentation]를 참조하세요.

이 문서에서는 AKS의 Kubernetes 클러스터에서 Draft를 사용하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 조건

이 문서에 설명된 단계에서는 AKS 클러스터를 만들고 클러스터와 `kubectl` 연결을 설정했다고 가정합니다. 이러한 항목이 필요한 경우 [AKS 빠른 시작][aks-quickstart]을 참조하세요.

ACR(Azure Container Registry)에 프라이빗 Docker 레지스트리가 필요합니다. ACR 인스턴스를 만드는 방법 단계는 [Azure Container Registry 빠른 시작][acr-quickstart]을 참조하세요.

또한 Helm을 AKS 클러스터에 설치해야 합니다. Helm을 설치 및 구성하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)에서 Helm 사용][aks-helm]을 참조하세요.

마지막으로 [Docker](https://www.docker.com)를 설치해야 합니다.

## <a name="install-draft"></a>Draft 설치

Draft CLI는 개발 시스템에서 실행되는 클라이언트로, Kubernetes 클러스터에 코드를 배포할 수 있습니다. Mac에서 Draft CLI를 설치하려면 `brew`를 사용합니다. 추가 설치 옵션에 대해서는 [Draft 설치 가이드][draft-documentation]를 참조하세요.

> [!NOTE]
> 버전 0.12 전에 Draft를 설치했다면 먼저 `helm delete --purge draft`을 사용하여 클러스터에서 Draft를 삭제한 다음, `rm -rf ~/.draft`를 실행하여 로컬 구성을 제거합니다. MacOS에서 작업하는 경우 `brew upgrade draft`를 실행합니다.

```console
brew tap azure/draft
brew install draft
```

이제 `draft init` 명령으로 Draft를 초기화합니다.

```console
draft init
```

## <a name="configure-draft"></a>Draft 구성

Draft는 컨테이너 이미지를 로컬에서 빌드한 다음, 로컬 레지스트리(예: Minikube)에서 이미지를 배포하거나, 사용자가 지정한 이미지 레지스트리를 사용합니다. 이 문서에서는 ACR(Azure Container Registry)를 사용함으로써 AKS 클러스터와 ACR 레지스트리 간에 트러스트 관계를 설정하고 Draft를 구성하여 컨테이너 이미지를 ACR로 푸시합니다.

### <a name="create-trust-between-aks-cluster-and-acr"></a>AKS 클러스터와 ACR 사이의 트러스트 만들기

AKS 클러스터와 ACR 레지스트리 간에 트러스트를 설정하려면 AKS 클러스터에서 ACR 레지스트리에 액세스하는 데 사용하는 Azure Active Directory 서비스 주체에 대한 권한을 부여합니다. 다음 명령에서 자체 `<resourceGroupName>`을 제공하고, `<aksName>`을 AKS 클러스터의 이름으로 바꾼 후 `<acrName>`을 ACR 레지스트리의 이름으로 바꿉니다.

```azurecli
# Get the service principal ID of your AKS cluster
AKS_SP_ID=$(az aks show --resource-group <resourceGroupName> --name <aksName> --query "servicePrincipalProfile.clientId" -o tsv)

# Get the resource ID of your ACR instance
ACR_RESOURCE_ID=$(az acr show --resource-group <resourceGroupName> --name <acrName> --query "id" -o tsv)

# Create a role assignment for your AKS cluster to access the ACR instance
az role assignment create --assignee $AKS_SP_ID --scope $ACR_RESOURCE_ID --role contributor
```

ACR에 액세스하기 위한 이러한 단계에 대한 자세한 내용은 [ACR을 사용하여 인증](../container-registry/container-registry-auth-aks.md)을 참조하세요.

### <a name="configure-draft-to-push-to-and-deploy-from-acr"></a>ACR에서 배포하고 푸시할 Draft 구성

AKS와 ACR 간에 트러스트 관계가 있으므로 AKS 클러스터에서 ACR을 사용하도록 설정합니다.

1. Draft 구성 설정 *레지스트리* 값을 설정합니다. 다음 명령에서 `<acrName>`을 ACR 레지스트리의 이름으로 바꿉니다.

    ```console
    draft config set registry <acrName>.azurecr.cn
    ```

1. [az acr login][az-acr-login]을 사용하여 ACR 레지스트리에 로그온합니다.

    ```azurecli
    az acr login --name <acrName>
    ```

AKS와 ACR 간에 트러스트가 형성되었으므로 ACR 레지스트리에서 밀어넣거나 끌어올 때 암호 또는 비밀이 필요하지 않습니다. Azure Active Directory를 사용하여 Azure Resource Manager 수준에서 인증이 발생합니다.

## <a name="run-an-application"></a>애플리케이션 실행

작동 중인 Draft를 보기 위해 [Draft 리포지토리][draft-repo]에서 샘플 애플리케이션을 배포해보겠습니다. 먼저 다음과 같이 리포지토리를 복제합니다.

```console
git clone https://github.com/Azure/draft
```

Java 예제 디렉터리로 변경합니다.

```console
cd draft/examples/example-java/
```

`draft create` 명령을 사용하여 프로세스를 시작합니다. 이 명령은 Kubernetes 클러스터에서 애플리케이션을 실행하는 데 사용되는 아티팩트를 만듭니다. 이러한 항목에는 Dockerfile, Helm 차트 포함 및 Draft 구성 파일인 *draft.toml* 파일이 포함됩니다.

```
$ draft create

--> Draft detected Java (92.205567%)
--> Ready to sail
```

AKS 클러스터에서 샘플 애플리케이션을 실행하려면 `draft up` 명령을 사용합니다. 이 명령은 Dockerfile을 빌드하여 컨테이너 이미지를 만들고 이미지를 ACR로 푸시하고 마지막으로 Helm 차트를 설치하여 AKS에서애플리케이션을 시작합니다.

처음으로 이 명령을 실행하면 컨테이너 이미지의 밀어넣기 및 끌어오기 작업에 다소 시간이 걸릴 수 있습니다. 기본 계층이 캐시되면 애플리케이션을 배포하는 데 걸리는 시간이 크게 줄어듭니다.

```
$ draft up

Draft Up Started: 'example-java': 01CMZAR1F4T1TJZ8SWJQ70HCNH
example-java: Building Docker Image: SUCCESS ⚓  (73.0720s)
example-java: Pushing Docker Image: SUCCESS ⚓  (19.5727s)
example-java: Releasing Application: SUCCESS ⚓  (4.6979s)
Inspect the logs with `draft logs 01CMZAR1F4T1TJZ8SWJQ70HCNH`
```

Docker 이미지 밀어넣기 중에 문제가 발생하는 경우 [az acr login][az-acr-login] 명령을 사용하여 ACR 레지스트리에 성공적으로 로그인되었는지 확인한 후 `draft up` 명령을 다시 실행합니다.

## <a name="test-the-application-locally"></a>로컬에서 애플리케이션 테스트

애플리케이션을 테스트하려면 `draft connect` 명령을 사용합니다. 이 명령은 Kubernetes Pod로의 보안 연결을 프록시합니다. 완료되면 제공된 URL에서 애플리케이션에 액세스할 수 있습니다.

> [!NOTE]
> 컨테이너 이미지를 다운로드하고 애플리케이션을 시작하는 데 몇 분이 걸릴 수 있습니다. 애플리케이션에 액세스할 때 오류가 발생하면 연결을 다시 시도합니다.

```
$ draft connect

Connect to java:4567 on localhost:49804
[java]: SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
[java]: SLF4J: Defaulting to no-operation (NOP) logger implementation
[java]: SLF4J: See https://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
[java]: == Spark has ignited ...
[java]: >> Listening on 0.0.0.0:4567
```

주소 및 포트에 지정 된 웹 브라우저를 열고 응용 프로그램에 액세스 하는 `draft connect` 와 같은 출력 `http://localhost:49804`합니다. 

![Draft를 사용하여 실행 중인 샘플 Java 앱](media/kubernetes-draft/sample-app.png)

`Control+C`를 사용하여 프록시 연결을 중지합니다.

> [!NOTE]
> `draft up --auto-connect` 명령을 사용하여 애플리케이션을 빌드 및 배포한 후, 실행 중인 첫 번째 컨테이너에 즉시 연결할 수 있습니다.

## <a name="access-the-application-on-the-internet"></a>인터넷의 애플리케이션 액세스

이전 단계에서는 AKS 클러스터에 애플리케이션 pod에 대한 프록시 연결을 만들었습니다. 애플리케이션을 개발하고 테스트할 때는 인터넷에서 애플리케이션을 사용할 수 있도록 하는 것이 좋습니다. 인터넷에서 애플리케이션에 노출하려면 [LoadBalancer][kubernetes-service-loadbalancer] 형식으로 Kubernetes 서비스를 만들거나 [수신 컨트롤러][kubernetes-ingress]를 만듭니다. *LoadBalancer* 서비스를 만들어보겠습니다.

먼저 *values.yaml* Draft 팩을 업데이트하여 *LoadBalancer* 형식의 서비스가 만들어지도록 지정합니다.

```console
vi charts/java/values.yaml
```

*service.type* 속성을 찾고 다음의 압축된 예제에 표시된 것처럼 해당 값을 *ClusterIP*에서 *LoadBalancer*로 업데이트합니다.

```yaml
[...]
service:
  name: java
  type: LoadBalancer
  externalPort: 80
  internalPort: 4567
[...]
```

파일을 저장한 후 닫고, `draft up`을 사용하여 애플리케이션을 다시 실행합니다.

```console
draft up
```

서비스가 공인 IP 주소를 반환하는 데 몇 분이 걸릴 수 있습니다. 진행률을 모니터링하려면 `kubectl get service` 명령과 *watch* 매개 변수를 함께 사용합니다.

```console
kubectl get service --watch
```

처음에는 서비스에 대한 *EXTERNAL-IP*가 *보류 중*으로 표시됩니다.

```
NAME                TYPE          CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
example-java-java   LoadBalancer  10.0.141.72   <pending>     80:32150/TCP   2m
```

EXTERNAL-IP 주소가 *보류 중*에서 IP 주소로 변경되면 `Control+C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다.

```
NAME                TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
example-java-java   LoadBalancer   10.0.141.72   52.175.224.118  80:32150/TCP   7m
```

애플리케이션을 보려면 `curl`을 사용하여 Load Balancer의 외부 IP 주소로 이동합니다.

```
$ curl 52.175.224.118

Hello World, I'm Java
```

## <a name="iterate-on-the-application"></a>애플리케이션 반복

이제 Draft가 구성되었고 애플리케이션이 Kubernetes에서 실행 중이므로 코드 반복에 대해 설정되었습니다. 업데이트된 코드를 테스트할 때마다 `draft up` 명령을 실행하여 실행 중인 애플리케이션을 업데이트합니다.

이 예제에서는 Java 샘플 애플리케이션을 업데이트하여 표시 텍스트를 변경합니다. *Hello.java* 파일을 엽니다.

```console
vi src/main/java/helloworld/Hello.java
```

표시할 출력 텍스트, *Hello World, I'm Java in AKS!* 를 업데이트합니다.

```java
package helloworld;

import static spark.Spark.*;

public class Hello {
    public static void main(String[] args) {
        get("/", (req, res) -> "Hello World, I'm Java in AKS!");
    }
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

업데이트된 애플리케이션을 확인하려면 Load Balancer의 IP 주소에 대해 curl을 다시 실행합니다.

```
$ curl 52.175.224.118

Hello World, I'm Java in AKS!
```

## <a name="next-steps"></a>다음 단계

Draft 사용에 대한 자세한 내용은 Github의 Draft 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Draft 설명서][draft-documentation]

<!-- LINKS - external -->
[draft-documentation]: https://github.com/Azure/draft/tree/master/docs
[kubernetes-service-loadbalancer]: https://kubernetes.io/docs/concepts/services-networking/service/#type-loadbalancer
[draft-repo]: https://github.com/Azure/draft

<!-- LINKS - internal -->
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-helm]: ./kubernetes-helm.md
[kubernetes-ingress]: ./ingress-basic.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[az-acr-login]: https://docs.azure.cn/zh-cn/cli/acr?view=azure-cli-latest#az-acr-login