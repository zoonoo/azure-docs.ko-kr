---
title: Azure Integration Services - 간단한 엔터프라이즈 통합
description: Azure Logic Apps 및 Azure API Management를 사용하여 간단한 엔터프라이즈 통합 패턴을 구현하는 방법을 보여 주는 참조 아키텍처입니다.
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232114"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>간단한 엔터프라이즈 통합 - 참조 아키텍처

## <a name="overview"></a>개요

이 참조 아키텍처에서는 Azure Integration Services를 사용하는 통합 응용 프로그램에 대해 검증된 일단의 사례를 보여 줍니다. 이 아키텍처는 HTTP API, 워크플로 및 오케스트레이션을 필요로 하는 다양한 응용 프로그램 패턴의 기초로 사용할 수 있습니다.

*간단한 지점 간 응용 프로그램에서 완전한 엔터프라이즈 Service Bus에 이르기까지 통합 기술을 사용할 수 있는 많은 응용 프로그램이 있습니다. 이 아키텍처 시리즈는 모든 통합 응용 프로그램을 구축하기 위한 재사용 가능한 구성 요소를 설계합니다. 설계자는 자신의 응용 프로그램과 인프라에 필요한 구성 요소를 고려해야 합니다.*

   ![아키텍처 다이어그램 - 간단한 엔터프라이즈 통합](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>아키텍처

이 아키텍처의 구성 요소는 다음과 같습니다.

- 리소스 그룹 리소스 그룹은 Azure 리소스에 대한 논리 컨테이너입니다.
- Azure API Management. Azure API Management는 HTTP API를 게시, 보안 및 변환하기 위한 완벽하게 관리되는 플랫폼입니다.
- Azure API Management 개발자 포털. Azure API Management의 각 인스턴스에는 개발자 포털이 함께 제공되어 설명서, 코드 샘플 및 API 테스트 기능에 액세스할 수 있습니다.
- Azure Logic Apps. Logic Apps는 엔터프라이즈 워크플로 및 통합을 구축하기 위한 서버를 사용하지 않는 플랫폼입니다.
- 커넥터. 커넥터는 Logic Apps에서 일반적으로 사용되는 서비스에 연결하는 데 사용됩니다. Logic Apps에는 이미 수백 개의 다양한 커넥터가 있지만 사용자 지정 커넥터를 사용하여 만들 수도 있습니다.
- IP 주소입니다. Azure API Management 서비스에는 고정된 공용 IP 주소와 도메인 이름이 있습니다. 도메인 이름은 azure-api.net의 하위 도메인(예: contoso.azure-api.net)입니다. 또한 Logic Apps에는 공용 IP 주소도 있습니다. 그러나 이 아키텍처에서는 Logic Apps 엔드포인트를 호출하는 액세스를 API Management의 IP 주소(보안용)로만 제한합니다.
- Azure DNS. Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 사용자 지정 도메인 이름(예: contoso.com)을 사용하려면 사용자 지정 도메인 이름을 IP 주소에 매핑하는 DNS 레코드를 만듭니다. 자세한 내용은 'Azure App Service에서 사용자 지정 도메인 이름 구성'을 참조하세요.
- Azure AD(Azure Active Directory). 인증을 위해 Azure AD 또는 다른 ID 공급자를 사용합니다. Azure AD는 유효성 검사를 위해 API Management용 JSON 웹 토큰을 전달하여 API 엔드포인트에 액세스할 수 있는 인증을 제공하고, API Management 개발자 포털(표준 및 프리미엄 계층만)에 대한 액세스를 보호할 수 있습니다.

이 아키텍처에는 작업에 대한 몇 가지 기본 패턴이 있습니다.

1. 기존 백 엔드 HTTP API는 API Management 개발자 포털을 통해 게시되므로 개발자(조직 내부, 외부 또는 둘 다)가 이러한 API에 대한 호출을 응용 프로그램에 통합할 수 있습니다.
2. 복합 API는 Logic Apps를 사용하여 빌드됩니다. SAAS 시스템, Azure 서비스 및 API Management에 게시된 모든 API에 대한 호출을 오케스트레이션합니다. 또한 Logic Apps는 API Management 개발자 포털을 통해 게시됩니다.
3. 응용 프로그램은 Azure Active Directory를 사용하여 API에 액세스하는 데 필요한 OAuth 2.0 보안 토큰을 얻습니다.
4. Azure API Management는 보안 토큰의 유효성을 검사하고 요청을 백 엔드 API/Logic App으로 전달합니다.

## <a name="recommendations"></a>권장 사항

개발자의 요구 사항이 여기에 설명된 아키텍처와 다를 수 있습니다. 이 섹션의 권장 사항을 시작점으로 사용합니다.

### <a name="azure-api-management-tier"></a>Azure API Management 계층

프로덕션 SLA를 제공하고 Azure 지역 내에서 규모 확장을 지원하므로 기본, 표준 또는 프리미엄 계층을 사용합니다(단위 수는 계층에 따라 다릅니다). 프리미엄 계층은 여러 Azure 지역에 걸쳐 규모 확장을 지원합니다. 필요한 처리량 수준 및 기능 집합에서 선택한 계층을 기반으로 합니다. 자세한 내용은 [API Management 가격](https://azure.microsoft.com/pricing/details/api-management/)을 참조하세요.

실행되고 있는 모든 API Management 인스턴스에 대한 요금이 청구됩니다. 규모를 강화했으며 항상 이러한 수준의 성능이 필요하지 않은 경우 API Management의 시간별 청구 및 규모 축소를 활용하는 것이 좋습니다.

### <a name="logic-apps-pricing"></a>Logic Apps 가격 책정

Logic Apps는 [서버를 사용하지 않는](logic-apps-serverless-overview.md) 모델로 작동하며, 청구는 작업 및 커넥터 실행에 따라 계산됩니다. 자세한 내용은 [Logic Apps 가격](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. 현재 Logic Apps에 대한 계층 고려 사항은 없습니다.

### <a name="logic-apps-for-asynchronous-api-calls"></a>비동기 API 호출에 대한 Logic Apps

Logic Apps는 짧은 대기 시간이 필요하지 않은 시나리오에 적합합니다(예: 비동기 또는 반장기 실행 API 호출). 사용자 인터페이스를 차단하는 호출과 같이 짧은 대기 시간이 필요한 경우 다른 기술을 사용하여 해당 API 또는 작업을 구현하는 것이 좋습니다(예: App Service를 사용하여 배포되는 Azure Functions 또는 웹 API). 이 API는 API Management를 사용하여 API 소비자에게 제공하는 것도 좋습니다.

### <a name="region"></a>지역

동일한 지역에 API Management 및 Logic Apps를 프로비전하여 네트워크 대기 시간을 최소화합니다. 일반적으로 사용자에게 가장 가까운 지역을 선택합니다.

또한 리소스 그룹에는 배포 메타데이터가 저장되는 위치와 배포 템플릿이 실행되는 위치를 지정하는 지역이 있습니다. 리소스 그룹과 해당 리소스를 같은 지역에 배치합니다. 이렇게 하면 배포 중 가용성을 향상할 수 있습니다.

## <a name="scalability-considerations"></a>확장성 고려 사항

API Management 관리자는 해당하는 경우 [캐싱 정책](../api-management/api-management-howto-cache.md)을 추가하여 서비스의 확장성을 높이고 백 엔드 서비스의 로드를 줄여야 합니다.

Azure API Management 기본, 표준 및 프리미엄 계층은 Azure 지역 내에서 확장하여 더 많은 용량을 제공할 수 있습니다. 관리자는 [메트릭] 메뉴의 [용량 메트릭]을 사용하여 서비스의 사용량을 분석하고 적절하게 강화 또는 축소할 수 있습니다.

API Management 서비스 크기 조정에 대한 권장 사항:

- 크기 조정은 트래픽 패턴을 고려해야 합니다. 트래픽 패턴이 심하게 변동하는 고객은 용량 증가에 대한 요구 사항이 더 큽니다.
- 66%를 초과하는 일관된 용량은 확장에 대한 필요성을 나타낼 수 있습니다.
- 20% 미만의 일관된 용량은 규모를 축소할 기회를 나타낼 수 있습니다.
- 항상 대표적인 부하 예를 사용하여 API Management 서비스의 부하를 테스트한 후에 프로덕션 환경에서 사용하도록 설정하는 것이 좋습니다.

프리미엄 계층 서비스는 여러 Azure 지역에 걸쳐 확장할 수 있습니다. 이런 방식으로 배포하는 고객은 더 높은 SLA(99.9%가 아니라 99.95%)를 얻게 되고 여러 지역의 사용자에 인접한 위치로 서비스를 프로비전할 수 있습니다.

서버를 사용하지 않는 Logic Apps 모델에서는 관리자가 서비스 확장성에 대해 특별히 고려할 필요가 없으며, 서비스는 수요에 맞게 자동으로 크기 조정됩니다.

## <a name="availability-considerations"></a>가용성 고려 사항

이 문서를 작성하는 시점에서 Azure API Management의 SLA(서비스 수준 계약)는 기본, 표준 및 프리미엄 계층에 대해 99.9%입니다. 둘 이상의 지역에 하나 이상의 단위가 배포되는 프리미엄 계층 구성의 SLA는 99.95%입니다.

이 문서를 작성하는 시점에서 Azure Logic Apps의 SLA는 99.9%입니다.

### <a name="backups"></a>Backup

Azure API Management 구성은 [정기적으로 백업](../api-management/api-management-howto-disaster-recovery-backup-restore.md)하고(정기적인 변경 규칙에 따라 적절하게), 백업 파일은 서비스가 있는 위치와 다른 위치 또는 Azure 지역에 저장해야 합니다. 그런 다음, 고객은 DR 전략으로 다음 두 옵션 중 하나를 선택할 수 있습니다.

1. DR 이벤트에서 새 API Management 인스턴스가 프로비전되고, 백업이 복원되며, DNS 레코드가 다시 지정됩니다.
2. 고객이 서비스의 수동 복사본을 다른 Azure 지역에서 유지하고(추가 비용 발생), 백업이 정기적으로 복원됩니다. DR 이벤트에서 DNS 레코드만 다시 지정되어 서비스를 복원해야 합니다.

Logic Apps는 매우 빠르게 다시 만들 수 있고 서버를 사용하지 않으므로 연결된 Azure Resource Manager 템플릿의 복사본을 저장하여 백업됩니다. 이러한 Logic Apps는 원본 제어에 저장하고 고객의 CI/CD(지속적인 통합/지속적인 배포) 프로세스에 통합할 수 있습니다.

API Management를 통해 게시된 Logic Apps를 다른 데이터 센터로 이동하는 경우 위치를 업데이트해야 합니다. 이 작업은 API의 Backend(백 엔드) 속성을 업데이트하는 간단한 PowerShell 스크립트를 통해 수행할 수 있습니다.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항

프로덕션, 개발 및 테스트 환경에 대해 별도의 리소스 그룹을 만듭니다. 이렇게 하면 배포 관리, 테스트 배포 삭제, 액세스 권한 할당 등이 더 간단해집니다.
리소스를 리소스 그룹에 할당할 때는 다음 사항을 고려하세요.

- 수명 주기. 일반적으로 수명 주기가 동일한 리소스를 동일한 리소스 그룹에 배치합니다.
- 액세스. [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하여 그룹의 리소스에 액세스 정책을 적용할 수 있습니다.
- 청구. 리소스 그룹에 대한 롤업 비용을 확인할 수 있습니다.
- API Management 가격 책정 계층 - 개발 및 테스트 환경에는 개발자 계층을 사용하는 것이 좋습니다. 사전 프로덕션 환경에서 비용을 최소화하려면 프로덕션 환경의 복제본을 배포하고, 테스트를 실행한 다음, 시스템을 종료하는 것이 좋습니다.

자세한 내용은 [리소스 그룹 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="deployment"></a>배포

[Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Azure API Management 및 Azure Logic Apps를 모두 배포하는 것이 좋습니다. 템플릿을 사용하면 PowerShell 또는 Azure CLI(명령줄 인터페이스)를 통해 배포를 쉽게 자동화할 수 있습니다.

Azure API Management 및 모든 개별 Logic Apps를 별도의 자체 Resource Manager 템플릿에 배치하는 것이 좋습니다. 이렇게 하면 원본 제어 시스템에 저장할 수 있습니다. 그런 다음, 이러한 템플릿을 CI/CD(지속적인 통합/지속적인 배포) 프로세스의 일부로 함께 배포하거나 개별적으로 배포할 수 있습니다.

### <a name="versions"></a>버전

Logic App 구성을 변경하거나 Resource Manager 템플릿을 통해 업데이트를 배포할 때마다 사용자의 편의를 위해 해당 버전의 복사본이 유지됩니다(실행 기록이 있는 모든 버전이 유지됨). 이러한 버전을 사용하여 기록 변경 내용을 추적하고 버전을 논리 앱의 현재 구성으로 승격할 수도 있습니다. 예를 들어 이렇게 하면 Logic App을 효과적으로 롤백할 수 있습니다.

API Management에는 별개의(그러나 무료) 두 가지 [버전 관리 개념](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/)이 있습니다.

- API 소비자에게 자신의 필요에 따라 사용할 수 있는 API(예: v1, v2 또는 베타, 프로덕션) 선택 옵션을 제공하는 데 사용되는 버전
- API 관리자가 API를 안전하게 변경하고 선택적인 설명을 사용하여 API를 사용자에게 배포할 수 있는 수정

배포 컨텍스트에서 API Management 수정은 안전하게 변경하고, 변경 기록을 유지하며, API 소비자가 변경 내용을 알 수 있도록 하는 방식으로 고려되어야 합니다. 수정은 개발 환경에서 만들고 Resource Manager 템플릿을 사용하여 다른 환경 간에 배포할 수 있습니다.

반면에 API가 '현재' 버전으로 만들어지고 사용자가 액세스할 수 있게 되기 전에 API를 테스트하는 데 수정을 사용할 수 있지만, 로드 또는 통합 테스트에는 이 메커니즘을 사용하지 않는 것이 좋습니다. 대신 별도의 테스트 또는 사전 프로덕션 환경을 사용해야 합니다.

### <a name="configuration"></a>구성

암호, 액세스 키 또는 연결 문자열을 원본 제어에 체크 인하면 안됩니다. 필요한 경우 적절한 기술을 사용하여 이러한 값을 배포하고 보호합니다. 

Logic Apps에서 논리 앱 내에 필요한 중요한 값(연결 형식으로 만들 수 없음)은 Azure Key Vault에 저장해야 하며 Resource Manager 템플릿에서 참조해야 합니다. 또한 각 환경에 대한 매개 변수 파일과 함께 배포 템플릿 매개 변수를 사용하는 것이 좋습니다. 자세한 지침은 [워크플로 내에서 매개 변수 및 입력 보안](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow)을 참조하세요.

API Management에서 비밀은 Named [명명된 값/속성]이라는 개체를 사용하여 관리됩니다. 이러한 개체는 API Management 정책에서 액세스할 수 있는 값을 안전하게 저장합니다. [API Management에서 비밀을 관리하는 방법](../api-management/api-management-howto-properties.md)을 참조하세요.

### <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

[API Management](../api-management/api-management-howto-use-azure-monitor.md)와 [Logic Apps](logic-apps-monitor-your-logic-apps.md)는 모두 [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md)를 통한 운영 모니터링을 지원합니다. Azure Monitor는 기본적으로 사용되며 각 서비스에 대해 구성된 다른 메트릭에 기반한 정보를 제공합니다.

또한 각 서비스에 대한 추가 옵션도 있습니다.

- Logic Apps 로그는 더 심층적인 분석 및 대시보드를 위해 [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md)로 보낼 수 있습니다.
- API Management는 Dev Ops 모니터링에 대한 Application Insights 구성을 지원합니다.
- API Management는 [사용자 지정 API 분석용 Power BI 솔루션 템플릿](http://aka.ms/apimpbi)을 지원합니다. 이 솔루션 템플릿을 사용하면 고객이 비즈니스 사용자를 위해 Power BI에서 사용할 수 있는 보고서를 통해 고유한 사용자 지정 분석 솔루션을 만들 수 있습니다.

## <a name="security-considerations"></a>보안 고려 사항

이 섹션에는 이 문서에서 설명한 대로 아키텍처에 배포된 Azure 서비스와 관련된 보안 고려 사항이 나와 있습니다. 보안 모범 사례가 완전히 다 나와 있는 것은 아닙니다.

- RBAC(역할 기반 액세스 제어)를 사용하여 사용자에게 적절한 수준의 액세스를 보장합니다.
- OAuth/Open IDConnect를 사용하여 API Management에서 공용 API 엔드포인트를 보호합니다. 이 작업은 ID 공급자를 구성하고 JWT 유효성 검사 정책을 추가하여 수행합니다.
- 상호 인증서를 사용하여 API Management에서 백 엔드 서비스에 연결합니다.
- API Management의 IP 주소를 가리키는 IP 주소 허용 목록을 만들어 HTTP 트리거 기반의 Logic Apps를 보호합니다. 이렇게 하면 먼저 API Management를 거치지 않고 공용 인터넷에서 논리 앱을 호출하지 않도록 방지할 수 있습니다.

이 참조 아키텍처에서는 Azure Integration Services를 사용하여 간단한 엔터프라이즈 통합 플랫폼을 구축하는 방법을 보여 주었습니다.

## <a name="next-steps"></a>다음 단계

* [큐 및 이벤트와 엔터프라이즈의 통합](logic-apps-architectures-enterprise-integration-with-queues-events.md)
