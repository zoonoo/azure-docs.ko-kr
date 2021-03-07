---
title: Azure Logic Apps 미리 보기 개요
description: Azure Logic Apps 미리 보기는 엔터프라이즈 수준 시나리오에 대 한 최소한의 코드와 앱, 데이터, 서비스 및 시스템을 통합 하는 자동화 된 상태 저장 및 상태 비저장 워크플로를 빌드하기 위한 클라우드 솔루션입니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: ad059931d87603c957e446e82b894731dca984dd
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442743"
---
# <a name="overview-azure-logic-apps-preview"></a>개요: Azure Logic Apps 미리 보기

> [!IMPORTANT]
> 이 기능은 공개 미리 보기 상태이고 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Logic Apps 미리 보기를 사용 하면 새 **논리 앱 (미리 보기)** 리소스 유형을 사용 하 여 [ *상태 저장* 및 *상태 비저장* 워크플로](#stateful-stateless) 를 포함 하는 논리 앱을 만들고 실행 하 여 앱, 데이터, 클라우드 서비스 및 시스템 간에 자동화 및 통합 솔루션을 빌드할 수 있습니다. 이 새 논리 앱 유형을 사용 하 여 Azure 뿐만 아니라 Docker 컨테이너를 비롯 하 여 다양 한 호스팅 환경에서 이식성, 향상 된 성능 및 유연성을 제공 하는 다시 디자인 된 Azure Logic Apps 미리 보기 런타임으로 구동 되는 여러 워크플로를 빌드할 수 있습니다.

가능한 방법은 무엇 인가요? 다시 디자인 된 런타임은 [Azure Functions 확장성 모델](../azure-functions/functions-bindings-register.md) 을 사용 하 고 Azure Functions 런타임에서 확장으로 호스팅됩니다. 이 아키텍처는 Azure Functions 실행 되는 모든 위치에서 새 논리 앱 유형을 실행할 수 있음을 의미 합니다. 거의 모든 네트워크 토폴로지에서 다시 디자인 된 런타임을 호스트 하 고 사용 가능한 계산 크기를 선택 하 여 워크플로에 필요한 작업을 처리할 수 있습니다. 자세한 내용은 [Azure Functions 소개](../azure-functions/functions-overview.md) 및 [Azure Functions 트리거 및 바인딩](../azure-functions/functions-triggers-bindings.md)을 참조 하세요.

[Azure Portal에서 시작](create-stateful-stateless-workflows-azure-portal.md) 하거나 [Azure Logic Apps (미리 보기) 확장을 사용 하 여 Visual Studio Code에 프로젝트를 만들어](create-stateful-stateless-workflows-visual-studio-code.md) **논리 앱 (미리 보기)** 리소스를 만들 수 있습니다. 또한 Visual Studio Code에서는 개발 환경에서 워크플로를 빌드하고 *로컬로 실행할* 수 있습니다. 포털을 사용 하 든 Visual Studio Code 든, 동일한 종류의 호스팅 환경에서 새 논리 앱 유형을 배포 하 고 실행할 수 있습니다.

이 개요는 다음 영역을 다룹니다.

* [Azure Logic Apps 미리 보기, Azure Logic Apps 다중 테 넌 트 환경 및 통합 서비스 환경의 차이점](#preview-differences)

* [중첩 된 상태 저장 워크플로와](#nested-behavior)상태 비저장 워크플로 간의 동작 차이를 비롯 하 여 [상태 저장 워크플로와 상태 비저장 워크플로 간의 차이점](#stateful-stateless)입니다.

* [이 공개 미리 보기의 기능](#public-preview-contents)입니다.

* [가격 책정 모델의 작동 방식](#pricing-model)

* [변경 됨, 제한 됨, 사용할 수 없음 또는 지원 되지 않는 기능](#limited-unavailable-unsupported)입니다.

* [Azure Logic Apps 미리 보기의 제한](#limits)입니다.

자세한 내용은 다음 항목을 참조 하십시오.

* [어디에서 나 실행 중인 Azure Logic Apps-런타임 심층 살펴보기](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps 공개 미리 보기의 알려진 문제 (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Azure Logic Apps 미리 보기는 어떻게 다릅니까?

Azure Logic Apps 미리 보기 런타임은 [Azure Functions](../azure-functions/functions-overview.md) 확장성을 사용 하며 Azure Functions 런타임에서 확장으로 호스팅됩니다. 이 아키텍처는 Azure Functions 실행 되는 모든 위치에서 새 논리 앱 유형을 실행할 수 있음을 의미 합니다. 원하는 거의 모든 네트워크 토폴로지에서 Azure Logic Apps Preview 런타임을 호스트 하 고 사용 가능한 계산 크기를 선택 하 여 워크플로에 필요한 작업을 처리할 수 있습니다. Azure Functions 확장성에 대 한 자세한 내용은 [WEBJOBS SDK: 사용자 지정 입력 및 출력 바인딩 만들기](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)를 참조 하세요.

이 새로운 방법을 사용 하는 경우 Azure Logic Apps 미리 보기 런타임과 워크플로는 모두 함께 패키지할 수 있는 앱의 일부입니다. 이 기능을 사용 하면 간단히 아티팩트를 호스팅 환경에 복사 하 고 앱을 시작 하 여 워크플로를 배포 및 실행할 수 있습니다. 또한이 방법은 프로덕션 환경에 변경 내용을 배포 하기 전에 필요한 테스트 및 유효성 검사를 실행 하기 위해 워크플로 프로젝트에 대 한 배포 파이프라인을 구축 하는 보다 표준화 된 환경을 제공 합니다. 자세한 내용은 [어디서 나 실행 Azure Logic Apps](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)을 참조 하세요.

다음 표에서는 워크플로가 실행 되는 환경에 따라 리소스를 공유 하는 방식의 차이를 간략히 요약 하 여 보여 줍니다. 제한에 대 한 차이점은 [Azure Logic Apps 미리 보기의 제한](#limits)을 참조 하세요.

| 환경 | 리소스 공유 및 소비 |
|-------------|----------------------------------|
| Azure Logic Apps (다중 테 넌 트) | *여러 테 넌 트에 있는 고객의* 워크플로는 동일한 처리 (계산), 저장소, 네트워크 등을 공유 합니다. |
| Azure Logic Apps (미리 보기) | *동일한 논리 앱의* 워크플로는 동일한 처리 (계산), 저장소, 네트워크 등을 공유 합니다. |
| 통합 서비스 환경 (미리 보기에서 사용할 수 없음) | *동일한 환경의* 워크플로는 동일한 처리 (계산), 저장소, 네트워크 등을 공유 합니다. |
||||

한편 원래 Azure Logic Apps 확장을 사용 하 여 Azure Portal 및 Visual Studio Code에서 원래 논리 앱 유형을 만들 수 있습니다. 개발 환경은 원래 논리 앱과 새 논리 앱 유형에 따라 다르지만 Azure 구독에는 두 가지 유형이 모두 포함 될 수 있습니다. Azure 구독에서 배포 된 모든 논리 앱을 보고 액세스할 수 있지만 앱은 자신의 범주 및 섹션으로 구성 됩니다.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>상태 저장 및 상태 비저장 워크플로

* *상태 저장*

  이전 이벤트의 데이터를 유지, 검토 또는 참조 해야 할 때 상태 저장 워크플로를 만듭니다. 이러한 워크플로는 각 작업에 대 한 입력과 출력 및 해당 상태를 외부 저장소에 저장 하 여 각 실행이 완료 된 후 가능한 실행 정보 및 기록을 검토할 수 있게 합니다. 상태 저장 워크플로는 중단이 발생 하는 경우 높은 복원 력을 제공 합니다. 서비스 및 시스템이 복원 된 후에는 저장 된 상태에서 중단 된 실행을 다시 생성 하 고 워크플로를 다시 실행 하 여 완료할 수 있습니다. 상태 저장 워크플로는 최대 1 년 동안 계속 실행 될 수 있습니다.

* *상태 비저장*

  이후 검토를 위해 외부 저장소에서 이전 이벤트의 데이터를 저장, 검토 또는 참조할 필요가 없는 경우 상태 비저장 워크플로를 만듭니다. 이러한 워크플로는이 데이터를 외부 저장소로 전송 하는 대신 각 작업에 대 한 입력 및 출력과 상태를 *메모리에* 저장 합니다. 결과적으로 상태 비저장 워크플로의 실행은 일반적으로 5 분 보다 더 짧은 실행, 더 빠른 응답 시간으로 더 빠른 성능, 더 높은 처리량, 실행 비용 감소, 실행 세부 정보 및 기록이 외부 저장소에 유지 되지 않기 때문입니다. 그러나 중단이 발생 하면 중단 된 실행이 자동으로 복원 되지 않으므로 호출자는 중단 된 실행을 수동으로 다시 전송 해야 합니다. 이러한 워크플로는 동기적 으로만 실행할 수 있습니다.

  디버깅을 용이 하 게 하기 위해 성능에 약간의 영향을 주는 상태 비저장 워크플로에 대 한 실행 기록을 사용 하도록 설정 하 고 완료 되 면 실행 기록을 사용 하지 않도록 설정할 수 있습니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) 를 참조 하거나 [Azure Portal에서 상태 저장 및 상태 비저장 워크플로를 만듭니다](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless).

  > [!NOTE]
  > 상태 비저장 워크플로는 현재 Azure에 배포 되 고 트리거가 아닌 [관리 되는 커넥터](../connectors/apis-list.md#managed-api-connectors)에 대 한 *동작만* 지원 합니다. 워크플로를 시작 하려면 [기본 제공 요청, Event Hubs 또는 Service Bus 트리거](../connectors/apis-list.md#built-ins)중 하나를 선택 합니다. 이러한 트리거는 Azure Logic Apps 미리 보기 런타임에 기본적으로 실행 됩니다. 제한 되었거나 사용할 수 없거나 지원 되지 않는 트리거, 작업 및 커넥터에 대 한 자세한 내용은 [변경 됨, 제한 됨, 사용할 수 없음 또는 지원 되지 않는 기능](#limited-unavailable-unsupported)을 참조 하세요.

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>상태 저장 워크플로와 상태 비저장 워크플로 간의 중첩 된 동작 차이점

[요청 트리거](../connectors/connectors-native-reqres.md), [HTTP Webhook 트리거](../connectors/connectors-native-webhook.md)또는 [APICONNECTIONWEBHOOK 유형을](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) 포함 하 고 HTTPS 요청을 받을 수 있는 관리 되는 커넥터 트리거를 사용 하 여 동일한 **논리 앱 (미리 보기)** 리소스에 있는 다른 워크플로에서 호출할 수 있는 [워크플로를 만들](../logic-apps/logic-apps-http-endpoint.md) 수 있습니다.

부모 워크플로를 통해 자식 워크플로를 호출한 후에 중첩 된 워크플로가 따를 수 있는 동작 패턴은 다음과 같습니다.

* 비동기 폴링 패턴

  부모는 초기 호출에 대 한 응답을 기다리지 않고 자식의 실행이 완료 될 때까지 계속 해 서 자식의 실행 기록을 확인 합니다. 기본적으로 상태 저장 워크플로는이 패턴을 따릅니다 .이 패턴은 [요청 시간 제한 제한을](../logic-apps/logic-apps-limits-and-config.md)초과할 수 있는 장기 실행 자식 워크플로에 적합 합니다.

* 동기 패턴 ("발생 및 잊어버린")

  자식은 즉시 응답을 반환 하 여 호출을 승인 `202 ACCEPTED` 하 고 부모는 자식의 결과를 기다리지 않고 다음 작업을 계속 합니다. 대신, 부모는 자식의 실행이 완료 될 때 결과를 받습니다. 응답 동작을 포함 하지 않는 자식 상태 저장 워크플로는 항상 동기 패턴을 따릅니다. 자식 상태 저장 워크플로의 경우 실행 기록을 검토할 수 있습니다.

  이 동작을 사용 하도록 설정 하려면 워크플로의 JSON 정의에서 `operationOptions` 속성을로 설정 합니다 `DisableAsyncPattern` . 자세한 내용은 [트리거 및 작업 형식-작업 옵션](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)을 참조 하세요.

* 트리거 및 대기

  자식 상태 비저장 워크플로의 경우 부모는 자식에서 결과를 반환 하는 응답을 기다립니다. 이 패턴은 기본 제공 [HTTP 트리거 또는 작업](../connectors/connectors-native-http.md) 을 사용 하 여 자식 워크플로를 호출 하는 것과 유사 하 게 작동 합니다. 응답 작업을 포함 하지 않는 자식 상태 비저장 워크플로는 즉시 `202 ACCEPTED` 응답을 반환 하지만, 부모는 자식이 완료 될 때까지 대기한 다음 작업을 계속 합니다. 이러한 동작은 자식 상태 비저장 워크플로에만 적용 됩니다.

이 테이블은 부모 및 자식이 상태 저장 인지, 상태 비저장 인지 또는 혼합 워크플로 유형 인지에 따라 자식 워크플로의 동작을 지정 합니다.

| 부모 워크플로 | 하위 워크플로 | 자식 동작 |
|-----------------|----------------|----------------|
| 상태 저장 | 상태 저장 | 설정으로 비동기 또는 동기 `"operationOptions": "DisableAsyncPattern"` |
| 상태 저장 | 상태 비저장 | 트리거 및 대기 |
| 상태 비저장 | 상태 저장 | 동기 |
| 상태 비저장 | 상태 비저장 | 트리거 및 대기 |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>기능

Azure Logic Apps 미리 보기에는 다음과 같은 많은 최신 기능과 추가 기능이 포함 되어 있습니다.

* SaaS (Software as a Service) 및 PaaS (Platform as a Service) 앱 및 서비스에 대해 [400 개 이상의 커넥터](/connectors/connector-reference/connector-reference-logicapps-connectors) 에서 논리 앱 및 해당 워크플로를 만들고 온-프레미스 시스템을 위한 커넥터를 만듭니다.

  * Azure Service Bus, Azure Event Hubs, SQL Server, MQ 등의 일부 관리 되는 커넥터는 기본 제공 트리거 및 Azure Logic Apps Preview 런타임에 기본적으로 제공 되는 작업 (예: 요청 트리거 및 HTTP 작업)과 유사 하 게 실행 됩니다.

  * [Preview 릴리스의 확장성 프레임 워크](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)를 사용 하 여 필요한 모든 서비스에 대 한 기본 제공 커넥터를 만듭니다. Azure Service Bus 및 SQL Server와 같이 기본 제공 되는 커넥터와 마찬가지로, 현재 미리 보기에 대해 지원 되지 않는 [사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors) 와 마찬가지로, 이러한 커넥터는 더 높은 처리량, 짧은 대기 시간, 로컬 연결을 제공 하 고 미리 보기 런타임과 동일한 프로세스에서 기본적으로 실행 됩니다.

    제작 기능은 현재 Visual Studio Code 에서만 사용할 수 있지만 기본적으로 사용 하도록 설정 되어 있지 않습니다. 이러한 커넥터를 만들려면 [확장 번들 기반 (Node.js)에서 NuGet 패키지 기반 (.net)으로 프로젝트를 전환](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring)합니다. 자세한 내용은 Anywhere를 실행 하는 [Azure Logic Apps 기본 제공 커넥터 확장성](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)을 참조 하세요.

  * 통합 계정 없이 액체 작업 및 XML 작업에 B2B 작업을 사용할 수 있습니다. 이러한 작업을 사용 하려면 Azure Portal의 각 작업을 통해 업로드 하거나 각 **맵** 및 **스키마** 폴더를 사용 하 여 Visual Studio Code 프로젝트의 **아티팩트** 폴더에 추가할 수 있는 액체 맵, xml 맵 또는 xml 스키마가 있어야 합니다.

  * Azure Logic Apps 서비스가 클라우드 연결 런타임 끝점으로 요청을 보내는 데 사용할 수 있는 SAS (공유 액세스 서명) 연결 문자열을 생성 하기 때문에 어디서 나 실행할 수 있는 논리 앱을 만들고 배포 합니다. Logic Apps 서비스는 Azure에 배포할 때 이러한 값을 Azure Key Vault에 쉽게 저장할 수 있도록 이러한 연결 문자열을 다른 응용 프로그램 설정과 함께 저장 합니다.

    > [!NOTE]
    > 기본적으로 **논리 앱 (미리 보기)** 리소스에는 런타임에 연결을 인증 하기 위해 자동으로 사용 하도록 설정 된 [시스템 할당 관리 id](../logic-apps/create-managed-service-identity.md) 가 있습니다. 이 id는 연결을 만들 때 사용 하는 인증 자격 증명 또는 연결 문자열과 다릅니다. 이 id를 사용 하지 않도록 설정 하면 런타임에 연결이 작동 하지 않습니다. 이 설정을 보려면 논리 앱의 메뉴에 있는 **설정** 에서 **id** 를 선택 합니다.

* 메모리 에서만 실행 되는 상태 비저장 워크플로를 사용 하 여 논리 앱을 만듭니다 .이 워크플로는 더 빠르게 완료 하 고, 더 빠르게 응답 하 고, 처리량을 높일 수 있을 뿐 아니라, 작업 간 실행 기록과 데이터가 외부 저장소에 유지 되지 않으므로 실행 비용이 줄어듭니다. 필요에 따라 디버깅을 용이 하 게 하기 위해 실행 기록을 사용 하도록 설정할 수 있습니다. 자세한 내용은 [상태 저장 및 상태 비저장 논리 앱](#stateful-stateless)을 참조 하세요.

* Visual Studio Code 개발 환경에서 논리 앱 및 해당 워크플로를 로컬로 실행, 테스트 및 디버그 합니다.

  논리 앱을 실행 하 고 테스트 하기 전에 워크플로에 대 한 파일 **의workflow.js** 내에서 중단점을 추가 하 고 사용 하 여 디버깅을 더 쉽게 수행할 수 있습니다. 그러나 중단점은 트리거가 아니라 현재 작업에 대해서만 지원 됩니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)를 참조 하세요.

* Azure 및 [Docker 컨테이너](/dotnet/core/docker/introduction)와 같은 다양 한 호스팅 환경에 Visual Studio Code에서 논리 앱 및 해당 워크플로를 직접 게시 하거나 배포 합니다.

* Azure 구독 및 논리 앱 설정에서 지원 되는 경우 [Application Insights](../azure-monitor/app/app-insights-overview.md) 를 사용 하 여 논리 앱에 대 한 진단 로깅 및 추적 기능을 사용 하도록 설정 합니다.

* [Azure Functions Premium 요금제](../azure-functions/functions-premium-plan.md)를 사용 하 여 논리 앱을 만들고 배포 하는 경우와 마찬가지로, Azure 가상 네트워크와의 연결 및 개인 연결과 같은 네트워킹 기능에 액세스 하는 것이 Azure Functions. 자세한 내용은 다음 항목을 검토 하십시오.

  * [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps 미리 보기를 사용 하 여 어디서 나 Azure Logic Apps 실행-네트워킹 가능성](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* **논리 앱 (미리 보기)** 리소스의 개별 워크플로에서 사용 하는 관리 되는 연결에 대 한 액세스 키를 다시 생성 합니다. 이 작업의 경우에는 논리 앱 리소스 수준이 아닌 [개별 워크플로 수준에서 **Logic Apps** 리소스에 대해 동일한 단계를 수행](logic-apps-securing-a-logic-app.md#regenerate-access-keys)합니다.

* 미리 보기가 아닌 디자이너와 동일한 단계를 수행 하 여 새 디자이너에서 병렬 분기를 추가 합니다.

자세한 내용은 GitHub의 [변경 됨, 제한 됨, 사용할 수 없음 및 지원 되지 않는 기능](#limited-unavailable-unsupported) 및 [Logic Apps 공개 미리 보기 알려진 문제 페이지](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)를 참조 하세요.

<a name="pricing-model"></a>

## <a name="pricing-model"></a>가격 책정 모델

Azure Portal에서 새 논리 앱 유형을 만들거나 Visual Studio Code에서 배포 하는 경우 논리 앱에서 사용할 호스팅 계획 ( [App Service 또는 프리미엄](../azure-functions/functions-scale.md))을 선택 해야 합니다. 이 계획은 논리 앱 실행에 적용 되는 가격 책정 모델을 결정 합니다. App Service 계획을 선택 하는 경우 [가격 책정 계층](../app-service/overview-hosting-plans.md)도 선택 해야 합니다.

*상태 저장* 워크플로는 [외부 저장소](../azure-functions/storage-considerations.md#storage-account-requirements)를 사용 하므로 Azure Logic Apps 미리 보기 런타임에서 수행 하는 저장소 트랜잭션에 [Azure Storage 가격이](https://azure.microsoft.com/pricing/details/storage/) 적용 됩니다. 예를 들어 큐는 일정에 사용 되는 반면 테이블 및 blob은 워크플로 상태를 저장 하는 데 사용 됩니다.

> [!NOTE]
> 공개 미리 보기 중에 App Service에서 논리 앱을 실행 하면 선택한 계획 위에 *추가* 요금이 발생 하지 않습니다.

이 새 리소스 유형에 적용 되는 가격 책정 모델에 대 한 자세한 내용은 다음 항목을 검토 하세요.

* [Azure Functions 비율 크기 조정 및 호스팅](../azure-functions/functions-scale.md)
* [Azure App Service에서 앱 강화](../app-service/manage-scale-up.md)
* [Azure Functions 가격 정보](https://azure.microsoft.com/pricing/details/functions/)
* [App Service 가격 정보](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage 가격 정보](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>변경 됨, 제한 됨, 사용할 수 없음 또는 지원 되지 않는 기능

Azure Logic Apps 미리 보기에서는 이러한 기능이 변경 되었거나 현재 제한 되었거나 사용할 수 없거나 지원 되지 않습니다.

* **OS 지원**: 현재 Visual Studio Code의 디자이너는 linux OS에서 작동 하지 않지만 Logic Apps Preview runtime을 사용 하는 논리 앱을 linux 기반 가상 머신에 계속 배포할 수 있습니다. 지금은 Windows 또는 macOS에서 Visual Studio Code 하 여 논리 앱을 빌드한 다음 Linux 기반 가상 머신에 배포할 수 있습니다.

* **트리거 및 작업**: 기본 제공 트리거 및 작업은 Azure Logic Apps 미리 보기 런타임에 기본적으로 실행 되 고, 관리 되는 커넥터는 Azure에 배포 됩니다. 슬라이딩 윈도우 및 일괄 처리와 같은 일부 기본 제공 트리거를 사용할 수 없습니다.

  워크플로를 시작 하려면 [기본 제공 되는 되풀이, 요청, http, Http Webhook, Event Hubs 또는 Service Bus 트리거](../connectors/apis-list.md)를 사용 합니다. 디자이너에서 기본 제공 트리거 및 작업은 **기본 제공** 탭 아래에 표시 되 고, 관리 되는 커넥터 트리거 및 작업은 **Azure** 탭 아래에 나타납니다.

  > [!NOTE]
  > Visual Studio Code에서 로컬로 실행 하려면 웹 후크 기반 트리거 및 작업에 추가 설정이 필요 합니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)를 참조 하세요.

  * *상태 비저장 워크플로의* 경우 트리거가 [아니라 관리 되는 커넥터](../connectors/apis-list.md#managed-api-connectors)작업만 선택할 수 있으므로 트리거를 선택 하면 **Azure** 탭이 나타나지 않습니다. 상태 비저장 워크플로에 대해 Azure에서 배포 된 관리 되는 커넥터를 사용 하도록 설정할 수 있지만, 사용자가 추가할 수 있는 관리 되는 커넥터 트리거가 디자이너에 표시 되지 않습니다.

  * 아래에서 사용할 수 없는 것으로 표시 된 트리거 및 작업 외에도 *상태 저장 워크플로의* 경우에 [는 관리 되는 커넥터 트리거와 작업](../connectors/apis-list.md#managed-api-connectors) 을 모두 사용할 수 있습니다.

  * 이러한 트리거와 동작은 변경 되었거나 현재 제한 되었거나 지원 되지 않거나 사용할 수 없습니다.

    * [온-프레미스 데이터 게이트웨이 *트리거*](../connectors/apis-list.md#on-premises-connectors) *를 사용할 수 없지만 게이트웨이 작업을* 사용할 수 있습니다.

    * 기본적으로 제공 되는 작업 [Azure Functions](logic-apps-azure-functions.md) azure Function을 선택 하는 Azure function **작업-Azure 함수를 호출** 합니다. 이 작업은 현재 **HTTP 트리거** 템플릿에서 만든 함수에 대해서만 작동 합니다.

      Azure Portal에서 사용자 환경을 통해 연결을 만들어 액세스 권한이 있는 HTTP 트리거 함수를 선택할 수 있습니다. 코드 뷰나 파일 **의workflow.js** 에서 함수 작업의 JSON 정의를 검사 하는 경우 작업은 참조를 사용 하 여 함수를 참조 합니다 `connectionName` . 이 버전은 연결을 만든 후에 사용할 수 있는 프로젝트의 **connections.js** 파일에서 찾을 수 있는 연결로 함수 정보를 추상화 합니다.

      > [!NOTE]
      > 미리 보기 버전에서 함수 동작은 쿼리 문자열 인증만 지원 합니다. Azure Logic Apps 미리 보기는 연결을 만들 때 함수에서 기본 키를 가져오고, 해당 키를 앱의 설정에 저장 하 고, 함수를 호출할 때 인증에 키를 사용 합니다.
      >
      > 원래 버전과 마찬가지로이 키를 갱신 하는 경우 (예: 포털의 Azure Functions 경험을 통해) 잘못 된 키로 인해 함수 작업이 더 이상 작동 하지 않습니다. 이 문제를 해결 하려면 호출 하려는 함수에 대 한 연결을 다시 만들거나 새 키로 앱의 설정을 업데이트 해야 합니다.

    * 기본 제공 작업 인 [인라인 코드 실행 Javascript 코드](logic-apps-add-run-inline-code.md) 는 이제 **인라인 코드 작업-인라인 javascript를 실행** 합니다.

      * 인라인 코드 작업 작업에는 더 이상 통합 계정이 필요 하지 않습니다.

      * MacOS 및 Linux의 경우 이제 Visual Studio Code에서 Azure Logic Apps (미리 보기) 확장을 사용 하는 경우 **인라인 코드 작업이** 지원 됩니다.

      * **인라인 코드 작업** 작업을 변경 하는 경우 더 이상 논리 앱을 다시 시작할 필요가 없습니다.

      * **인라인 코드 작업** 작업은 [제한을 업데이트](logic-apps-overview-preview.md#inline-code-limits)했습니다.

    * [통합 계정에 대 한 일부 기본 제공 B2B 트리거 및 작업](../connectors/apis-list.md#integration-account-connectors) 은 사용할 수 없습니다 (예: **플랫 파일** 인코딩 및 디코딩 작업).

    * 기본 제공 작업 인 [Azure Logic Apps-논리 앱 워크플로 선택](logic-apps-http-endpoint.md) 은 이제 워크플로 **작업-이 워크플로 앱에서 워크플로를 호출** 합니다.

* [사용자 지정 커넥터](../connectors/apis-list.md#custom-apis-and-connectors) 는 현재 미리 보기를 지원 하지 않습니다.

* **호스팅 계획 가용성**: Azure Portal에서 새 **논리 앱 (미리 보기)** 리소스 유형을 만들거나 Visual Studio Code에서 배포 하는 경우에는 Azure에서 Premium 또는 App Service 호스팅 계획만 사용할 수 있습니다. 소비 호스팅 계획을 사용할 수 없으며이 리소스 유형의 배포에 대해 지원 되지 않습니다. Visual Studio Code에서 Docker 컨테이너로 배포할 수 있지만 [ISE (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에는 배포할 수 없습니다.

* **Visual Studio Code의 중단점 디버깅**: 워크플로에 대 한 파일 **의workflow.js** 내에서 중단점을 추가 하 고 사용할 수 있지만, 중단점은 트리거가 아니라 현재 작업에 대해서만 지원 됩니다. 자세한 내용은 [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)를 참조 하세요.

* **확대/축소 컨트롤**: 현재 디자이너에서 확대/축소 컨트롤을 사용할 수 없습니다.

* **트리거 기록 및 실행 기록**: **논리 앱 (미리 보기)** 리소스 종류의 경우 논리 앱 수준이 아닌 워크플로 수준에 표시 되는 Azure Portal의 트리거 기록과 실행 기록이 표시 됩니다. 이 기록 데이터를 찾으려면 다음 단계를 수행 합니다.

   * 실행 기록을 보려면 논리 앱에서 워크플로를 엽니다. 워크플로 메뉴의 **개발자** 에서 **모니터** 를 선택 합니다.

   * 트리거 기록을 검토 하려면 논리 앱에서 워크플로를 엽니다. 워크플로 메뉴의 **개발자** 에서 **트리거 기록** 을 선택 합니다.

<a name="limits"></a>

## <a name="updated-limits"></a>업데이트 된 제한

Azure Logic Apps 미리 보기에 대 한 많은 제한이 [다중 테 넌 트 Azure Logic Apps에 대 한 제한과](logic-apps-limits-and-config.md)동일 하 게 유지 되지만, Azure Logic Apps 미리 보기에 대해서는 이러한 제한이 변경 되었습니다.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP 시간 제한

단일 인바운드 호출 또는 아웃 바운드 호출의 경우 이러한 트리거와 작업에 대해 제한 시간 제한이 230 초 (3.9 분)입니다.

* 아웃 바운드 요청: HTTP 트리거, HTTP 작업
* 인바운드 요청: 요청 트리거, HTTP Webhook 트리거, HTTP Webhook 작업

반면, 논리 앱과 해당 워크플로가 실행 되는 다른 환경에서는 이러한 트리거와 작업에 대 한 제한 시간 제한이 다음과 같이 적용 됩니다.

* 다중 테 넌 트 Azure Logic Apps: 120 초 (2 분)
* 통합 서비스 환경: 240 초 (4 분)

자세한 내용은 [HTTP 제한](logic-apps-limits-and-config.md#http-limits)을 참조 하세요.

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>관리되는 커넥터

관리 커넥터는 연결당 분당 50 요청으로 제한 됩니다. 커넥터 제한 문제를 해결 하려면 [Azure Logic Apps에서 제한 문제 처리 (429-"요청이 너무 많음" 오류)](handle-throttling-problems-429-errors.md#connector-throttling)를 참조 하세요.

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>인라인 코드 작업 (JavaScript 코드 실행)

단일 논리 앱 정의의 경우 [**JavaScript 코드를 실행**](logic-apps-add-run-inline-code.md)하는 인라인 코드 작업 작업에는 다음과 같은 업데이트 된 제한이 있습니다.

* 최대 코드 문자 수는 1024 자에서 10만 자로 늘어납니다.

* 실행 되는 코드의 최대 기간은 5 초에서 15 초로 늘어납니다.

자세한 내용은 [논리 앱 정의 제한](logic-apps-limits-and-config.md#definition-limits)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-azure-portal.md)
* [Visual Studio Code에서 상태 저장 및 상태 비저장 워크플로 만들기](create-stateful-stateless-workflows-visual-studio-code.md)
* [GitHub의 공개 미리 보기 알려진 문제 페이지 Logic Apps](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

또한 Azure Logic Apps 미리 보기로 제공 되는 경험에 대 한 의견을 듣고 싶습니다.

* 버그 또는 문제의 경우 [GitHub에서 문제를 만듭니다](https://github.com/Azure/logicapps/issues).
* 질문, 요청, 의견 및 기타 피드백은 [이 사용자 의견 양식을 사용](https://aka.ms/lafeedback)합니다.
