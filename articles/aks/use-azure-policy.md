---
title: Azure Policy를 사용 하 여 클러스터 보호
description: Azure Policy를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터를 보호 합니다.
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102193442"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Azure Policy를 사용 하 여 클러스터 보호

AKS (Azure Kubernetes Service) 클러스터의 보안을 강화 하기 위해 Azure Policy를 사용 하 여 클러스터에 기본 제공 보안 정책을 적용 하 고 적용할 수 있습니다. [Azure Policy][azure-policy] 는 조직의 표준을 적용 하 고 규정 준수를 평가 하는 데 도움이 됩니다. [AKS 용 Azure Policy 추가 기능][kubernetes-policy-reference]을 설치한 후에는 개별 정책 정의 또는 이니셔티브 (policysets 라고도 함) 라는 정책 정의 그룹을 클러스터에 적용할 수 있습니다. AKS 정책 및 이니셔티브 정의의 전체 목록은 [AKS에 대 한 기본 제공 정의 Azure Policy][aks-policies] 를 참조 하세요.

이 문서에서는 클러스터에 정책 정의를 적용 하 고 해당 할당이 적용 되는지 확인 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- 기존 AKS 클러스터 AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.
- AKS 클러스터에 설치 된 AKS 용 Azure Policy 추가 기능입니다. [Azure Policy 추가 기능을 설치 하려면 다음 단계를][azure-policy-addon]수행 합니다.

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>기본 제공 정책 정의 또는 이니셔티브 할당

정책 정의 또는 이니셔티브를 적용 하려면 Azure Portal을 사용 합니다.

1. Azure Portal에서 Azure Policy 서비스로 이동 합니다.
1. Azure Policy 페이지의 왼쪽 창에서 **정의** 를 선택합니다.
1. **범주** 아래에서 `Kubernetes` 를 선택 합니다.
1. 적용 하려는 정책 정의를 선택 합니다. 이 예에서는 이니셔티브를 선택 `Kubernetes cluster pod security baseline standards for Linux-based workloads` 합니다.
1. **할당** 을 선택합니다.
1. Azure Policy 추가 기능이 사용 하도록 설정 된 상태에서 AKS 클러스터의 리소스 그룹으로 **범위** 를 설정 합니다.
1. **매개 변수** 페이지를 선택 하 고에서로의 **효과** 를 업데이트 하 여 `audit` `deny` 새 배포에서 기준 이니셔티브를 위반 하는 것을 차단 합니다. 계산에서 제외할 네임 스페이스를 더 추가할 수도 있습니다. 이 예에서는 기본값을 유지 합니다.
1. **검토 + 만들기** , **만들기** 를 차례로 선택 하 여 정책 할당을 제출 합니다.

## <a name="validate-a-azure-policy-is-running"></a>Azure Policy 실행 중인지 확인

다음을 실행 하 여 정책 할당이 클러스터에 적용 되는지 확인 합니다.

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> 정책 할당을 각 클러스터에 [동기화 하는 데 최대 20 분][azure-policy-assign-policy] 이 걸릴 수 있습니다.

출력은 다음과 비슷해야 합니다.

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>권한 있는 pod의 거부 유효성 검사

먼저의 보안 컨텍스트를 사용 하 여 pod를 예약할 때 발생 하는 결과를 테스트 하겠습니다 `privileged: true` . 이 보안 컨텍스트는 pod의 권한을 에스컬레이션 합니다. 이니셔티브는 권한 있는 pod를 허용 하지 않으므로 요청이 거부 되어 배포가 거부 됩니다.

이라는 파일을 만들고 `nginx-privileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f nginx-privileged.yaml
```

예상 대로 다음 예제 출력과 같이 pod가 예약 되지 않습니다.

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Pod는 일정 단계에 도달 하지 않으므로 이동 하기 전에 삭제할 리소스가 없습니다.

### <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 pod의 테스트 만들기

이전 예제에서 컨테이너 이미지는 자동으로 root를 사용 하 여 NGINX를 포트 80에 바인딩합니다. 정책 이니셔티브에서이 요청을 거부 했으므로 pod를 시작 하지 못했습니다. 이제 특권 수준의 액세스 없이 동일한 NGINX pod를 실행 해 보겠습니다.

이라는 파일을 만들고 `nginx-unprivileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod가 성공적으로 예약 되었습니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 pod의 상태를 확인 하는 경우 Pod가 *실행 중* 입니다.

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

이 예제에서는 컬렉션에서 정책을 위반 하는 배포에만 영향을 주는 기준 이니셔티브를 보여 줍니다. 허용 되는 배포는 계속 작동 합니다.

[Kubectl delete][kubectl-delete] 명령을 사용 하 여 NGINX 권한 없는 pod를 삭제 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>정책 또는 이니셔티브 사용 안 함

기준 이니셔티브를 제거 하려면:

1. Azure Portal의 정책 창으로 이동 합니다.
1. 왼쪽 창에서 **할당** 을 선택 합니다.
1. 이니셔티브 옆의 **...** 단추를 클릭 합니다. `Kubernetes cluster pod security baseline standards for Linux-based workloads`
1. **할당 삭제** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

Azure Policy 작동 방법에 대 한 자세한 내용은 다음을 수행 하십시오.

- [Azure Policy 개요][azure-policy]
- [AKS에 대 한 Azure Policy 이니셔티브 및 정책][aks-policies]
- [Azure Policy 추가 기능][azure-policy-addon-remove]을 제거 합니다.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
