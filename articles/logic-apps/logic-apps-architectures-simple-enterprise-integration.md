---
title: Azure Integration Services 간단한 엔터프라이즈 통합 참조 아키텍처
description: Azure Logic Apps 및 Azure API Management를 사용하여 간단한 엔터프라이즈 통합 패턴을 구현하는 방법을 보여주는 참조 아키텍처를 설명합니다.
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: f73a9e59c0add664128b506172182afe566ca670
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444513"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>참조 아키텍처: 간단한 엔터프라이즈 통합

다음 참조 아키텍처는 Azure Integration Services를 사용하는 통합 응용 프로그램에 적용할 수 있는 검증된 일단의 사례를 보여 줍니다. 이 아키텍처는 HTTP API, 워크플로 및 오케스트레이션을 필요로 하는 다양한 응용 프로그램 패턴의 기초로 사용할 수 있습니다.

![아키텍처 다이어그램 - 간단한 엔터프라이즈 통합](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

통합 기술에 사용 가능한 많은 응용 프로그램이 있습니다. 간단한 지점 간 응용 프로그램부터 전체 엔터프라이즈 Azure Service Bus 응용 프로그램까지 다양합니다. 아키텍처 시리즈는 일반적인 통합 응용 프로그램 빌드에 적용될 수 있는 재사용 가능한 구성 요소 파트에 대해 설명합니다. 설계자는 응용 프로그램 및 인프라에 구현해야 하는 구성 요소를 고려해야 합니다.

## <a name="architecture"></a>아키텍처

이 아키텍처의 구성 요소는 다음과 같습니다.

- **리소스 그룹**. [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)은 Azure 리소스에 대한 논리적 컨테이너입니다.
- **Azure API Management**. [API Management](https://docs.microsoft.com/azure/api-management/)는 HTTP API를 게시, 보호 및 변환하는 데 사용되는 완전히 관리되는 플랫폼입니다.
- **Azure API Management 개발자 포털**. Azure API Management의 각 인스턴스에는 [개발자 포털](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)에 대한 액세스가 제공됩니다. API Management 개발자 포털을 사용하여 문서 및 코드 샘플에 액세스할 수 있습니다. 개발자 포털에서 API를 테스트할 수도 있습니다.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)는 서버리스 플랫폼으로, 엔터프라이즈 워크플로 및 통합을 빌드하는 데 사용됩니다.
- **커넥터**. Logic Apps는 [커넥터](https://docs.microsoft.com/azure/connectors/apis-list)를 사용하여 일반적으로 사용되는 서비스에 연결합니다. Logic Apps에는 이미 수백 개의 다양한 커넥터가 있지만 사용자 지정 커넥터를 만들 수도 있습니다.
- **IP 주소**. Azure API Management 서비스에는 고정된 공용 [IP 주소](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)와 도메인 이름이 있습니다. 주 도메인 이름은 azure-api.net의 하위 도메인(예: contoso.azure-api.net)이지만 [사용자 지정 도메인](https://docs.microsoft.com/azure/api-management/configure-custom-domain)도 구성할 수 있습니다. Logic Apps 및 Service Bus에도 공용 IP 주소가 있습니다. 그러나 이 아키텍처에서는 보안상 API Management의 IP 주소에서만 Logic Apps 엔드포인트를 호출할 수 있습니다. Service Bus 호출은 SAS(공유 액세스 서명)로 보호됩니다.
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/)는 DNS 도메인에 대한 호스팅 서비스입니다. Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스에 사용하는 것과 동일한 자격 증명, API, 도구 및 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 사용자 지정 도메인 이름(예: contoso.com)을 사용하려면 사용자 지정 도메인 이름을 IP 주소에 매핑하는 DNS 레코드를 만듭니다. 자세한 내용은 [API Management에서 사용자 지정 도메인 이름 구성](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)을 참조하세요.
- **Azure AD(Azure Active Directory)**. 인증을 위해 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 또는 다른 ID 공급자를 사용합니다. Azure AD는 유효성을 검사할 [API Management용 JSON Web Token](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)을 전달하여 API 엔드포인트 액세스에 대한 인증을 제공합니다. Azure AD는 API Management 개발자 포털에 대한 액세스를 보호할 수 있습니다(표준 및 프리미엄 계층만 해당).

이 아키텍처의 작동에는 기본적인 몇 가지 패턴이 있습니다.

- 복합 API는 논리 앱을 사용하여 빌드됩니다. 또한 SaaS(Software as a Service) 시스템, Azure 서비스 및 API Management에 게시된 모든 API에 대한 호출을 오케스트레이션합니다. [논리 앱도 API Management 개발자 포털을 통해 게시됩니다](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api).
- 응용 프로그램은 Azure AD를 사용하여 API에 액세스하는 데 필요한 [OAuth 2.0 보안 토큰을 획득](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)합니다.
- Azure API Management는 [보안 토큰의 유효성을 검사](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)하고 요청을 백 엔드 API 또는 논리 앱에 전달합니다.

## <a name="recommendations"></a>권장 사항

특정 요구 사항은 이 문서에 설명된 일반 아키텍처와 다를 수 있습니다. 이 섹션의 권장 사항을 시작점으로 사용합니다.

### <a name="azure-api-management-tier"></a>Azure API Management 계층

API Management 기본, 표준 또는 프리미엄 계층을 사용합니다. 프로덕션 SLA(서비스 수준 계약)를 제공하고 Azure 지역 내에서 확장을 지원합니다(단위 수는 계층에 따라 다름). 프리미엄 계층은 여러 Azure 지역에 걸쳐 확장을 지원합니다. 필요한 처리량 수준 및 기능 집합에서 선택한 계층을 기반으로 합니다. 자세한 내용은 [API Management 가격](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.

실행되고 있는 모든 API Management 인스턴스에 대한 요금이 청구됩니다. 확장했는데 항상 이러한 수준의 성능이 필요하지 않은 경우에는 API Management의 시간별 청구 및 축소를 활용하는 것이 좋습니다.

### <a name="logic-apps-pricing"></a>Logic Apps 가격 책정

Logic Apps는 [서버리스](logic-apps-serverless-overview.md) 모델을 사용합니다. 청구는 작업 및 커넥터 실행에 따라 계산됩니다. 자세한 내용은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. 현재 Logic Apps에 대한 계층 고려 사항은 없습니다.

### <a name="logic-apps-for-asynchronous-api-calls"></a>비동기 API 호출에 대한 Logic Apps

Logic Apps는 짧은 대기 시간이 필요하지 않은 시나리오에 적합합니다. 예를 들어, 비동기 또는 준장기 실행 API 호출에 적합합니다. 사용자 인터페이스를 차단하는 호출과 같이 짧은 대기 시간이 필요한 경우, 다른 기술을 사용하여 해당 API 또는 작업을 구현하는 것이 좋습니다. 예를 들어, Azure App Service를 사용하여 배포되는 Azure Functions 또는 Web API를 사용합니다. API Management를 사용하여 API 소비자에게 API를 제공하는 것이 좋습니다.

### <a name="region"></a>지역

동일한 지역에 API Management 및 Logic Apps를 프로비전하여 네트워크 대기 시간을 최소화합니다. 일반적으로 사용자에게 가장 가까운 지역을 선택합니다.

리소스 그룹에도 지역이 있습니다. 지역은 배포 메타데이터가 저장되는 위치와 배포 템플릿이 실행되는 위치를 지정합니다. 배포 중에 가용성을 향상하려면 리소스 그룹 및 해당 리소스를 동일한 지역에 배치합니다.

## <a name="scalability"></a>확장성

API Management 관리자는 해당하는 경우, [캐싱 정책](../api-management/api-management-howto-cache.md)을 추가하여 서비스의 확장성을 높여야 합니다. 또한 캐싱은 백 엔드 서비스의 로드를 줄이는 데 도움이 됩니다.

Azure API Management 기본, 표준 및 프리미엄 계층은 Azure 지역 내에서 확장하여 더 많은 용량을 제공할 수 있습니다. 관리자는 **메트릭** 메뉴의 **용량 메트릭** 옵션을 사용하여 서비스의 사용량을 분석하고 적절하게 확장 또는 축소할 수 있습니다.

API Management 서비스 크기 조정에 대한 권장 사항:

- 크기 조정은 트래픽 패턴을 고려해야 합니다. 트래픽 패턴이 심하게 변동하는 고객은 용량 증가에 대한 요구 사항이 더 큽니다.
- 66%를 초과하는 일관된 용량은 확장에 대한 필요성을 나타낼 수 있습니다.
- 20% 미만의 일관된 용량은 규모를 축소할 기회를 나타낼 수 있습니다.
- 항상 대표적인 부하를 사용하여 API Management 서비스의 부하를 테스트한 후에 프로덕션 환경에서 부하를 사용하도록 설정하는 것이 좋습니다.

프리미엄 계층 서비스는 여러 Azure 지역에 걸쳐 확장할 수 있습니다. 여러 Azure 지역에서 서비스를 확장하여 배포하는 고객은 더 높은 SLA를 얻으며(99.95% 대 99.9%) 여러 지역의 사용자에 가까운 곳에서 서비스를 프로비전할 수 있습니다.

Logic Apps 서버리스 모델은 관리자가 서비스 확장성을 계획할 필요가 없음을 의미합니다. 서비스가 수요를 충족하도록 자동으로 확장됩니다.

## <a name="availability"></a>가용성

현재, Azure API Management의 SLA는 기본, 표준 및 프리미엄 계층에 대해 99.9%입니다. 둘 이상의 지역에 하나 이상의 단위가 배포되는 프리미엄 계층 구성의 SLA는 99.95%입니다.

현재 Azure Logic Apps에 대한 SLA는 99.9%입니다.

### <a name="backups"></a>Backup

Azure API Management 구성을 [정기적으로 백업](../api-management/api-management-howto-disaster-recovery-backup-restore.md)해야 합니다(정기적인 변경을 기반으로 함). 백업 파일은 서비스가 있는 위치가 아닌 다른 위치 또는 Azure 지역에 저장되어야 합니다. 그런 다음, 고객은 재해 복구 전략으로 다음 두 옵션 중 하나를 선택할 수 있습니다.

- 재해 복구 이벤트에서 새 API Management 인스턴스가 프로비전되고, 백업이 새 인스턴스에 복원되며, DNS 레코드가 다시 지정됩니다.
- 고객이 서비스의 수동 복사본을 다른 Azure 지역에서 유지합니다(추가 비용 발생). 백업은 복사본으로 정기적으로 복원됩니다. 재해 복구 이벤트에서 DNS 레코드만 다시 지정되어 서비스를 복원해야 합니다.

Logic Apps는 빠르게 다시 만들 수 있고 서버를 사용하지 않으므로 연결된 Azure Resource Manager 템플릿의 복사본을 저장하여 백업됩니다. 템플릿은 원본 제어에 저장하고 고객의 CI/CD(지속적인 통합/지속적인 배포) 프로세스에 통합할 수 있습니다.

API Management를 통해 게시된 논리 앱이 다른 데이터 센터로 이동하는 경우, 앱 위치를 업데이트합니다. 앱 위치를 업데이트하려면 기본 PowerShell 스크립트를 사용하여 API의 **백 엔드** 속성을 업데이트합니다.

## <a name="manageability"></a>관리 효율성

프로덕션, 개발 및 테스트 환경에 대해 별도의 리소스 그룹을 만듭니다. 별도의 리소스 그룹을 사용하면 배포 관리, 테스트 배포 삭제, 액세스 권한 할당 등이 더 간단해집니다.

리소스 그룹에 리소스를 할당할 때는 다음 요소를 고려하세요.

- **수명 주기**. 일반적으로 수명 주기가 동일한 리소스를 동일한 리소스 그룹에 배치합니다.
- **액세스**. RBAC([역할 기반 액세스 제어](../role-based-access-control/overview.md))를 사용하여 그룹의 리소스에 액세스 정책을 적용할 수 있습니다.
- **청구**. 리소스 그룹에 대한 롤업 비용을 확인할 수 있습니다.
- **API Management 가격 책정 계층**. 개발 및 테스트 환경에는 개발자 계층을 사용하는 것이 좋습니다. 사전 프로덕션 환경에서 비용을 최소화하려면 프로덕션 환경의 복제본을 배포하고, 테스트를 실행한 다음, 시스템을 종료하는 것이 좋습니다.

자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="deployment"></a>배포

[Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 API Management 및 Logic Apps를 배포하는 것이 좋습니다. 템플릿을 사용하면 PowerShell 또는 Azure CLI를 통해 배포를 쉽게 자동화할 수 있습니다.

Azure API Management 및 모든 개별 논리 앱을 별도의 자체 Resource Manager 템플릿에 배치하는 것이 좋습니다. 별도의 템플릿을 사용하는 경우, 원본 제어 시스템에 리소스를 저장할 수 있습니다. 리소스를 CI/CD 배포 프로세스의 일부로 함께 또는 개별적으로 배포할 수도 있습니다.

### <a name="versions"></a>버전

논리 앱 구성을 변경하거나 Resource Manager 템플릿을 통해 업데이트를 배포할 때마다 사용자의 편의를 위해 해당 버전의 복사본이 유지됩니다(실행 기록이 있는 모든 버전이 유지됨). 이러한 버전을 사용하여 기록 변경 내용을 추적하고 버전을 논리 앱의 현재 구성으로 승격할 수도 있습니다. 예를 들어, 논리 앱을 효과적으로 롤백할 수 있습니다.

API Management에는 별개의(그러나 무료) 두 가지 [버전 관리 개념](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)이 있습니다.

- API 소비자에게 자신의 필요에 따라 사용할 수 있는 API(예: v1, v2 또는 베타, 프로덕션) 선택 옵션을 제공하는 데 사용되는 버전
- API 관리자가 API를 안전하게 변경하고 선택적인 설명을 사용하여 변경 내용을 사용자에게 배포할 수 있는 수정

배포 컨텍스트에서 API Management 수정은 안전하게 변경하고, 변경 기록을 유지하며, API 소비자가 변경 내용을 알 수 있도록 하는 방식으로 고려하는 것이 좋습니다. 수정은 개발 환경에서 만들고 Resource Manager 템플릿을 사용하여 다른 환경 간에 배포할 수 있습니다.

사용자에게 “최신 상태”로 액세스할 수 있도록 설정하기 전에 수정을 사용하여 API를 테스트할 수 있지만, 부하 또는 통합 테스트에는 이 메커니즘을 사용하지 않는 것이 좋습니다. 대신에 별도의 테스트 또는 사전 프로덕션 환경을 사용합니다.

### <a name="configuration"></a>구성

암호, 액세스 키 또는 연결 문자열을 원본 제어에 체크 인하면 안 됩니다. 이러한 값이 필요한 경우, 적절한 기술을 사용하여 이러한 값을 배포하고 보호합니다. 

Logic Apps에서 논리 앱에 필요한 중요한 값(연결 형식으로 만들 수 없음)은 Azure Key Vault에 저장해야 하며 Resource Manager 템플릿에서 참조해야 합니다. 또한 각 환경에 대한 배포 템플릿 매개 변수 및 매개 변수 파일을 사용하는 것이 좋습니다. 자세한 내용은 [워크플로 내에서 매개 변수 및 입력 보안](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)을 참조하세요.

API Management에서 비밀은 ‘명명된 값’ 또는 ‘속성’이라는 개체를 사용하여 관리됩니다. 이 개체는 API Management 정책에서 액세스할 수 있는 값을 안전하게 저장합니다. 자세한 내용은 [API Management에서 비밀 관리](../api-management/api-management-howto-properties.md)를 참조하세요.

### <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

[API Management](../api-management/api-management-howto-use-azure-monitor.md) 및 [Logic Apps](logic-apps-monitor-your-logic-apps.md)는 모두 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)를 통한 운영 모니터링을 지원합니다. Azure Monitor는 각 서비스에 구성된 메트릭을 기반으로 하여 정보를 제공합니다. Azure Monitor는 기본적으로 사용할 수 있습니다.

이러한 옵션은 다음 각 서비스에서도 사용할 수 있습니다.

- Logic Apps 로그는 더 심층적인 분석 및 대시보드를 위해 [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md)로 보낼 수 있습니다.
- API Management는 DevOps 모니터링에 대한 Azure Application Insights 구성을 지원합니다.
- API Management는 [사용자 지정 API 분석용 Power BI 솔루션 템플릿](http://aka.ms/apimpbi)을 지원합니다. 고객은 솔루션 템플릿을 사용하여 고유한 사용자 지정 분석 솔루션을 만들 수 있습니다. 보고서는 비즈니스 사용자용 Power BI에서 사용할 수 있습니다.

## <a name="security"></a>보안

이 섹션에는 이 문서에서 설명된 Azure 서비스와 관련되고 설명한 대로 아키텍처에 배포된 보안 고려 사항이 나와 있습니다. 보안 모범 사례가 완전히 다 나와 있는 것은 아닙니다.

- RBAC(역할 기반 액세스 제어)를 사용하여 사용자에게 적절한 수준의 액세스를 보장합니다.
- OAuth/OpenID Connect를 사용하여 API Management에서 공용 API 엔드포인트를 보호합니다. 공용 API 엔드포인트를 보호하려면 ID 공급자를 구성하고 JWT(JSON Web Token) 유효성 검사 정책을 추가합니다.
- 상호 인증서를 사용하여 API Management에서 백 엔드 서비스에 연결합니다.
- API Management IP 주소를 가리키는 IP 주소 허용 목록을 만들어 HTTP 트리거 기반 논리 앱을 보호합니다. 허용 목록에 포함된 IP 주소는 먼저 API Management를 거치지 않고 공용 인터넷에서 논리 앱을 호출하지 않도록 방지합니다.

## <a name="next-steps"></a>다음 단계

- [큐 및 이벤트와 엔터프라이즈의 통합](logic-apps-architectures-enterprise-integration-with-queues-events.md)을 알아봅니다.
