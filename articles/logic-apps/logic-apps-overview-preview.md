---
title: Azure Logic Apps 미리 보기 개요
description: Azure Logic Apps 미리 보기는 엔터프라이즈 수준 시나리오에 대한 최소한의 코드로 앱, 데이터, 서비스 및 시스템을 통합하는 자동화된 단일 테넌트 상태 비저장 워크플로를 빌드하는 데 사용되는 클라우드 솔루션입니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764822"
---
# <a name="overview-azure-logic-apps-preview"></a>개요: Azure Logic Apps 미리 보기

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Logic Apps 미리 보기를 사용하면 새로운 **논리 앱(미리 보기)** 리소스 종류를 통해 단일 테넌트 논리 앱을 만들고 실행하여 앱, 데이터, 클라우드 서비스 및 시스템에서 자동화 및 통합 솔루션을 빌드할 수 있습니다. 이 단일 테넌트 논리 앱 유형을 사용하면 Azure뿐 아니라 Docker 컨테이너까지 포함한 다양한 호스팅 환경에서 이식성, 향상된 성능 및 유연성을 제공하는 재설계된 Azure Logic Apps 미리 보기 런타임으로 구동되는 여러 [*상태 저장* 및 *상태 비저장* 워크플로](#stateful-stateless)를 빌드할 수 있습니다.

어떻게 가능한가요? 재설계된 런타임은 [Azure Functions 확장성 모델](../azure-functions/functions-bindings-register.md)을 사용하며 Azure Functions 런타임에 확장으로 호스트됩니다. 이 아키텍처는 Azure Functions가 실행되는 모든 위치에서 단일 테넌트 논리 앱 유형을 실행할 수 있음을 의미합니다. 재설계된 런타임을 거의 모든 네트워크 토폴로지에 호스트할 수 있으며, 사용 가능한 컴퓨팅 크기를 선택하여 워크플로에 필요한 워크로드를 처리할 수 있습니다. 자세한 내용은 [Azure Functions 소개](../azure-functions/functions-overview.md) 및 [Azure Functions 트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md)을 참조하세요.

[Azure Portal에서 시작](create-stateful-stateless-workflows-azure-portal.md)하거나 [Visual Studio Code에서 Azure Logic Apps(미리 보기) 확장을 사용하여 프로젝트를 만들어](create-stateful-stateless-workflows-visual-studio-code.md) **논리 앱(미리 보기)** 리소스를 만들 수 있습니다. 또한 Visual Studio Code를 사용하여 개발 환경에서 워크플로를 빌드하고 *로컬로 실행* 할 수 있습니다. 포털을 사용하든 Visual Studio Code를 사용하든, 동일한 종류의 호스팅 환경에 단일 테넌트 논리 앱 유형을 배포하고 실행할 수 있습니다.

이 개요에서는 다음 영역을 다룹니다.

* [Azure Logic Apps 미리 보기, Azure Logic Apps 다중 테넌트 환경 및 통합 서비스 환경의 차이점](#preview-differences)

* [중첩된 상태 저장 워크플로와 상태 비저장 워크플로](#nested-behavior)의 동작 차이를 비롯하여 [상태 저장 워크플로와 상태 비저장 워크플로의 차이점](#stateful-stateless)

* [이 공개 미리 보기의 기능](#public-preview-contents)

* [가격 책정 모델의 작동 방식](#pricing-model)

* [변경된 기능, 제한된 기능, 사용할 수 없는 기능 또는 지원되지 않는 기능](#limited-unavailable-unsupported)

* [Azure Logic Apps 미리 보기의 한도](#limits)

자세한 내용은 다음 토픽을 참조하세요.

* [어디서나 실행되는 Azure Logic Apps - 런타임 심층 살펴보기](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps 공개 미리 보기의 알려진 문제(GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure Logic Apps 미리 보기의 차이점

Azure Logic Apps 미리 보기는 [Azure Functions](../azure-functions/functions-overview.md) 확장성 모델을 사용하며 Azure Functions 런타임에 확장으로 호스트됩니다. 이 아키텍처는 Azure Functions가 실행되는 모든 위치에서 단일 테넌트 논리 앱 유형을 실행할 수 있음을 의미합니다. 원하는 거의 모든 네트워크 토폴로지에 Azure Logic Apps 미리 보기 런타임을 호스트하고, 사용 가능한 컴퓨팅 크기를 선택하여 워크플로에 필요한 워크로드를 처리할 수 있습니다. Azure Functions 확장성에 대한 자세한 내용은 [WebJobs SDK: 사용자 지정 입력 및 출력 바인딩 만들기](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)를 참조하세요.

이 새로운 접근 방식에서 Azure Logic Apps 미리 보기 런타임과 워크플로는 함께 패키징할 수 있는 앱의 일부입니다. 이 기능을 사용하면 간단히 아티팩트를 호스팅 환경에 복사하고 앱을 시작하여 워크플로를 배포하고 실행할 수 있습니다. 또한 이 접근 방식은 프로덕션 환경에 변경 내용을 배포하기 전에 필요한 테스트 및 유효성 검사를 실행할 수 있도록 워크플로 프로젝트에 대한 배포 파이프라인을 구축하는 보다 표준화된 환경을 제공합니다. 자세한 내용은 [어디서나 실행되는 Azure Logic Apps- 런타임 심층 살펴보기](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)를 참조하세요.

다음 표에는 워크플로가 실행 환경에 따라 리소스를 공유하는 방식의 차이가 간략하게 요약되어 있습니다. 한도의 차이는 [Azure Logic Apps 미리 보기의 한도](#limits)를 참조하세요.

| Environment | 리소스 공유 및 사용 |
|-------------|----------------------------------|
| Azure Logic Apps(다중 테넌트) | *다중 테넌트를 사용하는 고객* 의 워크플로가 동일한 처리(컴퓨팅), 스토리지, 네트워크 등을 공유합니다. |
| Azure Logic Apps(미리 보기, 단일 테넌트) | *동일한 논리 앱과 단일 테넌트* 의 워크플로가 동일한 처리(컴퓨팅), 스토리지, 네트워크 등을 공유합니다. |
| 통합 서비스 환경(미리 보기 버전에서는 사용할 수 없음) | *동일한 환경* 의 워크플로가 동일한 처리(컴퓨팅), 스토리지, 네트워크 등을 공유합니다. |
|||

한편 여전히 Azure Portal 및 Visual Studio Code에서 다중 테넌트 Azure Logic Apps 확장을 사용하여 다중 테넌트 논리 앱 유형을 만들 수 있습니다. 다중 테넌트 논리 앱 유형과 단일 테넌트 논리 앱 유형의 개발 환경이 서로 다르지만 Azure 구독에 두 유형을 모두 포함할 수 있습니다. Azure 구독에 배포된 모든 논리 앱을 살펴보고 액세스할 수 있지만, 앱은 자체 범주 및 섹션으로 구성됩니다.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>상태 저장 및 상태 비저장 워크플로

단일 테넌트 논리 앱 유형을 사용하면 동일한 논리 앱 내에서 이러한 워크플로 유형을 만들 수 있습니다.

* *상태 저장*

  이전 이벤트의 데이터를 유지, 검토 또는 참조해야 하는 경우 상태 저장 워크플로를 만듭니다. 이러한 워크플로는 각 작업에 대한 입출력과 해당 상태를 외부 스토리지에 저장하므로, 각 실행이 완료된 후 실행 정보 및 기록을 검토할 수 있습니다. 상태 저장 워크플로는 중단이 발생할 때 높은 복원력을 제공합니다. 서비스 및 시스템이 복원된 후에는 저장된 상태로부터 중단된 실행을 다시 생성하고 워크플로를 다시 실행하여 완료할 수 있습니다. 상태 저장 워크플로는 최대 1년 동안 계속 실행될 수 있습니다.

* *상태 비저장*

  나중에 검토할 수 있도록 이전 이벤트의 데이터를 외부 스토리지에 저장하고 검토 또는 참조할 필요가 없는 경우에는 상태 비저장 워크플로를 만듭니다. 이러한 워크플로는 이 데이터를 외부 스토리지에 전송하는 대신 각 작업에 대한 입출력과 해당 상태를 *메모리에만* 저장합니다. 결과적으로 상태 비저장 워크플로는 실행 시간이 더 짧고(일반적으로 5분을 넘지 않음), 응답 시간이 더 짧아서 더욱 빠르고, 처리량이 더 많고, 실행 정보 및 기록을 외부 스토리지에 보관하지 않으므로 실행 비용이 절감됩니다. 그러나 중단이 발생하면 중단된 실행이 자동으로 복원되지 않으므로 호출자는 중단된 실행을 수동으로 다시 제출해야 합니다. 이러한 워크플로는 동기적으로만 실행할 수 있습니다.

  간편한 디버깅을 위해 상태 비저장 워크플로에 실행 기록을 사용하도록 설정하고, 모두 마친 후 실행 기록을 사용하지 않도록 설정할 수 있습니다. 실행 기록을 사용하면 성능에 약간의 영향이 있습니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) 또는 [Azure Portal에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)를 참조하세요.

  > [!NOTE]
  > 상태 비저장 워크플로는 현재 트리거가 아닌 Azure에 배포되는 [관리 커넥터](../connectors/managed.md)에 대한 *작업* 만 지원합니다. 워크플로를 시작하려면 [기본 제공 요청, Event Hubs 또는 Service Bus 트리거](../connectors/built-in.md) 중 하나를 선택합니다. 이러한 트리거는 기본적으로 Azure Logic Apps 미리 보기 런타임에서 실행됩니다. 제한된/사용할 수 없는/지원되지 않는 트리거, 작업 및 커넥터에 대한 자세한 내용은 [변경된 기능, 제한된 기능, 사용할 수 없는 기능 또는 지원되지 않는 기능](#limited-unavailable-unsupported)을 참조하세요.

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>상태 저장 워크플로와 상태 비저장 워크플로의 중첩된 동작 차이점

[요청 트리거](../connectors/connectors-native-reqres.md), [HTTP Webhook 트리거](../connectors/connectors-native-webhook.md) 또는 [ApiConnectionWebhook 유형](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger)을 포함하고 있으며 HTTPS 요청을 받을 수 있는 관리형 커넥터 트리거를 사용하여 동일한 **논리 앱(미리 보기)** 리소스에 있는 다른 워크플로에서 호출 가능한 [워크플로를 만들 수 있습니다](../logic-apps/logic-apps-http-endpoint.md).

다음은 부모 워크플로가 자식 워크플로를 호출한 후에 중첩된 워크플로가 따를 수 있는 동작 패턴입니다.

* 비동기 폴링 패턴

  부모는 초기 호출에 대한 응답을 기다리지 않지만, 자식의 실행이 완료될 때까지 계속해서 자식의 실행 기록을 확인합니다. 기본적으로 상태 저장 워크플로는 이 패턴을 따릅니다. 이 패턴은 [요청 시간 제한](../logic-apps/logic-apps-limits-and-config.md)을 초과할 가능성이 있는 장기 실행 자식 워크플로에 적합합니다.

* 동기 패턴("자체 유도")

  자식은 즉시 `202 ACCEPTED` 응답을 반환하여 호출을 승인하고, 부모는 자식의 결과를 기다리지 않고 다음 작업을 계속합니다. 그 대신 부모는 자식의 실행이 완료될 때 결과를 받습니다. 응답 작업이 없는 자식 상태 저장 워크플로는 항상 동기 패턴을 따릅니다. 자식 상태 저장 워크플로의 경우 실행 기록을 검토할 수 있습니다.

  이 동작을 사용하려면 워크플로의 JSON 정의에서 `operationOptions` 속성을 `DisableAsyncPattern`으로 설정합니다. 자세한 내용은 [트리거 및 작업 형식 - 작업 옵션](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)을 참조하세요.

* 트리거 및 대기

  자식 상태 비저장 워크플로의 경우 부모는 결과를 반환하는 자식의 응답을 기다립니다. 이 패턴은 기본 제공 [HTTP 트리거 또는 작업](../connectors/connectors-native-http.md)을 사용하여 자식 워크플로를 호출할 때와 비슷한 방식으로 작동합니다. 응답 작업이 없는 자식 상태 비저장 워크플로는 즉시 `202 ACCEPTED` 응답을 반환하지만, 부모는 자식이 완료될 때까지 기다렸다가 다음 작업을 계속합니다. 이러한 동작은 자식 상태 비저장 워크플로에만 적용됩니다.

다음 표는 부모와 자식이 상태 저장인지, 상태 비저장인지 아니면 혼합 워크플로 유형인지에 따라 자식 워크플로의 동작을 지정합니다.

| 부모 워크플로 | 하위 워크플로 | 자식 동작 |
|-----------------|----------------|----------------|
| 상태 저장 | 상태 저장 | 비동기 또는 `"operationOptions": "DisableAsyncPattern"` 설정을 사용하는 동기 |
| 상태 저장 | 상태 비저장 | 트리거 및 대기 |
| 상태 비저장 | 상태 저장 | 동기 |
| 상태 비저장 | 상태 비저장 | 트리거 및 대기 |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>기능

Azure Logic Apps 미리 보기에는 다음과 같은 여러 최신 기능과 추가 기능이 포함되어 있습니다.

* SaaS(Software as a Service) 및 PaaS(Platform as a Service) 앱과 서비스를 위한 [400개가 넘는 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors)와 온-프레미스 시스템을 위한 커넥터를 사용하여 논리 앱과 해당 워크플로를 만들 수 있습니다.

  * 이제 일부 관리형 커넥터를 기본 제공 버전으로 사용할 수 있습니다. 이러한 커넥터는 요청 트리거나 HTTP 작업처럼 기본적으로 Azure Logic Apps 미리 보기 런타임에서 실행되는 기본 제공 트리거 및 작업과 비슷한 방식으로 실행됩니다. 예를 들어 새로운 기본 제공 커넥터로 Azure Service Bus, Azure Event Hubs, SQL Server 및 MQ가 있습니다.

    > [!NOTE]
    > 기본 제공 SQL Server 커넥터의 경우 **쿼리 실행** 작업만 [온-프레미스 데이터 게이트웨이](logic-apps-gateway-connection.md)를 요구하지 않고 Azure 가상 네트워크에 직접 연결할 수 있습니다.

  * [미리 보기 릴리스의 확장성 프레임워크](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)를 사용하여 필요한 모든 서비스에 대한 기본 제공 커넥터를 만들어 보세요. 이러한 커넥터는 Azure Service Bus 및 SQL Server와 같은 기본 제공 커넥터와 비슷하게, 하지만 [사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors)와는 다르게 더 높은 처리량, 짧은 대기 시간, 로컬 연결을 제공하고 기본적으로 미리 보기 런타임과 동일한 프로세스에서 실행됩니다.

    제작 기능은 현재 Visual Studio Code에서만 사용할 수 있지만, 기본적으로 사용하도록 설정되어 있지 않습니다. 이러한 커넥터를 만들려면 [프로젝트를 확장 번들 기반(Node.js)에서 NuGet 패키지 기반(.NET)으로 변환](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring)해야 합니다. 자세한 내용은 [어디서나 실행되는 Azure Logic Apps - 기본 제공 커넥터 확장성](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)을 참조하세요.

  * 통합 계정 없이 Liquid 작업 및 XML 작업에 B2B 작업을 사용할 수 있습니다. 이러한 작업을 사용하려면 Liquid 맵, XML 맵 또는 XML 스키마가 필요하며, Azure Portal에서 각 작업을 통해 업로드하거나 각각 **Maps** 및 **Schemas** 폴더를 사용하여 Visual Studio Code 프로젝트의 **Artifacts** 폴더에 추가할 수 있습니다.

  * Azure Logic Apps 서비스는 클라우드 연결 런타임 엔드포인트에 요청을 보내는 데 사용할 수 있는 SAS(공유 액세스 서명) 연결 문자열을 생성하기 때문에 어디서나 실행 가능한 논리 앱을 만들고 배포합니다. Logic Apps 서비스는 Azure에 배포할 때 연결 문자열을 Azure Key Vault에 쉽게 저장할 수 있도록 연결 문자열을 다른 애플리케이션 함께 저장합니다.

    > [!NOTE]
    > 기본적으로 **논리 앱(미리 보기)** 리소스에는 런타임에 연결을 인증하기 위해 자동으로 사용하도록 설정되는 [시스템이 할당한 관리 ID](../logic-apps/create-managed-service-identity.md)가 있습니다. 이 ID는 연결을 만들 때 사용하는 인증 자격 증명이나 연결 문자열과는 다릅니다. 이 ID를 사용하지 않도록 설정하면 런타임에 연결이 작동하지 않습니다. 이 설정을 보려면 논리 앱 메뉴의 **설정** 에서 **ID** 를 선택합니다.

* 메모리에서만 실행되는 상태 비저장 워크플로를 사용하여 논리 앱을 만듭니다. 이렇게 하면 작업 간 실행 기록과 데이터가 외부 스토리지에 보관되지 않으므로 더 빠르게 완료되고, 더 빠르게 응답하고, 처리량이 더 많고, 실행 비용이 절감됩니다. 필요에 따라 용이한 디버깅을 위해 실행 기록을 사용할 수 있습니다. 자세한 내용은 [상태 저장 논리 앱과 상태 비저장 논리 앱의 차이](#stateful-stateless)를 참조하세요.

* Visual Studio Code 개발 환경에서 논리 앱 및 해당 워크플로를 로컬로 실행, 테스트 및 디버그합니다.

  논리 앱을 실행하고 테스트하기 전에 워크플로에 대한 **workflow.json** 파일 내에 중단점을 추가하고 사용하면 디버깅을 더 쉽게 수행할 수 있습니다. 하지만 현재는 트리거가 아닌 작업에만 중단점이 지원됩니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)를 참조하세요.

* Visual Studio Code에서 Azure 및 [Docker 컨테이너](/dotnet/core/docker/introduction)와 같은 다양한 호스팅 환경에 논리 앱 및 해당 워크플로를 직접 게시하거나 배포합니다.

* Azure 구독 및 논리 앱 설정에서 지원하는 경우 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용하여 논리 앱에 진단 로깅 및 추적 기능을 사용하도록 설정합니다.

* Azure Functions와 마찬가지로 [Azure Functions 프리미엄 플랜](../azure-functions/functions-premium-plan.md)을 사용하여 논리 앱을 만들고 배포할 때 Azure 가상 네트워크와 비공개로 연결하고 통합하는 기능을 비롯한 네트워킹 기능을 사용합니다. 자세한 내용은 다음 토픽을 참조하세요.

  * [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)

  * [어디서나 실행되는 Azure Logic Apps - Azure Logic Apps 미리 보기의 네트워킹 기능](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* 단일 테넌트 **논리 앱(미리 보기)** 리소스의 개별 워크플로에서 사용하는 관리형 연결을 위한 액세스 키를 다시 생성합니다. 이 작업의 경우 [논리 앱 리소스 수준이 아닌 개별 워크플로 수준에서 다중 테넌트 **Logic Apps** 리소스에 대해 동일한 단계를 수행](logic-apps-securing-a-logic-app.md#regenerate-access-keys)합니다.

* 단일 테넌트 디자이너에서 다중 테넌트 디자이너와 동일한 단계를 수행하여 병렬 분기를 추가합니다.

자세한 내용은 [변경된 기능, 제한된 기능, 사용할 수 없는 기능 및 지원되지 않는 기능](#limited-unavailable-unsupported) 및 [GitHub의 Logic Apps 공개 미리 보기의 알려진 문제 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조하세요.

<a name="pricing-model"></a>

## <a name="pricing-model"></a>가격 책정 모델

Azure Portal에서 단일 테넌트 논리 앱 유형을 만들거나 Visual Studio Code에서 배포하는 경우 논리 앱에 사용할 호스팅 계획을 [App Service 또는 프리미엄](../azure-functions/functions-scale.md) 중에 선택해야 합니다. 이 요금제에 따라 논리 앱 실행에 적용되는 가격 책정 모델이 결정됩니다. App Service 요금제를 선택하는 경우 [가격 책정 계층](../app-service/overview-hosting-plans.md)도 선택해야 합니다.

*상태 저장* 워크플로는 [외부 스토리지](../azure-functions/storage-considerations.md#storage-account-requirements)를 사용하므로 Azure Logic Apps 미리 보기 런타임에서 수행하는 스토리지 트랜잭션에 [Azure Storage 가격](https://azure.microsoft.com/pricing/details/storage/)이 적용됩니다. 예를 들어 큐는 예약에 사용되고 테이블 및 Blob은 워크플로 상태를 저장하는 데 사용됩니다.

> [!NOTE]
> 공개 미리 보기로 제공되는 기간에는 App Service에서 논리 앱을 실행해도 선택한 요금제 외에는 *추가* 요금이 발생하지 않습니다.

단일 테넌트 리소스 종류에 적용되는 가격 책정 모델에 대한 자세한 내용은 다음 토픽을 검토하세요.

* [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)
* [Azure App Service에서 앱 강화](../app-service/manage-scale-up.md)
* [Azure Functions 가격 책정 정보](https://azure.microsoft.com/pricing/details/functions/)
* [App Service 가격 책정 정보](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage 가격 책정 정보](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>변경된 기능, 제한된 기능, 사용할 수 없는 기능 또는 지원되지 않는 기능

Azure Logic Apps 미리 보기에서는 이러한 기능이 변경되었거나, 현재 제한되었거나, 사용할 수 없거나, 지원되지 않습니다.

* **OS 지원**: 현재 Visual Studio Code의 디자이너는 Linux OS에서 작동하지 않지만, 여전히 Logic Apps 미리 보기 런타임을 사용하는 논리 앱을 Linux 기반 가상 머신에 배포할 수 있습니다. 지금은 Windows 또는 macOS의 Visual Studio Code에서 논리 앱을 빌드한 다음, Linux 기반 가상 머신에 배포할 수 있습니다.

* **트리거 및 작업**: 기본 제공 트리거 및 작업은 Azure Logic Apps 미리 보기 런타임에 기본적으로 실행되고, 관리형 커넥터는 Azure에 배포됩니다. 슬라이딩 윈도우 및 Batch와 같은 일부 기본 제공 트리거는 사용할 수 없습니다.

  워크플로를 시작하려면 [기본 제공 되풀이, 요청, HTTP, HTTP Webhook, Event Hubs 또는 Service Bus 트리거](../connectors/apis-list.md)를 사용합니다. 디자이너에서 기본 제공 트리거 및 작업은 **기본 제공** 탭에 표시되고, 관리형 커넥터 트리거 및 작업은 **Azure** 탭에 표시됩니다.

  > [!NOTE]
  > Visual Studio Code에서 로컬로 실행하려면 webhook 기반 트리거 및 작업에서 추가 설정이 필요합니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)를 참조하세요.

  * *상태 비저장 워크플로* 의 경우 [트리거가 아닌 관리형 커넥터 *작업*](../connectors/managed.md)만 선택할 수 있으므로 트리거를 선택해도 **Azure** 탭이 나타나지 않습니다. 상태 비저장 워크플로에 대해 Azure 배포 관리형 커넥터를 사용하도록 설정할 수 있지만, 사용자가 추가할 수 있는 관리형 커넥터 트리거가 디자이너에 표시되지 않습니다.

  * *상태 저장 워크플로* 의 경우 아래에 사용할 수 없는 것으로 나와 있는 트리거 및 작업을 제외한 [관리형 커넥터 트리거 및 작업](../connectors/managed.md)을 모두 사용할 수 있습니다.

  * 사용할 수 없는 트리거 및 작업은 변경되었거나, 현재 제한되었거나, 지원되지 않거나, 제공되지 않습니다.

    * [온-프레미스 데이터 게이트웨이 *트리거*](../connectors/managed.md#on-premises-connectors)는 사용할 수 없지만 게이트웨이 작업은 *사용할 수 있습니다*.

    * 기본 제공 작업 [Azure Functions - Azure 함수 선택](logic-apps-azure-functions.md)은 이제 **Azure Function 작업 - Azure 함수 호출** 입니다. 이 작업은 현재 **HTTP 트리거** 템플릿에서 만든 함수에 대해서만 작동합니다.

      Azure Portal에서 사용자 환경을 통해 연결을 만들어 액세스 권한이 있는 HTTP 트리거 함수를 선택할 수 있습니다. 코드 뷰 또는 **workflow.json** 파일에서 함수 작업의 JSON 정의를 살펴보면 작업이 `connectionName` 참조를 사용하여 함수를 참조한다는 것을 알 수 있습니다. 이 버전은 함수 정보를 연결로 추상화하며, 연결을 만든 후에 사용할 수 있는 프로젝트의 **connections.json** 파일에서 찾을 수 있습니다.

      > [!NOTE]
      > 단일 테넌트 버전에서 함수 작업은 쿼리 문자열 인증만 지원합니다. Azure Logic Apps 미리 보기는 연결을 만들 때 함수에서 기본 키를 가져오고, 해당 키를 앱의 설정에 저장하고, 함수를 호출할 때 이 키를 인증에 사용합니다.
      >
      > 다중 테넌트 버전과 마찬가지로 이 키를 갱신하는 경우(예: 포털의 Azure Functions 환경을 통해) 잘못된 키로 인해 함수 작업이 더 이상 작동하지 않습니다. 이 문제를 해결하려면 호출하려는 함수에 대한 연결을 다시 만들거나 앱의 설정을 새 키로 업데이트해야 합니다.

    * 기본 제공 작업 [인라인 코드 - JavaScript 코드 실행](logic-apps-add-run-inline-code.md)은 이제 **인라인 코드 작업 - 인라인 JavaScript 실행** 입니다.

      * 인라인 코드 작업에는 더 이상 통합 계정이 필요하지 않습니다.

      * macOS 및 Linux의 경우 이제 Visual Studio Code에서 Azure Logic Apps(미리 보기) 확장을 사용할 때 **인라인 코드 작업** 이 지원됩니다.

      * **인라인 코드 작업** 을 변경할 때 더 이상 논리 앱을 다시 시작할 필요가 없습니다.

      * **인라인 코드 작업** 에서는 [업데이트가 제한](logic-apps-overview-preview.md#inline-code-limits)됩니다.

    * [통합 계정에 대한 일부 기본 제공 B2B 트리거 및 작업](../connectors/managed.md#integration-account-connectors)은 사용할 수 없습니다(예: **플랫 파일** 인코딩 및 디코딩 작업).

    * 기본 제공 작업 [Azure Logic Apps - 논리 앱 워크플로 선택](logic-apps-http-endpoint.md)은 이제 **워크플로 작업 - 이 워크플로 앱의 워크플로 호출** 입니다.

* [사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors)는 현재 미리 보기를 지원하지 않습니다.

* **호스팅 계획 가용성**: Azure Portal에서 단일 테넌트 **논리 앱(미리 보기)** 리소스 종류를 만들거나 Visual Studio Code에서 배포할 때 Azure의 프리미엄 또는 App Service 호스팅 계획만 사용할 수 있습니다. 사용량 호스팅 계획은 사용할 수 없으며 이 리소스 종류를 배포할 수 없습니다. Visual Studio Code에서 Docker 컨테이너에 배포할 수 있지만 [ISE(통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에는 배포할 수 없습니다.

* **Visual Studio Code에서 중단점 디버깅**: 워크플로에 대한 **workflow.json** 파일 내에서 중단점을 추가하고 사용할 수 있지만, 현재는 트리거가 아닌 작업에만 중단점이 지원됩니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)를 참조하세요.

* **확대/축소 컨트롤**: 현재는 디자이너에서 확대/축소 컨트롤을 사용할 수 없습니다.

* **트리거 기록 및 실행 기록**: **논리 앱(미리 보기)** 리소스 종류의 경우 논리 앱 수준이 아닌 워크플로 수준에서 트리거 기록 및 실행 기록이 Azure Portal에 표시됩니다. 이 기록 데이터를 찾으려면 다음 단계를 수행합니다.

   * 실행 기록을 보려면 논리 앱에서 워크플로를 엽니다. 워크플로 메뉴의 **개발자** 에서 **모니터** 를 선택합니다.

   * 트리거 기록을 검토하려면 논리 앱에서 워크플로를 엽니다. 워크플로 메뉴의 **개발자** 에서 **트리거 기록** 을 선택합니다.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>엄격한 네트워크 및 방화벽 시나리오에서 트래픽 허용

현재 환경에 트래픽을 제한하는 엄격한 네트워크 요구 사항 또는 방화벽이 있는 경우 논리 앱 워크플로의 트리거 또는 작업 연결에 대한 액세스를 허용해야 합니다.

이러한 연결에 대한 FQDN(정규화된 도메인 이름)을 찾으려면 다음 토픽에서 해당 섹션을 검토하세요.

* [단일 테넌트 논리 앱에 대한 방화벽 권한 - Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [단일 테넌트 논리 앱에 대한 방화벽 권한 - Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>업데이트된 한도

Azure Logic Apps 미리 보기의 여러 한도가 [다중 테넌트 Azure Logic Apps의 한도](logic-apps-limits-and-config.md)와 동일하게 유지되지만, Azure Logic Apps 미리 보기에서는 이러한 한도가 변경되었습니다.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP 시간 제한

단일 인바운드 호출 또는 아웃바운드 호출의 경우 트리거와 작업의 시간 제한이 230초(3.9분)입니다.

* 아웃바운드 요청: HTTP 트리거, HTTP 작업
* 인바운드 요청: 요청 트리거, HTTP Webhook 트리거, HTTP Webhook 작업

반면, 논리 앱과 해당 워크플로가 실행되는 다른 환경에서는 트리거와 작업에 대해 다음과 같은 시간 제한이 적용됩니다.

* 다중 테넌트 Azure Logic Apps: 120초(2분)
* 통합 서비스 환경: 240초(4분)

자세한 내용은 [HTTP 한도](logic-apps-limits-and-config.md#http-limits)를 참조하세요.

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>관리되는 커넥터

관리형 커넥터는 1분에 연결당 50개 요청으로 제한됩니다. 커넥터 제한 문제를 해결하려면 [Azure Logic Apps의 제한 문제 처리(429 - "요청이 너무 많음" 오류)](handle-throttling-problems-429-errors.md#connector-throttling)를 참조하세요.

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>인라인 코드 작업(JavaScript 코드 실행)

단일 논리 앱 정의의 경우 인라인 코드 작업 [**JavaScript 코드 실행**](logic-apps-add-run-inline-code.md)의 한도가 다음과 같이 업데이트되었습니다.

* 최대 코드 문자 수가 1,024자에서 100,000자로 늘어납니다.

* 최대 코드 실행 기간이 5초에서 15초로 늘어납니다.

자세한 내용은 [논리 앱 정의 한도](logic-apps-limits-and-config.md#definition-limits)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-azure-portal.md)
* [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps 공개 미리 보기의 알려진 문제(GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Azure Logic Apps(미리 보기)에 대한 여러분의 의견을 듣고 싶습니다.

* 버그 또는 문제가 발견되는 경우 [GitHub에서 문제를 알려주세요](https://github.com/Azure/logicapps/issues).
* [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)하여 질문, 요청, 의견 및 기타 피드백을 보내주세요.
