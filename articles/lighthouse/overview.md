---
title: Azure Lighthouse란?
description: Azure Lighthouse에서 서비스 공급자는 더 높은 자동화 및 효율성으로 고객을 위한 관리형 서비스를 제공할 수 있습니다.
ms.date: 10/19/2020
ms.topic: overview
ms.openlocfilehash: a76606ff48a09c0c31584882e3d2aa164ec97325
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92203792"
---
# <a name="what-is-azure-lighthouse"></a>Azure Lighthouse란?

Azure Lighthouse는 교차 및 다중 테넌트 관리를 지원하여 리소스 및 테넌트 간에 더 높은 자동화, 확장성 및 향상된 거버넌스를 허용합니다.

Azure Lighthouse를 통해 서비스 공급자는 Azure 플랫폼에 기본 제공되는 포괄적이고 강력한 관리 도구를 사용하여 관리형 서비스를 제공할 수 있습니다. 고객은 테넌트에 액세스할 수 있는 사용자, 액세스할 수 있는 리소스 및 수행할 수 있는 작업에 대한 제어를 유지합니다. 이 제품은 여러 테넌트 간에 리소스를 관리하는 [엔터프라이즈 IT 조직](concepts/enterprise.md)에서도 활용할 수 있습니다.

![Azure Lighthouse의 개요 다이어그램](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>이점

Azure Lighthouse를 사용하면 서비스 공급자가 관리되는 서비스를 효율적으로 빌드하고 제공할 수 있습니다. 이점은 다음과 같습니다.

- **대규모 관리** : 고객 참여 및 수명 주기 작업을 통해 고객 리소스를 보다 쉽게 관리하고 확장할 수 있습니다. 기존 API, 관리 도구 및 워크플로는 위치에 관계없이 Azure 외부에서 호스팅되는 컴퓨터를 포함하여 위임된 리소스에서 사용할 수 있습니다.
- **고객을 위한 가시성 및 제어 향상** : 고객은 관리를 위임하는 범위와 허용되는 권한을 정확하게 제어할 수 있습니다. 원하는 경우 서비스 공급자 작업을 감사하고 액세스 권한을 완전히 제거할 수 있습니다.
- **포괄적이고 통합된 플랫폼 도구** : Microsoft의 도구 환경은 EA, CSP 및 종량제 등의 여러 라이선스 모델을 포함하는 주요 서비스 공급자 시나리오를 다룹니다. Azure Lighthouse는 기존 도구와 API, 라이선스 모델, [Azure 관리형 애플리케이션](concepts/managed-applications.md) 및 파트너 프로그램(예:[CSP(클라우드 솔루션 공급자 프로그램)](/partner-center/csp-overview))에서 작동합니다. Azure Lighthouse를 기존 워크플로 및 애플리케이션에 통합하고, [파트너 ID를 연결](./how-to/partner-earned-credit.md)하여 고객 참여에 대한 영향을 추적할 수 있습니다.

Azure Lighthouse를 사용하여 Azure 리소스를 관리하는 것과 관련된 추가 비용은 없습니다. 모든 Azure 고객 또는 파트너는 Azure Lighthouse를 사용할 수 있습니다.

## <a name="capabilities"></a>기능

Azure Lighthouse에는 참여 및 관리를 간소화하는 데 도움이 되는 여러 방법이 포함되어 있습니다.

- **Azure 위임 리소스 관리** : 컨텍스트 및 컨트롤 플레인을 전환할 필요 없이 [사용자 고유의 테넌트 내에서 고객의 Azure 리소스를 안전하게 관리](concepts/azure-delegated-resource-management.md)합니다. 고객 구독 및 리소스 그룹은 필요에 따라 액세스 권한을 제거할 수 있는 기능을 통해 관리 테넌트의 지정된 사용자 및 역할에 위임할 수 있습니다.
- **새로운 Azure Portal 환경** : Azure Portal의 [**내 고객** 페이지](how-to/view-manage-customers.md)에서 테넌트 간 정보를 확인합니다. 고객은 해당 [**서비스 공급자** 페이지](how-to/view-manage-service-providers.md)에서 해당 서비스 공급자 액세스 권한을 보고 관리할 수 있습니다.
- **Azure Resource Manager 템플릿** : ARM 템플릿을 사용하여 [위임된 고객 리소스를 온보딩](how-to/onboard-customer.md)하고 [교차 테넌트 간 관리 작업을 수행](samples/index.md)합니다.
- **Azure Marketplace의 관리형 서비스 제품** : 프라이빗 또는 퍼블릭 제안을 통해 [고객에게 서비스를 제공](concepts/managed-services-offers.md)하고, 자동으로 Azure Lighthouse에 온보딩합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 위임 리소스 관리](concepts/azure-delegated-resource-management.md)에 대해 알아봅니다.
- [테넌트 간 관리 환경](concepts/cross-tenant-management-experience.md)을 살펴봅니다.
- [엔터프라이즈 내에서 Azure Lighthouse를 사용](concepts/enterprise.md)하는 방법을 참조하세요.
