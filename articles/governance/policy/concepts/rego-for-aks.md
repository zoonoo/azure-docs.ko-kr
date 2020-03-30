---
title: Azure Kubernetes 서비스에 대한 Azure 정책 알아보기
description: Azure 정책이 Rego 및 Open 정책 에이전트를 사용하여 Azure Kubernetes 서비스의 클러스터를 관리하는 방법을 알아봅니다.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372653"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Azure Kubernetes Service에 대한 Azure Policy 이해

Azure 정책은 AZURE [Kubernetes](../../../aks/intro-kubernetes.md) 서비스(AKS)와 통합되어 중앙 집중적이고 일관된 방식으로 클러스터에 대규모 적용 및 보호 장치를 적용합니다.
개방형 정책 에이전트(OPA)에 대한 설치 [Open Policy Agent](https://www.openpolicyagent.org/) _컨트롤러 웹후크인_ [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3의 사용을 확장함으로써 Azure 정책을 사용하면 Azure 리소스 및 AKS 클러스터의 규정 준수 상태를 한 곳에서 관리하고 보고할 수 있습니다.

> [!IMPORTANT]
> AKS에 대한 Azure 정책은 미리 보기에 있으며 기본 제공 정책 정의만 지원합니다. 기본 제공 정책은 **Kubernetes** 범주에 있습니다. **강제 RegoPolicy** 효과 및 관련 **Kubernetes 서비스** 범주 정책 은 더 이상 _사용되지_않습니다. 대신 업데이트된 [EnforceOPAConstraint](./effects.md#enforceopaconstraint) 효과를 사용합니다.

> [!WARNING]
> 이 기능은 아직 모든 지역에서 사용할 수 없습니다. 롤아웃에 대한 상태는 [AKS 문제 - 정책 추가 기능의 주요 변경](https://github.com/Azure/AKS/issues/1529)을 참조하십시오.

## <a name="overview"></a>개요

AKS 클러스터에서 AKS에 대한 Azure 정책을 활성화하고 사용하려면 다음 작업을 수행하십시오.

- [미리 보기 기능용 옵트인](#opt-in-for-preview)
- [Azure 정책 추가 기능 설치](#installation-steps)
- [AKS에 대한 정책 정의 할당](#built-in-policies)
- [유효성 검사 대기](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>미리 보기에 대한 옵트인

Azure 정책 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 구독에서 **Microsoft.ContainerService** 리소스 공급자 및 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정한 다음 미리 보기에 참여하도록 승인을 받아야 합니다. 미리 보기에 참여하려면 Azure 포털 또는 Azure CLI에서 다음 단계를 따릅니다.

- Azure Portal:

  1. **Microsoft.ContainerService** 및 **Microsoft.PolicyInsights** 리소스 공급자를 등록합니다. 단계는 [리소스 공급자 및 형식을](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)참조하십시오.

  1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

     ![모든 서비스에서 정책 검색](../media/rego-for-aks/search-policy.png)

  1. Azure 정책 페이지의 왼쪽에서 **미리 보기 조인을** 선택합니다.

     ![AKS 미리 보기에 대한 정책 참여](../media/rego-for-aks/join-aks-preview.png)

  1. 미리 보기에 추가할 구독행을 선택합니다.

  1. 구독 목록 상단의 **수신 버튼을** 선택합니다.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Azure 정책 추가 기능

Kubernetes에 대한 _Azure 정책 추가 기능이_ Azure 정책 서비스를 게이트키퍼 승인 컨트롤러에 연결합니다. _kube 시스템_ 네임스페이스에 설치된 추가 기능은 다음과 같은 기능을 합니다.

- Azure 정책 서비스로 클러스터에 대한 할당을 확인합니다.
- 클러스터의 정책을 제약 [조건 템플릿](https://github.com/open-policy-agent/gatekeeper#constraint-templates) 및 [제약 조건부](https://github.com/open-policy-agent/gatekeeper#constraints) 사용자 지정 리소스로 배포합니다.
- 감사 및 규정 준수 세부 정보를 Azure 정책 서비스에 다시 보고합니다.

### <a name="installing-the-add-on"></a>추가 기능 설치

#### <a name="prerequisites"></a>사전 요구 사항

AKS 클러스터에 추가 기능을 설치하기 전에 미리 보기 확장을 설치해야 합니다. 이 단계는 Azure CLI를 통해 수행됩니다.

1. 게이트키퍼 v2 정책이 설치된 경우 **정책(미리 보기)** 페이지에서 AKS 클러스터의 **사용 안 함** 버튼으로 추가 기능을 제거합니다.

1. Azure CLI 버전 2.0.62 이상 설치 및 구성이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. AKS 클러스터는 버전 _1.14_ 이상이어야 합니다. 다음 스크립트를 사용하여 AKS 클러스터 버전의 유효성을 검사합니다.

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. AKS에 대한 Azure CLI 미리 보기 확장의 `aks-preview`버전 _0.4.0을_ 설치합니다.

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 이전에 _aks 미리 보기_ 확장을 설치한 경우 명령을 `az extension update --name aks-preview` 사용하여 업데이트를 설치합니다.

#### <a name="installation-steps"></a>설치 단계

필수 구성 프로그램이 완료되면 관리하려는 AKS 클러스터에 Azure 정책 추가 기능을 설치합니다.

- Azure portal

  1. **모든 서비스를**클릭한 다음 **Kubernetes 서비스를**검색하고 선택하여 Azure 포털에서 AKS 서비스를 시작합니다.

  1. AKS 클러스터 중 하나를 선택합니다.

  1. Kubernetes 서비스 페이지 왼쪽에서 **정책(미리 보기)을** 선택합니다.

     ![AKS 클러스터의 정책](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 기본 페이지에서 **추가 기능 활성화** 단추를 선택합니다.

     ![AKS 추가 기능의 Azure 정책 사용](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > 추가 **기능 활성화** 버튼이 회색으로 표시되면 구독이 미리 보기에 아직 추가되지 않은 것입니다. 필요한 [단계는 미리 보기를 보려면 옵트인을](#opt-in-for-preview) 참조하십시오. 사용 **안 함** 단추를 사용할 수 있는 경우 게이트키퍼 v2는 여전히 설치되어 있으며 제거해야 합니다.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>검증 및 보고 빈도

추가 기능 은 15분마다 정책 할당의 변경 내용을 Azure Policy 서비스에 체크 인합니다.
이 새로 고침 주기 동안 추가 기능 변경 내용을 확인 합니다. 이러한 변경 사항은 제약 조건 템플릿 및 제약 조건을 생성, 업데이트 또는 삭제합니다.

> [!NOTE]
> 클러스터 관리자는 제약 조건 템플릿 및 제약 조건 리소스를 만들고 업데이트할 수 있는 권한이 있을 수 있지만 수동 업데이트가 덮어쓰므로 지원되는 시나리오는 아닙니다.

15분마다 추가 기능을 사용하면 클러스터의 전체 스캔이 호출됩니다. 전체 검사에 대한 세부 정보와 클러스터에 대한 변경 시도의 Gatekeeper의 실시간 평가를 수집한 후 추가 기능에서는 결과를 Azure Policy 할당과 같은 [규정 준수 세부 정보에](../how-to/get-compliance-data.md#portal) 포함시키기 위해 Azure Policy 서비스로 다시 보고합니다. 활성 정책 할당에 대한 결과만 감사 주기 중에 반환됩니다. 감사 결과는 실패한 제약 조건의 상태 필드에 나열된 [위반으로도](https://github.com/open-policy-agent/gatekeeper#audit) 볼 수 있습니다.

## <a name="policy-language"></a>정책 언어

Kubernetes 관리를 위한 Azure 정책 언어 구조는 기존 정책의 구조를 따릅니다. _시행OpAConstraint_ 효과는 Kubernetes 클러스터를 관리하는 데 사용되며 [OPA 제약 프레임워크](https://github.com/open-policy-agent/frameworks/tree/master/constraint) 및 게이트키퍼 v3 작업과 관련된 세부 정보 속성을 취합니다. 자세한 내용 및 예제는 [EnforceOPAConstraint](./effects.md#enforceopaconstraint) 효과를 참조하십시오.
  
정책 정의에서 _details.constraintTemplate_ 및 _details.constraint_ 속성의 일부로 Azure Policy는 이러한 사용자 지정 리소스 정의(CRD)의 URI를 추가 기능으로 [전달합니다.](https://github.com/open-policy-agent/gatekeeper#constraint-templates) Rego는 OPA와 게이트키퍼가 Kubernetes 클러스터에 대한 요청의 유효성을 검사하기 위해 지원하는 언어입니다. Kubernetes 관리에 대한 기존 표준을 지원함으로써 Azure Policy를 사용하면 기존 규칙을 다시 사용하고 통합 클라우드 규정 준수 보고 환경을 위해 Azure 정책과 페어링할 수 있습니다. 자세한 내용은 [Rego이란 무엇입니까?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="built-in-policies"></a>기본 제공 정책

Azure 포털을 사용하여 클러스터를 관리하기 위한 기본 제공 정책을 찾으려면 다음 단계를 따르십시오.

1. Azure 포털에서 Azure 정책 서비스를 시작합니다. 왼쪽 창에서 모든 서비스를 선택한 다음 **정책을**검색하고 선택합니다.

1. Azure 정책 페이지의 왼쪽 창에서 **정의를 선택합니다.**

1. 범주 드롭다운 목록 상자에서 모두 선택을 사용하여 필터를 지운 다음 **Kubernetes를**선택합니다.

1. 정책 정의를 선택한 다음 **할당** 단추를 선택합니다.

1. **범위를** 정책 할당이 적용되는 Kubernetes 클러스터의 관리 그룹, 구독 또는 리소스 그룹으로 설정합니다.

   > [!NOTE]
   > AKS 정의에 대한 Azure 정책을 할당할 때 **범위에** AKS 클러스터 리소스가 포함되어야 합니다.

1. 정책 할당에 쉽게 식별하는 데 사용할 수 있는 **이름과** **설명을** 지정합니다.

1. 정책 [적용을](./assignment-structure.md#enforcement-mode) 아래 값 중 하나로 설정합니다.

   - **사용** - 클러스터에 정책을 적용합니다. 위반시 Kubernetes 입학 요청은 거부됩니다.
   
   - **사용 안 함** - 클러스터에 정책을 적용하지 마십시오. 위반이 있는 Kubernetes 입학 요청은 거부되지 않습니다. 규정 준수 평가 결과는 여전히 사용할 수 있습니다. 클러스터 실행에 새 정책을 롤아웃할 때 _사용 중지_ 옵션은 위반이 있는 승인 요청이 거부되지 않으므로 정책을 테스트하는 데 유용합니다.

1. **다음**을 선택합니다.

1. **매개변수 값** 설정
   
   - 정책 평가에서 Kubernetes 네임스페이스를 제외하려면 매개 변수 **네임스페이스 제외에서**네임스페이스 목록을 지정합니다. 제외하는 것이 좋습니다: _쿠베 시스템_

1. **검토 + 만들기를**선택합니다.

또는 [정책 할당 - 포털](../assign-policy-portal.md) 퀵스타트를 사용하여 AKS 정책을 찾고 할당합니다. 샘플 '감사 vms' 대신 Kubernetes 정책 정의를 검색합니다.

> [!IMPORTANT]
> **Kubernetes** 범주의 기본 제공 정책은 AKS에서만 사용할 수 있습니다. 기본 제공 정책 목록은 [Kubernetes 샘플을](../samples/built-in-policies.md#kubernetes)참조하십시오.

## <a name="logging"></a>로깅

### <a name="azure-policy-add-on-logs"></a>Azure 정책 추가 기능 로그

Kubernetes 컨트롤러/컨테이너로 Azure 정책 추가 기능 및 게이트키퍼 모두 AKS 클러스터에 로그를 유지합니다. 로그는 AKS 클러스터의 **인사이트** 페이지에 노출됩니다. 자세한 내용은 [컨테이너에 대한 Azure 모니터를 통해 AKS 클러스터 성능 이해를](../../../azure-monitor/insights/container-insights-analyze.md)참조하십시오.

## <a name="remove-the-add-on"></a>추가 기능 제거

AKS 클러스터에서 Azure 정책 추가 기능을 제거하려면 Azure 포털 또는 Azure CLI를 사용합니다.

- Azure portal

  1. **모든 서비스를**클릭한 다음 **Kubernetes 서비스를**검색하고 선택하여 Azure 포털에서 AKS 서비스를 시작합니다.

  1. Azure 정책 추가 기능을 사용하지 않도록 설정할 AKS 클러스터를 선택합니다.

  1. Kubernetes 서비스 페이지 왼쪽에서 **정책(미리 보기)을** 선택합니다.

     ![AKS 클러스터의 정책](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. 기본 페이지에서 **추가 기능 사용 안 함** 단추를 선택합니다.

     ![AKS 추가 기능의 Azure 정책을 사용하지 않도록 설정](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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
