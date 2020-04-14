---
title: 제한 문제 처리 또는 '429 - 너무 많은 요청' 오류
description: Azure Logic Apps에서 제한 문제 또는 'HTTP 429 너무 많은 요청' 오류를 해결하는 방법
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272681"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Azure 논리 앱에서 제한 문제 처리(429 - "너무 많은 요청" 오류)

[Azure Logic Apps에서](../logic-apps/logic-apps-overview.md)논리 앱은 요청 수가 특정 시간 동안 처리할 수 있는 속도를 초과할 때 발생하는 제한을 경험할 때 ["HTTP 429 너무 많은 요청" 오류를](https://developer.mozilla.org/docs/Web/HTTP/Status/429) 반환합니다. 제한은 지연된 데이터 처리, 성능 속도 저하 및 지정된 재시도 정책을 초과하는 오류와 같은 문제를 일으킬 수 있습니다.

![SQL Server 커넥터의 제한](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

다음은 논리 앱에서 발생할 수 있는 몇 가지 일반적인 제한 유형입니다.

* [논리 앱](#logic-app-throttling)
* [커넥터](#connector-throttling)
* [대상 서비스 또는 시스템](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>논리 앱 제한

Azure 논리 앱 서비스에는 자체 [처리량 제한이](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)있습니다. 따라서 논리 앱이 이러한 제한을 초과하면 논리 앱 리소스가 특정 인스턴스 나 실행뿐만 아니라 제한됩니다.

이 수준에서 제한 이벤트를 찾으려면 Azure 포털에서 논리 앱의 **메트릭** 창을 확인합니다.

1. Azure [포털에서](https://portal.azure.com)논리 앱 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴에서 **모니터링**에서 **메트릭을 선택합니다.**

1. **차트 제목**에서 **메트릭 추가를** 선택하여 기존 메트릭에 다른 메트릭을 추가합니다.

1. 첫 번째 메트릭 표시줄에서 **메트릭** 목록에서 **작업 제한 이벤트를**선택합니다. 두 번째 메트릭 표시줄에서 **METRIC** 목록에서 **트리거 제한 이벤트를**선택합니다.

이 수준에서 제한을 처리하려면 다음 옵션이 있습니다.

* 동시에 실행할 수 있는 논리 앱 인스턴스 수를 제한합니다.

  기본적으로 논리 앱의 트리거 조건이 동시에 두 번 이상 충족되는 경우 논리 앱에 대한 여러 트리거 인스턴스가 동시에 또는 *병렬로*실행됩니다. 이 동작은 이전 워크플로 인스턴스 실행이 완료되기 전에 각 트리거 인스턴스가 실행된다는 것을 의미합니다.

  동시에 실행할 수 있는 트리거 인스턴스의 기본 수는 [무제한이지만](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits) [트리거의 동시성 설정을 켜서](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)이 수를 제한하고 필요한 경우 기본값 이외의 제한을 선택할 수 있습니다.

* 높은 처리량 모드를 활성화합니다.

  논리 앱에는 [5분 롤링 간격을 통해 실행할 수 있는 작업 수에 대한 기본 제한이 있습니다.](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) 이 제한을 최대 작업 수로 높이려면 논리 앱에서 [높은 처리량 모드를](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) 켭니다.

* 트리거에서 배열 디배치("분할") 동작을 사용하지 않도록 설정합니다.

  트리거가 처리할 나머지 워크플로 작업에 대한 배열을 반환하는 경우 트리거의 [ **Split On** 설정은](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) 배열 항목을 분할하고 각 배열 항목에 대한 워크플로 인스턴스를 시작하여 [ **분할 온** 한계까지](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)여러 동시 실행을 효과적으로 트리거합니다. 제한을 제어하려면 **Split On** 동작을 끄고 논리 앱에서 호출당 단일 항목을 처리하는 대신 단일 호출로 전체 배열을 처리하도록 합니다.

* 작업을 더 작은 논리 앱으로 리팩터링합니다.

  앞에서 설명한 것처럼 논리 앱은 [5분 동안 실행할 수 있는 기본 작업 수로](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)제한됩니다. [높은 처리량 모드를](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)사용하도록 설정하여 이 제한을 늘릴 수 있지만 각 논리 앱에서 실행되는 작업 수가 제한 아래로 유지되도록 논리 앱의 작업을 더 작은 논리 앱으로 세분화할지 여부를 고려할 수도 있습니다. 이렇게 하면 단일 논리 앱 리소스에 대한 부담을 줄이고 여러 논리 앱에 부하를 분산할 수 있습니다. 이 솔루션은 큰 데이터 집합을 처리하거나 동시에 실행 중인 작업, 루프 반복 또는 작업 실행 제한을 초과하는 각 루프 반복 내의 작업을 너무 많이 스핀업하는 작업에 더 적합합니다.

  예를 들어 이 논리 앱은 SQL Server 데이터베이스에서 테이블을 얻기 위해 모든 작업을 수행하고 각 테이블에서 행을 가져옵니다. **For 각** 루프는 각 테이블을 동시에 반복하므로 **행 받기** 작업이 각 테이블에 대한 행을 반환합니다. 이러한 테이블의 데이터 양에 따라 이러한 작업은 작업 실행 제한을 초과할 수 있습니다.

  ![논리 앱 "이전" 리팩터링](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  리팩터링 후 논리 앱은 이제 부모 및 자식 논리 앱이 됩니다. 부모는 SQL Server에서 테이블을 가져옵니다 다음 행을 가져옵니다 각 테이블에 대 한 자식 논리 응용 프로그램을 호출 합니다.

  ![하나의 작업에 대한 논리 앱 만들기](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  다음은 각 테이블에 대한 행을 얻기 위해 부모 논리 앱에서 호출되는 자식 논리 앱입니다.

  ![두 번째 작업에 대한 다른 논리 앱 만들기](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>커넥터 스로틀링

각 커넥터에는 고유한 제한 제한이 있으며 커넥터의 기술 참조 페이지에서 찾을 수 있습니다. 예를 들어 [Azure Service Bus 커넥터에는](https://docs.microsoft.com/connectors/servicebus/) 분당 최대 6,000개의 호출을 허용하는 제한 제한이 있는 반면 SQL Server 커넥터에는 [작업 유형에 따라 달라지는 제한 제한이](https://docs.microsoft.com/connectors/sql/)있습니다.

HTTP와 같은 일부 트리거 및 작업에는 예외 처리를 구현하기 위해 [재시도 정책 제한에](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) 따라 사용자 지정할 수 있는 ["재시도 정책"이](../logic-apps/logic-apps-exception-handling.md#retry-policies) 있습니다. 이 정책은 원래 요청이 실패하거나 시간이 부족하여 408, 429 또는 5xx 응답이 발생할 때 트리거 또는 작업이 요청을 다시 시도할지 여부 또는 빈도를 지정합니다. 따라서 제한이 시작되고 429 오류를 반환하면 Logic Apps가 지원되는 재시도 정책을 따릅니다.

트리거 또는 작업이 다시 시도 정책을 지원하는지 여부를 알아보려면 트리거 또는 작업의 설정을 확인합니다. 트리거 또는 작업의 재시도 시도를 보려면 논리 앱의 실행 기록으로 이동하여 검토할 실행을 선택하고 해당 트리거 또는 작업을 확장하여 입력, 출력 및 재시도에 대한 세부 정보를 봅니다.

![작업의 실행 기록, 재시도, 입력 및 출력 보기](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

재시도 기록은 오류 정보를 제공하지만 커넥터 제한과 [대상 제한을](#destination-throttling)구분하는 데 문제가 있을 수 있습니다. 이 경우 응답의 세부 정보를 검토하거나 일부 제한 간격 계산을 수행하여 소스를 식별해야 할 수 있습니다.

글로벌 다중 테넌트 Azure Logic Apps 서비스의 논리 앱의 경우 *연결* 수준에서 제한이 수행됩니다. 예를 들어 [통합 서비스 환경(ISE)에서](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)실행되는 논리 앱의 경우 전역 다중 테넌트 논리 앱 서비스에서 실행되므로 ISE가 아닌 연결에 대해 제한이 계속 발생합니다. 그러나 ISE 커넥터에 의해 생성되는 ISE 연결은 ISE에서 실행되기 때문에 제한되지 않습니다.

이 수준에서 제한을 처리하려면 다음 옵션이 있습니다.

* 논리 앱이 처리를 위해 데이터를 분할하도록 단일 작업에 대해 여러 연결을 설정합니다.

  이 옵션의 경우 동일한 자격 증명을 사용하여 여러 연결에서 동일한 대상에 대한 작업의 요청을 분할하여 워크로드를 배포할 수 있는지 여부를 고려합니다.

  예를 들어 논리 앱이 SQL Server 데이터베이스에서 테이블을 가져옵니다가 각 테이블에서 행을 가져옵니다. 처리해야 하는 행 수에 따라 여러 연결과 각 **루프에** 대해 여러 개의 행수를 더 작은 집합으로 나누어 처리할 수 있습니다. 이 시나리오에서는 두 개의 **각** 루프를 사용하여 총 행 수를 반으로 분할합니다. 첫 번째 **For 각** 루프는 첫 번째 절반을 얻는 식을 사용합니다. 다른 **각** 루프는 예를 들어 후반을 얻는 다른 식을 사용합니다.<p>

    * 표현식 1: 함수는 `take()` 컬렉션의 전면을 가져옵니다. 자세한 내용은 [ **`take()`** 기능을](workflow-definition-language-functions-reference.md#take)참조하십시오.

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 표현식 2: 함수는 `skip()` 컬렉션의 전면을 제거하고 다른 모든 항목을 반환합니다. 자세한 내용은 [ **`skip()`** 기능을](workflow-definition-language-functions-reference.md#skip)참조하십시오.

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    다음은 이러한 식을 사용하는 방법을 보여 주는 시각적 예제입니다.

    ![단일 작업에 대해 여러 연결 생성 및 사용](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 각 작업에 대해 다른 연결을 설정합니다.

  이 옵션의 경우 작업이 동일한 서비스 또는 시스템에 연결되고 동일한 자격 증명을 사용하는 경우에도 각 작업의 요청을 자신의 연결을 통해 분산하여 워크로드를 배포할 수 있는지 여부를 고려합니다.

  예를 들어 논리 앱이 SQL Server 데이터베이스에서 테이블을 받고 각 테이블의 각 행을 가져옵니다. 테이블 가져오는 것은 하나의 연결을 사용하고 각 행은 다른 연결을 사용하도록 별도의 연결을 사용할 수 있습니다.

  ![각 작업에 대해 다른 연결 만들기 및 사용](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* "각 루프에 [대해" 루프에서](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)동시성 또는 병렬 처리변경을 변경합니다.

  기본적으로 "For each" 루프 반복은 [동시성 제한까지](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)동시에 실행됩니다. "For each" 루프 내에서 제한되는 커넥터가 있는 경우 병렬로 실행되는 루프 반복 횟수를 줄일 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.
  
  * ["각" 루프 - 동시성을 변경하거나 순차적으로 실행](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [워크플로 정의 언어 스키마 - 각 루프에 대해](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [워크플로 정의 언어 스키마 - "각" 루프 동시성 변경](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [워크플로 정의 언어 스키마 - "각 에 대해" 루프를 순차적으로 실행합니다.](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>대상 서비스 또는 시스템 제한

커넥터에는 자체 제한 제한이 있지만 커넥터에서 호출하는 대상 서비스 또는 시스템에도 제한 제한이 있을 수 있습니다. 예를 들어 Microsoft Exchange 서버의 일부 API는 Office 365 Outlook 커넥터보다 더 엄격한 제한 제한을 갖습니다.

기본적으로 논리 앱의 인스턴스와 해당 인스턴스 내의 모든 루프 또는 분기는 *병렬로*실행됩니다. 이 동작은 여러 인스턴스가 동시에 동일한 끝점을 호출할 수 있음을 의미합니다. 각 인스턴스는 상대방의 존재를 알지 못하므로 실패한 작업을 다시 시도하면 여러 호출이 동시에 실행되려는 [경합 조건을](https://en.wikipedia.org/wiki/Race_condition) 만들 수 있지만 성공하려면 제한이 발생하기 전에 해당 호출이 대상 서비스 또는 시스템에 도착해야 합니다.

예를 들어 100개의 항목이 있는 배열이 있다고 가정합니다. "for each" 루프를 사용하여 배열을 반복하고 루프의 동시성 컨트롤을 켜서 병렬 반복 횟수를 20 또는 [현재 기본 제한으로](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)제한할 수 있습니다. 이 루프 내에서 작업은 배열의 항목을 SQL Server 데이터베이스에 삽입하여 초당 15개의 호출만 허용합니다. 이 시나리오에서는 다시 시도의 백로그가 누적되어 실행되지 않으므로 제한 문제가 발생합니다.

이 표에서는 작업의 재시도 간격이 1초일 때 루프에서 발생하는 작업에 대한 타임라인을 설명합니다.

| 지정 시간 | 실행되는 작업 수 | 실패한 작업 수 | 대기 중이도 인 재시도 횟수 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 초 | 인서트가 20개 | SQL 제한으로 인해 5실패 | 5번의 재시도 |
| T + 0.5 초 | 15 인서츠, 이전 5 번의 재시도 대기 로 인해 | 이전 SQL 제한으로 인해 15개 모두 실패하여 다른 0.5초 동안 여전히 적용됩니다. | 20회 재시도 <br>(이전 5 + 15 새로운) |
| T + 1초 | 인서트가 20개 | SQL 제한으로 인해 5번 실패하고 이전 20번의 재시도 | 25번의 재시도(이전 20회 + 신규 5회)
|||||

이 수준에서 제한을 처리하려면 다음 옵션이 있습니다.

* 각 작업이 단일 작업을 처리되도록 논리 앱을 만듭니다.

  * 이 섹션의 예제 SQL Server 시나리오를 계속 계속하면 [Azure Service Bus 큐와](../connectors/connectors-create-api-servicebus.md)같은 배열 항목을 큐에 넣는 논리 앱을 만들 수 있습니다. 그런 다음 해당 큐의 각 항목에 대한 삽입 작업만 수행하는 다른 논리 앱을 만듭니다. 이렇게 하면 삽입 작업을 완료하고 큐의 다음 항목으로 이동하거나 인스턴스에 429 오류가 있지만 비생산적인 재시도를 시도하지 않는 특정 시간에 하나의 논리 앱 인스턴스만 실행됩니다.

  * 각 작업에 대해 자식 또는 중첩논리 앱을 호출하는 부모 논리 앱을 만듭니다. 부모가 부모의 결과에 따라 다른 자식 앱을 호출해야 하는 경우 조건 작업 또는 호출할 자식 앱을 결정하는 스위치 작업을 사용할 수 있습니다. 이 패턴을 사용하면 통화 또는 작업 수를 줄일 수 있습니다.

    예를 들어 각각 "성공" 또는 "실패"와 같은 특정 주제에 대해 매분마다 이메일 계정을 확인하는 폴링 트리거가 있는 두 개의 논리 앱이 있다고 가정합니다. 이 설정으로 인해 시간당 120개의 호출이 발생합니다. 대신 매분 마다 폴링하지만 제목이 "성공" 또는 "실패"인지 여부에 따라 실행되는 자식 논리 앱을 호출하는 단일 부모 논리 앱을 만드는 경우 폴링 검사 수를 절반으로 줄이거나 이 경우 60개로 줄입니다.

* 일괄 처리 처리를 설정합니다.

  대상 서비스가 일괄 처리를 지원하는 경우 개별이 아닌 그룹 또는 일괄 처리로 항목을 처리하여 제한을 해결할 수 있습니다. 일괄 처리 솔루션을 구현하려면 "일괄 처리 수신기" 논리 앱과 "일괄 처리 보낸자" 논리 앱을 만듭니다. 일괄 처리 보낸 사람은 지정된 기준이 충족될 때까지 메시지 또는 항목을 수집한 다음 단일 그룹에서 해당 메시지 또는 항목을 보냅니다. 일괄 처리 수신기는 해당 그룹을 수락하고 해당 메시지 또는 항목을 처리합니다. 자세한 내용은 [그룹에서 일괄 처리 메시지를](../logic-apps/logic-apps-batch-process-send-receive-messages.md)참조하십시오.

* 폴링 버전이 아닌 트리거 및 작업에 웹후크 버전을 사용합니다.

  그 이유는 폴링 트리거는 특정 간격으로 대상 서비스 또는 시스템을 계속 확인합니다. 매 초마다 와 같이 매우 빈번한 간격으로 제한 문제가 발생할 수 있습니다. 그러나 [HTTP Webhook과](../connectors/connectors-native-webhook.md)같은 웹후크 트리거 또는 작업은 구독 시간에 발생하는 대상 서비스 또는 시스템에 대한 단일 호출만 생성하고 이벤트가 발생할 때만 대상이 트리거 또는 작업을 알수 도록 요청합니다. 이렇게 하면 트리거 또는 작업이 대상을 지속적으로 확인할 필요가 없습니다.
  
  따라서 대상 서비스 또는 시스템이 웹후크를 지원하거나 웹후크 버전이 있는 커넥터를 제공하는 경우 이 옵션은 폴링 버전을 사용하는 것보다 낫습니다. 웹후크 트리거 및 작업을 식별하려면 `ApiConnectionWebhook` 해당 유형이 있는지 또는 되풀이를 지정할 필요가 없는지 확인합니다. 자세한 내용은 [APIConnectionWebhook 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) 및 [APIConnectionWebhook 작업을](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [로직 앱 제한 및 구성에](../logic-apps/logic-apps-limits-and-config.md) 대해 자세히 알아보기
