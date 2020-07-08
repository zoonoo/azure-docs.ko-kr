---
title: AKS (Azure Kubernetes Service)에서 Azure Policy를 사용 하 여 pod 보호
description: AKS (Azure Kubernetes Service)에서 Azure Policy를 사용 하 여 pod를 보호 하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 8a5107b9ba3c05c92a06753b2cb30bcfc2896d91
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86090937"
---
# <a name="secure-pods-with-azure-policy-preview"></a>Azure Policy를 사용 하 여 pod 보호 (미리 보기)

AKS 클러스터의 보안을 강화 하기 위해 pod에 부여 된 기능과 회사 정책에 대해 실행 중인 항목이 무엇 인지 제어할 수 있습니다. 이 액세스는 [AKS 용 Azure Policy 추가 기능][kubernetes-policy-reference]에서 제공 하는 기본 제공 정책을 통해 정의 됩니다. 루트 권한과 같이 pod 사양의 보안 측면에 대 한 추가 제어 기능을 제공 하 여 클러스터에 배포 된 항목을 보다 엄격 하 게 보안을 준수 하 고 볼 수 있습니다. Pod가 정책에 지정 된 조건을 충족 하지 않는 경우 pod가 위반을 시작 하거나 플래그를 지정 하지 못하게 할 수 Azure Policy. 이 문서에서는 Azure Policy를 사용 하 여 AKS에서 pod의 배포를 제한 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대 한 고객 지원에서 부분적으로 다룹니다. 따라서 이러한 기능은 프로덕션 용도로 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

### <a name="install-the-azure-policy-add-on-for-aks"></a>AKS 용 Azure Policy 추가 기능 설치

Azure Policy를 통해 AKS pod를 보호 하려면 AKS 클러스터에 AKS 용 추가 기능을 Azure Policy 설치 해야 합니다. [Azure Policy 추가 기능을 설치 하려면 다음 단계를](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks)수행 합니다.

이 문서에서는 위에 링크 된 연습에 배포 된 다음이 있다고 가정 합니다.

* 를 `Microsoft.ContainerService` `Microsoft.PolicyInsights` 사용 하 여 및 리소스 공급자를 등록 했습니다.`az provider register`
* 다음을 `AKS-AzurePolicyAutoApprove` 사용 하 여 미리 보기 기능 플래그를 등록 했습니다.`az feature register`
* 확장 버전이 0.4.53 이상인 Azure CLI 설치 되어 있습니다. `aks-preview`
* Azure Policy 추가 기능을 사용 하 여 설치 된 1.15 이상의 지원 되는 버전에 대 한 AKS 클러스터

## <a name="overview-of-securing-pods-with-azure-policy-for-aks-preview"></a>AKS (미리 보기)에 대 한 Azure Policy를 사용 하 여 pod 보안 개요

>[!NOTE]
> 이 문서에서는 pod를 Azure Policy 사용 하 여를 보호 하는 방법에 대해 자세히 설명 합니다 .이 작업은 [미리 보기의 Kubernetes pod 보안 정책 기능](use-pod-security-policies.md)입니다.
> **AKS 용 pod 보안 정책 (미리 보기) 및 Azure Policy 추가 기능을 동시에 사용 하도록 설정할 수 없습니다.**
> 
> Pod 보안 정책을 사용 하는 클러스터에 Azure Policy 추가 기능을 설치 하는 경우 [다음 단계에 따라 pod 보안 정책을 사용 하지 않도록 설정](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster)합니다.

AKS 클러스터에서 허용 컨트롤러는 리소스를 만들고 업데이트할 때 API 서버에 대 한 요청을 가로채는 데 사용 됩니다. 그러면 허용 컨트롤러는 생성 되어야 하는지 여부에 대 한 규칙 집합에 대해 리소스 요청의 *유효성을 검사할* 수 있습니다.

이전에는 Kubernetes 프로젝트를 통해 기능 [pod 보안 정책 (미리 보기)](use-pod-security-policies.md) 을 사용 하 여 배포할 수 있는 pod를 제한 했습니다. 이 기능은 Kubernetes 프로젝트에서 더 이상 개발 되지 않습니다.

AKS 클러스터는 Azure Policy 추가 기능을 사용 하 여 이전에 pod 보안 정책과 유사한 pod 및 기타 Kubernetes 리소스를 보호 하는 기본 제공 Azure 정책을 사용할 수 있습니다. AKS 용 Azure Policy 추가 기능에서는 유효성 검사 허용 컨트롤러인 관리 되는 [게이트 키퍼](https://github.com/open-policy-agent/gatekeeper)를 설치 합니다. Kubernetes에 대 한 Azure Policy은 [Rego 정책 언어](../governance/policy/concepts/policy-for-kubernetes.md#policy-language)를 사용 하는 오픈 소스 개방 정책 에이전트를 기반으로 합니다.

이 문서에서는 Azure Policy를 사용 하 여 AKS 클러스터의 pod를 보호 하 고 pod 보안 정책 (미리 보기)에서 마이그레이션하는 방법에 대해 설명 합니다.

## <a name="limitations"></a>제한 사항

* 미리 보기가 제공 되는 동안 Kubernetes 정책에 대해 20 Azure Policy의 제한 200 pod 단일 클러스터에서 실행할 수 있습니다.
* AKS managed pod를 포함 하는 [일부 시스템 네임 스페이스](#namespace-exclusion) 는 정책 평가에서 제외 됩니다.
* Windows pod는 [보안 컨텍스트를 지원 하지](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods)않으므로 많은 Azure 정책은 Linux pod (예: windows pod에서 에스컬레이션 될 수 없는 루트 권한 허용 안 함)에만 적용 됩니다.
* Pod 보안 정책 및 AKS 용 Azure Policy 추가 기능을 모두 사용 하도록 설정할 수 없습니다. Pod 보안 정책을 사용 하는 클러스터에 Azure Policy 추가 기능을 설치 하는 경우 [다음 지침](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster)에 따라 pod 보안 정책을 사용 하지 않도록 설정 합니다.

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Kubernetes pod를 보호 하는 Azure 정책

Azure Policy 추가 기능을 설치한 후에는 기본적으로 정책이 적용 되지 않습니다.

14 (14) 기본 제공 개별 Azure 정책 및 AKS 클러스터에서 특별히 pod를 보호 하는 두 개의 기본 제공 이니셔티브 (2)가 있습니다.
각 정책은 효과를 사용 하 여 사용자 지정할 수 있습니다. AKS 정책의 전체 목록과 [지원 되는 영향은 여기에 나열 되어][policy-samples]있습니다. [Azure Policy 효과](../governance/policy/concepts/effects.md)에 대 한 자세한 내용을 참조 하세요.

Azure 정책은 관리 그룹, 구독 또는 리소스 그룹 수준에서 적용할 수 있습니다. 리소스 그룹 수준에서 정책을 할당 하는 경우 대상 AKS 클러스터의 리소스 그룹이 정책 범위 내에서 선택 되었는지 확인 합니다. Azure Policy 추가 기능이 설치 된 할당 된 범위의 모든 클러스터는 정책 범위에 있습니다.

[Pod 보안 정책 (미리 보기)](use-pod-security-policies.md)을 사용 하는 경우 [Azure Policy로 마이그레이션하는 방법과 다른 동작 차이점에](#migrate-from-kubernetes-pod-security-policy-to-azure-policy)대해 알아봅니다.

### <a name="built-in-policy-initiatives"></a>기본 제공 정책 이니셔티브

Azure Policy 이니셔티브는 단일 수준의 목표를 달성 하는 데 맞게 조정 된 정책 정의의 컬렉션입니다. 이니셔티브를 사용 하면 AKS 클러스터에서 정책의 관리 및 할당을 간소화할 수 있습니다. 이니셔티브는 단일 개체로 존재 하며 [Azure Policy 이니셔티브](../governance/policy/overview.md#initiative-definition)에 대해 자세히 알아보세요.

Kubernetes에 대 한 Azure Policy는 pod, [기준선](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) 및 [제한](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00)의 보안을 유지 하는 두 가지 기본 제공 이니셔티브를 제공 합니다.

기본 제공 이니셔티브는 모두 [Kubernetes의 pod 보안 정책](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml)에 사용 된 정의를 기반으로 빌드됩니다.

|[Pod 보안 정책 컨트롤](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Azure Policy 정의 링크| 기준 이니셔티브 | 제한 된 이니셔티브 |
|---|---|---|---|
|권한 있는 컨테이너의 실행 허용 안 함|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| 예 | 예
|호스트 네임 스페이스의 공유 사용 허용 안 함|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| 예 | 예
|호스트 네트워킹 및 포트의 사용을 알려진 목록으로 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| 예 | 예
|호스트 파일 시스템의 사용 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| 예 | 예
|[기본 집합](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) 이외의 Linux 기능 추가|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | 예 | 예
|정의 된 볼륨 유형의 사용 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Yes
|루트에 대 한 권한 상승|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|컨테이너의 사용자 및 그룹 Id 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes |
|Pod의 볼륨을 소유 하는 FSGroup 할당 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes |
|Seccomp 프로필을 사용 해야 합니다.|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | - |
|컨테이너에서 사용 하는 sysctl 프로필 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F56d0a13f-712f-466b-8416-56fb354fb823) | - | - |
|기본 프로시저 탑재 유형은 공격 노출 영역을 줄이도록 정의 됩니다.|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff85eb0dd-92ee-40e9-8a76-db25a507d6d3) | - | - |
|특정 vervolume 드라이버로 제한|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | - | - |
|읽기 전용이 아닌 탑재 허용|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | - | - |
|컨테이너의 사용자 지정 SELinux 컨텍스트를 정의 합니다.|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e6c427-07d9-46ab-9689-bfa85431e636) | - | - |
|컨테이너에서 사용 하는 AppArmor 프로필 정의|[공용 클라우드](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | - | - |

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>네임 스페이스 제외

> [!WARNING]
> 클러스터를 정상 상태로 유지 하기 위해 kube와 같은 관리 네임 스페이스의 pod을 실행 해야 합니다. 제외 된 기본 네임 스페이스 목록에서 필수 네임 스페이스를 제거 하면 필수 시스템 pod로 인해 정책 위반이 발생할 수 있습니다.

AKS를 사용 하려면 클러스터에서 시스템 pod을 실행 하 여 중요 한 서비스 (예: DNS 확인)를 제공 해야 합니다. Pod 기능을 제한 하는 정책은 시스템 pod 안정성 기능에 영향을 줄 수 있습니다. 결과적으로 다음 네임 스페이스는 **만들기, 업데이트 및 정책 감사 중에 허용 요청 중에 정책 평가에서 제외**됩니다. 이렇게 하면 이러한 네임 스페이스에 대 한 새 배포가 Azure 정책에서 제외 됩니다.

1. kube-시스템
1. 게이트 키퍼-시스템
1. azure-호
1. aks-periscope

추가 사용자 지정 네임 스페이스는 생성, 업데이트 및 감사 중에 평가에서 제외할 수 있습니다. 사용 권한 네임 스페이스에서 실행 되는 특수화 된 pod이 있고 감사 위반을 트리거하지 않도록 하려면이를 사용 해야 합니다.

## <a name="apply-the-baseline-initiative"></a>기준 이니셔티브 적용

> [!TIP]
> 모든 정책은 기본적으로 감사 효과를 가집니다. Azure Policy를 통해 언제 든 지 거부 하도록 효과를 업데이트할 수 있습니다.

기준 이니셔티브를 적용 하기 위해 Azure Portal를 통해 할당할 수 있습니다.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. [Azure Portal에 대 한이 링크](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) 를 따라 pod 보안 기준 이니셔티브를 검토 합니다.
1. **범위** 를 구독 수준으로 설정 하거나 Azure Policy 추가 기능이 사용 하도록 설정 된 AKS 클러스터를 보유 하는 리소스 그룹만 설정 합니다.
1. **매개 변수** 페이지를 선택 하 고에서로의 **효과** 를 업데이트 하 여 `audit` `deny` 새 배포가 기본 이니셔티브를 위반 하는 것을 차단 합니다.
1. 생성, 업데이트 및 감사를 수행 하는 동안 평가에서 제외할 추가 네임 스페이스를 추가 [합니다. 일부 네임 스페이스는 정책 평가에서 강제로 제외 됩니다.](#namespace-exclusion) 
 ![ 업데이트 효과](media/use-pod-security-on-azure-policy/update-effect.png)
1. **검토 + 만들기** 를 선택 하 여 정책을 제출 합니다.

을 실행 하 여 정책이 클러스터에 적용 되는지 확인 `kubectl get constrainttemplates` 합니다.

> [!NOTE]
> 정책을 각 클러스터에 [동기화 하는 데 최대 20 분](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) 이 걸릴 수 있습니다.

출력은 다음과 비슷해야 합니다.

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>권한 있는 pod의 거부 유효성 검사

먼저의 보안 컨텍스트를 사용 하 여 pod를 예약할 때 발생 하는 결과를 테스트 하겠습니다 `privileged: true` . 이 보안 컨텍스트는 pod의 권한을 에스컬레이션 합니다. 기준 이니셔티브는 권한 있는 pod를 허용 하지 않으므로 요청이 거부 되어 배포가 거부 됩니다.

이라는 파일을 만들고 `nginx-privileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx
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

## <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 pod의 테스트 만들기

이전 예제에서 컨테이너 이미지는 자동으로 root를 사용 하 여 NGINX를 포트 80에 바인딩합니다. 기준 정책 이니셔티브에서이 요청을 거부 했으므로 pod를 시작 하지 못했습니다. 이제 특권 수준의 액세스 없이 동일한 NGINX pod를 실행 해 보겠습니다.

이라는 파일을 만들고 `nginx-unprivileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pod가 성공적으로 예약 되었습니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 pod의 상태를 확인 하는 경우 Pod가 *실행 중*입니다.

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

## <a name="disable-policies-and-the-azure-policy-add-on"></a>정책 및 Azure Policy 추가 기능 사용 안 함

기준 이니셔티브를 제거 하려면:

1. Azure Portal의 정책 창으로 이동 합니다.
1. 왼쪽 창에서 **할당** 을 선택 합니다.
1. "..."를 클릭 합니다. 기준 프로필 옆의 단추
1. "할당 삭제"를 선택 합니다.

![할당 삭제](media/use-pod-security-on-azure-policy/delete-assignment.png)

Azure Policy 추가 기능을 사용 하지 않도록 설정 하려면 [az aks addons][az-aks-disable-addons] 명령을 사용 합니다.

```azure-cli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

[Azure Portal에서 Azure Policy 추가 기능](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks)을 제거 하는 방법에 대해 알아봅니다.

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Kubernetes pod 보안 정책에서 Azure Policy로 마이그레이션

Pod 보안 정책에서 마이그레이션하려면 클러스터에서 다음 작업을 수행 해야 합니다.

1. 클러스터에서 [pod 보안 정책 사용 안 함](use-pod-security-policies.md#clean-up-resources)
1. [Azure Policy 추가 기능][kubernetes-policy-reference] 사용
1. [사용 가능한 기본 제공 정책][policy-samples] 에서 원하는 Azure 정책 사용

다음은 pod 보안 정책과 Azure Policy 간의 동작 변경 내용에 대 한 요약입니다.

|시나리오| Pod 보안 정책 | Azure Policy |
|---|---|---|
|설치|Pod 보안 정책 기능 사용 |Azure Policy 추가 기능 사용
|정책 배포| Pod 보안 정책 리소스 배포| 구독 또는 리소스 그룹 범위에 Azure 정책을 할당 합니다. Kubernetes 리소스 응용 프로그램에 Azure Policy 추가 기능이 필요 합니다.
| 기본 정책 | Pod 보안 정책을 AKS에서 사용 하도록 설정 하면 기본 권한 및 무제한 정책이 적용 됩니다. | Azure Policy 추가 기능을 사용 하도록 설정 하면 기본 정책이 적용 되지 않습니다. Azure Policy에서 정책을 명시적으로 사용 하도록 설정 해야 합니다.
| 정책을 만들고 할당할 수 있는 사람 | 클러스터 관리자가 pod 보안 정책 리소스를 만듭니다. | 사용자에 게는 AKS 클러스터 리소스 그룹에 대 한 ' owner ' 또는 ' 리소스 정책 참가자 '의 최소 역할이 있어야 합니다. -API를 통해 사용자는 AKS 클러스터 리소스 범위에서 정책을 할당할 수 있습니다. 사용자에 게 AKS 클러스터 리소스에 대 한 최소 ' 소유자 ' 또는 ' 리소스 정책 참가자 ' 권한이 있어야 합니다. -Azure Portal에서 관리 그룹/구독/리소스 그룹 수준에서 정책을 할당할 수 있습니다.
| 권한 부여 정책| 사용자 및 서비스 계정에는 pod 보안 정책을 사용 하기 위한 명시적 권한이 필요 합니다. | 정책에 권한을 부여 하는 데 추가 할당이 필요 하지 않습니다. Azure에서 정책이 할당 되 면 모든 클러스터 사용자는 이러한 정책을 사용할 수 있습니다.
| 정책 적용 가능성 | 관리 사용자는 pod 보안 정책의 적용을 무시 합니다. | 모든 사용자 (관리자 & 관리자가 아닌)는 동일한 정책을 볼 수 있습니다. 사용자를 기반으로 하는 특별 한 대/소문자가 없습니다. 정책 응용 프로그램은 네임 스페이스 수준에서 제외할 수 있습니다.
| 정책 범위 | Pod 보안 정책은 namespaced 되지 않습니다. | Azure Policy에서 사용 하는 제약 조건 템플릿은 namespaced 되지 않습니다.
| 거부/감사/변형 동작 | Pod 보안 정책은 거부 동작만 지원 합니다. Mutatation는 만들기 요청에 대 한 기본값을 사용 하 여 수행할 수 있습니다. 업데이트 요청 중에 유효성 검사를 수행할 수 있습니다.| Azure Policy 감사 & 거부 동작을 모두 지원 합니다. 변형은 아직 지원 되지 않지만 계획 되었습니다.
| Pod 보안 정책 준수 | Pod 보안 정책을 사용 하기 전에 존재 했던 pod의 준수에 대 한 가시성은 없습니다. Pod 보안 정책을 사용 하도록 설정한 후에 만든 비규격 pod는 거부 됩니다. | Azure 정책을 적용 하기 전에 있던 비규격 pod 정책 위반에 표시 됩니다. 정책이 거부 효과로 설정 된 경우 Azure 정책을 사용 하도록 설정한 후에 만든 비규격 pod가 거부 됩니다.
| 클러스터에서 정책을 보는 방법 | `kubectl get psp` | `kubectl get constrainttemplate`-모든 정책이 반환 됩니다.
| Pod 보안 정책 표준-권한 | 기능을 사용 하도록 설정 하면 기본적으로 권한 있는 pod 보안 정책 리소스가 생성 됩니다. | 특권 모드는 제한이 없음을 의미 하므로 Azure Policy 할당이 없는 것과 같습니다.
| [Pod 보안 정책 표준-기준선/기본값](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | 사용자가 pod 보안 정책 기준 리소스를 설치 합니다. | Azure Policy는 기본 제공 기본 [이니셔티브](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) 를 제공 하 여 기준 pod 보안 정책에 매핑됩니다.
| [Pod 보안 정책 표준-제한 됨](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | 사용자가 pod 보안 정책 제한 리소스를 설치 합니다. | Azure Policy는 제한 된 pod 보안 정책에 매핑되는 [기본 제공 제한 이니셔티브](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) 를 제공 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 권한 있는 액세스를 사용 하지 않도록 권한 있는 pod를 배포 하는 것을 제한 하는 Azure 정책을 적용 하는 방법을 살펴보았습니다. 볼륨 사용을 제한 하는 정책과 같이 적용할 수 있는 많은 정책이 있습니다. 사용할 수 있는 옵션에 대 한 자세한 내용은 [Azure Policy Kubernetes 참조 문서][kubernetes-policy-reference]를 참조 하세요.

Pod 네트워크 트래픽을 제한 하는 방법에 대 한 자세한 내용은 [AKS에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보호][network-policies]를 참조 하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: policy-samples.md#microsoftcontainerservice
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
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete