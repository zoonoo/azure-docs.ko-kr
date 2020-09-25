---
title: Azure Arc를 사용 하 여 대규모로 하이브리드 인프라 관리
description: Azure 외부에서 고객의 컴퓨터 및 Kubernetes 클러스터를 효과적으로 관리 하는 방법을 알아봅니다.
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 66a798265683045d7ff9f3d8d811141800d08f9b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336618"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Azure Arc를 사용 하 여 대규모로 하이브리드 인프라 관리

서비스 공급자로 서 [Azure Lighthouse](../overview.md)에 여러 고객 테 넌 트를 등록 했을 수 있습니다. Azure Lighthouse를 사용 하면 서비스 공급자가 Azure Active Directory 여러 Azure AD (Azure AD) 테 넌 트에서 한 번에 대규모 작업을 수행 하 여 관리 작업을 보다 효율적으로 만들 수 있습니다.

[Azure Arc](../../azure-arc/overview.md) 를 사용 하면 온-프레미스, edge 및 다중 클라우드에서 복잡 하 고 분산 된 환경을 간소화 하 여 azure 서비스를 어디서 나 배포 하 고 azure 관리를 모든 인프라로 확장할 수 있습니다.

[Azure Arc 사용 서버](../../azure-arc/servers/overview.md)를 사용 하는 경우 고객은 기본 azure virtual machines를 관리 하는 것과 같은 방식으로 회사 네트워크의 azure 외부에서 호스트 되는 모든 Windows 및 Linux 컴퓨터를 관리할 수 있습니다. 하이브리드 머신을 Azure에 연결하면 해당 머신은 Azure에서 리소스로 연결되고 처리됩니다. 그런 다음 서비스 공급자는 고객의 Azure 리소스와 함께 이러한 비 Azure 컴퓨터를 관리할 수 있습니다.

[Azure Arc Enabled Kubernetes (미리 보기)](../../azure-arc/kubernetes/overview.md) 를 통해 고객은 azure 내부 또는 외부에서 Kubernetes 클러스터를 연결 하 고 구성할 수 있습니다. Kubernetes 클러스터가 Azure Arc에 연결 되 면 Azure Resource Manager ID와 관리 되는 id를 사용 하 여 Azure Portal에 표시 됩니다. 클러스터는 표준 Azure 구독에 연결되고, 리소스 그룹에 위치하며, 다른 Azure 리소스와 마찬가지로 태그를 받을 수 있습니다.

이 항목에서는 관리 되는 모든 고객 테 넌 트에 대 한 가시성을 통해 서비스 공급자가 Azure Arc 사용 서버 및 Azure Arc enabled Kubernetes (미리 보기)를 확장 가능한 방법으로 사용 하 여 고객의 하이브리드 환경을 관리 하는 방법에 대 한 개요를 제공 합니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만이 지침은 [Azure Lighthouse를 사용 하 여 여러 테 넌 트를 관리](../concepts/enterprise.md)하는 기업에도 적용 됩니다.

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>Azure Arc 사용 서버를 사용 하 여 대규모로 하이브리드 서버 관리

서비스 공급자는 azure 외부의 온-프레미스 Windows Server 또는 Linux 컴퓨터를 관리 하 여 고객이 [Azure 연결 된 컴퓨터 에이전트](../../azure-arc/servers/agent-overview.md)를 사용 하 여 구독에 연결한 것을 확인할 수 있습니다.

Azure Portal에서 위임 된 구독에 대 한 리소스를 볼 때 **Azure Arc**로 레이블이 지정 된 연결 된 컴퓨터가 표시 됩니다. 고객의 Azure 리소스를 관리 하는 것과 같은 방식으로 Azure Policy 및 태그 지정과 같은 Azure 구문을 사용 하 여 이러한 연결 된 컴퓨터를 관리할 수 있습니다. 또한 고객 테 넌 트 간에 작업 하 여 연결 된 모든 하이브리드 컴퓨터를 함께 관리할 수 있습니다.

예를 들어 [동일한 정책 집합이 고객의 하이브리드 컴퓨터에 적용 되도록](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)할 수 있습니다. 또한 Azure Security Center를 사용 하 여 모든 고객의 하이브리드 환경에서 준수를 모니터링 하거나 [Azure Monitor를 사용 하 여 하이브리드 컴퓨터에서](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md) Log Analytics 작업 영역으로 데이터를 직접 수집할 수 있습니다. [가상 머신 확장](../../azure-arc/servers/manage-vm-extensions.md) 은 비 Azure Windows 및 Linux vm에 배포할 수 있으며, 고객의 하이브리드 컴퓨터 관리를 간소화 합니다.

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes-preview"></a>Azure Arc enabled Kubernetes (미리 보기)를 사용 하 여 대규모로 하이브리드 Kubernetes 클러스터 관리

> [!NOTE]
> Azure Arc enabled Kubernetes는 현재 미리 보기 상태입니다. 지금은 프로덕션 워크 로드에는 권장 하지 않습니다.

Azure에서 실행 되는 것 처럼 [Azure Arc를 사용 하 여 고객 구독에 연결](../../azure-arc/kubernetes/connect-cluster.md)된 Kubernetes 클러스터를 관리할 수 있습니다.

고객이 [Kubernetes 클러스터를 Azure Arc에 등록 하는 서비스 주체 계정을](../../azure-arc/kubernetes/create-onboarding-service-principal.md)만든 경우이 서비스 사용자 계정에 액세스 하 여 클러스터를 등록 하 고 관리할 수 있습니다. 이 작업은 서비스 주체 계정이 포함 된 구독이 [Azure Lighthouse로 등록](onboard-customer.md)때 "Kubernetes Cluster-Azure Arc 온 보 딩" azure 기본 제공 역할이 부여 된 관리 테 넌 트의 사용자가 수행할 수 있습니다.

연결 된 클러스터에 GitOps를 사용 하 여 [구성](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) 및 [투구 차트](../../azure-arc/kubernetes/use-gitops-with-helm.md) 를 배포할 수 있습니다.

또한 Azure Monitor를 사용 하 여 연결 된 클러스터를 모니터링 하 고 [Azure Policy를 사용 하 여 클러스터 구성을 대규모로 적용할](../../azure-arc/kubernetes/use-azure-policy.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Arc GitHub 리포지토리에서](https://github.com/microsoft/azure_arc)전력망 및 샘플을 탐색 합니다. 
- [Azure Arc 사용 서버에 대해 지원 되는 시나리오](../../azure-arc/servers/overview.md#supported-scenarios)에 대해 알아봅니다.
- [Azure Arc에서 지 원하는 Kubernetes 배포판](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)에 대해 알아봅니다.
- [대규모로 정책을 배포](policy-at-scale.md)하는 방법에 대해 알아봅니다.
- [대규모로 Azure Monitor 로그를 사용](monitor-at-scale.md)하는 방법에 대해 알아봅니다.

