---
title: 서비스 공급자를 위한 Azure 모니터 로그 | 마이크로 소프트 문서
description: Azure Monitor Logs는 관리되는 서비스 공급자(MSP), 대기업, ISV(독립 소프트웨어 공급업체) 및 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리하고 모니터링하는 데 도움이 될 수 있습니다.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658883"
---
# <a name="azure-monitor-logs-for-service-providers"></a>서비스 공급자를 위한 Azure 모니터 로그

Azure Monitor의 Log Analytics 작업 영역은 관리되는 서비스 공급자(MSP), 대기업, ISV(독립 소프트웨어 공급업체) 및 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리하고 모니터링하는 데 도움이 될 수 있습니다.

대기업은 서비스 공급자와 많은 유사성을 공유하는데, 특히 중앙 IT 팀이 다양한 사업부의 IT 관리를 담당한다는 점이 그렇습니다. 간단한 설명을 위해 이 문서에서는 *서비스 공급자*라는 용어를 사용하지만 기업 및 기타 고객에 대해서도 같은 기능을 사용할 수 있습니다.

[CSP(클라우드 솔루션 공급자)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) 프로그램에 속한 파트너 및 서비스 공급자의 경우 Azure 모니터의 로그 분석은 Azure CSP 구독에서 사용할 수 있는 Azure 서비스 중 하나입니다.

Azure 모니터의 로그 분석은 [Azure 등대에서](https://docs.microsoft.com/azure/lighthouse/overview)Azure 위임 리소스 관리 기능을 통해 고객 리소스를 관리하는 서비스 공급자가 사용할 수도 있습니다.

## <a name="architectures-for-service-providers"></a>서비스 공급자의 아키텍처

Log Analytics 작업 영역은 관리자가 [로그](data-platform-logs.md) 데이터의 흐름및 격리를 제어하고 특정 비즈니스 요구 사항을 해결하는 아키텍처를 만드는 방법을 제공합니다. [이 문서에서는](design-logs-deployment.md) 작업 영역에 대한 디자인, 배포 및 마이그레이션 고려 사항에 대해 설명하고 [액세스 관리](manage-access.md) 문서에서는 로그 데이터에 대한 사용 권한을 적용하고 관리하는 방법에 대해 설명합니다. 서비스 공급자에게는 추가 고려 사항이 있습니다.

Log Analytics 작업 영역에 관련된 서비스 공급자에 대해 세 가지 아키텍처를 사용할 수 있습니다.

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. 분산 - 로그는 고객의 테넌트에 있는 작업 영역에 저장됩니다.

이 아키텍처에서 작업 영역은 해당 고객의 모든 로그에 사용되는 고객의 테넌트에 배포됩니다.

서비스 공급자 관리자가 고객 테넌트의 Log Analytics 작업 영역에 액세스할 수 있는 방법에는 두 가지가 있습니다.

- 고객은 서비스 공급자의 개별 사용자를 [B2B(Azure Active Directory 게스트 사용자)로](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)추가할 수 있습니다. 서비스 공급자 관리자는 이러한 작업 영역에 액세스하려면 Azure Portal의 각 고객의 디렉터리에 로그인해야 합니다. 또한 고객은 각 서비스 공급자 관리자에 대한 개별 액세스를 관리해야 합니다.
- 확장성과 유연성을 높이기 위해 서비스 공급자는 [Azure 등대에서](https://docs.microsoft.com/azure/lighthouse/overview) [Azure 위임리소스 관리](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) 기능을 사용하여 고객의 테넌트에 액세스할 수 있습니다. 이 방법을 사용하면 서비스 공급자 관리자가 서비스 공급자의 테넌트의 Azure AD 사용자 그룹에 포함되며 이 그룹에는 각 고객에 대한 온보딩 프로세스 중에 액세스 권한이 부여됩니다. 그런 다음 이러한 관리자는 각 고객의 테넌트에 개별적으로 로그인하지 않고도 자체 서비스 공급자 테넌트 내에서 각 고객의 작업 영역에 액세스할 수 있습니다. 이러한 방식으로 고객의 Log Analytics 작업 영역 리소스에 액세스하면 고객 측에서 필요한 작업이 줄어들고 [Azure Monitor 통합 문서와](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview)같은 도구를 통해 동일한 서비스 공급자가 관리하는 여러 고객 간에 데이터를 쉽게 수집하고 분석할 수 있습니다. 자세한 내용은 [규모에 따라 고객 리소스 모니터링을](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale)참조하십시오.

분산 아키텍처의 장점은 다음과 같습니다.

* 고객은 [Azure 위임된 리소스 관리를](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)통해 특정 권한 수준을 확인하거나 자신의 역할 기반 액세스를 사용하여 로그에 [대한](https://docs.microsoft.com/azure/role-based-access-control/overview)액세스를 관리할 수 있습니다.
* 로그는 에이전트 기반 VM 데이터뿐만 아니라 모든 유형의 리소스에서 수집할 수 있습니다. Azure 감사 로그 등을 예로 들 수 있습니다.
* 각 고객은 보존 및 데이터 제한과 같은 해당 작업 영역의 설정이 서로 다를 수 있습니다.
* 규정 및 준수를 위해 고객을 서로 격리합니다.
* 각 작업 영역에 대한 요금이 고객의 구독에 롤인됩니다.

분산 아키텍처의 단점은 다음과 같습니다.

* Azure Monitor 통합 문서와 같은 도구를 사용하여 고객 테넌트 전체에서 데이터를 중앙에서 시각화하고 분석하면 특히 50개 이상의 작업 영역에서 데이터를 분석할 때 경험이 느려질 수 있습니다.
* Azure 위임리소스 관리에 대한 온보딩되지 않은 고객은 고객 디렉터리에서 서비스 공급자 관리자를 프로비전해야 하며 서비스 공급자가 한 번에 많은 수의 고객 테넌트를 관리하기가 더 어렵습니다.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. 중앙 - 로그는 서비스 공급자 테넌트에 있는 작업 영역에 저장됩니다.

이 아키텍처에서 로그는 고객의 테넌트에 저장되지 않고 서비스 공급자의 구독 내의 중앙 위치에만 저장됩니다. 고객의 VM에 설치된 에이전트는 작업 영역 ID 및 비밀 키를 사용하여 이 작업 영역에 해당 로그를 보내도록 구성됩니다.

중앙 집중식 아키텍처의 장점은 다음과 같습니다.

* 쉽게 다수의 고객을 관리하고 다양한 백 엔드 시스템에 통합할 수 있습니다.
* 서비스 공급자에게는 함수 및 저장된 쿼리와 같은 로그 및 다양한 아티팩트에 대한 전체 소유권이 있습니다.
* 서비스 공급자는 모든 고객에 대해 분석을 수행할 수 있습니다.

중앙 집중식 아키텍처의 단점은 다음과 같습니다.

* 이 아키텍처는 에이전트 기반 VM 데이터에만 적용 가능하고, PaaS, SaaS 및 Azure 패브릭 데이터 원본을 다루지 않습니다.
* 단일 작업 영역으로 병합된 고객 간에 데이터를 구분하기가 어려울 수 있습니다. 이렇게 하려면 유일한 방법은 컴퓨터의 FQDN(정규화된 도메인 이름)을 사용하거나 Azure 구독 ID를 통해서 수행하는 것입니다. 
* 모든 고객의 모든 데이터는 단일 청구서 및 동일한 보존 및 구성 설정을 사용하여 동일한 지역에 저장됩니다.
* Azure Diagnostics 및 Azure 감사 로그와 같은 Azure 패브릭 및 PaaS 서비스에는 리소스와 동일한 테넌트에 있는 작업 영역이 필요합니다. 따라서 중앙 작업 영역에 로그를 보낼 수 없습니다.
* 모든 고객의 모든 VM 에이전트는 동일한 작업 영역 ID 및 키를 사용하여 중앙 작업 영역에 인증됩니다. 다른 고객을 방해하지 않고 특정 고객의 로그를 차단하는 방법은 없습니다.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. 하이브리드 - 로그는 고객의 테넌트에 있는 작업 공간에 저장되며 그 중 일부는 중앙 위치로 당겨지습니다.

두 가지 옵션 중 세 번째 아키텍처 조합입니다. 로그가 각 고객에게 로컬인 첫 번째 분산 아키텍처에 기반하지만 로그의 중앙 리포지토리를 만드는 일부 메커니즘을 사용합니다. 보고 및 분석을 위해 중앙 위치로 로그의 일부를 끌어옵니다. 이 부분은 적은 수의 데이터 형식 또는 일별 통계와 같은 작업 요약일 수 있습니다.

중앙 위치에서 로그를 구현하는 두 가지 옵션이 있습니다.

1. 중앙 작업 영역: 서비스 공급자는 해당 테넌트에서 작업 영역을 만들고 [데이터 수집 API](../../azure-monitor/platform/data-collector-api.md)와 함께 [쿼리 API](https://dev.loganalytics.io/)를 활용하는 스크립트를 사용하여 데이터를 다양한 작업 영역에서 중앙 위치로 가져올 수 있습니다. 스크립트 외에 사용 가능한 또 다른 옵션은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)를 사용하는 것입니다.

2. 중앙 위치로서의 Power BI: Power BI는 다양한 작업 영역이 Log Analytics 작업 영역과 [Power BI](../../azure-monitor/platform/powerbi.md)간의 통합을 사용하여 데이터를 내보낼 때 중앙 위치 역할을 할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Resource Manager 템플릿](template-workspace-configuration.md)을 사용하여 작업 영역 생성 및 구성 자동화

* [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)을 사용하여 작업 영역 생성 자동화 

* [경고](../../azure-monitor/platform/alerts-overview.md)를 사용하여 기존 시스템과 통합

* [Power BI](../../azure-monitor/platform/powerbi.md)를 사용하여 요약 보고서 생성

* Azure 위임 [리소스 관리에](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)대한 고객을 온보딩합니다.
