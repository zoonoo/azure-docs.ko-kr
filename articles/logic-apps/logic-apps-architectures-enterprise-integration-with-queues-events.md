---
title: Azure Integration Services 참조 아키텍처
description: Logic Apps, API Management, Service Bus 및 Event Grid를 사용하여 엔터프라이즈 통합 패턴을 구현하는 방법을 보여 주는 참조 아키텍처입니다.
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232210"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>큐 및 이벤트와 엔터프라이즈의 통합: 참조 아키텍처

## <a name="overview"></a>개요

이 참조 아키텍처에서는 Azure Integration Services를 사용하는 통합 응용 프로그램에 대해 검증된 일단의 사례를 보여 줍니다. 이 아키텍처는 HTTP API, 워크플로 및 오케스트레이션을 필요로 하는 다양한 응용 프로그램 패턴의 기초로 사용할 수 있습니다.

*간단한 지점 간 응용 프로그램에서 완전한 엔터프라이즈 Service Bus에 이르기까지 통합 기술을 사용할 수 있는 많은 응용 프로그램이 있습니다. 이 아키텍처 시리즈는 모든 통합 응용 프로그램을 구축하기 위한 재사용 가능한 구성 요소를 설계합니다. 설계자는 자신의 응용 프로그램과 인프라에 필요한 구성 요소를 고려해야 합니다.*

![아키텍처 다이어그램 - 큐 및 이벤트와 엔터프라이즈의 통합](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>아키텍처

이 아키텍처는 [간단한 엔터프라이즈 통합](logic-apps-architectures-simple-enterprise-integration.md)에 표시된 아키텍처를 기반으로 합니다. 다음 구성 요소가 포함되어 있습니다.

- 리소스 그룹 리소스 그룹은 Azure 리소스에 대한 논리 컨테이너입니다.
- Azure API Management. Azure API Management는 HTTP API를 게시, 보안 및 변환하기 위한 완벽하게 관리되는 플랫폼입니다.
- Azure API Management 개발자 포털. Azure API Management의 각 인스턴스에는 개발자 포털이 함께 제공되어 설명서, 코드 샘플 및 API 테스트 기능에 액세스할 수 있습니다.
- Azure Logic Apps. Logic Apps는 엔터프라이즈 워크플로 및 통합을 구축하기 위한 서버를 사용하지 않는 플랫폼입니다.
- 커넥터. 커넥터는 Logic Apps에서 일반적으로 사용되는 서비스에 연결하는 데 사용됩니다. Logic Apps에는 이미 수백 개의 다양한 커넥터가 있지만 사용자 지정 커넥터를 사용하여 만들 수도 있습니다.
- Azure Service Bus. Service Bus는 안전하고 신뢰할 수 있는 메시징을 제공합니다. 메시징은 응용 프로그램을 서로 분리하고 다른 메시지 기반 시스템과 통합하는 데 사용할 수 있습니다.
- Azure Event Grid. Event Grid는 서버를 사용하지 않는 플랫폼으로, 응용 프로그램 이벤트를 게시하고 전달합니다.
- IP 주소입니다. Azure API Management 서비스에는 고정된 공용 IP 주소와 도메인 이름이 있습니다. 도메인 이름은 azure-api.net의 하위 도메인(예: contoso.azure-api.net)입니다. 또한 Logic Apps와 Service Bus에는 공용 IP 주소도 있습니다. 그러나 이 아키텍처에서는 Logic Apps 엔드포인트를 호출하는 액세스를 API Management의 IP 주소(보안용)로만 제한합니다. Service Bus에 대한 호출은 공유 액세스 서명으로 보호됩니다.
- Azure DNS. Azure DNS는 DNS 도메인에 대한 호스팅 서비스로, Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 사용자 지정 도메인 이름(예: contoso.com)을 사용하려면 사용자 지정 도메인 이름을 IP 주소에 매핑하는 DNS 레코드를 만듭니다. 자세한 내용은 'Azure App Service에서 사용자 지정 도메인 이름 구성'을 참조하세요.
- Azure AD(Azure Active Directory). 인증을 위해 Azure AD 또는 다른 ID 공급자를 사용합니다. Azure AD는 유효성 검사를 위해 API Management용 JSON 웹 토큰을 전달하여 API 엔드포인트에 액세스할 수 있는 인증을 제공하고, API Management 개발자 포털(표준 및 프리미엄 계층만)에 대한 액세스를 보호할 수 있습니다.

이 아키텍처에는 작업에 대한 몇 가지 기본 패턴이 있습니다.

1. 기존 백 엔드 HTTP API는 API Management 개발자 포털을 통해 게시되므로 개발자(조직 내부, 외부 또는 둘 다)가 이러한 API에 대한 호출을 응용 프로그램에 통합할 수 있습니다.
2. 복합 API는 Logic Apps를 사용하여 빌드됩니다. SAAS 시스템, Azure 서비스 및 API Management에 게시된 모든 API에 대한 호출을 오케스트레이션합니다. 또한 Logic Apps는 API Management 개발자 포털을 통해 게시됩니다.
3. 응용 프로그램은 Azure Active Directory를 사용하여 API에 액세스하는 데 필요한 OAuth 2.0 보안 토큰을 얻습니다.
4. Azure API Management는 보안 토큰의 유효성을 검사하고 요청을 백 엔드 API/Logic App으로 전달합니다.
5. Service Bus 큐는 응용 프로그램 활동을 분리하고 부하에서 스파이크를 완화하는 데 사용됩니다. API Management를 통해 큐를 HTTP API로 게시하여 Logic Apps, 타사 응용 프로그램 또는 그림에 표시되지 않은 응용 프로그램에서 메시지를 큐에 추가합니다.
6. 메시지가 Service Bus 큐에 추가되면 이벤트가 발생합니다. Logic App은 이 이벤트에서 트리거되고 메시지를 처리합니다.
7. 마찬가지로 다른 Azure 서비스(예: Blob Storage, Event Hub)도 Event Grid에 이벤트를 게시합니다. 이러한 이벤트는 해당 이벤트를 받도록 Logic Apps를 트리거하여 후속 작업을 수행합니다.

## <a name="recommendations"></a>권장 사항

개발자의 요구 사항이 여기에 설명된 아키텍처와 다를 수 있습니다. 이 섹션의 권장 사항을 시작점으로 사용합니다.

### <a name="service-bus-tier"></a>Service Bus 계층

현재 Event Grid 알림을 지원하므로 Service Bus 프리미엄 계층을 사용합니다(모든 계층 지원 예정). [Service Bus 가격](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

### <a name="event-grid-pricing"></a>Event Grid 가격 책정

Event Grid는 서버를 사용하지 않는 모델을 통해 작동하며, 청구는 작업 수(이벤트 실행)에 따라 계산됩니다. 자세한 내용은 [Event Grid 가격](https://azure.microsoft.com/pricing/details/event-grid/)을 참조하세요. 현재 Event Grid에 대한 계층 고려 사항은 없습니다.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Service Bus 메시지 사용 시 PeekLock 사용

Service Bus 메시지를 사용하는 Logic Apps를 만드는 경우 Logic App 내의 [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues)을 사용하여 메시지 그룹에 액세스합니다. 그런 다음, Logic App에서 완료 또는 중단하기 전에 각 메시지의 유효성을 검사하는 단계를 수행할 수 있습니다. 이 방법은 실수로 인한 메시지 손실을 방지합니다.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Event Grid 트리거 발생 시 여러 개체 확인

Event Grid 트리거가 발생하는 것은 단순히 "이러한 작업이 하나 이상 발생했다"는 것을 의미합니다. 예를 들어 Event Grid에서 Service Bus 큐에 표시되는 메시지에서 논리 앱을 트리거하는 경우, 논리 앱에서는 처리할 수 있는 메시지가 하나 이상 있을 수 있다고 항상 가정해야 합니다.

### <a name="region"></a>지역

동일한 지역에 API Management, Logic Apps 및 Service Bus를 프로비전하여 네트워크 대기 시간을 최소화합니다. 일반적으로 사용자에게 가장 가까운 지역을 선택합니다.

또한 리소스 그룹에는 배포 메타데이터가 저장되는 위치와 배포 템플릿이 실행되는 위치를 지정하는 지역이 있습니다. 리소스 그룹과 해당 리소스를 같은 지역에 배치합니다. 이렇게 하면 배포 중 가용성을 향상할 수 있습니다.

## <a name="scalability-considerations"></a>확장성 고려 사항

Azure Service Bus 프리미엄 계층에서는 메시징 단위의 수를 확장하여 더 높은 확장성을 달성할 수 있습니다. 프리미엄 계층은 1, 2 또는 4개의 메시징 단위를 갖출 수 있습니다. Azure Service Bus 크기 조정에 대한 자세한 지침은 [Service Bus 메시징을 사용한 성능 향상의 모범 사례](../service-bus-messaging/service-bus-performance-improvements.md)를 참조하세요.

## <a name="availability-considerations"></a>가용성 고려 사항

이 문서를 작성하는 시점에서 Azure API Management의 SLA(서비스 수준 계약)는 기본, 표준 및 프리미엄 계층에 대해 99.9%입니다. 둘 이상의 지역에 하나 이상의 단위가 배포되는 프리미엄 계층 구성의 SLA는 99.95%입니다.

이 문서를 작성하는 시점에서 Azure Logic Apps의 SLA는 99.9%입니다.

### <a name="disaster-recovery"></a>재해 복구

심각한 중단이 발생하는 경우 장애 조치할 수 있도록 하려면 Service Bus 프리미엄 계층에서 지리적 재해 복구를 구현하는 것이 좋습니다. [Azure Service Bus 지리적 재해 복구](../service-bus-messaging/service-bus-geo-dr.md)에 대해 자세히 알아보세요.

## <a name="manageability-considerations"></a>관리 효율성 고려 사항

프로덕션, 개발 및 테스트 환경에 대해 별도의 리소스 그룹을 만듭니다. 이렇게 하면 배포 관리, 테스트 배포 삭제, 액세스 권한 할당 등이 더 간단해집니다.
리소스를 리소스 그룹에 할당할 때는 다음 사항을 고려하세요.

- 수명 주기. 일반적으로 수명 주기가 동일한 리소스를 동일한 리소스 그룹에 배치합니다.
- 액세스. [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하여 그룹의 리소스에 액세스 정책을 적용할 수 있습니다.
- 청구. 리소스 그룹에 대한 롤업 비용을 확인할 수 있습니다.
- API Management 가격 책정 계층 - 개발 및 테스트 환경에는 개발자 계층을 사용하는 것이 좋습니다. 사전 프로덕션 환경에서 비용을 최소화하려면 프로덕션 환경의 복제본을 배포하고, 테스트를 실행한 다음, 시스템을 종료하는 것이 좋습니다.

자세한 내용은 [리소스 그룹 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="deployment"></a>배포

[Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 Azure API Management, Logic Apps, Event Grid 및 Service Bus를 배포하는 것이 좋습니다. 템플릿을 사용하면 PowerShell 또는 Azure CLI(명령줄 인터페이스)를 통해 배포를 쉽게 자동화할 수 있습니다.

Azure API Management, 개별 Logic Apps, Event Grid 항목 및 Service Bus 네임스페이스를 별도의 자체 Resource Manager 템플릿에 배치하는 것이 좋습니다. 이렇게 하면 원본 제어 시스템에 저장할 수 있습니다. 그런 다음, 이러한 템플릿을 CI/CD(지속적인 통합/지속적인 배포) 프로세스의 일부로 함께 배포하거나 개별적으로 배포할 수 있습니다.

### <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

Service Bus는 API Management 및 Logic Apps와 마찬가지로 Azure Monitor를 사용하여 모니터링할 수 있습니다. Azure Monitor는 기본적으로 사용되며 각 서비스에 대해 구성된 다른 메트릭에 기반한 정보를 제공합니다.

## <a name="security-considerations"></a>보안 고려 사항

공유 액세스 서명을 사용하여 Service Bus를 보호합니다. [SAS 인증](../service-bus-messaging/service-bus-sas.md)을 사용하면 특정 권한으로 Service Bus 리소스에 사용자 액세스 권한을 부여할 수 있습니다. [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)에 대해 자세히 알아보세요.

또한 Service Bus 큐를 HTTP 엔드포인트(새 메시지 게시 허용)로 노출해야 하는 경우, API Management를 통해 엔드포인트 앞에 배치하여 보호해야 합니다. 그런 다음, 인증서 또는 OAuth를 사용하여 적절히 보호할 수 있습니다. 이 작업을 수행하는 가장 쉬운 방법은 요청/응답 HTTP 트리거가 있는 Logic App을 중개자로 사용하는 것입니다.

Event Grid는 유효성 검사 코드를 통해 이벤트 전달을 보장합니다. LogicApps를 사용하여 이벤트를 사용하면 이 작업이 자동으로 수행됩니다. 자세한 내용은 [Azure Event Grid 보안 및 인증](../event-grid/security-authentication.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [간단한 엔터프라이즈 통합](logic-apps-architectures-simple-enterprise-integration.md)
