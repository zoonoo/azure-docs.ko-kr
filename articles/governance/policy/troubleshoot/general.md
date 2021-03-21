---
title: 일반적인 오류 문제 해결
description: Kubernetes에 대 한 정책 정의, 다양 한 Sdk 및 추가 기능을 만들 때 발생 하는 문제를 해결 하는 방법에 대해 알아봅니다.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 6e0e4067f07266bae9c87fd4443d27314cc28c0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592615"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Azure Policy를 사용 하 여 오류 해결

정책 정의를 만들거나 Sdk를 사용 하거나 Kubernetes 추가 기능 [에 대 한 Azure Policy](../concepts/policy-for-kubernetes.md) 를 설정 하는 경우 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양 한 일반적인 오류에 대해 설명 하 고이 오류를 해결 하는 방법을 제안 합니다.

## <a name="find-error-details"></a>오류 세부 정보 찾기

오류 정보의 위치는 작업 중인 Azure Policy의 측면에 따라 다릅니다.

- 사용자 지정 정책을 사용 하 여 작업 하는 경우 Azure Portal으로 이동 하 여 스키마에 대 한 lint 피드백을 얻거나 결과 [준수 데이터](../how-to/get-compliance-data.md) 를 검토 하 여 리소스 평가 방법을 확인 합니다.
- 다양 한 Sdk를 사용 하 여 작업 하는 경우 SDK는 함수가 실패 한 이유에 대 한 세부 정보를 제공 합니다.
- Kubernetes에 대 한 추가 기능을 사용 하는 경우 클러스터의 [로깅을](../concepts/policy-for-kubernetes.md#logging) 시작 합니다.

## <a name="general-errors"></a>일반 오류

### <a name="scenario-alias-not-found"></a>시나리오: 별칭을 찾을 수 없음

#### <a name="issue"></a>문제

정책 정의에 잘못 되었거나 존재 하지 않는 별칭이 사용 되었습니다. Azure Policy [별칭](../concepts/definition-structure.md#aliases) 을 사용 하 여 Azure Resource Manager 속성에 매핑합니다.

#### <a name="cause"></a>원인

정책 정의에 잘못 되었거나 존재 하지 않는 별칭이 사용 되었습니다.

#### <a name="resolution"></a>해결 방법

먼저 리소스 관리자 속성에 별칭이 있는지 확인 합니다. 사용 가능한 별칭을 조회 하려면 Visual Studio Code 또는 SDK [에 대 한 Azure Policy 확장](../how-to/extension-for-vscode.md) 으로 이동 합니다.
리소스 관리자 속성에 대 한 별칭이 없으면 지원 티켓을 만듭니다.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>시나리오: 평가 정보가 최신 상태가 아닙니다.

#### <a name="issue"></a>문제

리소스가 _시작 안 됨_ 상태 이거나 호환성 정보가 최신이 아닙니다.

#### <a name="cause"></a>원인

새 정책 또는 이니셔티브 할당을 적용 하는 데 약 30 분이 걸립니다. 기존 할당 범위 내에서 새로운 또는 업데이트 된 리소스는 약 15 분 후에 사용할 수 있게 됩니다. 표준 준수 검사는 24 시간 마다 수행 됩니다. 자세한 내용은 [평가 트리거](../how-to/get-compliance-data.md#evaluation-triggers)를 참조 하세요.

#### <a name="resolution"></a>해결 방법

먼저 평가를 완료 하는 데 적절 한 시간을 기다리거나 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 합니다. Azure PowerShell 또는 REST API를 사용 하 여 새 평가 검색을 시작 하려면 [주문형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조 하세요.

### <a name="scenario-compliance-isnt-as-expected"></a>시나리오: 호환성이 예상과 일치 하지 않습니다.

#### <a name="issue"></a>문제

리소스가 리소스에 대해 예상 되는 _규격_ 또는 _비호환_ 평가 상태에 있지 않습니다.

#### <a name="cause"></a>원인

리소스가 정책 할당에 대 한 올바른 범위에 속하지 않거나 정책 정의가 의도 한 대로 작동 하지 않습니다.

#### <a name="resolution"></a>해결 방법

정책 정의 문제를 해결 하려면 다음을 수행 합니다.

1. 먼저 평가를 완료 하는 데 적절 한 시간을 기다린 후 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 합니다. 

1. Azure PowerShell 또는 REST API를 사용 하 여 새 평가 검색을 시작 하려면 [주문형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조 하세요.
1. 할당 매개 변수와 할당 범위가 올바르게 설정 되어 있는지 확인 하십시오.
1. [정책 정의 모드](../concepts/definition-structure.md#mode)를 확인합니다.
   - `all`모든 리소스 종류에 대 한 모드를 지정 해야 합니다.
   - `indexed`정책 정의가 태그나 위치를 확인 하는 경우 모드는 이어야 합니다.
1. 리소스의 범위가 [제외](../concepts/assignment-structure.md#excluded-scopes) [되거나 제외](../concepts/exemption-structure.md)되지 않는지 확인 합니다.
1. 정책 할당에 대 한 규정 준수 `0/0` 에서 리소스를 표시 하는 경우 할당 범위 내에서 적용 가능한 리소스가 결정 되지 않습니다. 정책 정의와 할당 범위를 모두 확인 합니다.
1. 호환 되는 것으로 예상 되는 비규격 리소스의 경우 [비준수의 이유 확인](../how-to/determine-non-compliance.md)을 참조 하세요. 정의를 평가 된 속성 값과 비교 하면 리소스가 비규격 인 이유를 알 수 있습니다.
   - **대상 값** 이 잘못 된 경우 정책 정의를 수정 합니다.
   - **현재 값** 이 잘못 된 경우을 통해 리소스 페이로드의 유효성을 검사 `resources.azure.com` 합니다.
1. 다른 일반적인 문제 및 해결 방법에 대해서는 [문제 해결: 예상 대로 적용](#scenario-enforcement-not-as-expected)을 참조 하세요.

중복 되 고 사용자 지정 된 기본 제공 정책 정의 또는 사용자 지정 정의에 문제가 계속 발생 하는 경우 **정책을 작성** 하 여 지원 티켓을 만들어 문제를 올바르게 라우팅합니다.

### <a name="scenario-enforcement-not-as-expected"></a>시나리오: 예상 대로 적용 되지 않음

#### <a name="issue"></a>문제

작업을 수행할 Azure Policy는 것으로 간주 되는 리소스가 작업 중인 것이 아니며 [Azure 활동 로그](../../../azure-monitor/essentials/platform-logs-overview.md)에 항목이 없습니다.

#### <a name="cause"></a>원인

[**EnforcementMode**](../concepts/assignment-structure.md#enforcement-mode) 설정에 대해 정책 할당이 구성 되어 _있지_ 않습니다.
**EnforcementMode** 를 사용 하지 않도록 설정 하는 동안 정책 효과는 적용 되지 않으며 활동 로그에 항목이 없습니다.

#### <a name="resolution"></a>해결 방법

다음을 수행 하 여 정책 할당의 적용 문제를 해결 합니다.

1. 먼저 평가를 완료 하는 데 적절 한 시간을 기다린 후 Azure Portal 또는 SDK에서 준수 결과를 사용할 수 있게 합니다. 

1. Azure PowerShell 또는 REST API를 사용 하 여 새 평가 검색을 시작 하려면 [주문형 평가 검사](../how-to/get-compliance-data.md#on-demand-evaluation-scan)를 참조 하세요.
1. 할당 매개 변수 및 할당 범위를 올바르게 설정 하 고 **enforcementMode** 를 _사용 하도록_ 설정 했는지 확인 하십시오.
1. [정책 정의 모드](../concepts/definition-structure.md#mode)를 확인합니다.
   - `all`모든 리소스 종류에 대 한 모드를 지정 해야 합니다.
   - `indexed`정책 정의가 태그나 위치를 확인 하는 경우 모드는 이어야 합니다.
1. 리소스의 범위가 [제외](../concepts/assignment-structure.md#excluded-scopes) [되거나 제외](../concepts/exemption-structure.md)되지 않는지 확인 합니다.
1. 리소스 페이로드가 정책 논리와 일치 하는지 확인 합니다. [HTTP Archive (HAR) 추적을 캡처하거나](../../../azure-portal/capture-browser-trace.md) Azure Resource Manager 템플릿 (ARM 템플릿) 속성을 검토 하 여이 작업을 수행할 수 있습니다.
1. 다른 일반적인 문제 및 해결 방법에 대해서는 [문제 해결: 예상 대로 준수 하지 않음](#scenario-compliance-isnt-as-expected)을 참조 하세요.

중복 되 고 사용자 지정 된 기본 제공 정책 정의 또는 사용자 지정 정의에 문제가 계속 발생 하는 경우 **정책을 작성** 하 여 지원 티켓을 만들어 문제를 올바르게 라우팅합니다.

### <a name="scenario-denied-by-azure-policy"></a>시나리오: Azure Policy에 의해 거부 됨

#### <a name="issue"></a>문제

리소스 만들기 또는 업데이트가 거부 되었습니다.

#### <a name="cause"></a>원인

새 리소스 또는 업데이트 된 리소스 범위에 대 한 정책 할당은 [거부](../concepts/effects.md#deny) 효과가 있는 정책 정의의 조건을 충족 합니다. 이러한 정의를 충족 하는 리소스는 만들거나 업데이트할 수 없습니다.

#### <a name="resolution"></a>해결 방법

거부 정책 할당의 오류 메시지에는 정책 정의 및 정책 할당 Id가 포함 됩니다. 메시지의 오류 정보가 누락 된 경우에도 [활동 로그](../../../azure-monitor/essentials/activity-log.md#view-the-activity-log)에서 사용할 수 있습니다. 이 정보를 사용 하 여 리소스 제한을 이해 하 고 허용 되는 값과 일치 하도록 요청에서 리소스 속성을 조정 하는 방법에 대 한 자세한 내용을 확인 하세요.

## <a name="template-errors"></a>템플릿 오류

### <a name="scenario-policy-supported-functions-processed-by-template"></a>시나리오: 템플릿에서 처리 된 정책 지원 함수

#### <a name="issue"></a>문제

Azure Policy는 정책 정의 에서만 사용할 수 있는 여러 ARM 템플릿 함수 및 함수를 지원 합니다. 리소스 관리자은 이러한 기능을 정책 정의의 일부가 아닌 배포의 일부로 처리 합니다.

#### <a name="cause"></a>원인

또는와 같은 지원 되는 함수를 사용 하면 `parameter()` `resourceGroup()` 정책 정의 및 Azure Policy 엔진에서 처리 하는 함수를 허용 하는 대신 배포 시 함수의 처리 된 결과가 발생 합니다.

#### <a name="resolution"></a>해결 방법

를 사용 하 여 함수를 정책 정의의 일부로 전달 하려면 속성이 처럼 표시 되도록 전체 문자열을 이스케이프 `[` `[[resourceGroup().tags.myTag]` 합니다. 이스케이프 문자를 통해 리소스 관리자은 템플릿을 처리할 때 값을 문자열로 처리 합니다. 그런 다음 Azure Policy는 함수를 정책 정의에 배치 하 여 예상 대로 동적이 될 수 있도록 합니다. 자세한 내용은 [Azure Resource Manager 템플릿의 구문 및 식](../../../azure-resource-manager/templates/template-expressions.md)을 참조 하세요.

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes 설치 오류에 대 한 추가 기능

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>시나리오: 암호 오류로 인해 투구 차트를 사용 하 여 설치 하지 못함

#### <a name="issue"></a>문제

`helm install azure-policy-addon`명령이 실패 하 고 다음 오류 중 하나를 반환 합니다.

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>원인

생성 된 암호에는 투구 차트를 분할 하는 쉼표 ()가 포함 되어 있습니다 `,` .

#### <a name="resolution"></a>해결 방법

를 실행할 때 `helm install azure-policy-addon` 암호 값에서 쉼표 ()를 `,` 백슬래시 ()로 이스케이프 `\` 합니다.

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>시나리오: 이름이 이미 존재 하기 때문에 투구 차트를 사용 하 여 설치할 때 실패 함

#### <a name="issue"></a>문제

`helm install azure-policy-addon`명령이 실패 하 고 다음과 같은 오류가 반환 됩니다.

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>원인

이름이 인 투구 차트가 `azure-policy-addon` 이미 설치 되었거나 부분적으로 설치 되었습니다.

#### <a name="resolution"></a>해결 방법

지침에 따라 [Kubernetes 추가 기능에 대 한 Azure Policy를 제거한](../concepts/policy-for-kubernetes.md#remove-the-add-on)후 명령을 다시 실행 `helm install azure-policy-addon` 합니다.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>시나리오: Azure 가상 머신 사용자 할당 id는 시스템 할당 관리 id로 대체 됨

#### <a name="issue"></a>문제

컴퓨터 내부에서 감사 설정에 게스트 구성 정책 이니셔티브를 할당 한 후에는 컴퓨터에 할당 된 사용자 할당 관리 id가 더 이상 할당 되지 않습니다. 시스템 할당 관리 id만 할당 됩니다.

#### <a name="cause"></a>원인

게스트 구성 DeployIfNotExists 정의에 이전에 사용 된 정책 정의는 시스템 할당 id가 컴퓨터에 할당 되어 있지만 사용자 할당 id 할당도 제거 되었는지 확인 합니다.

#### <a name="resolution"></a>해결 방법

이전에이 문제를 일으킨 정의는 _\[ 더 이상 \] 사용 되지_ 않는 것으로 표시 되며 사용자 할당 관리 id를 제거 하지 않고 필수 구성 요소를 관리 하는 정책 정의로 대체 됩니다. 수동 단계가 필요 합니다. _\[ 더 이상 사용 되지 \]_ 않는 것으로 표시 된 기존 정책 할당을 삭제 하 고 원래와 동일한 이름을 가진 업데이트 된 필수 조건 정책 이니셔티브 및 정책 정의로 바꿉니다.

자세한 내용은 블로그 게시물 [게스트 구성 감사 정책에 대해 릴리스된 중요 한 변경 내용](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)을 참조 하세요.

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes 일반 오류에 대 한 추가 기능

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>시나리오: 송신 제한으로 인해 추가 기능이 Azure Policy 서비스 끝점에 연결할 수 없습니다.

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 끝점에 연결할 수 없으며 다음 오류 중 하나가 반환 됩니다.

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>원인

이 문제는 클러스터 송신이 잠겨 있을 때 발생 합니다.

#### <a name="resolution"></a>해결 방법

다음 문서에서 설명 하는 도메인 및 포트가 열려 있는지 확인 합니다.

- [AKS 클러스터에 대 한 필수 아웃 바운드 네트워크 규칙 및 Fqdn (정규화 된 도메인 이름)](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Azure Arc 사용 Kubernetes (미리 보기)에 대 한 Azure Policy 추가 기능 설치](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>시나리오: aad-pod id 구성으로 인해 추가 기능이 Azure Policy 서비스 끝점에 연결할 수 없습니다.

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 끝점에 연결할 수 없으며 다음 오류 중 하나가 반환 됩니다.

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>원인

이 오류는 클러스터에 _kube_ 이 설치 되어 있고 pod에서 제외 _되지 않은 경우_ _에 발생 합니다._

_Aad-pod id_ 구성 요소 NMI (관리 id) Pod는 Azure 인스턴스 메타 데이터 끝점에 대 한 호출을 가로채는 노드의 iptables를 수정 합니다. 이 설정은 pod에서 _aad-pod_ 를 사용 하지 않는 경우에도 메타 데이터 끝점에 대 한 모든 요청이 NMI에 의해 차단 됨을 의미 합니다.
CRD에 정의 된 레이블과 일치 하는 pod에서 시작 되는 메타 데이터 끝점에 대 한 모든 요청은 NMI를 처리 하지 않고 프록시 되어야 하는 _AzurePodIdentityException_ CustomResourceDefinition (crd) _를 알리도록 구성할_ 수 있습니다.

#### <a name="resolution"></a>해결 방법

`kubernetes.azure.com/managedby: aks` _AzurePodIdentityException_ CRD를 구성 하 여 _kube_ 네임 스페이스에서 레이블이 있는 시스템 pod  를 제외 합니다.

자세한 내용은 [특정 pod/응용 프로그램에 대 한 Azure Active Directory (AZURE AD) pod Id 사용 안 함](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)을 참조 하세요.

예외를 구성 하려면 다음 예제를 따르세요.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>시나리오: 리소스 공급자가 등록 되지 않았습니다.

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 끝점에 연결할 수 있지만 추가 기능 로그에는 다음 오류 중 하나가 표시 됩니다.

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>원인

' Microsoft. PolicyInsights ' 리소스 공급자가 등록 되지 않았습니다. 정책 정의를 가져오고 준수 데이터를 반환 하려면 추가 기능에 등록 되어 있어야 합니다.

#### <a name="resolution"></a>해결 방법

클러스터 구독에서 ' Microsoft PolicyInsights ' 리소스 공급자를 등록 합니다. 자세한 내용은 [리소스 공급자 등록](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)을 참조 하세요.

### <a name="scenario-the-subscription-is-disabled"></a>시나리오: 구독이 사용 하지 않도록 설정 됨

#### <a name="issue"></a>문제

추가 기능이 Azure Policy 서비스 끝점에 도달할 수 있지만 다음과 같은 오류가 표시 됩니다.

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>원인

이 오류는 구독이 문제를 일으키는 것으로 확인 되었으며 기능 플래그가 `Microsoft.PolicyInsights/DataPlaneBlocked` 구독 차단에 추가 되었습니다.

#### <a name="resolution"></a>해결 방법

이 문제를 조사 하 고 해결 하려면 [기능 팀에 문의 하세요](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>다음 단계

문제가이 문서에 나열 되지 않거나 해결할 수 없는 경우 다음 채널 중 하나를 방문 하 여 지원을 받으세요.

- [Microsoft Q&A를](/answers/topics/azure-policy.html)통해 전문가의 답변을 받으세요.
- [@AzureSupport](https://twitter.com/azuresupport)에 연결합니다. Twitter의이 공식 Microsoft Azure 리소스는 Azure 커뮤니티를 올바른 답변, 지원 및 전문가에 게 연결 하 여 고객 환경을 개선 하는 데 도움이 됩니다.
- 여전히 도움이 필요한 경우 [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 이동 하 여 **지원 요청 제출** 을 선택 합니다.
