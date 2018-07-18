---
title: Azure Event Hubs에 연결 - Azure Logic Apps | Microsoft Docs
description: Azure Event Hubs 및 Azure Logic Apps를 사용하여 이벤트 관리 및 모니터링
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 86fc1c3542bea1be840041bb73df15631c066c7e
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294975"
---
# <a name="monitor-receive-and-send-events-with-azure-event-hubs-and-azure-logic-apps"></a>Azure Event Hubs 및 Azure Logic Apps를 사용하여 이벤트 모니터링, 수신 및 전송 

이 아티클에서는 Azure Event Hubs 커넥터를 사용하여 논리 앱 내에서 [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)로 전송된 이벤트를 모니터링하고 관리하는 방법을 보여줍니다. 이런 방식으로 Event Hubs에서 이벤트를 검사하고, 전송하고, 수신하는 작업 및 워크플로를 자동화하는 논리 앱을 만들 수 있습니다. 

Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">무료 Azure 계정에 등록</a>합니다. 논리 앱을 처음 사용하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토합니다.
커넥터 관련 기술 정보는 <a href="https://docs.microsoft.com/connectors/eventhubs/" target="blank">Azure Event Hubs 커넥터 참조</a>를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Azure Event Hubs 네임스페이스 및 Event Hub](../event-hubs/event-hubs-create.md)

* Event Hubs에 액세스하려는 논리 앱입니다. Azure Event Hubs 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다. 

<a name="permissions-connection-string"></a>

## <a name="check-permissions-and-get-connection-string"></a>사용 권한 확인 및 연결 문자열 가져오기

Event Hub에 액세스하는 논리 앱의 경우 Event Hubs 네임스페이스에 대한 관리 권한과 연결 문자열이 있어야 합니다.

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다. 

2. 특정 Event Hub가 아닌 Event Hubs *네임스페이스*로 이동합니다. 네임스페이스 페이지의 **설정**에서 **공유 액세스 정책**을 선택합니다. **클레임** 아래에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

   ![Event Hub 네임스페이스에 대한 관리 권한](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3. 연결 정보를 나중에 수동으로 입력하려는 경우 Event Hubs 네임스페이스에 대한 연결 문자열을 가져옵니다. 

   1. **정책**에서 **RootManageSharedAccessKey**를 선택합니다. 

   2. 기본 키 연결 문자열을 찾습니다. 복사 단추를 선택하고 나중에 사용할 연결 문자열을 저장합니다.

      ![Event Hubs 네임스페이스 연결 문자열 복사](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

      > [!TIP]
      > 연결 문자열이 Event Hubs 네임스페이스 또는 특정 Event Hub와 연결되어 있는지 확인하려면 연결 문자열에 `EntityPath` 매개 변수가 있는지 확인합니다. 이 매개 변수를 찾으면 연결 문자열은 특정 Event Hub "엔터티"에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

4. 이제 [Event Hubs 트리거 추가](#add-trigger) 또는 [Event Hubs 작업 추가](#add-action)를 계속합니다.

<a name="add-trigger"></a>

## <a name="add-an-event-hubs-trigger"></a>Event Hubs 트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 실행될 때마다 Logic Apps 엔진에서 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행하기 시작합니다.

이 예제에서는 새 이벤트를 Event Hub로 보내는 경우 논리 앱 워크플로를 시작할 수 있는 방법을 보여줍니다. 

1. Azure Portal 또는 Visual Studio에서 빈 논리 앱을 만들어 논리 앱 디자이너를 엽니다. 이 예에서는 Azure Portal을 사용합니다.

2. 검색 상자에 필터로 “event hubs”를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다. 

   이 예제에서는 **Event Hubs - Event Hub에서 이벤트를 사용할 수 있는 경우** 트리거를 사용합니다.

   ![트리거 선택](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

3. 연결 정보를 묻는 메시지가 표시되면 [이제 Event Hubs 연결을 만듭니다](#create-connection). 또는 연결이 이미 존재하는 경우 트리거에 대한 필요한 정보를 제공합니다.

   1. **Event Hub 이름** 목록에서 모니터링하려는 Event Hub를 선택합니다.

      ![Event Hub 또는 소비자 그룹 지정](./media/connectors-create-api-azure-event-hubs/select-event-hub.png)

   2. 트리거에서 Event Hub를 확인하려는 간격 및 빈도를 선택합니다.
 
   3. 고급 트리거 옵션 중 일부를 필요에 따라 선택하려면 **고급 옵션 표시**를 선택합니다.
   
      ![트리거 고급 옵션](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-advanced.png)

      | 자산 | 세부 정보 | 
      |----------|---------| 
      | 콘텐츠 형식  | 이벤트의 콘텐츠 형식을 선택합니다. 기본값은 "application/octet-stream"입니다. |
      | 콘텐츠 스키마 | Event Hub에서 읽는 이벤트에 대한 콘텐츠 스키마를 JSON에 입력합니다. |
      | 소비자 그룹 이름 | 이벤트를 읽기 위해 Event Hub [소비자 그룹 이름](../event-hubs/event-hubs-features.md#consumer-groups)을 입력합니다. 지정하지 않으면 기본값 소비자 그룹이 사용됩니다. |
      | 최소 파티션 키 | 읽을 최소 [파티션](../event-hubs/event-hubs-features.md#partitions) ID를 입력합니다. 기본적으로 모든 파티션이 읽혀집니다. |
      | 최대 파티션 키 | 읽을 최대 [파티션](../event-hubs/event-hubs-features.md#partitions) ID를 입력합니다. 기본적으로 모든 파티션이 읽혀집니다. |
      | 최대 이벤트 수 | 최대 이벤트 수에 대한 값을 입력합니다. 트리거는 1과 이 속성으로 지정된 이벤트의 수 범위의 숫자를 반환합니다. |
      |||

4. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

5. 이제 트리거 결과와 함께 수행하려는 작업에 대한 논리 앱에 하나 이상의 작업을 계속해서 추가합니다.

> [!NOTE]
> 모든 Event Hub 트리거는 *긴 폴링* 트리거입니다. 즉, 이 트리거는 발생될 때 모든 이벤트를 처리한 다음, 더 많은 이벤트가 Event Hub에 나타날 때까지 30초 동안 기다립니다.
> 30초 후에 이벤트가 수신되지 않으면 트리거 실행을 건너뜁니다. 그렇지 않으면 트리거는 Event Hub가 빈 상태가 될 때까지 이벤트를 계속 읽습니다.
> 다음 트리거 폴링은 트리거의 속성에 지정한 되풀이 간격을 기준으로 발생합니다.

<a name="add-action"></a>

## <a name="add-an-event-hubs-action"></a>Event Hubs 작업 추가

Azure Logic Apps에서 [작업](../logic-apps/logic-apps-overview.md#logic-app-concepts)은 트리거 또는 다른 작업을 수행하는 워크플로의 한 단계입니다. 이 예제에서 논리 앱은 Event Hub에서 새 이벤트를 확인하는 Event Hubs 트리거를 시작합니다. 

1. Azure Portal 또는 Visual Studio에서 논리 앱 디자이너에서 논리 앱을 엽니다. 이 예에서는 Azure Portal을 사용합니다.

2. 트리거 또는 작업에서 **새 단계** > **작업 추가**를 선택합니다.

   기존 단계 간에 작업을 추가하려면 연결 화살표 위로 마우스를 이동합니다. 
   표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

3. 검색 상자에 필터로 “event hubs”를 입력합니다.
작업 목록에서 원하는 작업을 선택합니다. 

   예를 들어 **Event Hubs - 이벤트 전송** 작업을 선택합니다.

   ![“Event Hubs - Send event” 선택](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

4. 연결 정보를 묻는 메시지가 표시되면 [이제 Event Hubs 연결을 만듭니다](#create-connection). 또는 연결이 이미 존재하는 경우 작업에 대한 필요한 정보를 제공합니다.

   | 자산 | 필수 | 설명 | 
   |----------|----------|-------------|
   | 이벤트 허브 이름 | 예 | 이벤트를 보내려는 Event Hub를 선택합니다. | 
   | 이벤트 콘텐츠 | 아니오 | 전송하려는 이벤트의 콘텐츠 | 
   | properties | 아니오 | 보낼 앱 속성 및 값 | 
   |||| 

   예:  

   ![Event Hub 이름을 선택하고 이벤트 콘텐츠를 제공](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

5. 완료되면 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="create-connection"></a>

## <a name="connect-to-your-event-hub"></a>Event Hub에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)] 

1. 연결 정보에 대한 메시지가 표시되면 다음과 같은 세부 정보를 입력합니다.

   | 자산 | 필수 | 값 | 설명 | 
   |----------|----------|-------|-------------|
   | 연결 이름 | 예 | <*connection-name*> | 연결에 만들 이름 |
   | Event Hubs 네임스페이스 | 예 | <*event-hubs-namespace*> | 사용하려는 Event Hubs 네임스페이스를 선택합니다. | 
   |||||  

   예: 

   ![Event Hub 연결 만들기](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-1.png)

   연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력**을 선택합니다. 
   [연결 문자열을 찾는 방법](#permissions-connection-string)을 알아보세요.

2. 선택하지 않은 경우 사용할 Event Hubs 정책을 선택합니다. **만들기**를 선택합니다.

   ![Event Hub 연결 만들기, 2부](./media/connectors-create-api-azure-event-hubs/create-event-hubs-connection-2.png)

3. 연결을 만든 후에 [Event Hubs 트리거 추가](#add-trigger) 또는 [Event Hubs 작업 추가](#add-action)를 계속 진행합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/eventhubs/)를 참조하세요. 

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.