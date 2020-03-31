---
title: Azure Kubernetes 서비스에서 RBAC 및 Azure AD를 통해 클러스터 리소스 제어
description: Azure Active Directory 그룹 구성원을 사용하여 AKS(Azure Kubernetes Service)의 역할 기반 액세스 제어(RBAC)를 사용하여 클러스터 리소스에 대한 액세스를 제한하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 456b6dcdd590b48e06c830db85b726d4bebb69e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596524"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Azure Kubernetes 서비스에서 역할 기반 액세스 제어 및 Azure Active Directory ID를 사용하여 클러스터 리소스에 대한 액세스를 제어합니다.

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용하여 AKS 클러스터에 로그인합니다. 사용자의 ID 또는 그룹 구성원 자격을 기반으로 클러스터 리소스에 대한 액세스를 제한하도록 Kubernetes 역할 기반 액세스 제어(RBAC)를 구성할 수도 있습니다.

이 문서에서는 Azure AD 그룹 구성원을 사용하여 AKS 클러스터의 Kubernetes RBAC를 사용하여 네임스페이스 및 클러스터 리소스에 대한 액세스를 제어하는 방법을 보여 주며 있습니다. 예제 그룹 및 사용자는 Azure AD에서 생성된 다음 역할 및 역할 바인딩이 AKS 클러스터에서 생성되어 리소스를 만들고 볼 수 있는 적절한 권한을 부여합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure AD 통합을 사용하도록 설정된 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure Active Directory 와 AKS 통합을][azure-ad-aks-cli]참조하십시오.

Azure CLI 버전 2.0.61 이상 설치 및 구성이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하십시오.

## <a name="create-demo-groups-in-azure-ad"></a>Azure AD에서 데모 그룹 만들기

이 문서에서는 Kubernetes RBAC 및 Azure AD가 클러스터 리소스에 대한 액세스를 제어하는 방법을 보여 주는 데 사용할 수 있는 두 가지 사용자 역할을 만들어 보겠습니다. 다음 두 가지 예제 역할이 사용됩니다.

* **응용 프로그램 개발자**
    * *appdev* 그룹의 일부인 *aksdev라는* 사용자입니다.
* **현장 신뢰성 엔지니어**
    * opssre 그룹의 일부인 *akssre라는* 사용자입니다. *opssre*

프로덕션 환경에서 Azure AD 테넌트 내에서 기존 사용자 및 그룹을 사용할 수 있습니다.

먼저 [az aks show][az-aks-show] 명령을 사용하여 AKS 클러스터의 리소스 ID를 가져옵니다. 추가 명령에서 참조할 수 있도록 *AKS_ID라는* 변수에 리소스 ID를 할당합니다.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

[az ad][az-ad-group-create] 그룹 만들기 명령을 사용하여 응용 프로그램 개발자를 위한 Azure AD의 첫 번째 예제 그룹을 만듭니다. 다음 예제는 *appdev라는*그룹을 만듭니다.

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

이제 az 역할 할당 만들기 명령을 사용하여 *appdev* 그룹에 대한 Azure [역할 할당을 만듭니다.][az-role-assignment-create] 이 할당을 사용하면 그룹의 `kubectl` 모든 구성원이 Azure *Kubernetes 서비스 클러스터 사용자 역할을*부여하여 AKS 클러스터와 상호 작용할 수 있습니다.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`과 같은 오류가 발생하면 Azure AD 그룹 개체 ID가 디렉터리를 통해 전파될 때까지 `az role assignment create` 몇 초 간 기다린 다음 명령을 다시 시도합니다.

*opssre라는*이름의 SR에 대해 두 번째 예제 그룹을 만듭니다.

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

다시, Azure *Kubernetes 서비스 클러스터 사용자 역할*그룹의 구성원을 부여 하는 Azure 역할 할당을 만듭니다.

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Azure AD에서 데모 사용자 만들기

응용 프로그램 개발자와 SR을 위해 Azure AD에서 만든 두 개의 예제 그룹을 사용하여 이제 두 예제 사용자를 만들 수 있습니다. 문서의 끝에서 RBAC 통합을 테스트하려면 이러한 계정으로 AKS 클러스터에 로그인합니다.

az 광고 사용자 만들기 명령을 사용하여 Azure AD에서 첫 번째 사용자 계정을 [만듭니다.][az-ad-user-create]

다음 예제에서는 *AKS Dev* 의 표시 이름과 의 사용자 주체 `aksdev@contoso.com`이름(UPN)을 가진 사용자를 만듭니다. Azure AD 테넌트에 대해 확인된 도메인을 포함하도록 UPN을 업데이트하고(contoso.com 자체 `--password` 도메인으로 대체) 사용자 고유의 보안 자격 증명을 제공합니다. *contoso.com*

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

이제 [az ad group 구성원 추가][az-ad-group-member-add] 명령을 사용하여 이전 섹션에서 만든 *appdev* 그룹에 사용자를 추가합니다.

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

두 번째 사용자 계정을 만듭니다. 다음 예제에서는 디스플레이 이름 *AKS SRE* 와 의 사용자 주체 `akssre@contoso.com`이름(UPN)을 가진 사용자를 만듭니다. 또한 UPN을 업데이트하여 Azure AD 테넌트에 대해 확인된 도메인을 포함하고(contoso.com 자체 `--password` 도메인으로 대체) 고유한 보안 자격 증명을 제공합니다. *contoso.com*

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>앱 개발자를 위한 AKS 클러스터 리소스 만들기

이제 Azure AD 그룹 및 사용자가 만들어집니다. 그룹 구성원이 일반 사용자로 AKS 클러스터에 연결하기 위해 Azure 역할 할당이 만들어졌습니다. 이제 이러한 서로 다른 그룹이 특정 리소스에 액세스할 수 있도록 AKS 클러스터를 구성해 보겠습니다.

먼저 [az aks get-CREDENTIALs][az-aks-get-credentials] 명령을 사용하여 클러스터 관리자 자격 증명을 가져옵니다. 다음 섹션 중 하나에서 일반 *사용자* 클러스터 자격 증명을 사용하여 Azure AD 인증 흐름이 작동하는 지 확인할 수 있습니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

kubectl create 네임스페이스 명령을 사용하여 AKS 클러스터에서 [네임스페이스를 만듭니다.][kubectl-create] 다음 예제는 네임스페이스 이름 *dev를*만듭니다.

```console
kubectl create namespace dev
```

Kubernetes에서 *역할은* 부여할 권한을 정의하고 *역할 바인딩은* 원하는 사용자 또는 그룹에 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

먼저 *개발자* 네임스페이스에 대한 역할을 만듭니다. 이 역할은 네임스페이스에 대한 전체 권한을 부여합니다. 프로덕션 환경에서는 다른 사용자 또는 그룹에 대해 보다 세분화된 권한을 지정할 수 있습니다.

다음 YAML 매니페스트라는 파일을 `role-dev-namespace.yaml` 만들고 붙여넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
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

[kubectl apply][kubectl-apply] 명령을 사용하여 역할을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f role-dev-namespace.yaml
```

다음으로 [az 광고 그룹 표시][az-ad-group-show] 명령을 사용하여 *appdev* 그룹의 리소스 ID를 가져옵니다. 이 그룹은 다음 단계에서 역할 바인딩의 제목으로 설정됩니다.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

이제 *appdev* 그룹에 대한 역할 바인딩을 만들어 네임스페이스 액세스에 대해 이전에 만든 역할을 사용합니다. `rolebinding-dev-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄에서 *groupObjectId를* 이전 명령의 개체 ID 출력 그룹으로 바꿉꿉습니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
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

[kubectl apply][kubectl-apply] 명령을 사용하여 RoleBinding을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>SR에 대한 AKS 클러스터 리소스 만들기

이제 이전 단계를 반복하여 SR에 대한 네임스페이스, 역할 및 역할 바인딩을 만듭니다.

먼저 kubectl create 네임스페이스 명령을 사용하여 *sre에* 대한 [네임스페이스를 만듭니다.][kubectl-create]

```console
kubectl create namespace sre
```

다음 YAML 매니페스트라는 파일을 `role-sre-namespace.yaml` 만들고 붙여넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
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

[kubectl apply][kubectl-apply] 명령을 사용하여 역할을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f role-sre-namespace.yaml
```

[az 광고 그룹 표시][az-ad-group-show] 명령을 사용하여 *opssre* 그룹의 리소스 ID를 가져옵니다.

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

*opssre* 그룹에 대한 역할 바인딩을 만들어 네임스페이스 액세스에 대해 이전에 만든 역할을 사용합니다. `rolebinding-sre-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄에서 *groupObjectId를* 이전 명령의 개체 ID 출력 그룹으로 바꿉꿉습니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
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

[kubectl apply][kubectl-apply] 명령을 사용하여 RoleBinding을 만들고 YAML 매니페스트의 파일 이름을 지정합니다.

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Azure AD ID를 사용하여 클러스터 리소스와 상호 작용

이제 AKS 클러스터에서 리소스를 만들고 관리할 때 예상되는 사용 권한이 작동하는지 테스트해 보겠습니다. 이 예제에서는 사용자의 할당된 네임스페이스에서 창을 예약하고 봅니다. 그런 다음 할당된 네임스페이스 외부에서 창을 예약하고 봅니다.

먼저 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 *kubeconfig* 컨텍스트를 재설정합니다. 이전 섹션에서는 클러스터 관리자 자격 증명을 사용하여 컨텍스트를 설정합니다. 관리자 사용자가 Azure AD 로그인 프롬프트를 우회합니다. 매개 `--admin` 변수가 없으면 Azure AD를 사용하여 모든 요청을 인증해야 하는 사용자 컨텍스트가 적용됩니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

*dev* 네임스페이스에서 [kubectl 실행][kubectl-run] 명령을 사용하여 기본 NGINX 포드를 예약합니다.

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

로그인 프롬프트가 표시되면 문서 시작 `appdev@contoso.com` 시 만든 자신의 계정에 대한 자격 증명을 입력합니다. 성공적으로 로그인되면 계정 토큰은 이후 `kubectl` 명령을 위해 캐시됩니다. NGINX는 다음 예제 출력과 같이 성공적으로 예약됩니다.

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

이제 [kubectl get pods][kubectl-get] 명령을 사용하여 *개발자* 네임스페이스에서 포드를 봅니다.

```console
kubectl get pods --namespace dev
```

다음 예제 출력과 같이 NGINX 포드가 성공적으로 *실행되고*있습니다.

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>할당된 네임스페이스 외부에서 클러스터 리소스 생성 및 보기

이제 *개발자* 네임스페이스 외부에서 창을 봅니다. [kubectl 을][kubectl-get] 사용하여 포드 명령을 다시 다시 `--all-namespaces` 사용하십시오.

```console
kubectl get pods --all-namespaces
```

다음 예제 출력과 같이 사용자의 그룹 구성원에는 이 작업을 허용하는 Kubernetes 역할이 없습니다.

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

같은 방법으로 *sre* 네임스페이스와 같은 다른 네임스페이스에서 포드를 예약해 보십시오. 다음 예제 출력과 같이 사용자의 그룹 구성원 자격은 Kubernetes 역할 및 역할 바인딩과 일치하지 않아 다음 예제 출력과 같이 이러한 권한을 부여하지 않습니다.

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>AKS 클러스터 리소스에 대한 SRE 액세스 테스트

Azure AD 그룹 구성원 자격 및 Kubernetes RBAC가 서로 다른 사용자와 그룹 간에 올바르게 작동하는지 확인하려면 *opssre* 사용자로 로그인할 때 이전 명령을 사용해 보십시오.

*aksdev* 사용자에 대해 이전에 캐시된 인증 토큰을 지우는 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 *kubeconfig* 컨텍스트를 재설정합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

할당된 *스리네스페이스에서* 창을 예약하고 보려고 합니다. 메시지가 표시되면 문서 의 `opssre@contoso.com` 시작 부분에 만든 고유한 자격 증명으로 로그인합니다.

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

다음 예제 출력과 같이 성공적으로 만들고 창을 볼 수 있습니다.

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

이제 할당된 SRE 네임스페이스 외부에서 창을 보거나 예약해 보십시오.

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

다음 `kubectl` 예제 출력과 같이 이러한 명령은 실패합니다. 사용자의 그룹 구성원 자격 및 Kubernetes 역할 및 역할 바인딩은 다른 네임스페이스에서 리소스를 만들거나 관리자할 수 있는 권한을 부여하지 않습니다.

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 AKS 클러스터의 리소스와 Azure AD의 사용자 및 그룹을 만들었습니다. 이러한 모든 리소스를 정리하려면 다음 명령을 실행합니다.

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

Kubernetes 클러스터를 보호하는 방법에 대한 자세한 내용은 [AKS에 대한 액세스 및 ID 옵션을 참조하십시오.][rbac-authorization]

ID 및 리소스 제어에 대한 모범 사례는 [AKS의 인증 및 권한 부여에 대한 모범 사례를][operator-best-practices-identity]참조하십시오.

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
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
