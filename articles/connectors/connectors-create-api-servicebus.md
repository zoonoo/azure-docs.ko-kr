---
title: Azure 서비스 버스로 메시지 교환
description: Azure 논리 앱에서 Azure Service Bus를 사용하여 메시지를 보내고 받는 자동화된 작업 및 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 1b38b8508dbe17d42bf191149410f5db638cf834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261622"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Azure 논리 앱 및 Azure 서비스 버스를 사용하여 클라우드에서 메시지 교환

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 Azure Service [Bus](../service-bus-messaging/service-bus-messaging-overview.md) 커넥터를 사용하면 조직의 응용 프로그램 간에 판매 및 구매 주문서, 저널 및 인벤토리 이동과 같은 데이터를 전송하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 커넥터는 메시지의 모니터링, 전송 및 관리뿐만 아니라 다음과 같이 큐, 세션, 토픽, 구독 등을 사용한 작업을 수행합니다.

* 큐, 토픽 및 토픽 구독에 메시지가 도착하거나(자동 완성) 수신되면(보기 잠금) 모니터링합니다.
* 메시지를 보냅니다.
* 토픽 구독을 만들고 삭제합니다.
* 큐 및 토픽 구독에서 메시지를 관리합니다. 예를 들어 가져오기, 지연된 가져오기, 완료, 지연, 중단 및 배달하지 못한 편지가 있습니다.
* 큐 및 토픽 구독에서 메시지 및 세션에 대한 잠금을 갱신합니다.
* 큐 및 토픽에서 세션을 닫습니다.

트리거를 사용하여 Service Bus에서 응답을 가져오고 논리 앱의 다른 작업에서 출력을 사용하도록 할 수 있습니다. 또한 다른 작업에서 Service Bus 작업의 출력을 사용하도록 할 수 있습니다. 서비스 버스 및 논리 앱을 새로 접하는 경우 [Azure 서비스 버스란 무엇이며](../service-bus-messaging/service-bus-messaging-overview.md) [Azure 논리 앱이란 무엇입니까?](../logic-apps/logic-apps-overview.md)

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Service Bus 네임스페이스 및 메시징 엔터티(예: 큐). 이러한 항목과 논리 앱은 동일한 Azure 구독을 사용해야 합니다. 이러한 항목에 없는 경우 [Service Bus 네임스페이스 및 큐를 만드는](../service-bus-messaging/service-bus-create-namespace-portal.md) 방법을 알아봅니다.

* [논리 앱을 만드는 방법에](../logic-apps/quickstart-create-first-logic-app-workflow.md) 대한 기본 지식

* Service Bus 네임스페이스 및 메시징 엔터티를 사용하는 논리 앱입니다. 논리 앱과 서비스 버스는 동일한 Azure 구독을 사용해야 합니다. Service Bus 트리거로 워크플로를 시작하려면 [빈 논리 앱을 만듭니다.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 워크플로에서 Service Bus 작업을 사용하려면 되풀이 [트리거와](../connectors/connectors-native-recurrence.md)같은 다른 트리거를 사용하여 논리 앱을 시작합니다.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>권한 확인

논리 앱에 Service Bus 네임스페이스에 액세스하기 위한 권한이 있는지 확인합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. Service Bus *네임스페이스*로 이동합니다. 네임스페이스 페이지의 **설정**에서 **공유 액세스 정책**을 선택합니다. **클레임에서**해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

   ![서비스 버스 네임스페이스에 대한 권한 관리](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Service Bus 네임스페이스에 대한 연결 문자열을 가져옵니다. 논리 앱에서 연결 정보를 제공할 때 이 문자열이 필요합니다.

   1. 공유 **액세스 정책** 창에서 **루트관리공유액세스키를**선택합니다.
   
   1. 기본 연결 문자열 옆에 복사 단추를 선택합니다. 나중에 사용할 연결 문자열을 저장합니다.

      ![Service Bus 네임스페이스 연결 문자열 복사](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 메시징 엔터티와 연결되어 있는지 확인하려면 연결 문자열을 검색하여 `EntityPath`  매개 변수를 찾습니다. 이 매개 변수를 찾은 경우 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="add-service-bus-trigger"></a>서비스 버스 트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure 포털에](https://portal.azure.com)로그인하고 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 "azure 서비스 버스"를 필터로 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다.

   예를 들어 새 항목이 Service Bus 큐로 전송될 때 논리 앱을 트리거하려면 **큐에서 메시지가 수신되는 경우(자동 완성) 트리거를** 선택합니다.

   ![Service Bus 트리거 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   모든 서비스 버스 트리거는 *긴 폴링* 트리거입니다. 이 설명은 트리거가 트리거가 발생하면 트리거가 모든 메시지를 처리한 다음 큐 또는 토픽 구독에 더 많은 메시지가 표시될 때까지 30초 동안 대기한다는 것을 의미합니다. 30초 동안 표시되는 메시지가 없으면 트리거 실행을 건너뜁니다. 그렇지 않으면 큐 또는 토픽 구독이 빈 상태가 될 때까지 트리거는 메시지를 계속 읽습니다. 다음 트리거 폴링은 트리거의 속성에 지정된 되풀이 간격을 기준으로 합니다.

   **하나 이상의 메시지가 큐(자동 완성) 트리거에 도착할 때와** 같은 일부 트리거는 하나 이상의 메시지를 반환할 수 있습니다. 이러한 트리거가 발생하면 트리거의 **최대 메시지 수** 속성에 의해 지정된 메시지 수와 하나 사이에 반환됩니다.

1. 트리거가 처음으로 Service Bus 네임스페이스에 연결되는 경우 논리 앱 디자이너가 연결 정보를 묻는 메시지가 표시될 때 다음 단계를 따릅니다.

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![Service Bus 연결 만들기, 1부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      대신 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력합니다.** 연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. 서비스 버스 정책을 선택하고 **을**선택합니다.

      ![Service Bus 연결 만들기, 2부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 큐 또는 토픽과 같이 원하는 메시징 엔터티를 선택합니다. 이 예제에서는 서비스 버스 대기열을 선택합니다.
   
      ![Service Bus 큐 선택](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 선택한 트리거에 필요한 정보를 제공합니다. 작업에 사용 가능한 다른 속성을 추가하려면 **새 매개 변수** 추가 목록을 열고 원하는 속성을 선택합니다.

   이 예제의 트리거에서 큐를 확인하기 위한 폴링 간격 및 빈도를 선택합니다.

   ![폴링 간격 설정](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   사용 가능한 트리거 및 속성에 대한 자세한 내용은 커넥터의 [참조 페이지를](/connectors/servicebus/)참조하십시오.

1. 원하는 작업을 추가하여 논리 앱을 계속 빌드합니다.

   예를 들어 새 메시지가 도착하면 이메일을 보내는 작업을 추가할 수 있습니다. 트리거가 큐를 확인하고 새 메시지를 발견하는 경우 논리 앱은 찾은 메시지에 대해 사용자가 선택한 작업을 실행합니다.

## <a name="add-service-bus-action"></a>서비스 버스 작업 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure 포털에](https://portal.azure.com)로그인하고 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 작업을 추가할 단계에서 **새 단계를**선택합니다.

   또는 단계 간에 작업을 추가하려면 해당 단계 사이의 화살표 위로 포인터를 이동합니다. 표시되는 더하기**+** 기호 ()를 선택하고 **작업 추가를**선택합니다.

1. **작업 선택에서**검색 상자에 필터로 "azure 서비스 버스"를 입력합니다. 작업 목록에서 원하는 작업을 선택합니다. 

   이 예제에서는 **메시지 보내기** 작업을 선택합니다.

   ![Service Bus 동작 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 작업이 처음으로 Service Bus 네임스페이스에 연결되는 경우 논리 앱 디자이너가 연결 정보를 묻는 메시지가 표시될 때 다음 단계를 따릅니다.

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![Service Bus 연결 만들기, 1부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      대신 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력합니다.** 연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. 서비스 버스 정책을 선택하고 **을**선택합니다.

      ![Service Bus 연결 만들기, 2부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 큐 또는 토픽과 같이 원하는 메시징 엔터티를 선택합니다. 이 예제에서는 서비스 버스 대기열을 선택합니다.

      ![Service Bus 큐 선택](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 선택한 작업에 필요한 정보를 입력합니다. 작업에 사용 가능한 다른 속성을 추가하려면 **새 매개 변수** 추가 목록을 열고 원하는 속성을 선택합니다.

   예를 들어 **콘텐츠** 및 **콘텐츠 유형** 속성을 선택하여 작업에 추가할 수 있습니다. 그런 다음 보낼 메시지의 내용을 지정합니다.

   ![메시지 콘텐츠 및 세부 정보를 입력합니다.](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   사용 가능한 작업 및 해당 속성에 대한 자세한 내용은 커넥터의 [참조 페이지를](/connectors/servicebus/)참조하십시오.

1. 원하는 다른 작업을 추가하여 논리 앱을 계속 빌드합니다.

   예를 들어 이메일을 보내는 작업을 추가하여 메시지가 전송된 것을 확인할 수 있습니다.

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="connector-reference"></a>커넥터 참조

Service Bus 커넥터는 서비스 버스에서 커넥터 캐시까지 한 번에 최대 1,500개의 고유 세션을 저장할 수 있습니다. 세션 수가 이 제한을 초과하면 이전 세션이 캐시에서 제거됩니다. 자세한 내용은 [메시지 세션을](../service-bus-messaging/message-sessions.md)참조하십시오.

커넥터의 OpenAPI(이전 Swagger) 설명에 설명된 트리거, 작업 및 제한에 대한 기타 기술 세부 정보는 커넥터의 [참조 페이지를](/connectors/servicebus/)검토합니다. Azure 서비스 버스 메시징에 대한 자세한 [내용은 Azure 서비스 버스란 무엇입니까?](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
