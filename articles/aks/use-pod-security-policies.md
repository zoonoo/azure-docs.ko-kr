---
title: AKS(Azure Kubernetes Service)에서 Pod 보안 정책 사용
description: AKS(Azure Kubernetes Service)에서 PodSecurityPolicy를 사용하여 Pod 허용을 제어하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d95cdb51136511bdd8529c829c3f680d19e14ba9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611772"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>미리 보기 - AKS(Azure Kubernetes Service)에서 Pod 보안 정책을 사용하여 클러스터 보호

> [!WARNING]
> **이 문서에서 설명하는 기능인 Pod 보안 정책(미리 보기)은 Kubernetes 버전 1.21에서 더 이상 사용되지 않으며, 1.25에서는 제거됩니다.** Kubernetes 업스트림이 이 마일스톤에 도달함에 따라 Kubernetes 커뮤니티는 실행 가능한 대안을 문서화하기 위해 노력하고 있습니다. 이전의 사용 중단은 고객에게 실행 가능한 옵션이 없어 당시에 발표되었습니다. 이제 Kubernetes 커뮤니티에서 대안을 찾고 있으므로 더 이상 Kubernetes보다 먼저 사용을 중단해야 할 필요가 없습니다.
>
> Pod 보안 정책(미리 보기)이 더 이상 사용되지 않는 경우 향후 클러스터 업그레이드를 수행하고 Azure 지원을 유지하려면 더 이상 사용되지 않는 기능을 사용하여 기존 클러스터에서 이 기능을 사용하지 않도록 설정해야 합니다.

AKS 클러스터의 보안을 향상시키기 위해 예약할 수 있는 Pod를 제한할 수 있습니다. 허용하지 않는 리소스를 요청하는 Pod는 AKS 클러스터에서 실행할 수 없습니다. Pod 보안 정책을 사용하여 이 액세스를 정의합니다. 이 문서에서는 Pod 보안 정책을 사용하여 AKS에서 Pod 배포를 제한하는 방법을 보여 줍니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Pod 보안 정책을 사용하려면 *aks-preview* CLI 확장 버전 0.4.1 이상이 필요합니다. [az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치한 후 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod 보안 정책 기능 공급자 등록

**이 문서와 기능은 2020년 10월 15일에 더 이상 사용되지 않도록 설정됩니다.**

Pod 보안 정책을 사용하도록 AKS 클러스터를 만들거나 업데이트하려면 먼저 구독에서 기능 플래그를 사용하도록 설정합니다. *PodSecurityPolicyPreview* 기능 플래그를 등록하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용합니다.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 보안 정책 개요

Kubernetes 클러스터에서 허용 컨트롤러는 리소스를 만들 때 API 서버에 대한 요청을 가로채는 데 사용됩니다. 그런 다음, 허용 컨트롤러는 규칙 세트에 대해 리소스 요청의 *유효성을 검사* 하거나 리소스를 *변경* 하여 배포 매개 변수를 변경할 수 있습니다.

*PodSecurityPolicy* 는 Pod 사양이 정의된 요구 사항을 충족하는지 확인하는 허용 컨트롤러입니다. 이러한 요구 사항에 따라 권한 있는 컨테이너의 사용, 특정 유형의 스토리지에 대한 액세스 또는 컨테이너를 실행할 수 있는 사용자 또는 그룹을 제한할 수 있습니다. Pod 사양이 Pod 보안 정책에 설명된 요구 사항을 충족하지 않는 리소스를 배포하려고 하면 요청이 거부됩니다. AKS 클러스터에서 예약할 수 있는 Pod를 제어하는 이 기능은 몇 가지 가능한 보안 취약성 또는 권한 에스컬레이션을 방지합니다.

AKS 클러스터에서 Pod 보안 정책을 사용하도록 설정하면 일부 기본 정책이 적용됩니다. 이러한 기본 정책은 예약할 수 있는 Pod를 정의하는 기본 제공 환경을 제공합니다. 그러나 클러스터 사용자에게 사용자 고유의 정책을 정의할 때까지 Pod를 배포하는 데 문제가 발생할 수 있습니다. 권장되는 방법은 다음과 같습니다.

* AKS 클러스터 만들기
* 사용자 고유의 Pod 보안 정책 정의
* Pod 보안 정책 기능 사용

기본 정책에서 Pod 배포를 제한하는 방법을 보여 주기 위해 이 문서에서는 먼저 Pod 보안 정책 기능을 사용하도록 설정한 다음, 사용자 지정 정책을 만듭니다.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Pod 보안 정책과 Azure Policy 간의 동작 변경

다음은 Pod 보안 정책과 Azure 정책 간의 동작 변경에 대한 요약입니다.

|시나리오| Pod 보안 정책 | Azure Policy |
|---|---|---|
|설치|Pod 보안 정책 기능을 사용하도록 설정합니다. |Azure Policy 추가 기능을 사용하도록 설정합니다.
|정책 배포| Pod 보안 정책 리소스를 배포합니다.| Azure 정책을 구독 또는 리소스 그룹 범위에 할당합니다. Kubernetes 리소스 애플리케이션에는 Azure Policy 추가 기능이 필요합니다.
| 기본 정책 | AKS에서 Pod 보안 정책을 사용하도록 설정하면 기본 권한 있음 및 제한 없음 정책이 적용됩니다. | Azure Policy 추가 기능을 사용하도록 설정하면 기본 정책이 적용되지 않습니다. Azure Policy에서 명시적으로 정책을 사용하도록 설정해야 합니다.
| 정책을 만들고 할당할 수 있는 사용자 | 클러스터 관리자가 Pod 보안 정책 리소스를 만듭니다. | 사용자에게 AKS 클러스터 리소스 그룹에 대한 '소유자' 또는 '리소스 정책 기여자' 권한의 최소 역할이 있어야 합니다. - API를 통해 사용자는 AKS 클러스터 리소스 범위에서 정책을 할당할 수 있습니다. 사용자에게 AKS 클러스터 리소스에 대한 최소 '소유자' 또는 '리소스 정책 기여자' 권한이 있어야 합니다. - Azure Portal에서 관리 그룹/구독/리소스 그룹 수준에서 정책을 할당할 수 있습니다.
| 정책 권한 부여| 사용자 및 서비스 계정에는 Pod 보안 정책을 사용하기 위한 명시적 권한이 필요합니다. | 정책에 대한 권한을 부여하는 데 추가 할당이 필요하지 않습니다. Azure에서 정책이 할당되면 모든 클러스터 사용자가 이러한 정책을 사용할 수 있습니다.
| 정책 적용 가능성 | 관리 사용자는 Pod 보안 정책의 적용을 무시합니다. | 모든 사용자(관리자 및 관리자가 아닌 사용자)에게 동일한 정책이 표시됩니다. 사용자를 기반으로 하는 특별한 대/소문자가 없습니다. 정책 애플리케이션은 네임스페이스 수준에서 제외할 수 있습니다.
| 정책 범위 | Pod 보안 정책은 네임스페이스로 지정되지 않습니다. | Azure Policy에서 사용하는 제약 조건 템플릿은 네임스페이스로 지정되지 않습니다.
| 거부/감사/변형 작업 | Pod 보안 정책은 거부 작업만 지원합니다. 변형은 만들기 요청에 대한 기본값을 사용하여 수행할 수 있습니다. 유효성 검사는 업데이트 요청 중에 수행할 수 있습니다.| Azure Policy는 감사 및 거부 작업을 모두 지원합니다. 변형은 아직 지원되지 않지만 계획되어 있습니다.
| Pod 보안 정책 준수 | Pod 보안 정책을 사용하도록 설정하기 전에 있었던 Pod 규정 준수에 대한 가시성이 없습니다. Pod 보안 정책을 하도록 설정한 후에 만든 비준수 Pod는 거부됩니다. | Azure 정책을 적용하기 전에 있었던 비준수 Pod는 정책 위반으로 표시됩니다. 정책이 거부 효과로 설정된 경우 Azure 정책을 사용하도록 설정한 후에 만든 비준수 Pod는 거부됩니다.
| 클러스터에서 정책을 보는 방법 | `kubectl get psp` | `kubectl get constrainttemplate` - 모든 정책이 반환됩니다.
| Pod 보안 정책 표준 - 권한 있음 | 이 기능을 사용하도록 설정하면 기본적으로 권한 있는 Pod 보안 정책 리소스가 만들어집니다. | 권한 있음 모드는 제한이 없음을 의미하므로 Azure Policy 할당이 없는 것과 같습니다.
| [Pod 보안 정책 표준 - 기준/기본값](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | 사용자가 Pod 보안 정책 기준 리소스를 설치합니다. | Azure Policy는 기준 Pod 보안 정책에 매핑되는 [기본 제공 이니셔티브](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d)를 제공합니다.
| [Pod 보안 정책 표준 - 제한됨](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | 사용자가 Pod 보안 정책 제한됨 리소스를 설치합니다. | Azure Policy는 제한됨 Pod 보안 정책에 매핑되는 [기본 제공 제한됨 이니셔티브](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)를 제공합니다.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS 클러스터에서 Pod 보안 정책 사용

[az aks update][az-aks-update] 명령을 사용하여 Pod 보안 정책을 사용하거나 사용하지 않도록 설정할 수 있습니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹의 *myAKSCluster* 클러스터 이름에 대한 Pod 보안 정책을 사용하도록 설정합니다.

> [!NOTE]
> 실제로 사용하려면 먼저 사용자 고유의 사용자 지정 정책을 정의한 후에 Pod 보안 정책을 사용하도록 설정하세요. 이 문서에서는 기본 정책이 Pod 배포를 제한하는 방법을 확인하는 첫 번째 단계로 Pod 보안 정책을 사용하도록 설정합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>기본 AKS 정책

Pod 보안 정책을 사용하도록 설정하면 AKS에서 *privileged* 라는 하나의 기본 정책을 만듭니다. 기본 정책을 편집하거나 제거하지 않습니다. 대신 제어하려는 설정을 정의하는 고유한 정책을 만듭니다. 먼저 이러한 기본 정책이 Pod 배포에 영향을 주는 방식을 살펴보겠습니다.

사용 가능한 정책을 보려면 다음 예제와 같이 [kubectl get psp][kubectl-get] 명령을 사용합니다.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*privileged* Pod 보안 정책은 AKS 클러스터에서 인증된 모든 사용자에게 적용됩니다. 이 할당은 ClusterRoles 및 ClusterRoleBindings를 통해 제어됩니다. [kubectl get rolebindings][kubectl-get] 명령을 사용하고, *kube-system* 네임스페이스에서 *default:privileged:* 바인딩을 검색합니다.

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

축약된 다음 출력과 같이 *psp:privileged* ClusterRole은 *system:authenticated* 사용자에게 할당됩니다. 이 기능은 사용자 고유의 정책을 정의하지 않고 기본 수준의 권한을 제공합니다.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

사용자 고유의 Pod 보안 정책 만들기를 시작하기 전에 이러한 기본 정책이 사용자 요청과 상호 작용하여 Pod를 예약하는 방법을 이해해야 합니다. 다음 몇 가지 섹션에서는 일부 Pod를 예약하여 이러한 기본 정책이 작동하는지 확인해 보겠습니다.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS 클러스터에서 테스트 사용자 만들기

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하는 경우 기본적으로 AKS 클러스터에 대한 *관리자* 자격 증명이 `kubectl` 구성에 추가됩니다. 관리 사용자는 Pod 보안 정책의 적용을 무시합니다. Azure Active Directory 통합을 AKS 클러스터에 사용하는 경우 관리자가 아닌 사용자의 자격 증명으로 로그인하여 작동 중인 정책의 적용을 확인할 수 있습니다. 이 문서에서는 AKS 클러스터에서 사용할 수 있는 테스트 사용자 계정을 만들어 보겠습니다.

[kubectl create namespace][kubectl-create] 네임스페이스 명령을 사용하여 테스트 리소스에 대해 *psp-aks* 라는 샘플 네임스페이스를 만듭니다. 그런 다음, [kubectl create serviceaccount][kubectl-create] 명령을 사용하여 *nonadmin-user* 라는 서비스 계정을 만듭니다.

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

다음으로 [kubectl create rolebinding][kubectl-create] 명령을 사용하여 네임스페이스에서 기본 작업을 수행할 *nonadmin-user* 에 대한 RoleBinding을 만듭니다.

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>관리자 및 관리자가 아닌 사용자에 대한 별칭 만들기 명령

`kubectl`을 사용할 때 일반 관리 사용자와 이전 단계에서 만든 관리자가 아닌 사용자의 차이를 강조 표시하려면 다음 두 개의 명령줄 별칭을 만듭니다.

* **kubectl-admin** 별칭은 일반 관리자에 대한 것이며, 범위는 *psp-aks* 네임스페이스로 지정됩니다.
* **kubectl-nonadminuser** 별칭은 이전 단계에서 만든 *nonadmin-user* 에 대한 것이며, 범위는 *psp-aks* 네임스페이스로 지정됩니다.

다음 명령과 같이 이러한 두 별칭을 만듭니다.

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>권한 있는 Pod 만들기 테스트

먼저 권한 있는 보안 컨텍스트(`privileged: true`)를 사용하여 Pod를 예약할 때 발생하는 결과를 테스트해 보겠습니다. 이 보안 컨텍스트는 Pod의 권한을 에스컬레이션합니다. 기본 AKS Pod 보안 정책을 보여 준 이전 섹션에서 *privilege* 정책은 이 요청을 거부해야 합니다.

`nginx-privileged.yaml`이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

다음 예제 출력과 같이 Pod가 예약되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod가 예약 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 Pod 만들기 테스트

이전 예제에서 Pod 사양은 권한 있는 에스컬레이션을 요청했습니다. 이 요청은 기본 *privilege* Pod 보안 정책에서 거부되므로 Pod가 예약되지 않습니다. 이제 권한 에스컬레이션 요청 없이 동일한 NGINX Pod를 실행해 보겠습니다

`nginx-unprivileged.yaml`이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

다음 예제 출력과 같이 Pod가 예약되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod가 예약 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>특정 사용자 컨텍스트를 사용하여 Pod 만들기 테스트

이전 예제에서 컨테이너 이미지는 자동으로 루트를 사용하여 NGINX를 80 포트에 바인딩하려고 했습니다. 이 요청은 기본 *privilege* Pod 보안 정책에서 거부되었으므로 Pod가 시작되지 않습니다. 이제 `runAsUser: 2000`과 같은 특정 사용자 컨텍스트를 사용하여 동일한 NGINX Pod를 실행해 보겠습니다.

`nginx-unprivileged-nonroot.yaml`이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

[kubectl apply][kubectl-apply] 명령을 사용하여 Pod를 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

다음 예제 출력과 같이 Pod가 예약되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod가 예약 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

## <a name="create-a-custom-pod-security-policy"></a>사용자 지정 Pod 보안 정책 만들기

이제 기본 Pod 보안 정책의 동작을 확인했으므로 *nonadmin-user* 가 Pod를 성공적으로 예약할 수 있는 방법을 제공하겠습니다.

권한 있는 액세스를 요청하는 Pod를 거부하는 정책을 만들어 보겠습니다. *runAsUser* 또는 허용된 *volumes* 와 같은 다른 옵션은 명시적으로 제한되지 않습니다. 이 유형의 정책은 권한 있는 액세스 요청을 거부하지만, 그렇지 않으면 클러스터에서 요청된 Pod를 실행할 수 있도록 합니다.

`psp-deny-privileged.yaml`이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

[kubectl apply][kubectl-apply] 명령을 사용하여 정책을 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f psp-deny-privileged.yaml
```

사용 가능한 정책을 보려면 다음 예제와 같이 [kubectl get psp][kubectl-get] 명령을 사용합니다. *psp-deny-privileged* 정책과 이전 예제에서 적용한 기본 *privilege* 정책을 비교하여 Pod를 만듭니다. 정책에 따라 *PRIV* 에스컬레이션 사용만 거부됩니다. *psp-deny-privileged* 정책에 대한 사용자 또는 그룹에는 제한이 없습니다.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>사용자 계정에서 사용자 지정 Pod 보안 정책을 사용하도록 허용

이전 단계에서는 권한 있는 액세스를 요청하는 Pod를 거부하는 Pod 보안 정책을 만들었습니다. 정책을 사용할 수 있도록 허용하려면 *Role* 또는 *ClusterRole* 을 만듭니다. 그런 다음, *RoleBinding* 또는 *ClusterRoleBinding* 을 사용하여 이러한 역할 중 하나를 연결합니다.

다음 예제에서는 이전 단계에서 만든 *psp-deny-privileged* 정책을 *사용* 할 수 있는 ClusterRole을 만듭니다. `psp-deny-privileged-clusterrole.yaml`이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

[kubectl apply][kubectl-apply] 명령을 사용하여 ClusterRole을 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

이제 이전 단계에서 만든 ClusterRole을 사용하는 ClusterRoleBinding을 만듭니다. `psp-deny-privileged-clusterrolebinding.yaml`이라는 파일을 만들고, 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

[kubectl apply][kubectl-apply] 명령을 사용하여 ClusterRoleBinding을 만들고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 이 문서의 첫 번째 단계에서 Pod 보안 정책 기능이 AKS 클러스터에서 사용하도록 설정되었습니다. 사용자 고유의 정책을 정의한 후에만 Pod 보안 정책 기능을 사용하도록 설정하는 것이 좋습니다. 이는 Pod 보안 정책 기능을 사용하도록 설정하는 단계입니다. 하나 이상의 사용자 지정 정책이 정의되었고, 사용자 계정이 해당 정책과 연결되었습니다. 이제 Pod 보안 정책 기능을 안전하게 사용하도록 설정하고, 기본 정책으로 인한 문제를 최소화할 수 있습니다.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>권한 없는 Pod 만들기 다시 테스트

사용자 지정 Pod 보안 정책이 적용되고 정책을 사용할 사용자 계정에 바인딩된 상태에서 권한 없는 Pod를 다시 만들어 보겠습니다. 동일한 `nginx-privileged.yaml` 매니페스트를 사용하여 [kubectl apply][kubectl-apply] 명령을 통해 Pod를 만듭니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod가 성공적으로 예약되었습니다. [kubectl get pods][kubectl-get] 명령을 사용하여 Pod의 상태를 확인하는 경우 Pod는 *실행 중* 상태입니다.

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

다음 예제에서는 여러 사용자 또는 그룹의 AKS 클러스터에 대한 액세스를 정의하는 사용자 지정 Pod 보안 정책을 만드는 방법을 보여 줍니다. 기본 AKS 정책은 실행할 수 있는 Pod에 대한 엄격한 제어를 제공하므로 사용자 고유의 사용자 지정 정책을 만든 다음, 필요한 제한을 올바르게 정의합니다.

[kubectl delete][kubectl-delete] 명령을 사용하여 NGINX 권한이 없는 Pod를 삭제하고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

Pod 보안 정책을 사용하지 않도록 설정하려면 [az aks update][az-aks-update] 명령을 다시 사용합니다. 다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹의 *myAKSCluster* 클러스터 이름에 대한 Pod 보안 정책을 사용하지 않도록 설정합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

다음으로 ClusterRole 및 ClusterRoleBinding을 삭제합니다.

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

[kubectl delete][kubectl-delete] 명령을 사용하여 보안 정책을 삭제하고, YAML 매니페스트의 이름을 지정합니다.

```console
kubectl delete -f psp-deny-privileged.yaml
```

마지막으로 *psp-aks* 네임스페이스를 삭제합니다.

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 권한 있는 액세스를 사용하지 않도록 방지하기 위한 Pod 보안 정책을 만드는 방법을 보여 주었습니다. 볼륨 유형 또는 RunAs 사용자와 같이 정책에서 적용할 수 있는 많은 기능이 있습니다. 사용 가능한 옵션에 대한 자세한 내용은 [Kubernetes Pod 보안 정책 참조 문서][kubernetes-policy-reference]를 참조하세요.

Pod 네트워크 트래픽을 제한하는 방법에 대한 자세한 내용은 [AKS에서 네트워크 정책을 사용하여 Pod 간 트래픽 보호][network-policies]를 참조하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
