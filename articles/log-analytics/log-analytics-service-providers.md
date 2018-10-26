---
title: 서비스 공급자의 Log Analytics | Microsoft Docs
description: Log Analytics는 MSP(Managed Service Providers), 대기업, ISV(Independent Software Vendor)를 지원하며 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리하고 모니터링할 수 있도록 합니다.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: meirm
ms.component: ''
ms.openlocfilehash: ef3cc76c0188cb2a0d3236d462d0645e3e45040b
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044283"
---
# <a name="log-analytics-for-service-providers"></a>서비스 공급자의 Log Analytics
Log Analytics는 MSP(Managed Service Providers), 대기업, ISV(Independent Software Vendor)를 지원하며 호스팅 서비스 공급자가 고객의 온-프레미스 또는 클라우드 인프라에서 서버를 관리하고 모니터링할 수 있도록 합니다. 

대기업은 서비스 공급자와 많은 유사성을 공유하는데, 특히 중앙 IT 팀이 다양한 사업부의 IT 관리를 담당한다는 점이 그렇습니다. 간단한 설명을 위해 이 문서에서는 *서비스 공급자*라는 용어를 사용하지만 기업 및 기타 고객에 대해서도 같은 기능을 사용할 수 있습니다.

[CSP(클라우드 솔루션 공급자)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) 프로그램에 참여하는 파트너 및 서비스 공급자의 경우 Log Analytics는 [Azure CSP 구독](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)에서 제공되는 Azure 서비스 중 하나입니다. 

## <a name="architectures-for-service-providers"></a>서비스 공급자의 아키텍처

Log Analytics 작업 영역은 관리자에게 메서드를 제공하여 흐름 및 로그의 격리를 제어하고 특정 비즈니스 요구 사항을 해결하는 로그 아키텍처를 만듭니다. [이 아티클](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access)에서는 작업 영역 관리와 관련된 일반적인 고려 사항을 설명합니다. 서비스 공급자에게는 추가 고려 사항이 있습니다.

Log Analytics 작업 영역에 관련된 서비스 공급자에 대해 세 가지 아키텍처를 사용할 수 있습니다.

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. 배포됨 - 로그는 고객의 테넌트에 있는 작업 영역에 저장됩니다. 

이 아키텍처에서 작업 영역은 해당 고객의 모든 로그에 사용되는 고객의 테넌트에서 배포됩니다. 서비스 공급자 관리자는 [Azure Active Directory 게스트 사용자(B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)를 사용하여 이 작업 영역에 대한 액세스 권한이 부여되었습니다. 서비스 공급자 관리자는 Azure Portal에서 고객 디렉터리로 전환해야 이러한 작업 영역에 액세스할 수 있습니다.

이 아키텍처의 장점은 다음과 같습니다.
* 고객은 고유한 [역할 기반 액세스](https://docs.microsoft.com/azure/role-based-access-control/overview)를 사용하여 로그에 대한 액세스 권한을 관리할 수 있습니다.
* 각 고객은 보존 및 데이터 제한과 같은 해당 작업 영역의 설정이 서로 다를 수 있습니다.
* 규정 및 준수를 위해 고객을 서로 격리합니다.
* 각 작업 영역에 대한 요금이 고객의 구독에 롤인됩니다.
* 로그는 에이전트 기반이 아닌 모든 형식의 리소스에서 수집될 수 있습니다. Azure 감사 로그 등을 예로 들 수 있습니다.

이 아키텍처의 단점은 다음과 같습니다.
* 서비스 공급자의 경우 한 번에 많은 수의 고객 테넌트를 관리하기가 더 어렵습니다.
* 서비스 공급자 관리자는 고객 디렉터리에서 프로비전되어야 합니다.
* 서비스 공급자는 해당 고객 간에 데이터를 분석할 수 없습니다.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. 중앙 - 로그가 서비스 공급자 테넌트에 있는 작업 영역에 저장됨

이 아키텍처에서 로그는 고객의 테넌트에 저장되지 않고 서비스 공급자의 구독 내의 중앙 위치에만 저장됩니다. 고객의 VM에 설치된 에이전트는 작업 영역 ID 및 암호 키를 사용하여 이 작업 영역에 해당 로그를 보내도록 구성됩니다.

이 아키텍처의 장점은 다음과 같습니다.
* 쉽게 다수의 고객을 관리하고 다양한 백 엔드 시스템에 통합할 수 있습니다.
* 서비스 공급자에게는 함수 및 저장된 쿼리와 같은 로그 및 다양한 아티팩트에 대한 전체 소유권이 있습니다.
* 서비스 공급자는 모든 고객에 대해 분석을 수행할 수 있습니다.

이 아키텍처의 단점은 다음과 같습니다.
* 이 아키텍처는 에이전트 기반 VM 데이터에만 적용 가능하고, PaaS, SaaS 및 Azure 패브릭 데이터 원본을 다루지 않습니다.
* 단일 작업 영역으로 병합된 고객 간에 데이터를 구분하기가 어려울 수 있습니다. 이렇게 하려면 유일한 방법은 컴퓨터의 FQDN(정규화된 도메인 이름)을 사용하거나 Azure 구독 ID를 통해서 수행하는 것입니다. 
* 모든 고객의 모든 데이터는 단일 청구서 및 동일한 보존 및 구성 설정을 사용하여 동일한 지역에 저장됩니다.
* Azure 진단 및 Azure 감사 로그와 같은 Azure 패브릭 및 PaaS 서비스에는 리소스와 동일한 테넌트에 있는 작업 영역이 필요합니다. 따라서 중앙 작업 영역에 로그를 보낼 수 없습니다.
* 모든 고객의 모든 VM 에이전트는 동일한 작업 영역 ID 및 키를 사용하여 중앙 작업 영역에 인증됩니다. 다른 고객을 방해하지 않고 특정 고객의 로그를 차단하는 방법은 없습니다.


### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. 하이브리드 - 로그는 고객의 테넌트에 있는 작업 영역에 저장되고 그 중 일부는 중앙 위치로 끌어옵니다.

두 가지 옵션 중 세 번째 아키텍처 조합입니다. 로그가 각 고객에게 로컬인 첫 번째 분산 아키텍처에 기반하지만 로그의 중앙 리포지토리를 만드는 일부 메커니즘을 사용합니다. 보고 및 분석을 위해 중앙 위치로 로그의 일부를 끌어옵니다. 이 부분은 적은 수의 데이터 형식 또는 일별 통계와 같은 작업 요약일 수 있습니다.

Log Analytics에서 중앙 위치를 구현하는 두 가지 옵션이 있습니다.

1. 중앙 작업 영역: 서비스 공급자는 해당 테넌트에서 작업 영역을 만들고 [데이터 수집 API](log-analytics-data-collector-api.md)와 함께 [쿼리 API](https://dev.loganalytics.io/)를 활용하는 스크립트를 사용하여 데이터를 다양한 작업 영역에서 중앙 위치로 가져올 수 있습니다. 스크립트 외에 사용 가능한 또 다른 옵션은 [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)를 사용하는 것입니다.

2. 중앙 위치인 Power BI: Power BI는 다양한 작업 영역이 Log Analytics와 [Power BI](log-analytics-powerbi.md) 간의 통합을 사용하여 데이터를 내보내는 경우 중앙 위치로 작동할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
* [Resource Manager 템플릿](log-analytics-template-workspace-configuration.md)을 사용하여 작업 영역 생성 및 구성 자동화
* [PowerShell](log-analytics-powershell-workspace-configuration.md)을 사용하여 작업 영역 생성 자동화 
* [경고](log-analytics-alerts.md)를 사용하여 기존 시스템과 통합
* [Power BI](log-analytics-powerbi.md)를 사용하여 요약 보고서 생성
* [여러 CSP 고객을 모니터링하도록 Log Analytics 및 Power BI를 구성](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)하는 프로세스 검토
