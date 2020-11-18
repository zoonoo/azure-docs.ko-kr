---
title: 클러스터에 Azure AD 및 Kubernetes RBAC 사용
titleSuffix: Azure Kubernetes Service
description: AKS (Azure Kubernetes Service)에서 Kubernetes (역할 기반 access control)를 사용 하 여 클러스터 리소스에 대 한 액세스를 제한 하는 Azure Active Directory 그룹 구성원 자격을 사용 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: f49e9f6b4f5aaf58ff055043b52cfe99e3e39f19
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684290"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Azure Kubernetes Service에서 Kubernetes 역할 기반 access control 및 Azure Active Directory id를 사용 하 여 클러스터 리소스에 대 한 액세스 제어

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 합니다. 또한 사용자의 id 또는 그룹 멤버 자격을 기반으로 클러스터 리소스에 대 한 액세스를 제한 하도록 Kubernetes (역할 기반 액세스 제어) Kubernetes RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다.

이 문서에서는 Azure AD 그룹 멤버 자격을 사용 하 여 AKS 클러스터에서 Kubernetes RBAC를 사용 하 여 네임 스페이스 및 클러스터 리소스에 대 한 액세스를 제어 하는 방법을 보여 줍니다. 예제 그룹 및 사용자가 Azure AD에 생성 된 다음, 리소스를 만들고 볼 수 있는 적절 한 권한을 부여 하기 위해 역할 및 RoleBindings가 AKS 클러스터에 생성 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 Azure AD 통합을 사용 하도록 설정 되어 있다고 가정 합니다. AKS 클러스터가 필요한 경우 [AKS와 Azure Active Directory 통합][azure-ad-aks-cli]을 참조 하세요.

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="create-demo-groups-in-azure-ad"></a>Azure AD에서 데모 그룹 만들기

이 문서에서는 Kubernetes RBAC 및 Azure AD에서 클러스터 리소스에 대 한 액세스를 제어 하는 방법을 보여 주는 데 사용할 수 있는 두 개의 사용자 역할을 만들어 보겠습니다. 다음 두 가지 예제 역할이 사용 됩니다.

* **애플리케이션 개발자**
    * *Appdev* 그룹의 일부인 *aksdev* 이라는 사용자입니다.
* **사이트 안정성 엔지니어**
    * *Opssre* 그룹의 일부인 *akssre* 이라는 사용자입니다.

프로덕션 환경에서는 Azure AD 테 넌 트 내에서 기존 사용자 및 그룹을 사용할 수 있습니다.

먼저 [az AKS show][az-aks-show] 명령을 사용 하 여 AKS 클러스터의 리소스 ID를 가져옵니다. 추가 명령에서 참조 될 수 있도록 *AKS_ID* 라는 변수에 리소스 ID를 할당 합니다.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

[Az AD group create][az-ad-group-create] 명령을 사용 하 여 응용 프로그램 개발자를 위한 Azure AD의 첫 번째 예제 그룹을 만듭니다. 다음 예제에서는 *appdev* 라는 그룹을 만듭니다.

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

이제 [az role 대입문 create][az-role-assignment-create] 명령을 사용 하 여 *appdev* 그룹에 대 한 Azure 역할 할당을 만듭니다. 이 할당을 사용 하면 그룹의 모든 멤버가 `kubectl` *Azure Kubernetes Service 클러스터 사용자 역할* 을 부여 하 여 AKS 클러스터와 상호 작용할 수 있습니다.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 와 같은 오류가 발생 하는 경우 `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` AZURE AD 그룹 개체 ID가 디렉터리를 통해 전파 될 때까지 몇 초 정도 기다린 후 `az role assignment create` 명령을 다시 시도 합니다.

이름이 *opssre* 인 sres에 대해 두 번째 예제 그룹을 만듭니다.

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

다시 Azure 역할 할당을 만들어 그룹 구성원에 게 *Azure Kubernetes Service 클러스터 사용자 역할* 을 부여 합니다.

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Azure AD에서 데모 사용자 만들기

이제 응용 프로그램 개발자 및 SREs에 대해 Azure AD에서 만든 두 가지 예제 그룹을 사용 하 여 두 예제 사용자를 만들 수 있습니다. 이 문서의 끝에서 Kubernetes RBAC 통합을 테스트 하려면 이러한 계정을 사용 하 여 AKS 클러스터에 로그인 합니다.

[Az AD user create][az-ad-user-create] 명령을 사용 하 여 Azure AD에서 첫 번째 사용자 계정을 만듭니다.

다음 예에서는 표시 이름 *AKS Dev* 및의 UPN (사용자 계정 이름)을 사용 하 여 사용자를 만듭니다 `aksdev@contoso.com` . Azure AD 테 넌 트에 대해 확인 된 도메인을 포함 하도록 UPN을 업데이트 하 고 ( *contoso.com* 를 사용자의 도메인으로 대체) 사용자 고유의 보안 자격 증명을 제공 합니다 `--password` .

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

이제 [az ad group member add][az-ad-group-member-add] 명령을 사용 하 여 이전 섹션에서 만든 *appdev* 그룹에 사용자를 추가 합니다.

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

두 번째 사용자 계정을 만듭니다. 다음 예에서는 표시 이름 *AKS SRE* 와의 UPN (사용자 계정 이름)을 사용 하 여 사용자를 만듭니다 `akssre@contoso.com` . 다시, Azure AD 테 넌 트에 대해 확인 된 도메인을 포함 하도록 UPN을 업데이트 하 고 ( *contoso.com* 를 사용자의 도메인으로 대체) 사용자 고유의 보안 자격 증명을 제공 합니다 `--password` .

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>앱 개발자에 대 한 AKS 클러스터 리소스 만들기

이제 Azure AD 그룹 및 사용자를 만들었습니다. AKS 클러스터에 일반 사용자로 연결 하기 위해 그룹 구성원에 대해 Azure 역할 할당을 만들었습니다. 이제 이러한 여러 그룹이 특정 리소스에 액세스할 수 있도록 AKS 클러스터를 구성 하겠습니다.

먼저 [az aks][az-aks-get-credentials] 명령을 사용 하 여 클러스터 관리자 자격 증명을 가져옵니다. 다음 섹션 중 하나에서 일반 *사용자* 클러스터 자격 증명을 가져와 Azure AD 인증 흐름이 작동 하는지 확인 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

[Kubectl create namespace][kubectl-create] 명령을 사용 하 여 AKS 클러스터에 네임 스페이스를 만듭니다. 다음 예제에서는 네임 스페이스 이름 *dev* 를 만듭니다.

```console
kubectl create namespace dev
```

Kubernetes에서 *역할* 은 부여할 사용 권한을 정의 하 고 *rolebindings* 는 원하는 사용자 또는 그룹에 해당 권한을 적용 합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [KUBERNETES RBAC 권한 부여 사용][rbac-authorization]을 참조 하세요.

먼저 *dev* 네임 스페이스에 대 한 역할을 만듭니다. 이 역할은 네임 스페이스에 대 한 모든 권한을 부여 합니다. 프로덕션 환경에서는 다른 사용자 또는 그룹에 대 한 보다 세부적인 사용 권한을 지정할 수 있습니다.

이라는 파일을 만들고 `role-dev-namespace.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 역할을 만들고 yaml 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f role-dev-namespace.yaml
```

다음으로 [az ad group show][az-ad-group-show] 명령을 사용 하 여 *appdev* 그룹에 대 한 리소스 ID를 가져옵니다. 이 그룹은 다음 단계에서 RoleBinding의 주체로 설정 됩니다.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

이제 네임 스페이스 액세스에 대해 이전에 만든 역할을 사용 하는 *appdev* 그룹에 대해 rolebinding을 만듭니다. `rolebinding-dev-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄에서 *groupObjectId*  을 이전 명령의 그룹 개체 ID 출력으로 바꿉니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 rolebinding을 만들고 yaml 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>SREs에 대 한 AKS 클러스터 리소스 만들기

이제 이전 단계를 반복 하 여 SREs에 대 한 네임 스페이스, 역할 및 RoleBinding을 만듭니다.

먼저 [kubectl create namespace][kubectl-create] 명령을 사용 하 여 *sre* 에 대 한 네임 스페이스를 만듭니다.

```console
kubectl create namespace sre
```

이라는 파일을 만들고 `role-sre-namespace.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 역할을 만들고 yaml 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f role-sre-namespace.yaml
```

[Az ad group show][az-ad-group-show] 명령을 사용 하 여 *opssre* 그룹에 대 한 리소스 ID를 가져옵니다.

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

네임 스페이스 액세스에 이전에 만든 역할을 사용 하려면 *opssre* 그룹에 대해 rolebinding을 만듭니다. `rolebinding-sre-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄에서 *groupObjectId*  을 이전 명령의 그룹 개체 ID 출력으로 바꿉니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 rolebinding을 만들고 yaml 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Azure AD id를 사용 하 여 클러스터 리소스와 상호 작용

이제 AKS 클러스터에서 리소스를 만들고 관리할 때 예상 되는 사용 권한을 테스트 하겠습니다. 이 예에서는 사용자의 할당 된 네임 스페이스에서 pod를 예약 하 고 확인 합니다. 그런 다음, 할당 된 네임 스페이스 외부에서 pod 일정을 예약 하 고 확인 하려고 합니다.

먼저 [az aks get 자격 증명][az-aks-get-credentials] 명령을 사용 하 여 *kubeconfig* 컨텍스트를 다시 설정 합니다. 이전 섹션에서는 클러스터 관리자 자격 증명을 사용 하 여 컨텍스트를 설정 합니다. 관리 사용자는 Azure AD 로그인 프롬프트를 무시 합니다. `--admin`매개 변수를 사용 하지 않으면 AZURE AD를 사용 하 여 모든 요청을 인증 해야 하는 사용자 컨텍스트가 적용 됩니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

*Dev* 네임 스페이스의 [kubectl run][kubectl-run] 명령을 사용 하 여 기본 NGINX pod를 예약 합니다.

```console
kubectl run nginx-dev --image=nginx --namespace dev
```

로그인 프롬프트로, `appdev@contoso.com` 문서의 시작 부분에서 만든 사용자 계정에 대 한 자격 증명을 입력 합니다. 성공적으로 로그인 되 면 이후 명령에 대해 계정 토큰이 캐시 됩니다 `kubectl` . 다음 예제 출력과 같이 NGINX이 성공적으로 예약 됩니다.

```console
$ kubectl run nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

이제 [kubectl get pod][kubectl-get] 명령을 사용 하 여 *dev* 네임 스페이스의 pod를 확인 합니다.

```console
kubectl get pods --namespace dev
```

다음 예제 출력과 같이 NGINX pod가 성공적으로 *실행* 되 고 있습니다.

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>할당 된 네임 스페이스 외부에서 클러스터 리소스 만들기 및 보기

이제 *dev* 네임 스페이스 외부에서 pod를 보려고 시도 합니다. 이번에는 [kubectl get pod][kubectl-get] 명령을 다시 사용 하 여 다음과 같이 표시 합니다 `--all-namespaces` .

```console
kubectl get pods --all-namespaces
```

사용자의 그룹 멤버 자격에는 다음 예제 출력과 같이이 작업을 허용 하는 Kubernetes 역할이 없습니다.

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

동일한 방식으로 *sre* 네임 스페이스와 같은 다른 네임 스페이스의 pod를 예약 하려고 합니다. 다음 예제 출력과 같이 사용자의 그룹 멤버 자격은 Kubernetes 역할 및 RoleBinding과 일치 하 여 이러한 권한을 부여 하지 않습니다.

```console
$ kubectl run nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>AKS 클러스터 리소스에 대 한 SRE 액세스 테스트

Azure AD 그룹 멤버 자격 및 Kubernetes RBAC가 서로 다른 사용자와 그룹 간에 올바르게 작동 하는지 확인 하려면 *opssre* 사용자로 로그인 한 경우 이전 명령을 사용해 보세요.

*Aksdev* 사용자에 대해 이전에 캐시 된 인증 토큰을 지우는 [az aks][az-aks-get-credentials] 명령을 사용 하 여 *kubeconfig* 컨텍스트를 다시 설정 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

할당 된 *sre* 네임 스페이스에서 pod을 예약 하 고 봅니다. 메시지가 표시 되 면 `opssre@contoso.com` 문서의 시작 부분에서 만든 자격 증명을 사용 하 여 로그인 합니다.

```console
kubectl run nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

다음 예제 출력과 같이 성공적으로 pod을 만들고 볼 수 있습니다.

```console
$ kubectl run nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

이제 할당 된 SRE 네임 스페이스의 외부에서 pod을 보거나 예약 해 봅니다.

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=nginx --namespace dev
```

`kubectl`다음 예제 출력에 표시 된 것 처럼 이러한 명령은 실패 합니다. 사용자의 그룹 멤버 자격 및 Kubernetes 역할 및 RoleBindings는 다른 네임 스페이스에서 리소스를 만들거나 관리자는 권한을 부여 하지 않습니다.

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Azure AD의 AKS 클러스터와 사용자 및 그룹에서 리소스를 만들었습니다. 이러한 모든 리소스를 정리 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 [AKS에 대 한 액세스 및 id 옵션][rbac-authorization]을 참조 하세요.

Id 및 리소스 제어에 대 한 모범 사례는 [AKS의 인증 및 권한 부여에 대 한 모범 사례][operator-best-practices-identity]를 참조 하세요.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
