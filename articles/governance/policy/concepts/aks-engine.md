---
title: AKS 엔진에 대한 Azure 정책 알아보기
description: Azure 정책이 게이트키퍼 v3의 CustomResourceDefinitions 및 열린 정책 에이전트를 사용하여 AKS 엔진을 사용하여 클러스터를 관리하는 방법을 알아봅니다.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436435"
---
# <a name="understand-azure-policy-for-aks-engine"></a>AKS 엔진에 대한 Azure 정책 이해

Azure Policy는 Azure에서 자체 관리되는 Kubernetes 클러스터를 신속하게 부트스트랩할 수 있는 편리한 툴링을 제공하는 시스템인 [AKS Engine과](https://github.com/Azure/aks-engine/blob/master/docs/README.md)통합됩니다. 이러한 통합을 통해 AKS Engine 자체 관리 클러스터에서 중앙 집중적이고 일관된 방식으로 대규모 로 의 집행 및 보호 조치를 수행할 수 있습니다. Kubernetes의 _입학 컨트롤러 웹후크인_ [OPA(개방형 정책 에이전트)](https://www.openpolicyagent.org/) [게이트키퍼](https://github.com/open-policy-agent/gatekeeper) v3(베타)의 사용을 확장함으로써 Azure Policy를 사용하면 Azure 리소스 및 AKS Engine 자체 관리 클러스터의 규정 준수 상태를 한 곳에서 관리하고 보고할 수 있습니다.

> [!NOTE]
> AKS 엔진에 대한 Azure 정책은 공개 미리 보기에 있으며 SLA가 없습니다. 게이트키퍼 v3는 베타 버전이며 오픈 소스 커뮤니티에서 지원됩니다. 이 서비스는 서비스 주체로 구성된 각 리소스 그룹에 대해 기본 제공 정책 정의 및 단일 AKS 엔진 클러스터만 지원합니다.

> [!IMPORTANT]
> AKS 엔진, AKS 엔진 또는 게이트키퍼 v3에 대한 Azure 정책에 대한 지원을 받으려면 AKS 엔진 GitHub 리포지토리에서 [새 문제를](https://github.com/Azure/aks-engine/issues/new/choose) 만듭니다.

## <a name="overview"></a>개요

Azure에서 자체 관리되는 Kubernetes 클러스터를 사용하여 AKS 엔진에 대한 Azure 정책을 활성화하고 사용하려면 다음 작업을 수행합니다.

- [필수 조건](#prerequisites)
- [Azure 정책 추가 기능 설치](#installing-the-add-on)
- [AKS 엔진에 대한 정책 정의 할당](#built-in-policies)
- [유효성 검사 대기](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>사전 요구 사항

Azure 정책 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 구독은 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정하고 클러스터 서비스 주체에 대한 역할 할당을 만들어야 합니다. 

1. 리소스 공급자를 사용하려면 리소스 [공급자 및 형식의](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) 단계를 따르거나 Azure CLI 또는 Azure PowerShell 명령을 실행합니다.

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 클러스터 서비스 주체에 대한 역할 할당 만들기

   - 클러스터 서비스 주서버 앱 ID를 모르는 경우 다음 명령으로 찾습니다.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLI를 사용하여 클러스터 서비스 주 서버 앱 ID(이전 단계의 값 _aadClientID)에_ '정책 인사이트 데이터 작성기(미리 보기)' 역할 할당을 할당합니다. 구독 `<subscriptionId>` `<aks engine cluster resource group>` ID로 바꾸고 AKS Engine 자체 관리 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉습니다.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure 정책 추가 기능

Kubernetes에 대한 _Azure 정책 추가 기능이_ Azure 정책 서비스를 게이트키퍼 승인 컨트롤러에 연결합니다. _kube 시스템_ 네임스페이스에 설치된 추가 기능은 다음과 같은 기능을 합니다.

- AKS 엔진 클러스터에 할당에 대 한 Azure 정책으로 확인
- 정책 세부 정보, 제약 조건 템플릿 및 제약 조건을 다운로드하고 설치합니다.
- AKS 엔진 클러스터에서 전체 검사 준수 검사 실행
- 감사 및 규정 준수 세부 정보를 Azure 정책으로 다시 보고합니다.

### <a name="installing-the-add-on"></a>추가 기능 설치

필수 구성 조건이 완료되면 Azure 정책 추가 기능을 설치할 수 있습니다. 설치는 AKS 엔진의 생성 또는 업데이트 주기 동안 또는 기존 클러스터에서 독립적인 작업으로 사용될 수 있습니다.

- 생성 또는 업데이트 주기 동안 설치

  새 자체 관리 클러스터를 만드는 동안 또는 기존 클러스터에 대한 업데이트로 Azure Policy 추가 기능을 사용하려면 AKS 엔진에 대한 **addons** 속성 클러스터 정의를 포함합니다.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  자세한 내용은 외부 가이드 [AKS 엔진 클러스터 정의를](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)참조하십시오.

- 투구 차트가 있는 기존 클러스터에 설치

  다음 단계를 사용하여 클러스터를 준비하고 추가 기능을 설치합니다.

  1. 게이트키퍼 시스템 네임스페이스에 _게이트키퍼를_ 설치합니다.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. _kube 시스템에_ _제어 평면_ 레이블을 추가합니다. 이 레이블은 게이트키퍼 및 Azure 정책 추가 기능의 _kube 시스템_ 포드 및 서비스에 대한 감사를 제외합니다.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. OPA와 Kubernetes 데이터 (네임 스페이스, 포드, 침투, 서비스)를 동기화합니다.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     자세한 내용은 [OPA - 데이터 복제](https://github.com/open-policy-agent/gatekeeper#replicating-data)를 참조하십시오.

  1. 헬름에 Azure 정책 리포지토리를 추가합니다.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     자세한 내용은 [헬름 차트 - 빠른 시작 가이드를](https://helm.sh/docs/using_helm/#quickstart-guide)참조하십시오.

  1. 헬름 차트로 추가 기능을 설치합니다. 구독 `<subscriptionId>` `<aks engine cluster resource group>` ID로 바꾸고 AKS Engine 자체 관리 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉습니다.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     추가 기능 헬름 차트가 설치하는 내용에 대한 자세한 내용은 GitHub의 [Azure 정책 추가 기능 헬름 차트 정의를](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) 참조하십시오.

     > [!NOTE]
     > Azure Policy 추가 기능과 리소스 그룹 ID 간의 관계로 인해 Azure Policy는 각 리소스 그룹에 대해 하나의 AKS 엔진 클러스터만 지원합니다.

추가 기능 설치가 성공하고 _Azure 정책_ 포드가 실행되고 있는지 확인하려면 다음 명령을 실행합니다.

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>검증 및 보고 빈도

추가 기능이 Azure 정책에서 5분마다 정책 할당의 변경 내용을 체크 인합니다. 이 새로 고침 주기 동안 추가 기능 변경 내용을 확인 합니다. 이러한 변경 사항은 제약 조건 템플릿 및 제약 조건을 생성, 업데이트 또는 삭제합니다.

> [!NOTE]
> 클러스터 _관리자는_ 제약 조건 템플릿 및 제약 조건을 변경할 수 있는 권한이 있을 수 있지만 Azure Policy에서 만든 제약 조건 템플릿 또는 제약 조건을 변경하는 것은 권장되지 않습니다. 새로 고침 주기 동안 수동으로 변경한 내용이 손실됩니다.

5분마다 추가 기능이 클러스터의 전체 검사를 호출합니다. 전체 검사에 대한 세부 정보와 클러스터에 대한 변경 시도의 Gatekeeper의 실시간 평가를 수집한 후 추가 기능에서는 결과를 Azure Policy 할당과 같은 [규정 준수 세부 정보에](../how-to/get-compliance-data.md) 포함시키기 위해 Azure 정책으로 다시 보고합니다. 활성 정책 할당에 대한 결과만 감사 주기 중에 반환됩니다. 감사 결과는 실패한 제약 조건의 상태 필드에 나열된 위반으로도 볼 수 있습니다.

## <a name="policy-language"></a>정책 언어

AKS 엔진을 관리하기 위한 Azure 정책 언어 구조는 기존 정책의 구조를 따릅니다. _시행OpAConstraint_ 효과는 AKS 엔진 클러스터를 관리하는 데 사용되며 [OPA 제약 프레임워크](https://github.com/open-policy-agent/frameworks/tree/master/constraint) 및 게이트키퍼 v3 작업과 관련된 _세부 정보_ 속성을 취합니다. 자세한 내용 및 예제는 [EnforceOPAConstraint](effects.md#enforceopaconstraint) 효과를 참조하십시오.

정책 정의에서 _details.constraintTemplate_ 및 _details.constraint_ 속성의 일부로 Azure Policy는 이러한 사용자 지정 리소스 정의(CRD)의 URI를 추가 기능으로 [전달합니다.](https://github.com/open-policy-agent/gatekeeper#constraint-templates) Rego는 OPA와 게이트키퍼가 Kubernetes 클러스터에 대한 요청의 유효성을 검사하기 위해 지원하는 언어입니다. Kubernetes 관리에 대한 기존 표준을 지원함으로써 Azure Policy를 사용하면 기존 규칙을 다시 사용하고 통합 클라우드 규정 준수 보고 환경을 위해 Azure 정책과 페어링할 수 있습니다. 자세한 내용은 [Rego이란 무엇입니까?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)

## <a name="built-in-policies"></a>기본 제공 정책

Azure 포털을 사용하여 AKS Engine 클러스터를 관리하기 위한 기본 제공 정책을 찾으려면 다음 단계를 따르십시오.

1. Azure 포털에서 Azure 정책 서비스를 시작합니다. 왼쪽 창에서 **모든 서비스를** 선택한 다음 **정책을**검색하고 선택합니다.

1. Azure 정책 페이지의 왼쪽 창에서 **정의를 선택합니다.**

1. 범주 드롭다운 목록 상자에서 **모두 선택을** 사용하여 필터를 지운 다음 **Kubernetes를**선택합니다.

1. 정책 정의를 선택한 다음 **할당** 단추를 선택합니다.

> [!NOTE]
> AKS 엔진 정의에 대한 Azure 정책을 할당할 때 **범위는** AKS 엔진 클러스터의 리소스 그룹이어야 합니다.

또는 [정책 할당 - 포털](../assign-policy-portal.md) 퀵스타트를 사용하여 AKS 엔진 정책을 찾고 할당합니다. 샘플 '감사 vms' 대신 AKS 엔진 정책 정의를 검색합니다.

> [!IMPORTANT]
> **Kubernetes** 범주의 기본 제공 정책은 AKS 엔진에서만 사용할 수 있습니다.

## <a name="logging"></a>로깅

### <a name="azure-policy-add-on-logs"></a>Azure 정책 추가 기능 로그

Kubernetes 컨트롤러/컨테이너로 Azure 정책 추가 기능은 AKS 엔진 클러스터에 로그를 유지합니다.

Azure 정책 추가 기능 로그를 보려면 `kubectl`다음을 사용합니다.

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>게이트키퍼 로그

게이트키퍼 포드, _게이트키퍼-컨트롤러-관리자-0은_일반적으로 `gatekeeper-system` 또는 `kube-system` 네임스페이스에 있지만 배포 방법에 따라 다른 네임스페이스에 있을 수 있습니다.

게이트키퍼 로그를 보려면 다음을 사용합니다. `kubectl`

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

자세한 내용은 OPA 설명서의 [게이트키퍼 디버깅을](https://github.com/open-policy-agent/gatekeeper#debugging) 참조하십시오.

## <a name="remove-the-add-on"></a>추가 기능 제거

AKS 엔진 클러스터에서 Azure 정책 추가 기능 및 게이트키퍼를 제거하려면 추가 기능이 설치된 방법과 일치하는 메서드를 사용합니다.

- AKS 엔진에 대 한 클러스터 정의에서 **addons** 속성을 설정 하 여 설치 하는 경우:

  _Azure 정책에_ 대한 애드온 속성을 false로 변경한 후 클러스터 정의를 AKS 엔진에 다시 **배포합니다.**


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- 투구 차트와 함께 설치한 경우:

  1. 이전 구속조건 제거

     현재 제거 메커니즘은 게이트키퍼 시스템만 제거하며 사용자가 만든 _제약 조건,_ _제약 조건_또는 _구성_ 리소스를 제거하지 않으며 함께 제공되는 _CRD를_제거하지도 않습니다.

     게이트키퍼가 실행 중일 때 다음을 통해 원치 않는 제약 조건을 제거할 수 있습니다.

     - 제약 조건 리소스의 모든 인스턴스 삭제
     - _CRD를_ 자동으로 정리해야 하는 _제약 조건 템플릿_ 리소스 삭제
     - _구성_ 리소스를 삭제하여 동기화된 리소스에서 종료자를 제거합니다.

  1. Azure 정책 추가 기능 제거
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. 게이트키퍼 제거
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure 정책 추가 기능에서 수집한 진단 데이터

Kubernetes에 대한 Azure 정책 추가 기능이 제한된 클러스터 진단 데이터를 수집합니다. 이 진단 데이터는 소프트웨어 및 성능과 관련된 중요한 기술 데이터입니다. 다음과 같은 방법으로 사용됩니다.

- Azure 정책 추가 기능을 최신 상태로 유지
- Azure 정책 추가 기능 의 안전하고 신뢰할 수 있는 성능 유지
- Azure 정책 추가 기능 개선 - 추가 기능 사용에 대한 집계 분석을 통해

추가 기능에서 수집한 정보는 개인 데이터가 아닙니다. 현재 다음 세부 정보가 수집됩니다.

- Azure 정책 추가 에이전트 버전
- 클러스터 유형
- 클러스터 영역
- 클러스터 리소스 그룹
- 클러스터 리소스 ID
- 클러스터 구독 ID
- 클러스터 OS(예: Linux)
- 클러스터 시티(예: 시애틀)
- 클러스터 시/도(예: 워싱턴)
- 클러스터 국가 또는 지역(예: 미국)
- 정책 평가에 에이전트를 설치하는 동안 Azure 정책 추가 기능에서 발생하는 예외/오류
- Azure 정책 추가 기능에서 설치하지 않은 게이트키퍼 정책 수

## <a name="next-steps"></a>다음 단계

- Azure 정책 [샘플의 예제를 검토합니다.](../samples/index.md)
- 정책 [정의 구조를 검토합니다.](definition-structure.md)
- [정책 효과 이해 검토](effects.md).
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md)방법을 이해합니다.
- [규정 준수 데이터를 얻는](../how-to/get-compliance-data.md)방법에 대해 알아봅니다.
- [비준수 리소스를 수정하는](../how-to/remediate-resources.md)방법에 대해 알아봅니다.
- 관리 그룹이 Azure 관리 그룹으로 리소스 구성을 통해 어떤 내용인지 [검토합니다.](../../management-groups/overview.md)