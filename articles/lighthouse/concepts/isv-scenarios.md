---
title: ISV 시나리오의 Azure Lighthouse
description: Isv는 Azure Lighthouse의 기능을 사용 하 여 고객 제품의 유연성을 높일 수 있습니다.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: d6a12a51d360ad236563b871dbd94cc442ade434
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97696356"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV 시나리오의 Azure Lighthouse

[Azure Lighthouse](../overview.md) 에 대 한 일반적인 시나리오는 고객의 azure AD (Azure Active Directory) 테 넌 트에서 리소스를 관리 하는 서비스 공급자입니다. Azure Lighthouse의 기능을 고객과 함께 SaaS 기반 제품을 사용 하는 Isv (독립 소프트웨어 공급 업체) 에서도 사용할 수 있습니다. Azure Lighthouse는 구독 범위에 액세스 해야 하는 관리 되는 서비스 또는 지원을 제공 하는 Isv에 특히 유용할 수 있습니다.

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace의 관리되는 서비스 제안

ISV는 이미 Azure Marketplace에 솔루션을 게시 했을 수 있습니다. 관리 서비스를 고객에 게 제공 하는 경우 관리 서비스 제품을 게시 하 여 수행할 수 있습니다. 이를 통해 온 보 딩 프로세스를 간소화 하 고 필요한 만큼의 고객에 게 서비스를 보다 쉽게 확장할 수 있습니다. Azure Lighthouse는 고객에 게 가치를 제공 하는 데 사용할 수 있는 광범위 한 [관리 작업 및 시나리오](cross-tenant-management-experience.md#enhanced-services-and-scenarios) 를 지원 합니다.

자세한 내용은 [Azure Marketplace에 관리 되는 서비스 제품 게시](../how-to/publish-managed-services-offers.md)를 참조 하세요.

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Azure 관리 되는 응용 프로그램에서 Azure Lighthouse 사용

[Azure 관리 되는 응용 프로그램](../../azure-resource-manager/managed-applications/overview.md) 은 isv가 고객에 게 서비스를 제공할 수 있는 또 다른 방법입니다. Azure 관리 되는 응용 프로그램과 함께 Azure Lighthouse를 사용 하 여 향상 된 시나리오를 사용할 수 있습니다.

자세한 내용은 [Azure Lighthouse 및 azure 관리 되는 응용 프로그램](managed-applications.md)을 참조 하세요.

## <a name="saas-based-multi-tenant-offerings"></a>SaaS 기반 다중 테 넌 트 제공

또 다른 시나리오는 ISV에서 자체 테 넌 트의 구독에 있는 리소스를 호스팅하고 Azure Lighthouse를 사용 하 여 고객이 이러한 리소스에 액세스할 수 있도록 하는 것입니다. 그러면 고객은 자신의 테 넌 트에 로그인 하 여 필요에 따라 이러한 리소스에 액세스할 수 있습니다. Isv는 자신의 테 넌 트에 해당 IP를 유지 관리 하 고, 고객의 계획을 사용 하는 대신 자체 지원 계획을 사용 하 여 테 넌 트에 호스트 된 솔루션과 관련 된 티켓을 발생 시킬 수 있습니다. 리소스가 ISV의 테 넌 트에 있기 때문에 모든 작업을 ISV에서 직접 수행할 수 있습니다. 예를 들어 Vm에 로그인 하 고, 앱을 설치 하 고, 유지 관리 작업을 수행할 수 있습니다.

이 시나리오에서 고객의 테 넌 트에 있는 사용자는 고객이 ISV의 리소스를 관리 하지 않는 경우에도 기본적으로 "테 넌 트 관리"로 액세스 권한이 부여 됩니다. ISV의 테 넌 트에 직접 액세스 하기 때문에 고객이 솔루션 또는 다른 ISV 리소스를 실수로 변경할 수 없도록 필요한 최소 권한만 부여 하는 것이 중요 합니다.

이 아키텍처를 사용 하도록 설정 하려면 ISV는 고객의 Azure AD 테 넌 트에 있는 사용자 그룹의 개체 ID를 해당 테 넌 트 ID와 함께 가져와야 합니다. 그런 다음 ISV는이 사용자에 게 적절 한 권한을 부여 하는 ARM 템플릿을 작성 하 고, 고객이 액세스 하는 리소스를 포함 하는 [isv의 구독에 배포](../how-to/onboard-customer.md) 합니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure 위임 리소스 관리](azure-delegated-resource-management.md)에 대해 알아봅니다.