---
title: 간단한 엔터프라이즈 통합 아키텍처 패턴 - Azure Integration Services
description: 이 아키텍처 참조에서는 Azure Logic Apps 및 Azure API Management를 사용하여 간단한 엔터프라이즈 통합 패턴을 구현하는 방법을 보여줍니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5ed6fa9f514bae3ea651edba6702714e2680091f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955948"
---
# <a name="simple-enterprise-integration-architecture"></a>간단한 엔터프라이즈 통합 아키텍처

이 문서에서는 Azure Integration Services를 사용하는 경우 통합 응용 프로그램에 적용할 수 있는 입증된 방법을 사용하는 엔터프라이즈 통합 아키텍처에 대해 설명합니다. 이 아키텍처는 HTTP API, 워크플로 및 오케스트레이션이 필요한 다양한 응용 프로그램 패턴의 기반으로 사용할 수 있습니다.

![아키텍처 다이어그램 - 간단한 엔터프라이즈 통합](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

이 시리즈는 일반적인 통합 응용 프로그램 빌드에 적용될 수 있는 재사용 가능한 구성 요소 부분에 대해 설명합니다. 통합 기술에는 단순한 지점 간 프로토콜 앱에서 전체 엔터프라이즈 Azure Service Bus 응용 프로그램에 이르는 다양한 응용 프로그램이 있으므로, 응용 프로그램과 인프라에 대해 어떤 구성 요소를 구현해야 하는지 고려하는 것이 좋습니다.

## <a name="architecture-components"></a>아키텍처 구성 요소

이 엔터프라이즈 통합 아키텍처에는 다음 구성 요소가 포함됩니다.

- **리소스 그룹**: [리소스 그룹](../azure-resource-manager/resource-group-overview.md)은 Azure 리소스에 대한 논리 컨테이너입니다.

- **Azure API Management**: [API Management](https://docs.microsoft.com/azure/api-management/) 서비스는 HTTP API를 게시, 보호 및 변환하기 위한 완벽하게 관리되는 플랫폼입니다.

- **Azure API Management 개발자 포털**: Azure API Management의 각 인스턴스는 [개발자 포털](../api-management/api-management-customize-styles.md)에 대한 액세스를 제공합니다. 이 포털을 통해 설명서 및 코드 샘플에 액세스할 수 있습니다. 개발자 포털에서 API를 테스트할 수도 있습니다.

- **Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md)는 서버를 사용하지 않는 플랫폼이며, 엔터프라이즈 워크플로 및 통합을 구축하기 위한 플랫폼입니다.

- **커넥터**: Logic Apps는 일반적으로 사용되는 서비스에 연결하는 데 [커넥터](../connectors/apis-list.md)를 사용합니다. Logic Apps에는 수백 개의 커넥터가 제공되지만 사용자 지정 커넥터를 만들 수도 있습니다.

- **IP 주소**: Azure API Management 서비스에는 고정된 공용 [IP 주소](../virtual-network/virtual-network-ip-addresses-overview-arm.md)와 도메인 이름이 있습니다. 기본 도메인 이름은 azure-api.net의 하위 도메인(예: contoso.azure-api.net)이지만 [사용자 지정 도메인](../api-management/configure-custom-domain.md)을 구성할 수도 있습니다. Logic Apps 및 Service Bus에도 공용 IP 주소가 있습니다. 단, 보안을 위해 이 아키텍처에서는 Logic Apps 엔드포인트 호출에 대한 액세스를 API Management의 IP 주소로만 제한합니다. Service Bus 호출은 SAS(공유 액세스 서명)로 보호됩니다.

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/)는 DNS 도메인에 대한 호스팅 서비스입니다. Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에서 도메인을 호스팅하면 다른 Azure 서비스에 사용하는 것과 동일한 자격 증명, API, 도구 및 결제(청구) 정보를 사용하여 DNS 레코드를 관리할 수 있습니다. 사용자 지정 도메인 이름(예: contoso.com)을 사용하려면 사용자 지정 도메인 이름을 IP 주소에 매핑하는 DNS 레코드를 만듭니다. 자세한 내용은 [API Management에서 사용자 지정 도메인 이름 구성](../api-management/configure-custom-domain.md)을 참조하세요.

- **Azure AD(Azure Active Directory)**:인증을 위해 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 또는 다른 ID 공급자를 사용할 수 있습니다. Azure AD는 유효성을 검사할 [API Management용 JSON Web Token](../api-management/policies/authorize-request-based-on-jwt-claims.md)을 전달하여 API 엔드포인트 액세스에 대한 인증을 제공합니다. 표준 및 프리미엄 계층의 경우, Azure AD는 API Management 개발자 포털에 대한 액세스를 보호할 수 있습니다.

## <a name="patterns"></a>패턴 

이 아키텍처의 작동에는 몇 가지 기본적인 패턴이 사용됩니다.

* 복합 API는 SaaS(Software as a Service) 시스템, Azure 서비스 및 API Management에 게시된 모든 API에 대한 호출을 오케스트레이션하는 논리 앱을 사용하여 작성됩니다. 논리 앱은 [API Management 개발자 포털을 통해 게시](../api-management/import-logic-app-as-api.md)되기도 합니다.

* 응용 프로그램은 Azure AD를 사용하여 API에 대한 액세스를 확보하는 데 필요한 [OAuth 2.0 보안 토큰을 확보](../api-management/api-management-howto-protect-backend-with-aad.md)합니다.

* Azure API Management는 [보안 토큰의 유효성을 검사](../api-management/api-management-howto-protect-backend-with-aad.md)하고 요청을 백 엔드 API 또는 논리 앱에 전달합니다.

## <a name="recommendations"></a>권장 사항

특정 요구 사항은 이 문서에 설명된 일반 아키텍처와 다를 수 있습니다. 이 섹션의 권장 사항을 시작점으로 사용합니다.

### <a name="azure-api-management-tier"></a>Azure API Management 계층

API Management 기본, 표준 또는 프리미엄 계층을 사용합니다. 이러한 계층은 프로덕션 SLA(서비스 수준 계약)를 제공하고 Azure 지역 내에서 확장을 지원합니다. 단위 수는 계층에 따라 다릅니다. 프리미엄 계층은 여러 Azure 지역에 걸친 확장(스케일 아웃)도 지원합니다. 기능 집합 및 필요한 처리량의 수준을 기준으로 계층을 선택합니다. 자세한 내용은 [API Management 가격](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.

실행되고 있는 모든 API Management 인스턴스에 대한 요금이 청구됩니다. 이미 확장(스케일 업)했는데 항상 이러한 수준의 성능이 필요하지 않은 경우에는 API Management의 시간별 청구를 활용하여 축소(스케일 다운)하는 것이 좋습니다.

### <a name="logic-apps-pricing"></a>Logic Apps 가격 책정

Logic Apps는 [서버리스](../logic-apps/logic-apps-serverless-overview.md) 모델을 사용합니다. 청구는 작업 및 커넥터 실행에 따라 계산됩니다. 자세한 내용은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. 현재 Logic Apps에 대한 계층 고려 사항은 없습니다.

### <a name="logic-apps-for-asynchronous-api-calls"></a>비동기 API 호출에 대한 Logic Apps

Logic Apps는 짧은 대기 시간이 필요하지 않은 시나리오에 적합합니다. 예를 들어 Logic Apps는 비동기 또는 준장기 실행 API 호출에 적합합니다. 사용자 인터페이스를 차단하는 호출과 같이 짧은 대기 시간이 필요한 경우 다른 기술을 사용하여 해당 API 또는 작업을 구현합니다. 예를 들어, Azure App Service를 사용하여 배포되는 Azure Functions 또는 Web API를 사용합니다. API 사용자에게 API를 향하도록 API Management를 사용합니다.

### <a name="region"></a>지역

네트워크 대기 시간을 최소화하려면 API Management, Logic Apps 및 Service Bus에 동일한 지역을 선택합니다. 일반적으로 사용자에게 가장 가까운 지역을 선택합니다.

리소스 그룹에도 지역이 있습니다. 이 지역은 배포 메타 데이터를 저장할 위치와 배포 템플릿을 실행할 위치를 지정합니다. 배포 중 가용성을 향상 시키려면 리소스 그룹과 리소스를 같은 지역에 배치합니다.

## <a name="scalability"></a>확장성

API Management 서비스를 관리할 때 확장성을 높이려면 해당하는 경우 [캐싱 정책](../api-management/api-management-howto-cache.md)을 추가합니다. 또한 캐싱은 백 엔드 서비스의 로드를 줄이는 데 도움이 됩니다.

더 많은 용량을 제공하기 위해 Azure 지역 내에서 Azure API Management 기본, 표준 및 프리미엄 계층을 확장할 수 있습니다. **메트릭** 메뉴에서 서비스의 사용량을 분석하려면 **용량 메트릭** 옵션을 선택한 다음, 적절하게 확장 또는 축소합니다.

API Management 서비스 크기 조정에 대한 권장 사항:

- 크기를 조정하는 경우 트래픽 패턴을 고려합니다. 더욱 일시적인 트래픽 패턴을 사용하는 고객은 더 많은 용량이 필요합니다.

- 66%보다 큰 일관된 용량은 확장(스케일 업)이 필요할 수 있습니다.

- 20% 아래의 일관된 용량은 축소(스케일 다운)가 필요할 수 있습니다.

- 프로덕션 환경에서 부하를 사용하도록 설정하기 전에 항상 대표적인 부하를 사용하여 API Management 서비스의 부하를 테스트합니다.

프리미엄 계층 서비스를 여러 Azure 지역에 걸쳐 확장할 수 있습니다. 여러 Azure 지역에서 서비스를 확장하여 배포하는 경우 더 높은 SLA를 얻으며(99.95% 대 99.9%) 여러 지역의 사용자에 가까운 곳에서 서비스를 프로비전할 수 있습니다.

Logic Apps 서버리스 모델은 관리자가 서비스 확장성을 계획할 필요가 없음을 의미합니다. 서비스가 수요를 충족하도록 자동으로 확장됩니다.

## <a name="availability"></a>가용성

* 기본, 표준 및 프리미엄 계층에 대한 Azure API Management의 SLA(서비스 수준 계약)는 현재 99.9%입니다. 둘 이상의 지역에 하나 이상의 단위가 배포되는 프리미엄 계층 구성의 경우 SLA가 99.95%입니다.

* Azure Logic Apps에 대한 SLA는 현재 99.9%입니다.

### <a name="backups"></a>Backup

정기적인 변경을 기반으로 Azure API Management 구성을 [정기적으로 백업](../api-management/api-management-howto-disaster-recovery-backup-restore.md)합니다. 서비스가 있는 곳과 다른 위치 또는 Azure 지역에 백업 파일을 저장합니다. 그런 다음, 재해 복구 전략으로 옵션 중 하나를 선택할 수 있습니다.

* 재해 복구 이벤트에서 새 API Management 인스턴스를 프로비전하고, 백업을 새 인스턴스로 복원하고, DNS 레코드를 다시 지정합니다.

* 서비스의 수동 복사본을 다른 Azure 지역에서 유지합니다. 추가 비용이 발생합니다. 해당 복사본에 정기적으로 백업을 복원합니다. 재해 복구 이벤트 중 서비스를 복원하려면 DNS 레코드만 다시 지정하면 됩니다.

논리 앱을 신속히 다시 만들 수 있고 서버리스이므로 연결된 Azure Resource Manager 템플릿의 복사본을 저장하여 백업합니다. 소스 제어에 템플릿을 저장하고, CI/CD(지속적인 통합/지속적인 배포) 프로세스를 사용하여 템플릿을 통합할 수 있습니다.

Azure API Management를 통해 논리 앱을 게시했고, 해당 논리 앱을 다른 데이터 센터로 이동하는 경우 앱 위치를 업데이트합니다. 기본 PowerShell 스크립트를 사용하여 API의 **백 엔드** 속성을 업데이트할 수 있습니다.

## <a name="manageability"></a>관리 효율성

프로덕션, 개발 및 테스트 환경에 대해 별도의 리소스 그룹을 만듭니다. 별도의 리소스 그룹을 만들면 배포 관리, 테스트 배포 삭제, 액세스 권한 할당 등이 더 간단해집니다.

리소스 그룹에 리소스를 할당할 때는 다음 요소를 고려합니다.

* **수명 주기**: 일반적으로 수명 주기가 동일한 리소스를 동일한 리소스 그룹에 배치합니다.

* **Access**: 그룹의 리소스에 액세스 정책을 적용하려는 경우에는 [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하면 됩니다.

* **청구**: 리소스 그룹에 대한 롤업 비용을 확인할 수 있습니다.

* **API Management 가격 책정 계층**: 개발 및 테스트 환경에는 개발자 계층을 사용합니다. 사전 프로덕션 기간에 비용을 최소화하려면 프로덕션 환경의 복제본을 배포하고 테스트를 실행한 다음, 종료합니다.

자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

## <a name="deployment"></a>배포

* API Management 및 Logic Apps를 배포하려면 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용합니다. 템플릿을 사용하면 PowerShell 또는 Azure CLI를 통해 배포를 보다 쉽게 자동화할 수 있습니다.

* API Management 및 모든 개별 논리 앱을 별도의 자체 Resource Manager 템플릿에 배치합니다. 별도의 템플릿을 사용하면, 소스 제어 시스템에 리소스를 저장할 수 있습니다. 그런 다음, 이러한 템플릿을 CI/CD(지속적인 통합/지속적인 배포) 프로세스의 일부로 함께 배포하거나 개별적으로 배포할 수 있습니다.

### <a name="versions"></a>버전

논리 앱의 구성을 변경하거나 Resource Manager 템플릿을 통해 업데이트를 배포할 때마다 Azure는 사용자의 편의를 위해 해당 버전의 복사본을 유지하고 실행 기록이 있는 모든 버전을 유지합니다. 기록 변경 내용 추적 또는 논리 앱의 현재 구성으로 버전 승격에 이러한 버전을 사용할 수 있습니다. 예를 들어, 논리 앱을 효과적으로 롤백할 수 있습니다.

Azure API Management에는 별개이지만 상호 보완적인 [버전 관리 개념](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)이 있습니다.

* API 소비자에게 소비자의 요구에 따라 API 버전을 선택하는 기능을 제공하는 버전(예: v1, v2, 베타 또는 프로덕션)

* API 관리자가 API를 안전하게 변경하고 선택적인 설명을 사용하여 해당 변경 내용을 사용자에게 배포할 수 있는 수정

배포의 경우 안전하게 변경하고, 변경 기록을 유지하고 API 소비자와 해당 변경 내용을 전하는 방법으로 API Management 수정 버전을 고려합니다. 개발 환경에서 수정을 만들고 Resource Manager 템플릿을 사용하여 다른 환경에서 해당 변경 내용을 배포할 수 있습니다.

해당 변경 내용을 “최신 상태”로 만들고 사용자에게 액세스할 수 있도록 설정하기 전에 수정을 사용하여 API를 테스트할 수 있지만, 부하 또는 통합 테스트에는 이 메서드를 사용하지 않는 것이 좋습니다. 대신에 별도의 테스트 또는 사전 프로덕션 환경을 사용합니다.

### <a name="configuration-and-sensitive-information"></a>구성 및 중요한 정보

암호, 액세스 키 또는 연결 문자열을 소스 제어로 체크 인하지 마세요. 이러한 값이 필요한 경우 적절한 기술을 사용하여 이러한 값을 보호하고 배포합니다. 

Logic Apps에서 논리 앱에 연결 내에서 만들 수 없는 중요한 값이 필요한 경우 Azure Key Vault에 해당 값을 저장하고 Resource Manager 템플릿에서 참조합니다. 각 환경에 대한 배포 템플릿 매개 변수 및 매개 변수 파일을 사용합니다. 자세한 내용은 [워크플로 내에서 매개 변수 및 입력 보안](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)을 참조하세요.

API Management는 *명명된 값* 또는 *속성*이라는 개체를 사용하여 비밀을 관리합니다. 이러한 개체는 API Management 정책을 통해 액세스할 수 있는 값을 안전하게 저장합니다. 자세한 내용은 [API Management에서 비밀 관리](../api-management/api-management-howto-properties.md)를 참조하세요.

## <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

[API Management](../api-management/api-management-howto-use-azure-monitor.md)와 [Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md) 모두에서 운영 모니터링에 [Azure Monitor](../azure-monitor/overview.md)를 사용할 수 있습니다. Azure Monitor는 각 서비스에 구성된 메트릭을 기반으로 하여 정보를 제공하고 기본적으로 활성화됩니다.

또한 각 서비스에는 다음 옵션이 있습니다.

* 더 심층적인 분석 및 대시보드를 위해 Logic Apps 로그를 [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md)로 보낼 수 있습니다.

* DevOps 모니터링의 경우 API Management에 대해 Azure Application Insights를 구성할 수 있습니다.

* API Management는 [사용자 지정 API 분석용 Power BI 솔루션 템플릿](http://aka.ms/apimpbi)을 지원합니다. 자체 분석 솔루션을 만들기 위해 이 솔루션 템플릿을 사용할 수 있습니다. 비즈니스 사용자의 경우 Power BI를 통해 보고서를 사용할 수 있습니다.

## <a name="security"></a>보안

이 목록은 모든 보안 모범 사례를 완벽하게 설명하지 않지만 특히 이 문서에서 설명된 아키텍처에서 배포된 Azure 서비스에 적용되는 몇 가지 보안 고려 사항은 다음과 같습니다.

* 사용자에게 적절한 액세스 수준이 있는지 확인하려면 RBAC(역할 기반 액세스 제어)를 사용합니다.

* OAuth 또는 OpenID Connect를 사용하여 API Management에서 공용 API 엔드포인트를 보호합니다. 공용 API 엔드포인트를 보호하려면 ID 공급자를 구성하고, JWT(JSON Web Token) 유효성 검사 정책을 추가합니다.

* 상호 인증서를 사용하여 API Management에서 백 엔드 서비스에 연결합니다.

* API Management IP 주소를 가리키는 IP 주소 허용 목록을 만들어 HTTP 트리거 기반의 논리 앱을 보호합니다. 허용 목록에 포함된 IP 주소는 먼저 API Management를 거치지 않고 공용 인터넷에서 논리 앱을 호출하지 않도록 방지합니다.

## <a name="next-steps"></a>다음 단계

* [큐 및 이벤트와 엔터프라이즈 통합](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)에 대해 알아봅니다.
