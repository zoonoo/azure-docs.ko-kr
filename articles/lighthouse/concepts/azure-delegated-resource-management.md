---
title: Azure 위임 리소스 관리
description: 관리형 서비스를 통해 서비스 공급자는 Azure Marketplace에서 고객에게 리소스 관리 제품을 판매할 수 있습니다.
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: e6f741ccbf0d52e0859722aa304aa6c273fb5acf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84219082"
---
# <a name="azure-delegated-resource-management"></a>Azure 위임 리소스 관리

Azure 위임 리소스 관리는 Azure Lighthouse의 주요 구성 요소 중 하나입니다. Azure 위임 리소스 관리를 통해 서비스 공급자는 고객 참여 및 온보딩 환경을 간소화하는 동시에, 민첩하고 정확하게 대규모의 위임된 리소스를 관리할 수 있습니다.

## <a name="what-is-azure-delegated-resource-management"></a>Azure 위임 리소스 관리란?

Azure 위임 리소스 관리를 통해 한 테넌트에서 다른 테넌트로 리소스를 논리적으로 프로젝션할 수 있습니다. 이렇게 하면 한 Azure AD(Azure Active Directory) 테넌트의 권한 있는 사용자가 해당 고객에게 속한 여러 다른 Azure AD 테넌트에서 관리 작업을 수행할 수 있습니다. 서비스 공급자는 자신의 Azure AD 테넌트에 로그인할 수 있으며 위임된 고객 구독 및 리소스 그룹에서 작업을 수행할 수 있는 권한을 받습니다. 이를 통해 각 개별 고객 테넌트에 로그인하지 않고도 고객을 대신하여 관리 작업을 수행할 수 있습니다.

> [!NOTE]
> 다중 테 넌 트 관리를 간소화 하기 위해 [자체의 여러 AZURE AD 테 넌 트가 있는 엔터프라이즈 내에서](enterprise.md) azure 위임 된 리소스 관리를 사용할 수도 있습니다.

Azure 위임 리소스 관리를 통해 권한 있는 사용자는 고객의 테넌트에 계정이 없거나 고객 테넌트의 공동 소유자가 되지 않고도 고객 구독의 컨텍스트에서 직접 작업할 수 있습니다. 또한 Azure Portal의 [새 **내 고객** 페이지에서 모든 위임된 고객 구독을 보고 관리](../how-to/view-manage-customers.md)할 수 있습니다.

[테넌트 간 관리 환경](cross-tenant-management-experience.md)을 사용하면 Azure Policy, Azure Security Center 등과 같은 Azure 관리 서비스를 보다 효율적으로 사용할 수 있습니다. 모든 서비스 공급자 작업은 고객의 테 넌 트에 저장 되는 활동 로그에서 추적 되며, 관리 테 넌 트의 사용자가 볼 수 있습니다. 즉, 고객과 서비스 공급자 모두 변경 내용과 관련된 사용자를 쉽게 식별할 수 있습니다.

Azure에서 위임 된 리소스 관리에 고객을 등록 하는 경우 사용자는 [제품, 서비스 공급자 및 위임 된 리소스를 확인 하 고 관리할](../how-to/view-manage-service-providers.md)수 있는 Azure Portal의 새 **서비스 공급자** 페이지에 액세스할 수 있습니다. 고객은 서비스 공급자에 대한 액세스 권한을 취소하려는 경우 언제든지 취소할 수 있습니다.

[새 관리 서비스 제안 유형을 Azure Marketplace에 게시](../how-to/publish-managed-services-offers.md) 하 여 고객에 게 Azure 위임 된 리소스 관리에 쉽게 등록할 수 있습니다. 또는 [Azure Resource Manager 템플릿을 배포하여 온보딩 프로세스를 완료](../how-to/onboard-customer.md)할 수 있습니다.

## <a name="how-azure-delegated-resource-management-works"></a>Azure 위임 리소스 관리의 작동 원리

다음은 Azure 위임 리소스 관리의 작동 원리를 개괄적으로 설명한 것입니다.

1. 서비스 공급자는 그룹, 서비스 주체 또는 사용자가 고객의 Azure 리소스를 관리하는 데 필요한 액세스 권한(역할)을 식별합니다. 액세스 정의에는 [기본 제공 **roleDefinition** 값](../../role-based-access-control/built-in-roles.md)(참가자, VM 참가자, 읽기 권한자 등)에 매핑된 테넌트의 **principalId**를 사용하여 정의된 제품의 필수 액세스 권한과 서비스 공급자의 테넌트 ID가 포함됩니다.
2. 다음 두 가지 방법 중 하나를 사용하여 이 액세스 권한을 지정하고 고객을 Azure 위임 리소스 관리에 온보딩합니다.
   - 고객이 허용할 [Azure Marketplace 관리 서비스 제품](../how-to/publish-managed-services-offers.md) (개인 또는 공용)을 게시 합니다.
   - 하나 이상의 특정 구독 또는 리소스 그룹에 대해 [고객의 테넌트에 Azure Resource Manager 템플릿 배포](../how-to/onboard-customer.md)
3. 고객이 온보딩한 후에는 권한 있는 사용자는 서비스 공급자 테넌트에 로그인한 후, 사용자가 정의한 액세스 권한에 따라, 지정된 고객 범위에서 관리 작업을 수행할 수 있습니다.

> [!NOTE]
> 다른 [지역](../../availability-zones/az-overview.md#regions)에 있는 위임 된 리소스를 관리할 수 있습니다. 그러나 [국가 클라우드](../../active-directory/develop/authentication-national-cloud.md) 및 Azure 공용 클라우드 또는 별도의 두 국가에서 구독을 위임 하는 것은 지원 되지 않습니다.

## <a name="support-for-azure-delegated-resource-management"></a>Azure 위임 리소스 관리에 대한 지원

Azure 위임 리소스 관리와 관련하여 도움이 필요한 경우 Azure Portal에서 지원 요청을 열 수 있습니다. **문제 유형**에서 **기술**을 선택합니다. 구독을 선택한 다음 **Lighthouse** ( **모니터링 & 관리**아래)를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure Marketplace의 관리형 서비스 제품](managed-services-offers.md)에 대해 알아봅니다.