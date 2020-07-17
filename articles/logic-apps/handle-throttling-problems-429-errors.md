---
title: 제한 문제 처리 또는 ' 429-너무 많은 요청 ' 오류
description: Azure Logic Apps에서 제한 문제 또는 ' HTTP 429 너무 많은 요청 ' 오류를 해결 하는 방법
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: fbfd52065bc0522668488492de2181f252f86a4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81272681"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Azure Logic Apps에서 제한 문제 (429-"요청이 너무 많음" 오류)를 처리 합니다.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md)에서 논리 앱은 제한 발생 시 ["HTTP 429 요청 수가 너무 많음" 오류](https://developer.mozilla.org/docs/Web/HTTP/Status/429) 를 반환 합니다 .이는 요청 수가 특정 시간 동안 처리 될 수 있는 속도를 초과할 때 발생 합니다. 제한은 지연 된 데이터 처리, 성능 속도 감소, 지정 된 재시도 정책 초과 등의 오류와 같은 문제를 만들 수 있습니다.

![SQL Server 커넥터에서 제한](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

다음은 논리 앱에서 경험할 수 있는 몇 가지 일반적인 제한 유형입니다.

* [논리 앱](#logic-app-throttling)
* [커넥터](#connector-throttling)
* [대상 서비스 또는 시스템](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>논리 앱 제한

Azure Logic Apps 서비스에는 고유한 [처리량 제한이](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)있습니다. 따라서 논리 앱이 이러한 제한을 초과 하면 특정 인스턴스 뿐만 아니라 실행 되는 논리 앱 리소스가 제한 됩니다.

이 수준에서 조정 이벤트를 찾으려면 Azure Portal에서 논리 앱의 **메트릭** 창을 확인 합니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **모니터링**아래에서 **메트릭**을 선택 합니다.

1. **차트 제목**에서 **메트릭 추가** 를 선택 하 여 기존에 다른 메트릭을 추가 합니다.

1. 첫 번째 메트릭 표시줄의 **메트릭** 목록에서 **작업 제한 이벤트**를 선택 합니다. 두 번째 메트릭 표시줄의 **메트릭** 목록에서 **스로틀 된 이벤트 트리거**를 선택 합니다.

이 수준에서 제한을 처리 하기 위해 다음 옵션을 사용할 수 있습니다.

* 동시에 실행할 수 있는 논리 앱 인스턴스 수를 제한 합니다.

  기본적으로 논리 앱의 트리거 조건이 동시에 두 번 이상 충족 되는 경우 논리 앱에 대 한 여러 트리거 인스턴스가 동시에 또는 동시 *에*실행 됩니다. 이 동작은 이전 워크플로 인스턴스의 실행이 완료 되기 전에 각 트리거 인스턴스가 발생 함을 의미 합니다.

  동시에 실행 될 수 있는 트리거 인스턴스의 기본 수는 [제한](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)되지 않지만 [트리거의 동시성 설정을](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)설정 하 여이 수를 제한 하 고 필요한 경우 기본값 이외의 제한을 선택할 수 있습니다.

* 높은 처리량 모드를 사용 합니다.

  논리 앱에는 [5 분의 이동 간격 동안 실행할 수 있는 작업 수에 대 한 기본 제한이](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)있습니다. 이 제한을 최대 작업 수로 올리려면 논리 앱에서 [높은 처리량 모드](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode) 를 설정 합니다.

* 트리거에서 배열 분리 처리 ("분할") 동작을 사용 하지 않도록 설정 합니다.

  트리거가 처리할 나머지 워크플로 작업에 대 한 배열을 반환 하는 경우 트리거의 [ **분할** 설정](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) 은 배열 항목을 분할 하 고 각 배열 항목에 대 한 워크플로 인스턴스를 시작 하 여 한 [도 **의 분할** ](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)에 대해 여러 동시 실행을 효과적으로 트리거합니다. 제한을 제어 하려면 **분할** 동작을 해제 하 고 논리 앱에서 호출 당 단일 항목을 처리 하는 대신 단일 호출로 전체 배열을 처리 하도록 합니다.

* 더 작은 논리 앱으로 작업을 리팩터링 합니다.

  앞에서 설명한 것 처럼 논리 앱은 [5 분 동안 실행 될 수 있는 기본 작업 수](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)로 제한 됩니다. [높은 처리량 모드](../logic-apps/logic-apps-workflow-actions-triggers.md#run-high-throughput-mode)를 사용 하도록 설정 하 여이 제한을 늘릴 수 있지만 각 논리 앱에서 실행 되는 작업 수가 한도 미만으로 유지 되도록 논리 앱의 작업을 더 작은 논리 앱으로 나눌 지 여부를 고려할 수도 있습니다. 이렇게 하면 단일 논리 앱 리소스에 대 한 부담을 줄이고 여러 논리 앱에 부하를 분산 시킬 수 있습니다. 이 솔루션은 많은 데이터 집합을 처리 하거나 동시에 실행 되는 여러 작업, 루프 반복 또는 작업 실행 제한을 초과 하는 각 루프 반복 내에서 작업을 실행 하는 작업에 더 적합 합니다.

  예를 들어이 논리 앱은 SQL Server 데이터베이스에서 테이블을 가져오는 모든 작업을 수행 하 고 각 테이블의 행을 가져옵니다. **For each** 루프는 **행 가져오기** 작업에서 각 테이블에 대 한 행을 반환 하도록 각 테이블을 동시에 반복 합니다. 이러한 작업은 해당 테이블에 있는 데이터의 양에 따라 작업 실행의 제한을 초과할 수 있습니다.

  ![논리 앱 "이전" 리팩터링](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  리팩터링 후 논리 앱은 이제 부모 및 자식 논리 앱입니다. 부모는 SQL Server에서 테이블을 가져온 다음 각 테이블에 대 한 자식 논리 앱을 호출 하 여 행을 가져옵니다.

  ![한 작업에 대 한 논리 앱 만들기](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  각 테이블에 대 한 행을 가져오기 위해 부모 논리 앱에서 호출 하는 자식 논리 앱은 다음과 같습니다.

  ![두 번째 작업에 대 한 다른 논리 앱 만들기](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>커넥터 제한

각 커넥터에는 커넥터의 기술 참조 페이지에서 찾을 수 있는 고유한 제한 제한이 있습니다. 예를 들어 [Azure Service Bus 커넥터](https://docs.microsoft.com/connectors/servicebus/) 에는 분당 최대 6000 호출을 허용 하는 제한 한도가 있으며 SQL Server 커넥터에는 [작업 유형에 따라 달라 지는](https://docs.microsoft.com/connectors/sql/)제한 제한이 있습니다.

HTTP와 같은 일부 트리거 및 작업에는 예외 처리를 구현 하기 위해 [재시도 정책 제한을](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) 기반으로 사용자 지정할 수 있는 ["재시도 정책"](../logic-apps/logic-apps-exception-handling.md#retry-policies) 이 있습니다. 이 정책은 원래 요청이 실패 하거나 시간이 초과 되 고 408, 429 또는 5xx 응답이 발생 하는 경우 트리거 또는 작업에서 요청을 다시 시도할지 여부와 빈도를 지정 합니다. 따라서 조정이 시작 되 고 429 오류가 반환 되 면 지원 되는 경우 다시 시도 정책을 따르는 Logic Apps 합니다.

트리거 또는 작업에서 재시도 정책을 지원 하는지 확인 하려면 트리거 또는 작업의 설정을 확인 합니다. 트리거 또는 작업의 재시도 횟수를 보려면 논리 앱의 실행 기록으로 이동 하 고, 검토할 실행을 선택 하 고, 해당 트리거 또는 작업을 확장 하 여 입력, 출력 및 재시도에 대 한 세부 정보를 확인 합니다. 예를 들면 다음과 같습니다.

![작업 실행 기록, 다시 시도, 입력 및 출력 보기](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

다시 시도 기록이 오류 정보를 제공 하지만 커넥터 제한과 [대상 제한을](#destination-throttling)구분 하는 데 문제가 있을 수 있습니다. 이 경우 응답의 세부 정보를 검토 하거나 일부 제한 간격 계산을 수행 하 여 원본을 식별 해야 할 수 있습니다.

전역 다중 테 넌 트 Azure Logic Apps 서비스에서 논리 앱의 경우 제한이 *연결* 수준에서 발생 합니다. 따라서 예를 들어 [ise (integration service environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)에서 실행 되는 논리 앱의 경우에는 전역 다중 테 넌 트 Logic Apps 서비스에서 실행 되기 때문에 ise가 아닌 연결에 대 한 제한이 여전히 발생 합니다. 그러나 ise 커넥터에서 생성 되는 ISE 연결은 ISE에서 실행 되기 때문에 제한 되지 않습니다.

이 수준에서 제한을 처리 하기 위해 다음 옵션을 사용할 수 있습니다.

* 논리 앱이 처리를 위해 데이터를 분할 하도록 단일 작업에 대해 여러 연결을 설정 합니다.

  이 옵션의 경우 동일한 자격 증명을 사용 하 여 여러 연결에서 동일한 대상으로 작업의 요청을 분할 하 여 작업 부하를 분산할 수 있는지 여부를 고려 합니다.

  예를 들어 논리 앱이 SQL Server 데이터베이스에서 테이블을 가져온 다음 각 테이블에서 행을 가져옵니다. 처리 해야 하는 행 수에 따라 여러 개의 연결 및 여러 개의 루프 **에 대해** 여러 개의 연결을 사용 하 여 전체 행 수를 더 작은 집합으로 분할 하 여 처리할 수 있습니다. 이 시나리오에서는 **각 루프에 대해** 2 개를 사용 하 여 총 행 수를 절반으로 분할 합니다. 첫 번째 **for each** 루프는 처음 절반을 가져오는 식을 사용 합니다. **For each** 루프는 두 번째 절반을 가져오는 다른 식을 사용 합니다. 예를 들면 다음과 같습니다.<p>

    * 식 1: `take()` 함수는 컬렉션의 앞 부분을 가져옵니다. 자세한 내용은 [ **`take()`** 함수](workflow-definition-language-functions-reference.md#take)를 참조 하세요.

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * 식 2: `skip()` 함수는 컬렉션의 앞을 제거 하 고 다른 모든 항목을 반환 합니다. 자세한 내용은 [ **`skip()`** 함수](workflow-definition-language-functions-reference.md#skip)를 참조 하세요.

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    다음은 이러한 식을 사용 하는 방법을 보여 주는 시각적 예제입니다.

    ![단일 작업에 대해 여러 연결 만들기 및 사용](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* 각 작업에 대해 서로 다른 연결을 설정 합니다.

  이 옵션의 경우 작업이 동일한 서비스 또는 시스템에 연결 되 고 동일한 자격 증명을 사용 하는 경우에도 각 작업의 요청을 자체 연결에 분산 하 여 작업 부하를 분산할 수 있는지 여부를 고려 합니다.

  예를 들어 논리 앱이 SQL Server 데이터베이스에서 테이블을 가져오고 각 테이블의 각 행을 가져오는 경우를 가정 합니다. 별도의 연결을 사용 하 여 테이블 가져오기에서 하나의 연결을 사용 하 고 각 행을 가져오는 동안 다른 연결을 사용 하도록 할 수 있습니다.

  ![각 작업에 대해 다른 연결 만들기 및 사용](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* ["For each" 루프](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)에서 동시성 또는 병렬 처리를 변경 합니다.

  기본적으로 "For each" 루프 반복은 [동시성 제한](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)까지 동시에 실행 됩니다. "For each" 루프 내에서 제한 된 커넥터가 있는 경우 병렬로 실행 되는 루프 반복 횟수를 줄일 수 있습니다. 자세한 내용은 다음 항목을 참조하세요.
  
  * ["For each" 루프 변경 동시성 또는 순차적으로 실행](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [워크플로 정의 언어 스키마-For each 루프](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [워크플로 정의 언어 스키마-"For each" 루프 동시성 변경](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [워크플로 정의 언어 스키마-"For each" 루프를 순차적으로 실행 합니다.](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>대상 서비스 또는 시스템 제한

커넥터에는 자체 제한 한도가 있지만 커넥터에 의해 호출 되는 대상 서비스 또는 시스템에도 제한 제한이 있을 수 있습니다. 예를 들어 Microsoft Exchange Server의 일부 Api는 Office 365 Outlook 커넥터 보다 제한 한도가 더 엄격 합니다.

기본적으로 논리 앱의 인스턴스 및 해당 인스턴스 내의 루프 또는 분기는 *병렬로*실행 됩니다. 이 동작은 여러 인스턴스가 동시에 동일한 끝점을 호출할 수 있음을 의미 합니다. 각 인스턴스는 다른의 존재에 대해 알지 못합니다. 실패 한 작업을 다시 시도 하면 여러 호출이 동시에 실행 하려고 시도 하는 [경합 상태](https://en.wikipedia.org/wiki/Race_condition) 를 만들 수 있지만, 성공적으로 실행 하려면 조정이 시작 되기 전에 대상 서비스 또는 시스템에 해당 호출이 도착 해야 합니다.

예를 들어 100 개의 항목이 있는 배열이 있다고 가정 합니다. "For each" 루프를 사용 하 여 배열을 반복 하 고 루프의 동시성 제어를 설정 하 여 병렬 반복의 수를 20 또는 [현재 기본 제한](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)으로 제한할 수 있습니다. 이 루프 내에서 작업은 배열의 항목을 SQL Server 데이터베이스에 삽입 하 여 초당 15 개의 호출만 허용 합니다. 이 시나리오에서는 다시 시도의 백로그가 작성 되 고 실행 되지 않기 때문에 제한 문제가 발생 합니다.

다음 표에서는 작업의 다시 시도 간격이 1 초 인 경우 루프에서 수행 되는 작업에 대 한 타임 라인을 설명 합니다.

| 지정 시간 | 실행 되는 작업 수 | 실패 한 작업 수 | 대기 중인 다시 시도 횟수 |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 초 | 20 개 삽입 | 5 실패 (SQL 제한으로 인해) | 5 회 다시 시도 |
| T + 0.5 초 | 이전 5 번의 재시도로 인해 15 개의 삽입 | 이전 SQL 제한이 다른 0.5 초 동안 계속 적용 되어 15 개 모두 실패 합니다. | 20 회 다시 시도 <br>(이전 5 + 15 개 새) |
| T + 1 초 | 20 개 삽입 | 5 실패 및 이전 20 번의 다시 시도 (SQL 제한으로 인해) | 25 번 다시 시도 (이전 20 + 5 개의 새)
|||||

이 수준에서 제한을 처리 하기 위해 다음 옵션을 사용할 수 있습니다.

* 각각 단일 작업을 처리 하도록 논리 앱을 만듭니다.

  * 이 섹션의 예제 SQL Server 시나리오를 계속 진행 하면 배열 항목을 큐에 저장 하는 논리 앱 (예: [Azure Service Bus 큐](../connectors/connectors-create-api-servicebus.md))을 만들 수 있습니다. 그런 다음 해당 큐의 각 항목에 대 한 삽입 작업만 수행 하는 다른 논리 앱을 만듭니다. 이렇게 하면 하나의 논리 앱 인스턴스만 특정 시간에 실행 됩니다 .이 인스턴스는 삽입 작업을 완료 하 여 큐의 다음 항목으로 이동 하 고, 인스턴스는 429 오류를 가져오지만 비생산적인 재시도는 시도 하지 않습니다.

  * 각 작업에 대해 자식 또는 중첩 된 논리 앱을 호출 하는 부모 논리 앱을 만듭니다. 부모가 부모의 결과에 따라 서로 다른 자식 앱을 호출 해야 하는 경우에는 호출할 자식 앱을 결정 하는 조건 작업 또는 전환 동작을 사용할 수 있습니다. 이 패턴은 호출 또는 작업의 수를 줄이는 데 도움이 됩니다.

    예를 들어 "성공" 또는 "실패"와 같이 특정 주제에 대해 1 분 마다 메일 계정을 확인 하는 폴링 트리거를 사용 하는 두 개의 논리 앱이 있다고 가정 합니다. 이 설정은 시간당 120 호출을 생성 합니다. 대신 1 분 마다 폴링하는 단일 부모 논리 앱을 만드는 경우 주체가 "성공" 또는 "실패" 인지 여부를 기반으로 실행 되는 자식 논리 앱을 만드는 경우 폴링 검사 수를 절반 또는 60으로 자릅니다.

* 일괄 처리를 설정 합니다.

  대상 서비스에서 일괄 처리 작업을 지 원하는 경우 개별적이 아닌 그룹 또는 일괄 처리로 항목을 처리 하 여 제한을 처리할 수 있습니다. 일괄 처리 솔루션을 구현 하려면 "일괄 처리 수신자" 논리 앱과 "일괄 처리 발신자" 논리 앱을 만듭니다. 일괄 처리 발신자는 지정 된 조건이 충족 될 때까지 메시지나 항목을 수집한 다음 해당 메시지 또는 항목을 단일 그룹으로 보냅니다. 일괄 처리 수신자는 해당 그룹을 수락 하 고 해당 메시지 또는 항목을 처리 합니다. 자세한 내용은 [그룹으로 메시지 일괄](../logic-apps/logic-apps-batch-process-send-receive-messages.md)처리를 참조 하세요.

* 폴링 버전이 아닌 트리거 및 작업에 대 한 webhook 버전을 사용 합니다.

  이유 폴링 트리거는 특정 간격으로 대상 서비스 또는 시스템을 계속 검사 합니다. 1 초 간격으로 매우 자주 수행 되는 경우에는 제한 문제가 발생할 수 있습니다. 그러나 웹 후크 트리거 또는 동작 (예: [HTTP webhook](../connectors/connectors-native-webhook.md))은 구독 시에 발생 하는 대상 서비스 또는 시스템에 대 한 단일 호출만 만들며 이벤트가 발생 하는 경우에만 대상에서 트리거 또는 작업을 알립니다. 이렇게 하면 트리거 또는 작업에서 대상을 지속적으로 확인할 필요가 없습니다.
  
  따라서 대상 서비스 또는 시스템이 웹 후크를 지원 하거나 webhook 버전이 있는 커넥터를 제공 하는 경우이 옵션은 폴링 버전을 사용 하는 것 보다 좋습니다. 웹 후크 트리거 및 작업을 식별 하려면 `ApiConnectionWebhook` 형식이 있거나 되풀이를 지정 하지 않아도 되는지 확인 합니다. 자세한 내용은 [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) and [APIConnectionWebhook action](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)항목을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md) 에 대 한 자세한 정보
