---
title: Azure Kubernetes Service에서 pod 보안 정책 사용 (AKS)
description: AKS (Azure Kubernetes Service)에서 PodSecurityPolicy를 사용 하 여 pod 입학을 제어 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 77c618429503caf9aa7bb6abda109504bbf68d71
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695999"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes Service에서 pod 보안 정책을 사용 하 여 클러스터 보호 (AKS)

> [!WARNING]
> **이 문서 pod 보안 정책 (미리 보기)에 설명 된 기능은 사용 중단에 대해 설정** 되며, [AKS의 Azure Policy](use-pod-security-on-azure-policy.md)에 2021 년 5 월 31 일까 지 더 이상 사용할 수 없습니다. 사용 중단 날짜가 2020 년 10 월 15 일 이전 날짜에서 연장 되었습니다.
>
> Pod 보안 정책(미리 보기)이 더 이상 사용되지 않는 경우 향후 클러스터 업그레이드를 수행하고 Azure 지원을 유지하려면 더 이상 사용되지 않는 기능을 사용하여 기존 클러스터에서 이 기능을 사용하지 않도록 설정해야 합니다.
>
> AKS에 대 한 Azure Policy를 사용 하 여 시나리오 테스트를 시작 하는 것이 좋습니다 .이는 기본 제공 정책을 사용 하 여 pod를 보호 하 고 기본 제공 이니셔티브를 사용 하 여 pod 보안 정책에 매핑합니다. [Pod 보안 정책 (미리 보기)에서 Azure Policy로 마이그레이션하](use-pod-security-on-azure-policy.md#migrate-from-kubernetes-pod-security-policy-to-azure-policy)는 방법에 대 한 자세한 내용을 보려면 여기를 클릭 하세요.

AKS 클러스터의 보안을 향상 시키기 위해 예약할 수 있는 pod을 제한할 수 있습니다. 허용 하지 않는 리소스를 요청 하는 pod는 AKS 클러스터에서 실행할 수 없습니다. Pod 보안 정책을 사용 하 여이 액세스를 정의 합니다. 이 문서에서는 pod 보안 정책을 사용 하 여 AKS에서 pod의 배포를 제한 하는 방법을 보여 줍니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Pod 보안 정책을 사용 하려면 *aks-preview* CLI extension version 0.4.1 이상이 필요 합니다. [az extension add][az-extension-add] 명령을 사용하여 *aks-preview* Azure CLI 확장을 설치한 후 [az extension update][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod 보안 정책 기능 공급자 등록

**이 문서와 기능은 2020 년 10 월 15 일에 사용 중단에 대해 설정 됩니다.**

Pod 보안 정책을 사용 하도록 AKS 클러스터를 만들거나 업데이트 하려면 먼저 구독에서 기능 플래그를 사용 하도록 설정 합니다. *PodSecurityPolicyPreview* 기능 플래그를 등록 하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 합니다.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

상태가 *Registered* 로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 보안 정책 개요

Kubernetes 클러스터에서 허용 컨트롤러는 리소스를 만들 때 API 서버에 대 한 요청을 가로채는 데 사용 됩니다. 그러면 허용 컨트롤러는 규칙 집합에 대해 리소스 요청의 *유효성을 검사* 하거나 배포 매개 변수를 변경 하도록 *리소스를 변경할* 수 있습니다.

*PodSecurityPolicy* 는 pod 사양이 정의 된 요구 사항을 충족 하는지 확인 하는 허용 컨트롤러입니다. 이러한 요구 사항에 따라 권한 있는 컨테이너의 사용, 특정 유형의 저장소에 대 한 액세스 또는 컨테이너를 실행할 수 있는 사용자 또는 그룹을 제한할 수 있습니다. Pod 사양이 pod 보안 정책에 설명 된 요구 사항을 충족 하지 않는 리소스를 배포 하려고 하면 요청이 거부 됩니다. AKS 클러스터에서 예약할 수 있는 pod을 제어 하는이 기능은 몇 가지 가능한 보안 취약점 또는 권한 에스컬레이션을 방지 합니다.

AKS 클러스터에서 pod 보안 정책을 사용 하도록 설정 하면 일부 기본 정책이 적용 됩니다. 이러한 기본 정책은 예약할 수 있는 pod를 정의 하는 기본 경험을 제공 합니다. 그러나 사용자 고유의 정책을 정의할 때까지 클러스터 사용자가 pod를 배포 하는 데 문제가 발생할 수 있습니다. 권장 되는 방법은 다음과 같습니다.

* AKS 클러스터 만들기
* 사용자 고유의 pod 보안 정책 정의
* Pod 보안 정책 기능 사용

기본 정책의 제한 pod 배포를 표시 하기 위해이 문서에서는 먼저 pod 보안 정책 기능을 사용 하도록 설정한 다음 사용자 지정 정책을 만듭니다.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS 클러스터에서 pod 보안 정책 사용

[Az aks update][az-aks-update] 명령을 사용 하 여 pod 보안 정책을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 다음 예제에서는 *Myresourcegroup* 이라는 리소스 그룹의 클러스터 이름 *myAKSCluster* 에서 pod 보안 정책을 사용 하도록 설정 합니다.

> [!NOTE]
> 실제 사용의 경우 사용자 고유의 사용자 지정 정책을 정의할 때까지 pod 보안 정책을 사용 하도록 설정 하지 마세요. 이 문서에서는 기본 정책이 pod 배포를 제한 하는 방법을 확인 하는 첫 번째 단계로 pod 보안 정책을 사용 하도록 설정 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>기본 AKS 정책

Pod 보안 정책을 사용 하도록 설정 하는 경우 AKS은 *권한* 있는 이라는 기본 정책 하나를 만듭니다. 기본 정책을 편집 하거나 제거 하지 마세요. 대신 제어 하려는 설정을 정의 하는 고유한 정책을 만듭니다. 먼저 이러한 기본 정책이 pod 배포에 영향을 주는 방식을 살펴보겠습니다.

사용 가능한 정책을 보려면 다음 예제에 표시 된 것 처럼 [kubectl get psp][kubectl-get] 명령을 사용 합니다.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*권한* 있는 pod 보안 정책은 AKS 클러스터의 모든 인증 된 사용자에 게 적용 됩니다. 이 할당은 ClusterRoles 및 ClusterRoleBindings에 의해 제어 됩니다. [Kubectl get rolebindings][kubectl-get] 명령을 사용 하 여 *kube* 네임 스페이스에서 *default: 특권:* binding을 검색 합니다.

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

다음 압축 된 출력과 같이 *psp: 권한* 있는 ClusterRole는 모든 *시스템: 인증* 된 사용자에 게 할당 됩니다. 이 기능은 사용자 고유의 정책을 정의 하지 않고 기본적인 수준의 권한을 제공 합니다.

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

사용자 고유의 pod 보안 정책을 만들기 전에 이러한 기본 정책에서 pod 일정을 예약 하는 방법을 이해 하는 것이 중요 합니다. 다음 몇 개 섹션에서는 몇 가지 pod를 예약 하 여 이러한 기본 정책이 작동 하는 것을 확인 하겠습니다.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS 클러스터에서 테스트 사용자 만들기

기본적으로 [az aks][az-aks-get-credentials] 명령을 사용 하는 경우 aks 클러스터에 대 한 *관리자* 자격 증명이 구성에 추가 됩니다 `kubectl` . 관리 사용자는 pod 보안 정책의 적용을 무시 합니다. AKS 클러스터에 Azure Active Directory 통합을 사용 하는 경우 관리자가 아닌 사용자의 자격 증명을 사용 하 여 로그인 하 여 정책 적용을 확인할 수 있습니다. 이 문서에서는 AKS 클러스터에서 사용할 수 있는 테스트 사용자 계정을 만들어 보겠습니다.

[Kubectl create namespace][kubectl-create] 명령을 사용 하 여 테스트 리소스에 대해 *psp-aks* 라는 샘플 네임 스페이스를 만듭니다. 그런 다음 [kubectl create serviceaccount][kubectl-create] 명령을 사용 하 여 *nonadmin-user* 라는 서비스 계정을 만듭니다.

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

그런 다음 [kubectl create rolebinding][kubectl-create] 명령을 사용 하 여 네임 스페이스에서 기본 작업을 수행 하기 위해 *nonadmin-사용자* 에 대 한 rolebinding을 만듭니다.

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>관리자 및 관리자가 아닌 사용자에 대 한 별칭 만들기 명령

를 사용할 때와 이전 단계에서 만든 비관리자 사용자의 차이를 강조 표시 하려면 `kubectl` 다음 두 명령줄 별칭을 만듭니다.

* **Kubectl** 별칭은 일반 관리자 사용자를 위한 것 이며,은 *psp-aks* 네임 스페이스로 범위가 지정 됩니다.
* **Kubectl 사용자** 별칭은 이전 단계에서 만든 *nonadmin* 이 아닌 사용자를 위한 것 이며,이 별칭은 *psp-aks* 네임 스페이스로 범위가 지정 됩니다.

다음 명령에 표시 된 것 처럼 이러한 두 별칭을 만듭니다.

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>권한 있는 pod 생성 테스트

먼저의 보안 컨텍스트를 사용 하 여 pod를 예약할 때 발생 하는 결과를 테스트 하겠습니다 `privileged: true` . 이 보안 컨텍스트는 pod의 권한을 에스컬레이션 합니다. 기본 AKS pod 보안 정책을 보여 준 이전 섹션에서 *권한* 정책은이 요청을 거부 해야 합니다.

이라는 파일을 만들고 `nginx-privileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

다음 예제 출력과 같이 pod가 예약 되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Pod는 일정 단계에 도달 하지 않으므로 이동 하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 pod의 테스트 만들기

이전 예제에서 pod 사양은 권한 있는 에스컬레이션을 요청 했습니다. 이 요청은 기본 *권한* pod 보안 정책에 의해 거부 되므로 pod를 예약 하지 못합니다. 이제 권한 상승 요청 없이 동일한 NGINX pod를 실행 해 보겠습니다.

이라는 파일을 만들고 `nginx-unprivileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

다음 예제 출력과 같이 pod가 예약 되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Pod는 일정 단계에 도달 하지 않으므로 이동 하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>특정 사용자 컨텍스트를 사용 하 여 pod 생성 테스트

이전 예제에서 컨테이너 이미지는 자동으로 root를 사용 하 여 NGINX를 포트 80에 바인딩합니다. 이 요청은 기본 *권한* pod 보안 정책에 의해 거부 되었으므로 pod를 시작 하지 못합니다. 이제와 같은 특정 사용자 컨텍스트를 사용 하 여 동일한 NGINX pod를 실행 해 보겠습니다 `runAsUser: 2000` .

이라는 파일을 만들고 `nginx-unprivileged-nonroot.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

다음 예제 출력과 같이 pod가 예약 되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Pod는 일정 단계에 도달 하지 않으므로 이동 하기 전에 삭제할 리소스가 없습니다.

## <a name="create-a-custom-pod-security-policy"></a>사용자 지정 pod 보안 정책 만들기

이제 기본 pod 보안 정책의 동작을 살펴보았으므로 *관리자가 아닌 사용자* 가 pod를 예약 하는 방법을 제공 하겠습니다.

권한 있는 액세스를 요청 하는 pod를 거부 하는 정책을 만들어 보겠습니다. *RunAsUser* 또는 허용 된 *볼륨과* 같은 기타 옵션은 명시적으로 제한 되지 않습니다. 이 유형의 정책은 권한 있는 액세스에 대 한 요청을 거부 하지만, 그렇지 않으면 클러스터에서 요청 된 pod을 실행할 수 있습니다.

이라는 파일을 만들고 `psp-deny-privileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 정책을 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged.yaml
```

사용 가능한 정책을 보려면 다음 예제와 같이 [kubectl get psp][kubectl-get] 명령을 사용 합니다. 이전 예제에서 적용 된 기본 *권한* 정책과 함께 *psp-거부 권한* 정책을 비교 하 여 pod를 만듭니다. 정책에 따라 *PRIV* 에스컬레이션 사용만 거부 됩니다. *Psp-거부 권한* 정책에 대 한 사용자 또는 그룹에는 제한이 없습니다.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>사용자 계정이 사용자 지정 pod 보안 정책을 사용할 수 있도록 허용

이전 단계에서는 권한 있는 액세스를 요청 하는 pod를 거부 하는 pod 보안 정책을 만들었습니다. 정책을 사용할 수 있도록 허용 하려면 *역할* 또는 *ClusterRole* 를 만듭니다. 그런 다음 *rolebinding* 또는 *clusterrolebinding* 을 사용 하 여 이러한 역할 중 하나를 연결 합니다.

이 예에서는 이전 단계에서 만든 *psp-거부 권한* 정책을 *사용할* 수 있는 ClusterRole를 만듭니다. 이라는 파일을 만들고 `psp-deny-privileged-clusterrole.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 ClusterRole를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

이제 이전 단계에서 만든 ClusterRole를 사용 하는 ClusterRoleBinding을 만듭니다. 이라는 파일을 만들고 `psp-deny-privileged-clusterrolebinding.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 ClusterRoleBinding을 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 이 문서의 첫 번째 단계에서는 pod 보안 정책 기능이 AKS 클러스터에서 사용 하도록 설정 되었습니다. 사용자 고유의 정책을 정의한 후에만 pod 보안 정책 기능을 사용 하도록 설정 하는 것이 좋습니다. Pod 보안 정책 기능을 사용 하도록 설정 하는 단계입니다. 하나 이상의 사용자 지정 정책이 정의 되었고 사용자 계정이 해당 정책과 연결 되어 있습니다. 이제 pod 보안 정책 기능을 안전 하 게 사용 하도록 설정 하 고 기본 정책으로 인해 발생 하는 문제를 최소화할 수 있습니다.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>권한 없는 pod 생성을 다시 테스트 합니다.

사용자 지정 pod 보안 정책이 적용 되 고 정책을 사용 하기 위한 사용자 계정에 대 한 바인딩이 있으면 권한 없는 pod를 다시 만들려고 시도 합니다. 같은 매니페스트를 사용 하 여 `nginx-privileged.yaml` [kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만듭니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod가 성공적으로 예약 되었습니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 pod의 상태를 확인 하는 경우 Pod가 *실행 중* 입니다.

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

이 예제에서는 여러 사용자 또는 그룹에 대 한 AKS 클러스터에 대 한 액세스를 정의 하는 사용자 지정 pod 보안 정책을 만드는 방법을 보여 줍니다. 기본 AKS 정책은 pod가 실행할 수 있는 작업에 대 한 긴밀 한 제어를 제공 하므로 사용자 지정 정책을 만들어 필요한 제한 사항을 올바르게 정의 합니다.

[Kubectl delete][kubectl-delete] 명령을 사용 하 여 NGINX 권한 없는 pod를 삭제 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

Pod 보안 정책을 사용 하지 않도록 설정 하려면 [az aks update][az-aks-update] 명령을 다시 사용 합니다. 다음 예제에서는 *Myresourcegroup* 이라는 리소스 그룹의 클러스터 이름 *myAKSCluster* 에서 pod 보안 정책을 사용 하지 않도록 설정 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

다음으로 ClusterRole 및 ClusterRoleBinding을 삭제 합니다.

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

[Kubectl delete][kubectl-delete] 명령을 사용 하 여 보안 정책을 삭제 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl delete -f psp-deny-privileged.yaml
```

마지막으로 *psp-aks* 네임 스페이스를 삭제 합니다.

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 권한 있는 액세스 사용을 방지 하기 위해 pod 보안 정책을 만드는 방법을 살펴보았습니다. 볼륨이 나 RunAs 사용자와 같은 정책에서 적용할 수 있는 많은 기능이 있습니다. 사용할 수 있는 옵션에 대 한 자세한 내용은 [Kubernetes pod 보안 정책 참조 문서][kubernetes-policy-reference]를 참조 하세요.

Pod 네트워크 트래픽을 제한 하는 방법에 대 한 자세한 내용은 [AKS에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보호][network-policies]를 참조 하세요.

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
