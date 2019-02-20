---
title: AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 보호
description: AKS(Azure Kubernetes Service)에서 Kubernetes 네트워크 정책을 사용하여 pod 안팎으로 흐르는 트래픽을 보호하는 방법 알아보기
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232177"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호

Kubernetes에서 최신 마이크로 서비스 기반 애플리케이션을 실행할 경우 어느 구성 요소들이 서로 통신할 수 있는지 제어하고 싶을 때가 있습니다. AKS 클러스터의 pod 간에 트래픽이 흐르는 방법에 최소 권한 원칙을 적용해야 합니다. 예를 들어, 백 엔드 애플리케이션으로의 직접 트래픽을 차단할 수도 있습니다. Kubernetes에서 *네트워크 정책* 기능을 사용하여 클러스터의 pod 간 수신 및 송신 트래픽에 대한 규칙을 정의할 수 있습니다.

이 문서에서는 네트워크 정책을 사용하여 AKS에서 pod 간 트래픽 흐름을 제어하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.56 이상이 설치되고 구성되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="overview-of-network-policy"></a>네트워크 정책 개요

기본적으로 AKS 클러스터의 모든 pod는 트래픽을 제한 없이 송수신할 수 있습니다. 보안을 강화하기 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수 있습니다. 예를 들어, 백 엔드 애플리케이션은 필요한 프런트 엔드 서비스에만 자주 제공되고, 데이터베이스 구성 요소는 연결된 애플리케이션 계층에서만 액세스할 수 있습니다.

네트워크 정책은 pod 간 트래픽 흐름을 제어할 수 있는 Kubernetes 리소스입니다. 할당된 레이블, 네임스페이스 또는 트래픽 포트와 같은 설정에 따라 트래픽을 허용하거나 거부하도록 선택할 수 있습니다. 네트워크 정책은 YAML 매니페스트로 정의되고, 배포 또는 서비스도 만드는 보다 광범위한 매니페스트의 일부로 포함될 수 있습니다.

작동 중인 네트워크 정책을 보기 위해 다음과 같이 트래픽 흐름을 정의하는 정책을 만든 후 확장해 보겠습니다.

* pod에 대한 모든 트래픽을 거부합니다.
* pod 레이블을 기준으로 트래픽을 허용합니다.
* 네임스페이스를 기준으로 트래픽을 허용합니다.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS 클러스터 만들기 및 네트워크 정책 사용

네트워크 정책은 클러스터를 만든 경우에만 사용하도록 설정할 수 있습니다. 기존 AKS 클러스터에서는 네트워크 정책을 사용하도록 설정할 수 없습니다. 네트워크 정책을 사용하여 AKS를 만들려면 먼저 구독에 대해 기능 플래그를 사용하도록 설정합니다. *EnableNetworkPolicy* 기능 플래그를 등록하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용합니다.

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

AKS 클러스터에서 네트워크 정책을 사용하려면 [Azure CNI 플러그 인][azure-cni]을 사용하고 사용자 고유의 가상 네트워크 및 서브넷을 정의해야 합니다. 필요한 서브넷 범위를 계획하는 방법에 대한 자세한 내용은 [고급 네트워킹 구성][use-advanced-networking]을 참조하세요. 다음 예제 스크립트는 다음과 같은 작업을 수행합니다.

* 가상 네트워크 및 서브넷을 만듭니다.
* AKS 클러스터에서 사용할 Azure AD(Active Directory) 서비스 주체를 만듭니다.
* 가상 네트워크에서 AKS 클러스터 서비스 주체에 대해 *참가자* 권한을 할당합니다.
* 정의된 가상 네트워크에서 AKS 클러스터를 만들고 네트워크 정책을 사용하도록 설정합니다.

사용자 고유의 보안 *SP_PASSWORD*를 제공합니다. 원할 경우 *RESOURCE_GROUP_NAME* 및 *CLUSTER_NAME* 변수를 다음과 같이 바꿉니다.

```azurecli-interactive
SP_PASSWORD=mySecurePassword
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
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

클러스터를 만드는 데 몇 분이 걸립니다. 완료되면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 이 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>pod에 대한 모든 인바운드 트래픽 거부

특정 네트워크 트래픽을 허용하는 규칙을 정의하기 전에 먼저 모든 트래픽을 거부하는 네트워크 정책을 만듭니다. 이 정책은 원하는 트래픽만 허용 목록에 추가하기 위한 시작점을 제공합니다. 이 네트워크 정책을 적용하면 트래픽이 삭제되는 것을 명확하게 확인할 수 있습니다.

샘플 애플리케이션 환경 및 트래픽 규칙의 경우, *development*라는 네임스페이스를 만들어 예제 pod를 실행하겠습니다.

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

이제 NGINX를 실행하는 예제 백 엔드 pod를 만듭니다. 이 백 엔드 pod를 사용하여 샘플 백 엔드 웹 기반 애플리케이션을 시뮬레이트할 수 있습니다. *development* 네임스페이스에서 이 pod를 만들고 포트 *80*을 열어 웹 트래픽을 처리합니다. 다음 섹션에서 네트워크 정책의 대상으로 지정할 수 있도록 pod에 *app=webapp,role=backend* 레이블을 지정합니다.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

기본 NGINX 웹 페이지에 성공적으로 연결할 수 있는지 테스트하려면 다른 pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend
```

다음 샘플 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>네트워크 정책 만들기 및 적용

샘플 백 엔드 pod에서 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인했으므로 이제 모든 트래픽을 거부하는 네트워크 정책을 만들겠습니다. `backend-policy.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 이 매니페스트는 *podSelector*를 사용하여 *app:webapp,role:backend* 레이블이 있는 샘플 NGINX pod와 같은 pod에 해당 정책을 연결합니다. *ingress* 아래에 규칙이 정의되어 있지 않으므로 pod로의 모든 인바운드 트래픽이 거부됩니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>네트워크 정책 테스트

백 엔드 pod에서 NGINX 웹 페이지에 다시 액세스할 수 있는지 확인해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다. 이번에는 제한 시간 값을 *2*초로 설정합니다. 이제 네트워크 정책은 모든 인바운드 트래픽을 차단하므로 다음 예제와 같이 페이지를 로드할 수 없습니다.

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>pod 레이블을 기준으로 인바운드 트래픽 허용

이전 섹션에서는 백 엔드 NGINX pod를 예약했으며 모든 트래픽을 거부하는 네트워크 정책을 만들었습니다. 이제 프런트 엔드 pod를 만들고 프런트 엔드 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트해 보겠습니다.

*app:webapp,role:frontend* 레이블을 사용하여 네임스페이스에서 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트합니다. 이전의 *backend-policy.yaml* 파일을 편집하고 *matchLabels* 수신 규칙을 추가하여 매니페스트를 다음 예제와 같이 설정합니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 업데이트된 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

*app=webapp,role=frontend*로 레이블이 지정된 pod를 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend
```

수신 규칙이 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하므로 프런트 엔드 pod의 트래픽이 허용됩니다. 다음 예제 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. pod가 자동으로 삭제됩니다.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>일치하는 레이블이 없는 pod 테스트

네트워크 정책은 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하지만 다른 모든 트래픽을 거부합니다. 이러한 레이블이 없는 다른 pod가 백 엔드 NGINX pod에 액세스할 수 없는지 테스트해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다. 네트워크 정책은 인바운드 트래픽을 차단하므로 다음 예제와 같이 페이지를 로드할 수 없습니다.

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>정의된 네임스페이스 내의 트래픽만 허용

이전 예제에서는 모든 트래픽을 거부하는 네트워크 트래픽을 만든 후 특정 레이블이 있는 pod의 트래픽을 허용하도록 정책을 업데이트했습니다. 일반적으로 필요한 다른 요구 사항 한 가지는 지정된 네임스페이스 이내로만 트래픽을 제한하는 것입니다. 이전 예제가 *development* 네임스페이스의 트래픽용인 경우 다른 네임스페이스(예: *production*)의 트래픽이 pod에 도달하지 않도록 하는 네트워크 정책을 만들 수 있습니다.

먼저, 프로덕션 네임스페이스를 시뮬레이트하는 새 네임스페이스를 만듭니다.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* 레이블이 지정된 *production* 네임스페이스에서 테스트 pod를 예약합니다. 다음과 같이 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인합니다.

```console
wget -qO- http://backend.development
```

pod에 대한 레이블을 현재 네트워크 정책에서 허용되는 것과 일치하므로 트래픽이 허용됩니다. 네트워크 정책은 pod 레이블만 확인하고, 네임스페이스는 확인하지 않습니다. 다음 예제 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

### <a name="update-the-network-policy"></a>네트워크 정책 업데이트

이제 *development* 네임스페이스 내의 트래픽만 허용하도록 수신 규칙 *namespaceSelector* 섹션을 업데이트해 보겠습니다. 다음 예제와 같이 *backend-policy.yaml* 매니페스트 파일을 편집합니다.

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

좀 더 복잡한 예제에서 *namespaceSelector*를 사용한 다음 *podSelector*를 사용하는 경우처럼 여러 수신 규칙을 정의할 수 있습니다.

[kubectl apply][kubectl-apply] 명령을 사용하여 업데이트된 네트워크 정책을 적용하고 YAML 매니페스트의 이름을 지정합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>업데이트된 네트워크 정책 테스트

이제 *production* 네임스페이스에서 다른 pod를 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 네트워크 정책이 트래픽을 거부하는 것을 확인합니다.

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

테스트 pod를 종료합니다.

```console
exit
```

*production* 네임스페이스의 트래픽이 거부되므로, 이제 *development* 네임스페이스에서 테스트 pod를 다시 예약하고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`을 사용하여 네트워크 정책이 트래픽을 허용하는 것을 확인합니다.

```console
wget -qO- http://backend
```

네트워크 정책에서 허용되는 것과 일치하는 pod가 네임스페이스에서 예약되었으므로 트래픽이 허용됩니다. 다음 샘플 출력은 기본 NGINX 웹 페이지가 반환되었음을 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제됩니다.

```console
exit
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 두 네임스페이스를 만들고 네트워크 정책을 적용했습니다. 이러한 리소스를 정리하려면 [kubectl delete][kubectl-delete] 명령을 사용하고 다음과 같이 리소스 이름을 지정합니다.

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>다음 단계

네트워크 리소스에 대한 자세한 내용은 [애플리케이션에 대한 AKS(Azure Kubernetes Service)의 네트워크 개념][concepts-network]을 참조하세요.

정책을 사용하는 방법에 대한 자세한 내용은 [Kubernetes 네트워크 정책][kubernetes-network-policies]을 참조하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
