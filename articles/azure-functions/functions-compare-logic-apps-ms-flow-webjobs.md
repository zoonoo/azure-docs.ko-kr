---
title: "Flow, Logic Apps, Functions 및 WebJobs 중에서 선택 | Microsoft Docs"
description: "Microsoft의 클라우드 통합 서비스를 비교 및 대조하고 사용해야 할 서비스를 결정합니다."
services: functions,app-service\logic
documentationcenter: na
author: cephalin
manager: wpickett
tags: 
keywords: "Microsoft Flow, Flow, Logic Apps, Azure Functions, Functions, Azure Webjobs, Webjobs, 이벤트 처리, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2017
ms.author: chrande; glenga
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 256bc04684ba337fe8a403d977079c96b63ca61d
ms.contentlocale: ko-kr
ms.lasthandoff: 08/04/2017

---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Flow, Logic Apps, Functions 및 WebJobs 중에서 선택
이 문서는 Microsoft 클라우드의 다음 서비스를 비교하고 대조하며 비즈니스 프로세스의 자동화 및 통합 문제를 모두 해결할 수 있습니다.

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure 기능](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service-web/web-sites-create-web-jobs.md)

이러한 모든 서비스는 서로 다른 시스템을 함께 "부착"할 때 유용합니다. 입력, 동작, 조건 및 출력을 모두 정의할 수 있습니다. 각 서비스를 일정이나 트리거에 따라 실행할 수 있습니다. 그러나 각 서비스는 고유한 값 집합을 추가하며, 서비스를 비교하여 "가장 좋은 서비스"가 아니라 "이 상황에 가장 적합한 서비스"를 알아낼 수 있습니다. 종종 이러한 서비스의 조합이 확장 가능하고 전체 기능을 갖춘 통합 솔루션을 신속하게 구축하는 가장 좋은 방법입니다.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow 및 논리 앱
Microsoft Flow 및 Azure Logic Apps는 둘 다 *구성 중심* 통합 서비스이므로 함께 설명할 수 있습니다. 이는 프로세스 및 워크플로를 쉽게 빌드하고 다양한 SaaS 및 엔터프라이즈 응용 프로그램을 쉽게 통합하도록 합니다. 

* Flow는 Logic Apps를 기반으로 빌드됩니다.
* 둘 다 동일한 워크플로 디자이너가 있습니다.
* [커넥터](../connectors/apis-list.md) 는 다른 서비스에서도 작동할 수 있습니다.

Flow는 개발자나 IT를 통하지 않고 사무실 작업자가 간단히 통합할 수 있도록 해줍니다(예: 중요한 메일에 대해 SMS 수신). 반면에 Logic Apps를 통해 엔터프라이즈 수준의 DevOps 및 보안 사례가 요구되는 고급 또는 중요 업무용 통합(예: B2B 프로세스)을 사용할 수 있습니다. 비즈니스 워크플로의 복잡성이 점차 증가하는 것이 일반적입니다. 그에 따라 우선 Flow를 시작한 다음 필요에 따라 논리 앱으로 변환할 수 있습니다.

다음 표를 통해 Flow 또는 Logic Apps가 주어진 통합에 가장 적합한지 여부를 확인할 수 있습니다.

|  | 흐름 | 논리 앱 |
| --- | --- | --- |
| 대상 |사무실 작업자, 비즈니스 사용자 |IT 전문가, 개발자 |
| 시나리오 |셀프서비스 |중요 업무용 |
| 디자인 도구 |브라우저 및 모바일 앱에서 UI만 해당 |브라우저 내부 및 [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [코드 보기](../logic-apps/logic-apps-author-definitions.md) 사용 가능 |
| DevOps |애드혹, 프로덕션에서 개발 |소스 제어, 테스트, 지원 및 [Azure 리소스 관리](../logic-apps/logic-apps-arm-provision.md) |
| 관리자 환경 |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com/](https://portal.azure.com) |
| 보안 |표준 사례: 중요한 데이터에 대한 [데이터 독립성](https://wikipedia.org/wiki/Technological_Sovereignty), [휴지 상태의 암호화](https://wikipedia.org/wiki/Data_at_rest#Encryption) 등 |Azure의 보안 보증: [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [감사 로그](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) 등 |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions 및 웹 작업
Azure Functions와 Azure App Service WebJobs는 둘 다 *코드 중심* 통합 서비스이며 개발자용으로 설계되었으므로 함께 설명할 수 있습니다. 이를 통해 [새 저장소 Blob](functions-bindings-storage.md) 또는 [WebHook 요청](functions-bindings-http-webhook.md) 등 다양한 이벤트에 대한 응답으로 스크립트 또는 코드 조각을 실행할 수 있습니다. 두 서비스의 유사점은 다음과 같습니다. 

* [Azure App Service](../app-service/app-service-value-prop-what-is.md)에서 빌드되고 [소스 제어](../app-service-web/app-service-continuous-deployment.md), [인증](../app-service/app-service-authentication-overview.md) 및 [모니터링](../app-service-web/web-sites-monitor.md) 등의 기능을 활용합니다.
* 개발자 중심 서비스입니다.
* 표준 스크립팅 및 프로그래밍 언어를 지원합니다.
* NuGet 및 NPM을 지원합니다.

Functions는 WebJobs의 장점을 모아 개선한다는 점에서 WebJobs의 자연스러운 진화입니다. 향상된 기능은 다음과 같습니다. 

* 간소화된 개발, 테스트 및 브라우저에서 직접 코드를 실행합니다.
* 더 많은 Azure 서비스 및 타사 서비스([GitHub WebHooks](https://developer.github.com/webhooks/creating/)등)와 통합을 기본 제공합니다.
* 사용량 과금이므로 [App Service 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에 대한 요금을 지불할 필요가 없습니다.
* 자동으로 [동적 크기 조정](functions-scale.md)을 수행합니다.
* App Service의 기존 고객인 경우 App Service 계획 실행이 계속 가능합니다(사용량이 적은 리소스 활용).
* Logic Apps와 통합합니다.

다음 표에 Functions와 WebJobs의 차이점이 요약되어 있습니다.

|  | 함수 | 웹 작업 |
| --- | --- | --- |
| 확장 |구성이 없는 크기 조정 |App Service 계획 크기 조정 |
| 가격 |사용량 과금 또는  App Service 계획의 일부 |App Service 계획의 일부 |
| 실행 형식 |트리거됨, 예약됨(타이머 트리거 사용) |트리거됨, 연속, 예약됨 |
| 트리거 이벤트 |[timer](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Notification Hubs](functions-bindings-notification-hubs.md), [Azure Service Bus](functions-bindings-service-bus.md), [Azure Storage](functions-bindings-storage.md) |[Azure Storage](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Azure Service Bus](../app-service-web/websites-dotnet-webjobs-sdk-service-bus.md) |
| 브라우저 내부 개발 |지원됨 | 지원되지 않음 |
| Window 스크립팅 |실험적 |지원됨 |
| PowerShell |실험적 |지원됨 |
| C# |지원됨 |지원됨 |
| F# |지원됨 |지원되지 않음 |
| Bash |실험적 |지원됨 |
| PHP |실험적 |지원됨 |
| 파이썬 |실험적 |지원됨 |
| JavaScript |지원됨 |지원됨 |

Functions 또는 WebJobs를 사용할지는 궁극적으로 App Service로 이미 수행하고 있는 서비스에 따라 다릅니다. 코드 조각을 실행할 App Service 앱이 있고 동일한 DevOps 환경에서 코드 조각을 함께 관리하려는 경우 WebJobs를 사용해야 합니다. 다른 Azure 서비스나 심지어 타사 앱에 대한 코드 조각을 실행하려는 경우, App Service 앱에서 통합 코드 조각을 별도로 관리하려는 경우 또는 논리 앱에서 코드 조각을 호출하려는 경우 Functions의 모든 향상된 기능을 활용합니다.  

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps 및 Functions
앞에서 설명한 것처럼 가장 적합한 서비스는 상황에 따라 다릅니다. 

* 간단한 비즈니스 최적화에는 Flow를 사용합니다.
* Flow에는 지나치게 복잡한 통합 시나리오이거나 DevOps 기능 및 보안 정책을 준수해야 하는 경우에는 Logic Apps를 사용합니다.
* 통합 시나리오의 단계에서 고도의 사용자 지정 변환 또는 특수한 코드가 필요한 경우에는 함수 앱을 작성한 다음 논리 앱에서 동작으로 함수를 트리거합니다.

흐름에서 논리 앱을 호출할 수 있습니다. 논리 앱에서 함수를, 함수에서 논리 앱을 호출할 수도 있습니다. Flow, Logic Apps 및 Functions 간의 통합은 계속해서 점차 개선됩니다. 하나의 서비스에서 어떤 기능을 빌드하여 다른 서비스에서 사용할 수 있습니다. 따라서 이러한 세 가지 기술로 만드는 노력은 가치가 있습니다.

## <a name="next-steps"></a>다음 단계
우선 흐름, 논리 앱, 함수 앱 또는 WebJob을 만들어 각 서비스를 시작합니다. 다음 링크 중 하나를 클릭합니다.

* [Microsoft Flow 시작](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [논리 앱 만들기](../logic-apps/logic-apps-create-a-logic-app.md)
* [첫 번째 Azure Function 만들기](functions-create-first-azure-function.md)
* [Visual Studio를 사용하여 WebJob 배포](../app-service-web/websites-dotnet-deploy-webjobs.md)

또는 이러한 통합 서비스에 대한 자세한 정보는 다음 링크를 참조하세요.

* [Christopher Anderson의 통합 시나리오에 대한 Azure Functions 및 Azure App Service 활용](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple by Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps Live Webcast](http://aka.ms/logicappslive)
* [Microsoft Flow Frequently asked questions](https://flow.microsoft.com/documentation/frequently-asked-questions/)
* [Azure WebJobs 설명서 리소스](../app-service-web/websites-webjobs-resources.md)


