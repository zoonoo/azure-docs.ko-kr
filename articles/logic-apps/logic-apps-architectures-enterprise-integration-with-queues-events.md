---
title: Azure Integration Services 엔터프라이즈 통합 참조 아키텍처
description: Logic Apps, API Management, Service Bus 및 Event Grid를 사용하여 엔터프라이즈 통합 패턴을 구현하는 방법을 보여 주는 참조 아키텍처에 대해 설명합니다.
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
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116115"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>참조 아키텍처: 큐 및 이벤트와 엔터프라이즈의 통합

다음 참조 아키텍처는 Azure Integration Services를 사용하는 통합 응용 프로그램에 적용할 수 있는 검증된 일단의 사례를 보여 줍니다. 이 아키텍처는 HTTP API, 워크플로 및 오케스트레이션을 필요로 하는 다양한 응용 프로그램 패턴의 기초로 사용할 수 있습니다.

![아키텍처 다이어그램 - 큐 및 이벤트와 엔터프라이즈 통합](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

통합 기술에 사용 가능한 많은 응용 프로그램이 있습니다. 간단한 지점 간 응용 프로그램부터 전체 엔터프라이즈 Azure Service Bus 응용 프로그램까지 다양합니다. 아키텍처 시리즈는 일반적인 통합 응용 프로그램 빌드에 적용될 수 있는 재사용 가능한 구성 요소 파트에 대해 설명합니다. 설계자는 응용 프로그램 및 인프라에 구현해야 하는 구성 요소를 고려해야 합니다.

## <a name="architecture"></a>아키텍처

이 아키텍처는 [간단한 엔터프라이즈 통합](logic-apps-architectures-simple-enterprise-integration.md) 아키텍처를 *기반으로 합니다*. [간단한 엔터프라이즈 아키텍처에 대한 권장 사항](logic-apps-architectures-simple-enterprise-integration.md#recommendations)이 여기에 적용됩니다. 간단한 설명을 위해 이러한 권장 사항은 이 문서의 [권장 사항](#recommendations)에서 생략되었습니다. 

이 아키텍처의 구성 요소는 다음과 같습니다.

- **리소스 그룹**. [리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)은 Azure 리소스에 대한 논리적 컨테이너입니다.
- **Azure API Management**. [API Management](https://docs.microsoft.com/azure/api-management/)는 HTTP API를 게시, 보호 및 변환하는 데 사용되는 완전히 관리되는 플랫폼입니다.
- **Azure API Management 개발자 포털**. Azure API Management의 각 인스턴스에는 [개발자 포털](https://docs.microsoft.com/azure/api-management/api-management-customize-styles)에 대한 액세스가 제공됩니다. API Management 개발자 포털을 사용하여 문서 및 코드 샘플에 액세스할 수 있습니다. 개발자 포털에서 API를 테스트할 수도 있습니다.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)는 서버리스 플랫폼으로, 엔터프라이즈 워크플로 및 통합을 빌드하는 데 사용됩니다.
- **커넥터**. Logic Apps는 [커넥터](https://docs.microsoft.com/azure/connectors/apis-list)를 사용하여 일반적으로 사용되는 서비스에 연결합니다. Logic Apps에는 이미 수백 개의 다양한 커넥터가 있지만 사용자 지정 커넥터를 만들 수도 있습니다.
- **Azure Service Bus**. [Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)는 안전하고 신뢰할 수 있는 메시징을 제공합니다. 메시징을 사용하여 응용 프로그램을 분리하고 다른 메시지 기반 시스템과 통합할 수 있습니다.
- **Azure Event Grid**. [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)는 서버리스 플랫폼으로, 응용 프로그램 이벤트를 게시하고 전달하는 데 사용됩니다.
- **IP 주소**. Azure API Management 서비스에는 고정된 공용 [IP 주소](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)와 도메인 이름이 있습니다. 도메인 이름은 azure-api.net의 하위 도메인(예: contoso.azure-api.net)입니다. Logic Apps 및 Service Bus에도 공용 IP 주소가 있습니다. 그러나 이 아키텍처에서는 보안상 API Management의 IP 주소에서만 Logic Apps 끝점을 호출할 수 있습니다. Service Bus 호출은 SAS(공유 액세스 서명)로 보호됩니다.
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/)는 DNS 도메인에 대한 호스팅 서비스입니다. Azure DNS는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공합니다. Azure에 도메인을 호스트하면 다른 Azure 서비스에 사용하는 것과 동일한 자격 증명, API, 도구 및 청구를 사용하여 DNS 레코드를 관리할 수 있습니다. 사용자 지정 도메인 이름(예: contoso.com)을 사용하려면 사용자 지정 도메인 이름을 IP 주소에 매핑하는 DNS 레코드를 만듭니다. 자세한 내용은 [API Management에서 사용자 지정 도메인 이름 구성](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain)을 참조하세요.
- **Azure AD(Azure Active Directory)**. 인증을 위해 [Azure AD](https://docs.microsoft.com/azure/active-directory/) 또는 다른 ID 공급자를 사용합니다. Azure AD는 유효성을 검사할 [API Management용 JSON Web Token](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims)을 전달하여 API 끝점 액세스에 대한 인증을 제공합니다. Azure AD는 API Management 개발자 포털에 대한 액세스를 보호할 수 있습니다(표준 및 프리미엄 계층만 해당).

이 아키텍처의 작동에는 기본적인 몇 가지 패턴이 있습니다.

* 기존 백 엔드 HTTP API는 API Management 개발자 포털을 통해 게시됩니다. 포털에서 개발자(조직 내부, 외부 또는 둘 다)는 이러한 API 호출을 응용 프로그램에 통합할 수 있습니다.
* 복합 API는 논리 앱을 사용하고 SaaS(Software as a Service) 시스템, Azure 서비스 및 API Management에 게시된 모든 API에 대한 호출을 오케스트레이션하여 빌드됩니다. [논리 앱도 API Management 개발자 포털을 통해 게시됩니다](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api).
- 응용 프로그램은 Azure AD를 사용하여 API에 액세스하는 데 필요한 [OAuth 2.0 보안 토큰을 획득](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)합니다.
- API Management는 [보안 토큰의 유효성을 검사](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)하고 요청을 백 엔드 API 또는 논리 앱에 전달합니다.
- Service Bus 큐는 응용 프로그램 작업을 [분리](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview)하고 [부하 급증을 완화](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling)하는 데 사용됩니다. 메시지는 논리 앱이나 타사 응용 프로그램에 의해 또는 API Management를 통해 큐를 HTTP API로 게시하여(그림에 없음) 큐에 추가됩니다.
- 메시지가 Service Bus 큐에 추가되면 이벤트가 발생합니다. 논리 앱은 이벤트에 의해 트리거된 다음, 메시지를 처리합니다.
- 다른 Azure 서비스(예: Azure Blob Storage 및 Azure Event Hubs)도 Event Grid에 이벤트를 게시합니다. 이러한 서비스는 논리 앱을 트리거하여 이벤트를 받은 다음, 후속 작업을 수행합니다.

## <a name="recommendations"></a>권장 사항

특정 요구 사항은 이 문서에 설명된 일반 아키텍처와 다를 수 있습니다. 이 섹션의 권장 사항을 시작점으로 사용합니다.

### <a name="service-bus-tier"></a>Service Bus 계층

Service Bus 프리미엄 계층을 사용합니다. 프리미어 계층은 Event Grid 알림을 지원합니다. 자세한 내용은 [Service Bus 가격](https://azure.microsoft.com/pricing/details/service-bus/)을 참조하세요.

### <a name="event-grid-pricing"></a>Event Grid 가격 책정

Event Grid는 서버리스 모델을 사용합니다. 청구는 작업(이벤트 실행) 수를 기준으로 계산됩니다. 자세한 내용은 [Event Grid 가격](https://azure.microsoft.com/pricing/details/event-grid/)을 참조하세요. 현재, Event Grid에 대한 계층 고려 사항은 없습니다.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>PeekLock을 사용하여 Service Bus 메시지 사용

Service Bus 메시지를 사용할 논리 앱을 만드는 경우, 논리 앱에서 [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues)을 사용하여 메시지 그룹에 액세스합니다. PeekLock을 사용하는 경우, 논리 앱은 메시지를 완료하거나 중단하기 전에 각 메시지의 유효성을 검사하는 단계를 수행할 수 있습니다. 이 방법은 실수로 인한 메시지 손실을 방지합니다.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Event Grid 트리거 발생 시 여러 개체 확인

Event Grid 트리거가 실행되면 단순히 “이러한 사항 중 하나 이상이 발생했음”을 의미합니다. 예를 들어, Event Grid가 Service Bus 큐에 표시되는 메시지에서 논리 앱을 트리거하는 경우, 논리 앱은 처리할 수 있는 메시지가 하나 이상 있을 수 있다고 항상 가정해야 합니다.

### <a name="region"></a>지역

동일한 지역에 API Management, Logic Apps 및 Service Bus를 프로비전하여 네트워크 대기 시간을 최소화합니다. 일반적으로 사용자에게 가장 가까운 지역을 선택합니다.

리소스 그룹에도 지역이 있습니다. 지역은 배포 메타데이터가 저장되는 위치와 배포 템플릿이 실행되는 위치를 지정합니다. 배포 중에 가용성을 향상하려면 리소스 그룹 및 해당 리소스를 동일한 지역에 배치합니다.

## <a name="scalability"></a>확장성

Service Bus 프리미엄 계층에서는 메시징 단위 수를 확장하여 확장성을 높일 수 있습니다. 프리미엄 계층 구성에는 한 개, 두 개 또는 네 개의 메시징 단위가 있을 수 있습니다. Service Bus 크기 조정에 대한 자세한 내용은 [Service Bus 메시징을 사용한 성능 향상의 모범 사례](../service-bus-messaging/service-bus-performance-improvements.md)를 참조하세요.

## <a name="availability"></a>가용성

현재, Azure API Management의 SLA(서비스 수준 계약)는 기본, 표준 및 프리미엄 계층에 대해 99.9%입니다. 둘 이상의 지역에 하나 이상의 단위가 배포되는 프리미엄 계층 구성의 SLA는 99.95%입니다.

현재 Azure Logic Apps에 대한 SLA는 99.9%입니다.

### <a name="disaster-recovery"></a>재해 복구

심각한 중단이 발생할 경우, 장애 조치(failover)할 수 있도록 하려면 Service Bus 프리미엄 계층에서 지리적 재해 복구를 구현하는 것이 좋습니다. 자세한 내용은 [Azure Service Bus 지리적 재해 복구](../service-bus-messaging/service-bus-geo-dr.md)를 참조하세요.

## <a name="manageability"></a>관리 효율성

프로덕션, 개발 및 테스트 환경에 대해 별도의 리소스 그룹을 만듭니다. 별도의 리소스 그룹을 만들면 배포 관리, 테스트 배포 삭제, 액세스 권한 할당 등이 더 간단해집니다.

리소스 그룹에 리소스를 할당할 때는 다음 요소를 고려하세요.

- **수명 주기**. 일반적으로 수명 주기가 동일한 리소스를 동일한 리소스 그룹에 배치합니다.
- **액세스**. RBAC([역할 기반 액세스 제어](../role-based-access-control/overview.md))를 사용하여 그룹의 리소스에 액세스 정책을 적용할 수 있습니다.
- **청구**. 리소스 그룹에 대한 롤업 비용을 확인할 수 있습니다.
- **API Management 가격 책정 계층**. 개발 및 테스트 환경에는 개발자 계층을 사용하는 것이 좋습니다. 사전 프로덕션 환경에서 비용을 최소화하려면 프로덕션 환경의 복제본을 배포하고, 테스트를 실행한 다음, 시스템을 종료하는 것이 좋습니다.

자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.

### <a name="deployment"></a>배포

[Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)을 사용하여 API Management, Logic Apps, Event Grid 및 Service Bus를 배포하는 것이 좋습니다. 템플릿을 사용하면 PowerShell 또는 Azure CLI를 통해 배포를 쉽게 자동화할 수 있습니다.

API Management, 개별 논리 앱, Event Grid 항목 및 Service Bus 네임스페이스를 별도의 자체 Resource Manager 템플릿에 배치하는 것이 좋습니다. 별도의 템플릿을 사용하는 경우, 원본 제어 시스템에 리소스를 저장할 수 있습니다. 그런 다음, 이러한 템플릿을 CI/CD(지속적인 통합/지속적인 배포) 프로세스의 일부로 함께 배포하거나 개별적으로 배포할 수 있습니다.

### <a name="diagnostics-and-monitoring"></a>진단 및 모니터링

API Management 및 Logic Apps와 마찬가지로 Azure Monitor를 사용하여 Service Bus를 모니터링할 수 있습니다. Azure Monitor는 각 서비스에 구성된 메트릭을 기반으로 하여 정보를 제공합니다. Azure Monitor는 기본적으로 사용할 수 있습니다.

## <a name="security"></a>보안

SAS를 사용하여 Service Bus를 보호합니다. [SAS 인증](../service-bus-messaging/service-bus-sas.md)을 사용하여 특정 권한으로 Service Bus 리소스에 대한 액세스를 사용자에게 부여할 수 있습니다. 자세한 내용은 [Service Bus 인증 및 권한 부여](../service-bus-messaging/service-bus-authentication-and-authorization.md)를 참조하세요.

Service Bus 큐를 HTTP 끝점(새 메시지 게시용)으로 노출해야 하는 경우, API Management를 끝점 앞에 배치하여 끝점을 보호해야 합니다. 그런 다음, 인증서 또는 OAuth를 적절하게 사용하여 끝점을 보호할 수 있습니다. 끝점을 보호하는 가장 쉬운 방법은 요청/응답 HTTP 트리거가 있는 논리 앱을 중개자로 사용하는 것입니다.

Event Grid는 유효성 검사 코드를 통해 이벤트 전달을 보장합니다. Logic Apps를 통해 이벤트를 사용하는 경우, 유효성 검사가 자동으로 수행됩니다. 자세한 내용은 [Event Grid 보안 및 인증](../event-grid/security-authentication.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [간단한 엔터프라이즈 통합](logic-apps-architectures-simple-enterprise-integration.md)에 대해 알아봅니다.