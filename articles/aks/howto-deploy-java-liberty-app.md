---
title: AKS (Azure Kubernetes Service) 클러스터에서 Open Liberty 또는 WebSphere Liberty를 사용 하 여 Java 응용 프로그램 배포
description: AKS (Azure Kubernetes Service) 클러스터에서 Open Liberty 또는 WebSphere Liberty를 사용 하 여 Java 응용 프로그램을 배포 합니다.
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 02/01/2021
keywords: java, jakartaee, javaee, 마이크로 프로필, liberty, websphere-liberty, aks, kubernetes
ms.openlocfilehash: 93ffa3ded4d0771438c5d6a2dc23e6e184f04fe2
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99227630"
---
# <a name="deploy-a-java-application-with-open-liberty-or-websphere-liberty-on-an-azure-kubernetes-service-aks-cluster"></a>AKS (Azure Kubernetes Service) 클러스터에서 Open Liberty 또는 WebSphere Liberty를 사용 하 여 Java 응용 프로그램 배포

이 가이드에서는 open Liberty 또는 WebSphere Liberty 런타임에 Java, Java EE, [자카르타 ee](https://jakarta.ee/)또는 [마이크로 프로필](https://microprofile.io/) 응용 프로그램을 실행 한 다음 open Liberty 연산자를 사용 하 여 AKS 클러스터에 컨테이너 화 된 응용 프로그램을 배포 하는 방법을 보여 줍니다. Open Liberty 연산자는 Open Liberty Kubernetes 클러스터에서 실행 되는 응용 프로그램의 배포 및 관리를 간소화 합니다. 연산자를 사용 하 여 추적 및 덤프를 수집 하는 등의 고급 작업을 수행할 수도 있습니다. 이 문서에서는 Liberty 응용 프로그램을 준비 하 고, application Docker 이미지를 빌드하고, AKS 클러스터에서 컨테이너 화 된 응용 프로그램을 실행 하는 과정을 안내 합니다.  Open Liberty에 대 한 자세한 내용은 [Open Liberty project 페이지](https://openliberty.io/)를 참조 하세요. IBM WebSphere 대 한 자세한 내용은 Liberty [Websphere Liberty product 페이지](https://www.ibm.com/cloud/websphere-liberty)를 참조 하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 이 문서에는 최신 버전의 Azure CLI가 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.
* 이 가이드의 명령을 로컬로 실행 하는 경우 (Azure Cloud Shell 대신):
  * Unix와 비슷한 운영 체제가 설치 된 로컬 컴퓨터를 준비 합니다 (예: Ubuntu, macOS).
  * Java SE 구현 (예: [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9))을 설치 합니다.
  * [Maven](https://maven.apache.org/download.cgi) 3.5.0 이상을 설치 합니다.
  * OS에 대 한 [Docker](https://docs.docker.com/get-docker/) 를 설치 합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. *E미국* 위치에서 [az group create](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create) 명령을 사용 하 여 리소스 그룹 *liberty* 을 만듭니다. AKS (ACR) 인스턴스 및 나중에 클러스터를 Azure Container Registry 만드는 데 사용 됩니다. 

```azurecli-interactive
az group create --name java-liberty-project --location eastus
```

## <a name="create-an-acr-instance"></a>ACR 인스턴스 만들기

[Az acr create](/cli/azure/acr?view=azure-cli-latest&preserve-view=true#az_acr_create) 명령을 사용 하 여 acr 인스턴스를 만듭니다. 다음 예에서는 *youruniqueacrname* 라는 ACR 인스턴스를 만듭니다. *Youruniqueacrname* 가 Azure 내에서 고유한 지 확인 합니다.

```azurecli-interactive
az acr create --resource-group java-liberty-project --name youruniqueacrname --sku Basic --admin-enabled
```

잠시 후 다음을 포함 하는 JSON 출력이 표시 됩니다.

```output
  "provisioningState": "Succeeded",
  "publicNetworkAccess": "Enabled",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-acr-instance"></a>ACR 인스턴스에 연결

ACR 인스턴스에 이미지를 푸시 하려면 먼저 로그인 해야 합니다. 다음 명령을 실행 하 여 연결을 확인 합니다.

```azurecli-interactive
REGISTRY_NAME=youruniqueacrname
LOGIN_SERVER=$(az acr show -n $REGISTRY_NAME --query 'loginServer' -o tsv)
USER_NAME=$(az acr credential show -n $REGISTRY_NAME --query 'username' -o tsv)
PASSWORD=$(az acr credential show -n $REGISTRY_NAME --query 'passwords[0].value' -o tsv)

docker login $LOGIN_SERVER -u $USER_NAME -p $PASSWORD
```

`Login Succeeded`ACR 인스턴스에 성공적으로 로그인 한 경우 명령 출력의 끝에이 표시 됩니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

[az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create) 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *myAKSCluster* 라는 클러스터를 만듭니다. 이 작업을 완료하는 데는 몇 분 정도 걸립니다.

```azurecli-interactive
az aks create --resource-group java-liberty-project --name myAKSCluster --node-count 1 --generate-ssh-keys --enable-managed-identity
```

몇 분 후 명령이 완료 되 고 다음을 포함 하 여 클러스터에 대 한 JSON 형식 정보를 반환 합니다.

```output
  "nodeResourceGroup": "MC_java-liberty-project_myAKSCluster_eastus",
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "java-liberty-project",
```

### <a name="connect-to-the-aks-cluster"></a>AKS 클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 [az aks install-cli](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_install_cli) 명령을 사용합니다.

```azurecli-interactive
az aks install-cli
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials) 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group java-liberty-project --name myAKSCluster --overwrite-existing
```

> [!NOTE]
> 위의 명령은 [Kubernetes 구성 파일](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)의 기본 위치인 `~/.kube/config`를 사용합니다. *--file* 을 사용하여 Kubernetes 구성 파일의 다른 위치를 지정할 수 있습니다.

클러스터에 대한 연결을 확인하려면 [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 *준비* 인지 확인합니다.

```output
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-xxxxxxxx-yyyyyyyyyy   Ready    agent   76s     v1.18.10
```

## <a name="install-open-liberty-operator"></a>Open Liberty 연산자 설치

클러스터를 만들고 연결한 후 다음 명령을 실행 하 여 [Open Liberty 연산자](https://github.com/OpenLiberty/open-liberty-operator/tree/master/deploy/releases/0.7.0) 를 설치 합니다.

```azurecli-interactive
OPERATOR_NAMESPACE=default
WATCH_NAMESPACE='""'

# Install Custom Resource Definitions (CRDs) for OpenLibertyApplication
kubectl apply -f https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-crd.yaml

# Install cluster-level role-based access
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-cluster-rbac.yaml \
      | sed -e "s/OPEN_LIBERTY_OPERATOR_NAMESPACE/${OPERATOR_NAMESPACE}/" \
      | kubectl apply -f -

# Install the operator
curl -L https://raw.githubusercontent.com/OpenLiberty/open-liberty-operator/master/deploy/releases/0.7.0/openliberty-app-operator.yaml \
      | sed -e "s/OPEN_LIBERTY_WATCH_NAMESPACE/${WATCH_NAMESPACE}/" \
      | kubectl apply -n ${OPERATOR_NAMESPACE} -f -
```

## <a name="build-application-image"></a>응용 프로그램 이미지 빌드

AKS 클러스터에서 Liberty 응용 프로그램을 배포 하 고 실행 하려면 [Open Liberty container images](https://github.com/OpenLiberty/ci.docker) 또는 [websphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)를 사용 하 여 응용 프로그램을 Docker 이미지로 컨테이너 화.

1. 이 가이드에 대 한 샘플 코드를 복제 합니다. 이 샘플은 [GitHub](https://github.com/Azure-Samples/open-liberty-on-aks)에 있습니다.
1. `javaee-app-simple-cluster`로컬 클론의 디렉터리로 변경 합니다.
1. `mvn clean package`응용 프로그램을 패키지 하려면를 실행 합니다.
1. 다음 명령 중 하나를 실행 하 여 응용 프로그램 이미지를 빌드하고 ACR 인스턴스에 푸시합니다.
   * Open Liberty을 경량 오픈 소스 Java™ 런타임으로 사용 하려면 Open Liberty base 이미지를 사용 하 여 빌드 하세요.

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary Open Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME .
     ```

   * 상용 버전의 Open Liberty를 사용 하려는 경우 WebSphere Liberty base 이미지를 사용 하 여 빌드:

     ```azurecli-interactive
     # Build and tag application image. This will cause the ACR instance to pull the necessary WebSphere Liberty base images.
     az acr build -t javaee-cafe-simple:1.0.0 -r $REGISTRY_NAME --file=Dockerfile-wlp .
     ```

## <a name="deploy-application-on-the-aks-cluster"></a>AKS 클러스터에 응용 프로그램 배포

AKS 클러스터에 Liberty 응용 프로그램을 배포 하려면 아래 단계를 수행 합니다.

1. AKS 클러스터가 ACR 인스턴스에서 이미지를 가져오도록 인증 되도록 끌어오기 암호를 만듭니다.

   ```azurecli-interactive
   kubectl create secret docker-registry acr-secret \
      --docker-server=${LOGIN_SERVER} \
      --docker-username=${USER_NAME} \
      --docker-password=${PASSWORD}
   ```

1. 현재 작업 디렉터리가 `javaee-app-simple-cluster` 로컬 복제본 인지 확인 합니다.
1. 다음 명령을 실행 하 여 복제본이 3 개 있는 Liberty 응용 프로그램을 AKS 클러스터에 배포 합니다. 명령 출력도 인라인으로 표시 됩니다.

   ```azurecli-interactive
   # Create OpenLibertyApplication "javaee-app-simple-cluster"
   cat openlibertyapplication.yaml | sed -e "s/\${Container_Registry_URL}/${LOGIN_SERVER}/g" | sed -e "s/\${REPLICAS}/3/g" | kubectl apply -f -

   openlibertyapplication.openliberty.io/javaee-app-simple-cluster created

   # Check if OpenLibertyApplication instance is created
   kubectl get openlibertyapplication javaee-app-simple-cluster

   NAME                        IMAGE                                                   EXPOSED   RECONCILED   AGE
   javaee-app-simple-cluster   youruniqueacrname.azurecr.io/javaee-cafe-simple:1.0.0             True         59s

   # Check if deployment created by Operator is ready
   kubectl get deployment javaee-app-simple-cluster --watch

   NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-app-simple-cluster   0/3     3            0           20s
   ```

1. 열 아래에 표시 될 때까지 기다렸다가 `3/3` `READY` 열 아래에서를 `3` `AVAILABLE` 사용 `CTRL-C` 하 여 `kubectl` 조사식 프로세스를 중지 합니다.

### <a name="test-the-application"></a>애플리케이션 테스트

응용 프로그램이 실행 될 때 Kubernetes 부하 분산 장치 서비스는 응용 프로그램 프런트 엔드를 인터넷에 노출 합니다. 이 프로세스를 완료 하는 데 다소 시간이 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 명령을 사용합니다.

```azurecli-interactive
kubectl get service javaee-app-simple-cluster --watch

NAME                        TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
javaee-app-simple-cluster   LoadBalancer   10.0.251.169   52.152.189.57   9080:31732/TCP   68s
```

*외부 ip* 주소가 *보류 중* 에서 실제 공용 ip 주소로 변경 될 때까지 기다렸다가를 사용 `CTRL-C` 하 여 `kubectl` 조사 프로세스를 중지 합니다.

응용 프로그램 홈 페이지를 보려면 웹 브라우저에서 서비스의 외부 IP 주소와 포트 ( `52.152.189.57:9080` 위 예제)를 엽니다. 페이지의 왼쪽 위에 표시 되는 응용 프로그램 복제본의 pod 이름이 표시 됩니다. 몇 분 동안 기다린 후 페이지를 새로 고치면 AKS 클러스터에서 제공 하는 부하 분산으로 인해 다른 pod 이름이 표시 될 것입니다.

:::image type="content" source="./media/howto-deploy-java-liberty-app/java-liberty-app-aks-deployed-success.png" alt-text="AKS에 Java liberty 응용 프로그램이 배포 되었습니다.":::

>[!NOTE]
> - 현재 응용 프로그램에서 HTTPS를 사용 하 고 있지 않습니다. [사용자 고유의 인증서를 사용하여 TLS를 사용하도록 설정](ingress-own-tls.md)하는 것이 좋습니다.

## <a name="clean-up-the-resources"></a>리소스 정리

Azure 요금을 방지하려면 불필요한 리소스를 정리해야 합니다.  클러스터가 더 이상 필요 하지 않은 경우 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 명령을 사용 하 여 리소스 그룹, 컨테이너 서비스, 컨테이너 레지스트리 및 모든 관련 된 리소스를 제거 합니다.

```azurecli-interactive
az group delete --name java-liberty-project --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

이 가이드에 사용 되는 참조에서 자세히 알아볼 수 있습니다.

* [Azure Kubernetes Service](https://azure.microsoft.com/free/services/kubernetes-service/)
* [Liberty 열기](https://openliberty.io/)
* [Open Liberty 연산자](https://github.com/OpenLiberty/open-liberty-operator)
* [Liberty 서버 구성 열기](https://openliberty.io/docs/ref/config/)
* [Liberty Maven 플러그 인](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Liberty 컨테이너 이미지 열기](https://github.com/OpenLiberty/ci.docker)
* [WebSphere Liberty 컨테이너 이미지](https://github.com/WASdev/ci.docker)
