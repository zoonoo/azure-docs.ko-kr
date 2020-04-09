---
title: 네트워크 정책으로 포드 트래픽 보호
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service)에서 Kubernetes 네트워크 정책을 사용하여 포드 안팎으로 흐르는 트래픽을 보호하는 방법 알아보기
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 01ba9e7353b6783d1b4fd1649291a64405fd9382
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886707"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호

Kubernetes에서 최신 마이크로 서비스 기반 애플리케이션을 실행할 경우 어느 구성 요소들이 서로 통신할 수 있는지 제어하고 싶을 때가 있습니다. 최소 권한 원칙은 AKS(Azure Kubernetes Service) 클러스터의 포드 간에 트래픽이 흐르는 방식에 적용되어야 합니다. 백 엔드 응용 프로그램에 직접 트래픽을 차단하려는 경우를 가정해 보겠습니다. Kubernetes의 *네트워크 정책* 기능을 사용하면 클러스터의 포드 간에 유입 및 트래픽 이에 대한 규칙을 정의할 수 있습니다.

이 문서에서는 네트워크 정책 엔진을 설치하고 Kubernetes 네트워크 정책을 만들어 AKS의 포드 간 트래픽 흐름을 제어하는 방법을 보여 주며, 이를 보여 주시고 있습니다. 네트워크 정책은 AKS의 Linux 기반 노드 및 포드에만 사용해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.61 이상 설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

> [!TIP]
> 미리 보기 중에 네트워크 정책 기능을 사용한 경우 [새 클러스터를 만드는](#create-an-aks-cluster-and-enable-network-policy)것이 좋습니다.
> 
> 미리 보기 중에 네트워크 정책을 사용하는 기존 테스트 클러스터를 계속 사용하려면 클러스터를 최신 GA 릴리스의 새 Kubernetes 버전으로 업그레이드한 다음 다음 YAML 매니페스트를 배포하여 충돌하는 메트릭 서버 및 Kubernetes 대시보드를 수정합니다. 이 수정 프로그램은 Calico 네트워크 정책 엔진을 사용한 클러스터에만 필요합니다.
>
> 보안 모범 사례로 [이 YAML 매니페스트의 내용을 검토하여][calico-aks-cleanup] AKS 클러스터에 배포된 내용을 이해합니다.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>네트워크 정책 개요

AKS 클러스터의 모든 포드는 기본적으로 제한 없이 트래픽을 보내고 받을 수 있습니다. 보안을 강화하기 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수 있습니다. 백 엔드 응용 프로그램은 종종 필요한 프런트 엔드 서비스에만 노출됩니다. 또는 데이터베이스 구성 요소는 연결하는 응용 프로그램 계층에서만 액세스할 수 있습니다.

네트워크 정책은 포드 간의 통신에 대한 액세스 정책을 정의하는 Kubernetes 사양입니다. 네트워크 정책을 사용하여 트래픽을 보내고 받을 수 있도록 정렬된 규칙 집합을 정의하고 하나 이상의 레이블 선택기와 일치하는 포드 컬렉션에 적용합니다.

이러한 네트워크 정책 규칙은 YAML 매니페스트로 정의됩니다. 네트워크 정책은 배포 또는 서비스를 만드는 더 넓은 매니페스트의 일부로 포함될 수 있습니다.

### <a name="network-policy-options-in-aks"></a>AKS의 네트워크 정책 옵션

Azure는 네트워크 정책을 구현하는 두 가지 방법을 제공합니다. AKS 클러스터를 만들 때 네트워크 정책 옵션을 선택합니다. 클러스터를 만든 후에는 정책 옵션을 변경할 수 없습니다.

* Azure 자체 구현(Azure *네트워크 정책이라고*함).
* *칼리코 네트워크 정책*, [Tigera에][tigera]의해 설립 된 오픈 소스 네트워크 및 네트워크 보안 솔루션 .

두 구현 모두 Linux *IPTable을* 사용하여 지정된 정책을 적용합니다. 정책은 허용되고 허용되지 않는 IP 쌍 집합으로 변환됩니다. 그런 다음 이러한 쌍은 IPTable 필터 규칙으로 프로그래밍됩니다.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 및 Calico 정책과 해당 기능 간의 차이점

| 기능                               | Azure                      | 칼리코 (미국)                      |
|------------------------------------------|----------------------------|-----------------------------|
| 지원 플랫폼                      | Linux                      | Linux                       |
| 지원되는 네트워킹 옵션             | Azure CNI                  | Azure CNI 및 쿠베넷       |
| Kubernetes 사양 준수 | 지원되는 모든 정책 유형 |  지원되는 모든 정책 유형 |
| 추가 기능                      | None                       | 전역 네트워크 정책, 글로벌 네트워크 집합 및 호스트 끝점으로 구성된 확장된 정책 모델입니다. CLI를 `calicoctl` 사용하여 이러한 확장 된 기능을 관리하는 방법에 대한 자세한 내용은 [calicoctl 사용자 참조를][calicoctl]참조하십시오. |
| 지원                                  | Azure 지원 및 엔지니어링 팀 지원 | 칼리코 커뮤니티 지원. 추가 유료 지원에 대한 자세한 내용은 [프로젝트 Calico 지원 옵션을][calico-support]참조하십시오. |
| 로깅                                  | IPTable에 추가 /삭제된 규칙은 */var/log/azure-npm.log* 아래의 모든 호스트에 로그온됩니다. | 자세한 내용은 [Calico 구성 요소 로그를 참조하십시오.][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS 클러스터 만들기 및 네트워크 정책 사용

네트워크 정책이 작동하는 것을 보려면 트래픽 흐름을 정의하는 정책을 만든 다음 확장해 보겠습니다.

* pod에 대한 모든 트래픽을 거부합니다.
* pod 레이블을 기준으로 트래픽을 허용합니다.
* 네임스페이스를 기준으로 트래픽을 허용합니다.

먼저 네트워크 정책을 지원하는 AKS 클러스터를 만들어 보겠습니다. 

> [!IMPORTANT]
>
> 네트워크 정책 기능은 클러스터를 만들 때만 사용할 수 있습니다. 기존 AKS 클러스터에서는 네트워크 정책을 사용하도록 설정할 수 없습니다.

Azure 네트워크 정책을 사용하려면 [Azure CNI 플러그인을][azure-cni] 사용하고 고유한 가상 네트워크 및 서브넷을 정의해야 합니다. 필요한 서브넷 범위를 계획하는 방법에 대한 자세한 내용은 [고급 네트워킹 구성][use-advanced-networking]을 참조하세요. Calico 네트워크 정책은 동일한 Azure CNI 플러그인 또는 Kubenet CNI 플러그인과 함께 사용할 수 있습니다.

다음 예제 스크립트는 다음과 같은 작업을 수행합니다.

* 가상 네트워크 및 서브넷을 만듭니다.
* AKS 클러스터에 사용할 Azure Active Directory(Azure AD) 서비스 주체를 만듭니다.
* 가상 네트워크에서 AKS 클러스터 서비스 주체에 대해 *참가자* 권한을 할당합니다.
* 정의된 가상 네트워크에서 AKS 클러스터를 만들고 네트워크 정책을 활성화합니다.
    * *Azure* 네트워크 정책 옵션이 사용됩니다. 대신 Calico를 네트워크 정책 옵션으로 사용하려면 매개 변수를 `--network-policy calico` 사용합니다. 참고 : 칼리코는 중 `--network-plugin azure` 하나 `--network-plugin kubenet`또는 .

사용자 고유의 보안 *SP_PASSWORD*를 제공합니다. *RESOURCE_GROUP_NAME* 및 *CLUSTER_NAME* 변수를 바꿀 수 있습니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

클러스터를 만드는 데 몇 분이 걸립니다. 클러스터가 준비되면 az `kubectl` [aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 구성합니다. 이 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>pod에 대한 모든 인바운드 트래픽 거부

특정 네트워크 트래픽을 허용하는 규칙을 정의하기 전에 먼저 모든 트래픽을 거부하는 네트워크 정책을 만듭니다. 이 정책은 원하는 트래픽만 화이트리스트에 포함하기 시작하는 시작 지점을 제공합니다. 이 네트워크 정책을 적용하면 트래픽이 삭제되는 것을 명확하게 확인할 수 있습니다.

샘플 응용 프로그램 환경 및 트래픽 규칙의 경우 먼저 *개발이라는* 네임스페이스를 만들어 예제 포드를 실행해 보겠습니다.

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

NGINX를 실행하는 예제 백 엔드 포드를 만듭니다. 이 백 엔드 포드는 샘플 백 엔드 웹 기반 응용 프로그램을 시뮬레이션하는 데 사용할 수 있습니다. *development* 네임스페이스에서 이 pod를 만들고 포트 *80*을 열어 웹 트래픽을 처리합니다. 다음 섹션에서 네트워크 정책의 대상으로 지정할 수 있도록 pod에 *app=webapp,role=backend* 레이블을 지정합니다.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

다른 창을 만들고 터미널 세션을 연결하여 기본 NGINX 웹 페이지에 성공적으로 도달할 수 있는지 테스트합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다.

```console
wget -qO- http://backend
```

다음 샘플 출력은 기본 NGINX 웹 페이지가 반환됨을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은 것을 보여 주며 다음과 같은

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 창이 자동으로 삭제됩니다.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>네트워크 정책 만들기 및 적용

이제 샘플 백 엔드 포드에서 기본 NGINX 웹 페이지를 사용할 수 있음을 확인했습니다. `backend-policy.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 이 매니페스트는 *podSelector를* 사용하여 샘플 NGINX 포드와 같이 *앱:webapp, role:backend* 레이블이 있는 포드에 정책을 연결합니다. *ingress* 아래에 규칙이 정의되어 있지 않으므로 pod로의 모든 인바운드 트래픽이 거부됩니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

브라우저에서 [https://shell.azure.com](https://shell.azure.com) Azure 클라우드 셸을 열려면 이동합니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>네트워크 정책 테스트

백 엔드 포드에서 NGINX 웹 페이지를 다시 사용할 수 있는지 살펴보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다. 이번에는 제한 시간 값을 *2*초로 설정합니다. 이제 네트워크 정책은 모든 인바운드 트래픽을 차단하므로 다음 예제와 같이 페이지를 로드할 수 없습니다.

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 창이 자동으로 삭제됩니다.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>pod 레이블을 기준으로 인바운드 트래픽 허용

이전 섹션에서는 백 엔드 NGINX 포드가 예약되었으며 모든 트래픽을 거부하는 네트워크 정책이 만들어졌습니다. 프런트 엔드 포드를 만들고 네트워크 정책을 업데이트하여 프런트 엔드 포드의 트래픽을 허용해 보겠습니다.

*app:webapp,role:frontend* 레이블을 사용하여 네임스페이스에서 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트합니다. 이전 백 *엔드-policy.yaml* 파일을 편집하고 매니페스트가 다음 예제처럼 보이게 되도록 *matchLabels* 를 추가하는 규칙:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> 이 네트워크 정책은 수신 규칙에 대해 *namespaceSelector* 및 *podSelector* 요소를 사용합니다. YAML 구문은 받는 규칙이 추가되는 데 중요합니다. 이 예제에서 수신 규칙이 적용되려면 두 요소가 모두 일치해야 합니다. *1.12* 이전의 Kubernetes 버전은 이러한 요소를 올바르게 해석하지 못하고 예상대로 네트워크 트래픽을 제한할 수 있습니다. 이 동작에 대한 자세한 내용은 [선택기의 동작을][policy-rules]참조하십시오.

[kubectl apply][kubectl-apply] 명령을 사용하여 업데이트된 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f backend-policy.yaml
```

*app=webapp, role=frontend로* 레이블이 지정된 포드를 예약하고 터미널 세션을 첨부합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend
```

fromress 규칙은 레이블 앱이 있는 포드를 사용하여 트래픽을 허용하기 때문에 *웹앱, 역할: 프런트 엔드,* 프런트 엔드의 트래픽이 허용됩니다. 다음 예제 출력에는 반환된 기본 NGINX 웹 페이지가 표시됩니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 창이 자동으로 삭제됩니다.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>일치하는 레이블이 없는 pod 테스트

네트워크 정책은 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하지만 다른 모든 트래픽을 거부합니다. 이러한 레이블이 없는 다른 포드가 백 엔드 NGINX 포드에 액세스할 수 있는지 테스트해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다. 네트워크 정책은 인바운드 트래픽을 차단하므로 다음 예제와 같이 페이지를 로드할 수 없습니다.

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 창이 자동으로 삭제됩니다.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>정의된 네임스페이스 내의 트래픽만 허용

이전 예제에서는 모든 트래픽을 거부하는 네트워크 정책을 만든 다음 특정 레이블이 있는 포드의 트래픽을 허용하도록 정책을 업데이트했습니다. 또 다른 일반적인 요구는 지정된 네임스페이스 내에서만 트래픽을 제한하는 것입니다. 이전 예제가 *개발* 네임스페이스의 트래픽인 경우 *프로덕션과*같은 다른 네임스페이스의 트래픽이 포드에 도달하지 못하도록 하는 네트워크 정책을 만듭니다.

먼저, 프로덕션 네임스페이스를 시뮬레이트하는 새 네임스페이스를 만듭니다.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* 레이블이 지정된 *production* 네임스페이스에서 테스트 pod를 예약합니다. 다음과 같이 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다.

```console
wget -qO- http://backend.development
```

포드의 레이블이 네트워크 정책에서 현재 허용되는 레이블과 일치하므로 트래픽이 허용됩니다. 네트워크 정책은 pod 레이블만 확인하고, 네임스페이스는 확인하지 않습니다. 다음 예제 출력에는 반환된 기본 NGINX 웹 페이지가 표시됩니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 창이 자동으로 삭제됩니다.

```console
exit
```

### <a name="update-the-network-policy"></a>네트워크 정책 업데이트

*이제 다운로드* 규칙 *네임스페이스선택자* 섹션을 업데이트하여 개발 네임스페이스 내에서의 트래픽만 허용하도록 하겠습니다. 다음 예제와 같이 *backend-policy.yaml* 매니페스트 파일을 편집합니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

보다 복잡한 예제에서는 *네임스페이스선택자* 및 *podSelector*와 같은 여러 인거 규칙을 정의할 수 있습니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 업데이트된 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>업데이트된 네트워크 정책 테스트

*프로덕션* 네임스페이스에서 다른 포드를 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 네트워크 정책이 트래픽을 거부하는지 확인하는 데 사용합니다.

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

테스트 pod를 종료합니다.

```console
exit
```

*프로덕션* 네임스페이스에서 트래픽이 거부된 경우 *개발* 네임스페이스에서 테스트 포드를 다시 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget` 네트워크 정책으로 트래픽을 허용하는지 확인하는 데 사용합니다.

```console
wget -qO- http://backend
```

네트워크 정책에서 허용되는 것과 일치하는 네임스페이스에서 포드가 예약되어 있기 때문에 트래픽이 허용됩니다. 다음 샘플 출력에는 반환된 기본 NGINX 웹 페이지가 표시됩니다.

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 창이 자동으로 삭제됩니다.

```console
exit
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 두 개의 네임스페이스를 만들고 네트워크 정책을 적용했습니다. 이러한 리소스를 정리하려면 [kubectl delete][kubectl-delete] 명령을 사용하고 리소스 이름을 지정합니다.

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>다음 단계

네트워크 리소스에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)의 응용 프로그램에 대한 네트워크 개념을][concepts-network]참조하십시오.

정책에 대한 자세한 내용은 [Kubernetes 네트워크 정책을][kubernetes-network-policies]참조하십시오.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
