---
title: Azure Migrate를 사용 하 여 대규모 마이그레이션 프로젝트 관리
description: 위임 된 고객 리소스에 대 한 Azure Migrate를 효과적으로 사용 하는 방법을 알아봅니다.
ms.date: 12/3/2020
ms.topic: how-to
ms.openlocfilehash: 184307814bd3ceae6047734946f79b0ba5cb2e10
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603408"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>Azure Migrate를 사용 하 여 대규모 마이그레이션 프로젝트 관리

서비스 공급자로 서 [Azure Lighthouse](../overview.md)에 여러 고객 테 넌 트를 등록 했을 수 있습니다. Azure Lighthouse를 사용 하면 서비스 공급자가 Azure Active Directory 여러 Azure AD (Azure AD) 테 넌 트에서 한 번에 대규모 작업을 수행 하 여 관리 작업을 보다 효율적으로 만들 수 있습니다.

[Azure Migrate](../../migrate/migrate-services-overview.md) 는 Azure 온-프레미스 서버, 인프라, 응용 프로그램 및 데이터를 평가 하 고 마이그레이션하는 중앙 집중식 허브를 제공 합니다. 일반적으로 여러 고객에 대 한 대규모 평가 및 마이그레이션을 수행 하는 파트너는 [CSP (클라우드 솔루션 공급자) 구독 모델](/partner-center/customers-revoke-admin-privileges) 을 사용 하거나 [고객 테 넌 트에서 게스트 사용자를 만들어](/azure/active-directory/external-identities/what-is-b2b)각 고객 구독에 개별적으로 액세스 해야 합니다.

Azure Lighthouse를 Azure Migrate와 통합 하면 서비스 공급자가 규모에 따라 여러 고객에 대 한 워크 로드를 검색, 평가 및 마이그레이션할 수 있을 뿐만 아니라 고객에 게 환경을 완벽 하 게 표시 하 고 제어할 수 있습니다. Azure 위임 된 리소스 관리를 통해 서비스 공급자는 여러 고객 테 넌 트에서 관리 하는 모든 Azure Migrate 프로젝트의 단일 뷰를 제공 합니다.

> [!NOTE]
> 파트너는 Azure Lighthouse를 통해 온-프레미스 VMware Vm, Hyper-v Vm, 물리적 서버 및 AWS/GCP 인스턴스에 대 한 검색, 평가 및 마이그레이션을 수행할 수 있습니다. [VMWARE VM 마이그레이션](../../migrate/server-migrate-overview.md)에는 두 가지 옵션이 있습니다. 현재 위임 된 고객 구독의 마이그레이션 프로젝트에서 작업 하는 경우에는 에이전트 기반 마이그레이션 메서드만 사용할 수 있습니다. 에이전트 없는 복제를 사용한 마이그레이션은 현재 고객 범위에 대 한 위임 된 액세스를 통해 지원 되지 않습니다.

이 항목에서는 [Azure Migrate](../../migrate/migrate-services-overview.md) 을 확장 가능한 방법으로 사용 하는 방법에 대 한 개요를 제공 합니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만이 지침은 [Azure Lighthouse를 사용 하 여 여러 테 넌 트를 관리](../concepts/enterprise.md)하는 기업에도 적용 됩니다.

시나리오에 따라 고객 테 넌 트 또는 관리 테 넌 트에 Azure Migrate를 만들 수 있습니다. 아래 고려 사항을 검토 하 고 고객의 마이그레이션 요구에 가장 적합 한 모델을 결정 합니다.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>고객 테 넌 트에서 Azure Migrate 프로젝트 만들기

Azure Lighthouse를 사용할 때 한 가지 옵션은 고객 테 넌 트에 Azure Migrate 프로젝트를 만드는 것입니다. 그러면 관리 테 넌 트의 사용자가 마이그레이션 프로젝트를 만들 때 고객 구독을 선택할 수 있습니다. 관리 테 넌 트에서 서비스 공급자는 필요한 마이그레이션 작업을 수행할 수 있습니다. 여기에는 작업을 검색 하 고, Vm을 그룹화 하 고, 클라우드 관련 비용을 계산 하 고, VM 준비 상태를 검토 하 고, 마이그레이션을 수행 하는 Azure Migrate 어플라이언스 배포가 포함 될 수 있습니다.

이 시나리오에서는 검색 및 평가 단계를 시작 하 고 해당 테 넌 트에서 실행할 수 있는 경우에도 리소스가 관리 되는 테 넌 트에 생성 되 고 저장 되지 않습니다. 모든 리소스 (예: 온-프레미스 워크 로드에 대 한 평가 보고서, 온-워크 로드에 대 한 평가 보고서 및 대상 대상의 마이그레이션된 리소스)는 고객 구독에 배포 됩니다. 그러나 서비스 공급자는 자체 테 넌 트 및 포털 환경에서 모든 고객 프로젝트에 액세스할 수 있습니다.

이 방법은 여러 고객 간에 작업 하는 서비스 공급자에 대 한 컨텍스트 전환을 최소화 하는 동시에 고객이 자신의 테 넌 트에 모든 리소스를 유지할 수 있도록 합니다.

이 모델에 대 한 워크플로는 다음과 유사 합니다.

1. 고객은 [Azure Lighthouse로 등록](onboard-customer.md)됩니다. Azure Migrate와 함께 사용 될 id에는 참가자 기본 제공 역할이 필요 합니다.
1. 지정 된 사용자가 Azure Portal에서 관리 하는 테 넌 트에 로그인 한 다음 Azure Migrate로 이동 합니다. 이 사용자는 적절 한 위임 된 고객 구독을 선택 하 여 [Azure Migrate 프로젝트를 만듭니다](/migrate/create-manage-projects.md).
1. 사용자는 [검색 및 평가를 위한 단계를 수행](../../migrate/tutorial-discover-vmware.md)합니다.

   VMware Vm의 경우 어플라이언스를 구성 하기 전에 검색을 vCenter Server 데이터 센터, 클러스터, 클러스터, 호스트, 호스트 폴더 또는 개별 Vm으로 제한할 수 있습니다. 범위를 설정 하려면 어플라이언스에서 vCenter Server 액세스 하는 데 사용 하는 계정에 대 한 사용 권한을 할당 합니다. 이는 여러 고객의 Vm이 하이퍼바이저에서 호스트 되는 경우에 유용 합니다. Hyper-v의 검색 범위는 제한할 수 없습니다.

    > [!NOTE]
    > Azure Lighthouse에서 제공 하는 위임 된 액세스를 통해 Azure Migrate를 사용 하 여 마이그레이션할 VMware 가상 컴퓨터를 검색 하 고 평가할 수 있습니다. VMware 가상 컴퓨터를 마이그레이션하는 경우에는 위임 된 고객 구독의 마이그레이션 프로젝트에서 작업 하는 경우 에이전트 기반 메서드만 현재 지원 됩니다.

1. 대상 고객 구독이 준비 되 면 Azure Lighthouse에서 부여 된 액세스를 통해 마이그레이션을 진행 합니다. 평가 결과 및 마이그레이션된 리소스를 포함 하는 마이그레이션 프로젝트는 고객 테 넌 트의 대상 구독 아래에 생성 됩니다.

> [!TIP]
> 마이그레이션 전에는 기반 인프라 리소스를 프로 비전 하 고 가상 컴퓨터가 마이그레이션되는 구독을 준비 하기 위해 방문 영역을 배포 해야 합니다. 이 방문 영역에서 일부 리소스에 액세스 하거나이를 만들려면 Azure Lighthouse에서 현재 지원 되지 않는 소유자 기본 제공 역할이 필요할 수 있습니다. 이러한 시나리오의 경우 고객은 CSP 구독 모델을 통해 게스트 액세스 역할을 제공 하거나 관리 액세스 권한을 위임 해야 할 수 있습니다. 다중 테 넌 트 방문 영역을 만드는 방법에 대 한 자세한 내용은 GitHub의 [다중 테 넌 트-영역 데모 솔루션](https://github.com/Azure/Multi-tenant-Landing-Zones) 을 참조 하세요.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>관리 테 넌 트에서 Azure Migrate 프로젝트 만들기

이 시나리오에서는 관리 테 넌 트의 사용자가 검색 및 평가와 같은 마이그레이션 관련 작업을 계속 수행 합니다. 그러나 마이그레이션 프로젝트와 관련 된 모든 리소스는 파트너 테 넌 트에 상주 하 고 고객은 프로젝트를 직접 볼 수 없습니다 (원하는 경우 평가를 고객과 공유할 수 있음). 각 고객의 마이그레이션 대상은 고객의 구독입니다.

이 방법을 통해 서비스 공급자는 마이그레이션 검색 및 평가 프로젝트를 신속 하 게 시작 하 여 고객 구독 및 테 넌 트의 초기 단계를 추상화 합니다.

이 모델에 대 한 워크플로는 다음과 유사 합니다.

1. 고객은 [Azure Lighthouse로 등록](onboard-customer.md)됩니다. Azure Migrate와 함께 사용 될 id에는 참가자 기본 제공 역할이 필요 합니다.
1. 지정 된 사용자가 Azure Portal에서 관리 하는 테 넌 트에 로그인 한 다음 Azure Migrate로 이동 합니다. 이 사용자는 관리 테 넌 트에 속하는 구독에 [Azure Migrate 프로젝트를 만듭니다](/migrate/create-manage-projects.md) .
1. 사용자는 [검색 및 평가를 위한 단계를 수행](../../migrate/tutorial-discover-vmware.md)합니다. 온-프레미스 Vm은 관리 테 넌 트에서 생성 된 마이그레이션 프로젝트 내에서 검색 되 고 평가 된 후 여기에서 마이그레이션됩니다.

   동일한 Hyper-v 호스트에서 여러 고객을 관리 하는 경우 모든 워크 로드를 한 번에 검색할 수 있습니다. 특정 고객의 Vm을 동일한 그룹에서 선택할 수 있으며, 평가를 만들 수 있으며, 적절 한 고객의 구독을 대상 대상으로 선택 하 여 마이그레이션을 수행할 수 있습니다. 검색 범위를 제한 하지 않아도 되며, 하나의 마이그레이션 프로젝트에서 모든 고객 작업에 대 한 전체 개요를 유지할 수 있습니다.

1. 준비가 되 면 작업을 복제 하 고 마이그레이션하기 위한 대상 대상으로 위임 된 고객 구독을 선택 하 여 마이그레이션을 진행 합니다. 새로 만든 리소스는 고객 구독에 존재 하는 반면, 마이그레이션 프로젝트와 관련 된 평가 데이터 및 리소스는 관리 테 넌 트에 유지 됩니다.

## <a name="partner-recognition-for-customer-migrations"></a>고객 마이그레이션을 위한 파트너 인식

[Microsoft 파트너 네트워크](https://partner.microsoft.com)의 멤버로, 위임 된 고객 리소스를 관리 하는 데 사용 되는 자격 증명과 파트너 ID를 연결할 수 있습니다. PAL (파트너 관리자 링크)을 통해 Microsoft는 마이그레이션 프로젝트를 비롯 하 여 고객에 대해 수행 하는 작업을 기반으로 하 여 조직에 영향을 주고 Azure에서 사용 하는 수익에 대 한 영향을

자세한 내용은 [파트너 ID를 연결하여 위임된 리소스에 대한 영향 추적](partner-earned-credit.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Migrate](../../migrate/migrate-services-overview.md)에 대해 알아봅니다.
- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.

