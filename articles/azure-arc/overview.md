---
title: Azure Arc 개요
description: Azure Arc란 무엇이며 고객이 다른 Azure 서비스 및 기능을 사용하여 하이브리드 리소스의 관리 및 거버넌스를 활성화하는 데 어떤 도움이 되는지 알아봅니다.
ms.date: 09/23/2020
ms.topic: overview
ms.openlocfilehash: 8e48378e5032768b3f56a5d99c1189e282ff37f8
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714144"
---
# <a name="azure-arc-overview"></a>Azure Arc 개요

오늘날 회사는 점점 더 복잡해지면서 환경을 제어하고 관리하는 데 노력하고 있습니다. 이러한 환경은 데이터 센터, 여러 클라우드 및 에지 간에 확장됩니다. 각 환경 및 클라우드에는 학습하고 작동해야 하는 자체적인 연결되지 않은 관리 도구 집합이 있습니다.

기존 도구가 새로운 클라우드 네이티브 패턴에 대한 지원을 제공하지 않으므로 새 DevOps 및 ITOps 운영 모델은 구현하기 어렵습니다.

Azure Arc는 일관적인 다중 클라우드 및 온-프레미스 관리 플랫폼을 제공하여 거버넌스 및 관리를 간소화합니다. Azure Arc를 사용하면 기존 리소스를 Azure Resource Manager로 프로젝션하여 단일 창으로 전체 환경을 관리할 수 있습니다. 이제 Azure에서 실행되는 것처럼 가상 머신, Kubernetes 클러스터 및 데이터베이스를 관리할 수 있습니다. 실제 위치에 관계없이 친숙한 Azure 서비스 및 관리 기능을 사용할 수 있습니다. Azure Arc를 사용하면 기존 ITOps를 계속 사용할 수 있으며, 사용자 환경에서 새로운 클라우드 기본 패턴을 지원하기 위한 DevOps 사례를 도입할 수 있습니다.

:::image type="content" source="./media/overview/azure-arc-control-plane.png" alt-text="Azure Arc 관리 컨트롤 플레인 다이어그램" border="false":::

현재 Azure Arc를 사용하면 Azure 외부에서 호스트되는 다음 리소스 종류를 관리할 수 있습니다.

* 서버 - Windows 또는 Linux를 실행하는 물리적 머신 및 가상 머신
* Kubernetes 클러스터 - 여러 Kubernetes 배포 지원
* Azure 데이터 서비스 - Azure SQL Database 및 PostgreSQL 하이퍼스케일 서비스

## <a name="what-does-azure-arc-deliver"></a>Azure Arc는 어떤 기능을 제공하나요?

Azure Arc의 핵심 기능은 다음과 같습니다.

* 사용자 환경에서 서버에 대한 일관된 인벤토리, 관리, 거버넌스 및 보안을 구현합니다.

* Azure 관리 서비스를 사용하여 서버를 모니터링하고, 보안을 설정하고, 업데이트하도록 [Azure VM 확장](./servers/manage-vm-extensions.md)을 구성합니다.

* 대규모로 Kubernetes 클러스터를 관리하고 제어합니다.

* GitOps 기반 구성을 코드 관리로 사용하여 GitHub와 같은 원본 제어에서 직접 하나 이상의 클러스터에 애플리케이션 및 구성을 배포합니다.

* Azure Policy를 사용하는 Kubernetes 클러스터에 대한 제로 터치 준수 및 구성

* Azure에서 실행되는 것처럼 업그레이드/업데이트, 보안 및 모니터링 등의 혜택을 통해 Kubernetes 환경, 특히 Azure SQL Managed Instance 및 Azure Database for PostgreSQL 하이퍼스케일에서 Azure 데이터 서비스를 실행합니다. Azure에 대한 지속적인 연결이 없는 경우에도 탄력적 확장을 활용하고, 애플리케이션 가동 중지 시간 없이 업데이트를 적용합니다.

* Azure Portal, Azure CLI, Azure PowerShell 또는 Azure REST API를 사용하는지에 관계없이 Azure Arc 지원 리소스를 보는 통합 환경입니다.

## <a name="how-much-does-azure-arc-cost"></a>Azure Arc 비용은 얼마입니까?

다음은 현재 Azure Arc에서 사용 가능한 기능에 대한 가격 정보입니다.

### <a name="arc-enabled-servers"></a>Arc 지원 서버

Azure Arc 컨트롤 플레인 기능은 추가 비용 없이 제공됩니다.다음 내용이 포함됩니다.

* Azure 관리 그룹 및 태그 태그를 통한 리소스 구성.

* Azure Resource Graph를 통한 검색 및 인덱싱.

* Azure RBAC 및 구독을 통한 액세스 및 보안.

* 템플릿과 확장을 통한 환경 및 자동화.

* 업데이트 관리

Azure Security Center 또는 Azure Monitor와 같이 Arc 지원 서버에서 사용되는 모든 Azure 서비스는 해당 서비스에 대한 가격 책정에 따라 요금이 청구됩니다. 자세한 내용은 [Azure 가격 책정 페이지](https://azure.microsoft.com/pricing/)를 참조하세요.

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc를 지원하는 Kubernetes

현재 미리 보기 단계에서 Azure Arc 지원 Kubernetes는 추가 비용 없이 제공됩니다.

### <a name="azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스

현재 미리 보기 단계에서 Azure Arc 지원 데이터 서비스는 추가 비용 없이 제공됩니다.

## <a name="next-steps"></a>다음 단계

* Arc 지원 서버에 대한 자세한 내용은 다음 [개요](./servers/overview.md)를 참조하세요.

* Arc 지원 Kubernetes에 대한 자세한 내용은 다음 [개요](./kubernetes/overview.md)를 참조하세요.

* Arc 지원 데이터 서비스에 대한 자세한 내용은 다음 [개요](https://azure.microsoft.com/services/azure-arc/hybrid-data-services/)를 참조하세요.
