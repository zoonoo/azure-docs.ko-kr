---
title: Azure Kubernetes 서비스(AKS)에서 포드 보안 정책 사용
description: Azure Kubernetes 서비스(AKS)에서 PodSecurityPolicy를 사용하여 포드 입장을 제어하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: 9e3a17e4775150247ef7924dffec68cc86a0bcac
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998349"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>미리 보기 - AZURE Kubernetes 서비스(AKS)에서 포드 보안 정책을 사용하여 클러스터 를 보호합니다.

AKS 클러스터의 보안을 향상시키기 위해 예약할 수 있는 포드를 제한할 수 있습니다. 허용하지 않는 리소스를 요청하는 포드는 AKS 클러스터에서 실행할 수 없습니다. pod 보안 정책을 사용하여 이 액세스를 정의합니다. 이 문서에서는 POD 보안 정책을 사용하여 AKS에서 포드 배포를 제한하는 방법을 보여 주며 있습니다.

> [!IMPORTANT]
> AKS 미리보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 대로" 및 "사용 가능한 경우"로 제공되며 서비스 수준 계약 및 제한 보증에서 제외됩니다. AKS 미리 보기는 최선의 노력을 기울여 고객 지원에 의해 부분적으로 적용됩니다. 따라서 이러한 기능은 프로덕션 용으로 사용되지 않습니다. 자세한 내용은 다음 지원 문서를 참조하십시오.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

Azure CLI 버전 2.0.61 이상 설치 및 구성이 필요합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

포드 보안 정책을 사용하려면 *aks 미리 보기* CLI 확장 버전 0.4.1 이상이 필요합니다. az 확장 [추가][az-extension-add] 명령을 사용하여 *aks 미리 보기* Azure CLI 확장을 설치한 다음 az [확장 업데이트][az-extension-update] 명령을 사용하여 사용 가능한 업데이트를 확인합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>포드 보안 정책 기능 공급자 등록

포드 보안 정책을 사용하도록 AKS 클러스터를 만들거나 업데이트하려면 먼저 구독에서 기능 플래그를 사용하도록 설정합니다. *PodSecurityPolicyPreview* 기능 플래그를 등록하려면 다음 예제와 같이 [az 기능 레지스터][az-feature-register] 명령을 사용합니다.

> [!CAUTION]
> 구독에 기능을 등록할 때 해당 기능을 현재 등록 취소할 수 없습니다. 일부 미리 보기 기능을 활성화하면 구독에서 만든 모든 AKS 클러스터에 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용하도록 설정하지 마십시오. 별도의 구독을 사용하여 미리 보기 기능을 테스트하고 피드백을 수집합니다.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>포드 보안 정책 개요

Kubernetes 클러스터에서 리소스를 만들 때 API 서버에 대한 요청을 가로채는 데 입학 컨트롤러가 사용됩니다. 그런 다음 승인 컨트롤러는 규칙 집합에 대해 리소스 요청의 *유효성을 검사하거나* 리소스를 *변경하여* 배포 매개 변수를 변경할 수 있습니다.

*PodSecurityPolicy는* 포드 사양이 정의된 요구 사항을 충족하는지 확인하는 승인 컨트롤러입니다. 이러한 요구 사항은 권한 있는 컨테이너의 사용, 특정 유형의 저장소에 대한 액세스 또는 컨테이너가 실행할 수 있는 사용자 또는 그룹 사용을 제한할 수 있습니다. 포드 사양이 포드 보안 정책에 설명된 요구 사항을 충족하지 않는 리소스를 배포하려고 하면 요청이 거부됩니다. AKS 클러스터에서 예약할 수 있는 포드를 제어하는 이 기능은 몇 가지 가능한 보안 취약점 또는 권한 에스컬레이션을 방지합니다.

AKS 클러스터에서 포드 보안 정책을 사용하도록 설정하면 일부 기본 정책이 적용됩니다. 이러한 기본 정책은 예약할 수 있는 포드를 정의하는 기본 환경을 제공합니다. 그러나 클러스터 사용자는 자체 정책을 정의할 때까지 포드를 배포하는 데 문제가 발생할 수 있습니다. 권장되는 방법은 다음과 같은 것입니다.

* AKS 클러스터 만들기
* 고유한 포드 보안 정책 정의
* 포드 보안 정책 기능 사용

기본 정책이 포드 배포를 제한하는 방법을 보여 드리기 위해 이 문서에서는 먼저 포드 보안 정책 기능을 사용하도록 설정한 다음 사용자 지정 정책을 만듭니다.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS 클러스터에서 포드 보안 정책 사용

[az aks 업데이트][az-aks-update] 명령을 사용하여 포드 보안 정책을 활성화하거나 비활성화할 수 있습니다. 다음 예제에서는 *myResourceGroup이라는*리소스 그룹의 클러스터 이름 *myAKSCluster에* 대한 포드 보안 정책을 활성화합니다.

> [!NOTE]
> 실제 사용의 경우 사용자 지정 정책을 정의하기 전까지는 포드 보안 정책을 사용하도록 설정하지 마십시오. 이 문서에서는 기본 정책이 포드 배포를 제한하는 방법을 확인하는 첫 번째 단계로 포드 보안 정책을 사용하도록 설정합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>기본 AKS 정책

포드 보안 정책을 사용하도록 설정하면 AKS는 *권한*있는 라는 하나의 기본 정책을 만듭니다. 기본 정책을 편집하거나 제거하지 마세요. 대신 제어하려는 설정을 정의하는 고유한 정책을 만듭니다. 먼저 이러한 기본 정책이 포드 배포에 미치는 영향을 살펴보겠습니다.

사용 가능한 정책을 보려면 다음 예제와 같이 [kubectl get psp][kubectl-get] 명령을 사용합니다.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*권한 있는* 포드 보안 정책은 AKS 클러스터의 인증된 모든 사용자에게 적용됩니다. 이 할당은 ClusterRoles 및 ClusterRole바인딩에 의해 제어됩니다. [kubectl 을 사용하여 역할 바인딩][kubectl-get] 명령을 사용하고 기본 *:권한:* *kube 시스템* 네임 스페이스에서 바인딩을 검색합니다.

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

다음 압축 된 출력에 표시 된 대로 *psp:제한* ClusterRole 모든 시스템에 할당 *됩니다.: 인증 된* 사용자. 이 기능은 고유한 정책을 정의하지 않고 기본 수준의 제한을 제공합니다.

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

자체 포드 보안 정책을 만들기 전에 이러한 기본 정책이 사용자 요청과 상호 작용하여 포드를 예약하는 방법을 이해하는 것이 중요합니다. 다음 몇 섹션에서는 이러한 기본 정책이 작동하는 지 확인하기 위해 일부 창을 예약해 보겠습니다.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS 클러스터에서 테스트 사용자 만들기

기본적으로 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하면 AKS 클러스터의 *관리자* 자격 증명이 `kubectl` 구성에 추가됩니다. 관리자 사용자는 Pod 보안 정책의 적용을 무시합니다. AKS 클러스터에 Azure Active Directory 통합을 사용하는 경우 관리자가 아닌 사용자의 자격 증명으로 로그인하여 실행 중인 정책의 적용을 확인할 수 있습니다. 이 문서에서는 사용할 수 있는 AKS 클러스터에서 테스트 사용자 계정을 만들어 보겠습니다.

[kubectl create 네임스페이스][kubectl-create] 명령을 사용하여 테스트 리소스에 대해 *psp-aks라는* 샘플 네임스페이스를 만듭니다. 그런 다음 [kubectl create serviceaccount][kubectl-create] 명령을 사용하여 *비관리자 사용자라는* 서비스 계정을 만듭니다.

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

다음으로, [kubectl create rolebinding][kubectl-create] 명령을 사용 하 여 네임스페이스에서 기본 작업을 수행 하는 *비 관리자 사용자에* 대 한 역할 바인딩을 만듭니다.

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>관리자 및 비관리자 사용자를 위한 별칭 명령 만들기

이전 단계에서 만든 일반 관리자 사용자와 `kubectl` 비관리자 사용자 간의 차이점을 강조 표시하려면 두 개의 명령줄 별칭을 만듭니다.

* **kubectl-admin** 별칭은 일반 관리자 사용자를 위한 것이며 *psp-aks* 네임스페이스로 범위가 지정됩니다.
* **kubectl-nonadminuser** 별칭은 이전 단계에서 만든 *비관리자 사용자에* 대 한 이며 *psp-aks* 네임스페이스에 범위가 지정 됩니다.

다음 명령에 표시된 대로 다음 두 별칭을 만듭니다.

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>권한 있는 포드 만들기 테스트

먼저 `privileged: true`의 보안 컨텍스트를 통해 포드를 예약할 때 어떤 일이 발생하는지 테스트해 보겠습니다. 이 보안 컨텍스트는 포드의 권한을 에스컬레이션합니다. 기본 AKS 포드 보안 정책을 보여 준 이전 섹션에서 *제한된* 정책은 이 요청을 거부해야 합니다.

다음 YAML 매니페스트라는 파일을 `nginx-privileged.yaml` 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

[kubectl apply][kubectl-apply] 명령을 사용하여 포드를 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

다음 예제 출력과 같이 포드를 예약하지 못합니다.

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

포드가 일정 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-an-unprivileged-pod"></a>권한이 없는 포드의 테스트 생성

이전 예제에서 pod 사양은 권한 있는 에스컬레이션을 요청했습니다. 이 요청은 기본 *제한* 된 포드 보안 정책에 의해 거부 됩니다. 이제 권한 에스컬레이션 요청 없이 동일한 NGINX 포드를 실행해 보겠습니다.

다음 YAML 매니페스트라는 파일을 `nginx-unprivileged.yaml` 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

[kubectl apply][kubectl-apply] 명령을 사용하여 포드를 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

다음 예제 출력과 같이 포드를 예약하지 못합니다.

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

포드가 일정 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>특정 사용자 컨텍스트가 있는 포드 만들기 테스트

이전 예제에서 컨테이너 이미지는 자동으로 루트를 사용하여 NGINX를 포트 80에 바인딩하려고 했습니다. 이 요청은 기본 *제한* 된 포드 보안 정책에 의해 거부 되었습니다. 이제 `runAsUser: 2000`와 같은 특정 사용자 컨텍스트를 통해 동일한 NGINX 포드를 실행해 보겠습니다.

다음 YAML 매니페스트라는 파일을 `nginx-unprivileged-nonroot.yaml` 만들고 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

[kubectl apply][kubectl-apply] 명령을 사용하여 포드를 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

다음 예제 출력과 같이 포드를 예약하지 못합니다.

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

포드가 일정 단계에 도달하지 않으므로 계속 진행하기 전에 삭제할 리소스가 없습니다.

## <a name="create-a-custom-pod-security-policy"></a>사용자 지정 포드 보안 정책 만들기

이제 기본 포드 보안 정책의 동작을 확인했으므로 *비관리자 사용자가* 포드를 성공적으로 예약할 수 있는 방법을 제공해 보겠습니다.

권한 있는 액세스를 요청하는 포드를 거부하는 정책을 만들어 보겠습니다. *runAsUser* 또는 허용된 *볼륨과*같은 다른 옵션은 명시적으로 제한되지 않습니다. 이 유형의 정책은 권한 있는 액세스 요청을 거부하지만 그렇지 않으면 클러스터에서 요청된 포드를 실행할 수 있습니다.

다음 YAML 매니페스트라는 파일을 `psp-deny-privileged.yaml` 만들고 붙여넣습니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 정책을 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f psp-deny-privileged.yaml
```

사용 가능한 정책을 보려면 다음 예제와 같이 [kubectl get psp][kubectl-get] 명령을 사용합니다. *psp-deny 권한 정책을* 이전 예제에서 적용된 기본 *제한* 정책과 비교하여 포드를 만듭니다. *PRIV* 에스컬레이션의 사용만 정책에 의해 거부됩니다. *psp-deny 권한* 정책에 대한 사용자 또는 그룹에는 제한이 없습니다.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>사용자 계정이 사용자 지정 포드 보안 정책을 사용하도록 허용

이전 단계에서는 권한 있는 액세스를 요청하는 포드를 거부하는 포드 보안 정책을 만들었습니다. 정책을 사용할 수 있도록 역할 *또는* *ClusterRole*을 만듭니다. 그런 다음 *역할 바인딩* 또는 *ClusterRoleBinding*을 사용하여 이러한 역할 중 하나를 연결합니다.

이 예제에서는 이전 단계에서 만든 *psp-deny 권한* 정책을 *사용할* 수 있는 ClusterRole을 만듭니다. 다음 YAML 매니페스트라는 파일을 `psp-deny-privileged-clusterrole.yaml` 만들고 붙여넣습니다.

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

[kubectl apply][kubectl-apply] 명령을 사용하여 ClusterRole을 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

이제 이전 단계에서 만든 클러스터롤을 사용하도록 클러스터롤바인딩을 만듭니다. 다음 YAML 매니페스트라는 파일을 `psp-deny-privileged-clusterrolebinding.yaml` 만들고 붙여넣습니다.

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
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

[kubectl apply][kubectl-apply] 명령을 사용하여 ClusterRoleBinding을 만들고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 이 문서의 첫 번째 단계에서는 AKS 클러스터에서 포드 보안 정책 기능이 활성화되었습니다. 권장되는 방법은 자체 정책을 정의한 후에만 pod 보안 정책 기능을 사용하도록 설정하는 것이 좋습니다. 이 단계는 포드 보안 정책 기능을 사용하도록 설정하는 단계입니다. 하나 이상의 사용자 지정 정책이 정의되었으며 사용자 계정이 해당 정책과 연결되었습니다. 이제 Pod 보안 정책 기능을 안전하게 활성화하고 기본 정책으로 인한 문제를 최소화할 수 있습니다.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>권한이 없는 포드 만들기를 다시 테스트합니다.

사용자 지정 포드 보안 정책이 적용되고 사용자 계정이 정책을 사용할 수 있도록 바인딩된 경우 권한이 없는 포드를 다시 만들어 보겠습니다. `nginx-privileged.yaml` [kubectl apply][kubectl-apply] 명령을 사용하여 동일한 매니페스트를 사용하여 포드를 만듭니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

포드가 성공적으로 예약되었습니다. [kubectl get 포드][kubectl-get] 명령을 사용하여 포드의 상태를 확인하면 포드가 *실행 중입니다.*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

이 예제에서는 사용자 지정 포드 보안 정책을 만들어 다른 사용자 또는 그룹에 대한 AKS 클러스터에 대한 액세스를 정의하는 방법을 보여 주며 있습니다. 기본 AKS 정책은 실행할 수 있는 포드에 대한 엄격한 제어를 제공하므로 사용자 지정 정책을 직접 만들어 필요한 제한을 올바르게 정의합니다.

[kubectl delete][kubectl-delete] 명령을 사용하여 NGINX 비권한 포드를 삭제하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

포드 보안 정책을 사용하지 않으려면 [az aks 업데이트][az-aks-update] 명령을 다시 사용합니다. 다음 예제에서는 *myResourceGroup이라는*리소스 그룹의 클러스터 이름 *myAKSCluster에서* 포드 보안 정책을 사용하지 않도록 설정합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

다음으로, 클러스터롤 및 클러스터롤바인딩을 삭제합니다.

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

[kubectl delete][kubectl-delete] 명령을 사용하여 보안 정책을 삭제하고 YAML 매니페스트의 이름을 지정합니다.

```console
kubectl delete -f psp-deny-privileged.yaml
```

마지막으로, *psp-aks 네임스페이스를 삭제합니다.*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 권한 있는 액세스를 사용하지 못하도록 포드 보안 정책을 만드는 방법을 보여 주어 있습니다. 볼륨 유형 또는 RunAs 사용자와 같이 정책이 적용할 수 있는 기능이 많이 있습니다. 사용 가능한 옵션에 대한 자세한 내용은 [Kubernetes 포드 보안 정책 참조 문서를][kubernetes-policy-reference]참조하십시오.

포드 네트워크 트래픽 제한에 대한 자세한 내용은 [AKS의 네트워크 정책을 사용하여 포드 간의 보안 트래픽을][network-policies]참조하십시오.

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
