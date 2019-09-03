---
title: Azure Lighthouse 및 클라우드 솔루션 공급자 프로그램
description: Azure 위임 리소스 관리를 사용하는 경우 보안 및 액세스 제어를 고려하는 것이 중요합니다.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 399d2cb829c0425e3c9ee70a61cafde8568f903b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012093"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse 및 클라우드 솔루션 공급자 프로그램

[CSP(클라우드 솔루션 공급 기업)](https://docs.microsoft.com/partner-center/csp-overview) 파트너인 경우 [AOBO(관리 위임자)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 기능을 사용하여 CSP 프로그램을 통해 고객용으로 생성된 Azure 구독에 액세스할 수 있습니다. 이 액세스를 통해 고객의 구독을 직접 지원, 구성 및 관리할 수 있습니다.

Azure Lighthouse를 사용하면 AOBO와 함께 Azure 위임 리소스 관리를 사용할 수 있습니다. 이렇게 하면 사용자에게 더 세분화된 권한을 부여하여 보안을 개선하고 불필요한 액세스를 줄일 수 있습니다. 또한 사용자가 테넌트에서 단일 로그인을 사용하여 여러 고객 구독에서 작업할 수 있으므로 효율성 및 확장성이 향상됩니다.

## <a name="administer-on-behalf-of-aobo"></a>AOBO(관리 위임자)

AOBO를 사용하는 경우 테넌트에서 [관리 에이전트](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) 역할이 있는 사용자는 CSP 프로그램을 통해 만든 Azure 구독에 대해 AOBO 액세스 권한이 있습니다. 고객의 구독에 액세스해야 하는 모든 사용자는 이 그룹의 멤버여야 합니다. AOBO는 다른 고객에게 작용하는 고유한 그룹을 만들거나 그룹이나 사용자에 대해 다른 역할을 사용하도록 설정하는 유연성을 허용하지 않습니다.

![AOBO를 사용한 테넌트 관리](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure 위임 리소스 관리

Azure 위임 리소스 관리를 사용하여 다음 다이어그램에 표시된 것처럼 다른 고객 또는 역할에 다른 그룹을 할당할 수 있습니다. 사용자는 Azure 위임 리소스 관리를 통해 적절한 수준의 액세스 권한을 얻게 되므로 관리 에이전트 역할(즉, 전체 AOBO 액세스 권한)이 있는 사용자의 수를 줄일 수 있습니다. 이를 통해 고객의 리소스에 대한 불필요한 액세스가 제한되므로 보안이 강화됩니다. 또한 보다 유연하게 대규모로 여러 고객을 관리할 수 있습니다.

CSP 프로그램을 통해 만든 구독을 온보딩할 때는 [Azure 위임 리소스 관리에 구독 온보딩](../how-to/onboard-customer.md)에 설명된 단계를 수행하게 됩니다. 테넌트에서 관리 에이전트 역할이 있는 사용자는 이 온보딩을 수행할 수 있습니다.

![AOBO 및 Azure 위임 리소스 관리를 사용한 테넌트 관리](../media/csp-2.jpg)

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure 위임 리소스 관리에 구독을 등록](../how-to/onboard-customer.md)하는 방법을 알아봅니다.
- [클라우드 솔루션 공급자 프로그램](https://docs.microsoft.com/partner-center/csp-overview)에 대해 알아봅니다.
