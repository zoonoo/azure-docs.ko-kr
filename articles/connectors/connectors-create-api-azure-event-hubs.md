---
title: "Azure Logic Apps용 Azure Event Hubs로 이벤트 모니터 설정 | Microsoft Docs"
description: "Azure Event Hubs로 Azure Logic Apps에 대한 이벤트를 수신 및 전송하는 데이터 스트림 모니터"
services: logic-apps
keywords: "데이터 스트림, 이벤트 모니터, 이벤트 허브"
author: ecfan
manager: anneta
editor: 
documentationcenter: 
tags: connectors
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2017
ms.author: estfan; LADocs
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 2ca27fb8269d1796fb1181fc4d0a8744a592d548
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017

---

# <a name="monitor-receive-and-send-events-with-the-event-hubs-connector"></a>Event Hubs 커넥터로 이벤트 모니터, 수신 및 전송

논리 앱이 이벤트를 감지하고 이벤트를 수신 및 전송할 수 있도록 이벤트 모니터를 설정하려면 논리 앱에서 [Azure Event Hub](https://azure.microsoft.com/services/event-hubs)에 연결합니다. [Azure 이벤트 허브](../event-hubs/event-hubs-what-is-event-hubs.md)에 대해 자세히 알아봅니다.

## <a name="requirements"></a>요구 사항

* Azure에 [Event Hubs 네임스페이스 및 Event Hub](../event-hubs/event-hubs-create.md)가 있어야 합니다. [Event Hubs 네임스페이스 및 Event Hub를 만드는 방법](../event-hubs/event-hubs-create.md)에 대해 알아보세요. 

* 논리 앱에서 [커넥터](https://docs.microsoft.com/azure/connectors/apis-list)를 사용하려면 먼저 논리 앱을 만들어야 합니다. [논리 앱을 만드는 방법](../logic-apps/logic-apps-create-a-logic-app.md)을 알아보세요.

<a name="permissions-connection-string"></a>
## <a name="check-event-hubs-namespace-permissions-and-find-the-connection-string"></a>Event Hubs 네임스페이스 권한 확인 및 연결 문자열 찾기

논리 앱에서 모든 서비스에 액세스하려면 논리 앱과 서비스 사이에 [*연결*](./connectors-overview.md)을 만들어야 합니다(아직 없는 경우). 이렇게 연결되면 논리 앱에서 데이터에 액세스할 수 있는 권한이 부여됩니다.
Event Hub에 액세스하는 논리 앱의 경우 Event Hubs 네임스페이스에 대한 **관리** 권한과 연결 문자열이 있어야 합니다.

사용 권한을 확인하고 연결 문자열을 가져오려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com "Azure Portal")에 로그인합니다. 

2.  특정 Event Hub가 아닌 Event Hubs *네임스페이스*로 이동합니다. 네임스페이스 블레이드의 **설정** 아래에서 **공유 액세스 정책**을 선택합니다. **클레임** 아래에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

    ![Event Hub 네임스페이스에 대한 관리 권한](./media/connectors-create-api-azure-event-hubs/event-hubs-namespace.png)

3.  Event Hubs 네임스페이스에 대한 연결 문자열을 복사하려면 **RootManageSharedAccessKey**를 선택합니다. 기본 키 연결 문자열 옆에 있는 복사 단추를 선택합니다.

    ![Event Hubs 네임스페이스 연결 문자열 복사](media/connectors-create-api-azure-event-hubs/find-event-hub-namespace-connection-string.png)

    > [!TIP]
    > 연결 문자열이 Event Hubs 네임스페이스 또는 특정 Event Hub와 연결되어 있는지 확인하려면 `EntityPath` 매개 변수에 대한 연결 문자열을 확인하세요. 이 매개 변수를 찾으면 연결 문자열은 특정 Event Hub “엔터티”에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

4.  이제 Event Hubs 트리거 또는 작업을 논리 앱에 추가한 후 자격 증명을 묻는 메시지가 표시되면 Event Hubs 네임스페이스에 연결할 수 있습니다. 연결에 이름을 지정하고 복사한 연결 문자열을 입력하고 **만들기**를 선택합니다.

    ![Event Hubs 네임스페이스에 대한 연결 문자열 입력](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    연결을 만들면 연결 이름이 Event Hubs 트리거 또는 작업에 표시됩니다. 
    그런 다음 논리 앱에서 다른 단계를 진행합니다.

    ![Event Hubs 네임스페이스 연결 생성됨](./media/connectors-create-api-azure-event-hubs/event-hubs-connection-created.png)

## <a name="start-workflow-when-your-event-hub-receives-new-events"></a>Event Hub에서 새 이벤트를 수신하면 워크플로 시작

[*트리거*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)는 논리 앱에서 워크플로를 시작하는 이벤트입니다. 새 이벤트가 Event Hub에 전송되었을 때 워크플로를 시작하려면 이 이벤트를 감지하는 트리거를 추가하는 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com "Azure Portal")에서 기존 논리 앱으로 이동하거나 비어 있는 논리 앱을 만듭니다.

2.  논리 앱 디자이너의 검색 상자에 필터에 대한 `event hubs`를 입력합니다. 이 트리거 선택: **이벤트가 Event Hub에서 사용 가능한 경우**

    ![Event Hub에서 새 이벤트를 수신하는 경우에 대한 트리거 선택](./media/connectors-create-api-azure-event-hubs/find-event-hubs-trigger.png)

    Event Hubs 네임스페이스에 대한 연결이 아직 없는 경우 이 연결을 생성할지 묻는 메시지가 표시됩니다. 연결에 이름을 지정하고 Event Hubs 네임스페이스에 대한 연결 문자열을 입력합니다. 
    필요한 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아보세요.

    ![Event Hubs 네임스페이스에 대한 연결 문자열 입력](./media/connectors-create-api-azure-event-hubs/event-hubs-connection.png)

    연결을 생성한 후에는 **이벤트가 Event Hub에서 사용 가능한 경우** 트리거에 대한 설정이 나타납니다.

    ![Event Hub에서 새 이벤트를 수신하는 경우에 대한 트리거 설정](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger.png)

3.  모니터할 Event Hub에 대한 이름을 입력 또는 선택합니다. Event Hub를 얼마나 자주 확인할지 빈도와 간격을 선택합니다.

    > [!TIP]
    > 필요할 경우 이벤트를 읽기 위한 소비자 그룹을 선택하고 **고급 옵션 표시**를 선택합니다. 

    ![Event Hub 또는 소비자 그룹 지정](./media/connectors-create-api-azure-event-hubs/event-hubs-trigger-details.png)

    이제 논리 앱에 대한 워크플로를 시작할 트리거를 설정했습니다. 
    논리 앱에서 설정한 일정에 따라 지정된 Event Hub를 확인합니다. 
    앱이 Event Hub에서 새 이벤트를 찾으면 트리거가 논리 앱의 다른 작업 또는 트리거를 실행합니다.

## <a name="send-events-to-your-event-hub-from-your-logic-app"></a>논리 앱에서 Event Hub로 이벤트 보내기

[*작업*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)은 논리 앱 워크플로에서 수행하는 태스크입니다. 논리 앱에 트리거를 추가한 후에 해당 트리거에 의해 생성된 데이터를 사용하여 작업을 수행하는 작업을 추가할 수 있습니다. 논리 앱에서 Event Hub로 이벤트를 보내려면 다음 단계를 수행합니다.

1.  논리 앱 디자이너의 논리 앱 트리거 아래에서 **새 단계** > **작업 추가**를 선택합니다.

    ![[새 단계]를 선택한 후 [작업 추가]를 선택합니다.](./media/connectors-create-api-azure-event-hubs/add-action.png)

    이제 수행할 작업을 찾아 선택할 수 있습니다. 
    어떤 작업이라도 선택할 수 있지만 이 예에서는 이벤트를 보내는 Event Hubs 작업을 선택하고자 합니다.

2.  검색 상자에서 필터에 `event hubs`를 입력합니다.
이 작업 선택: **이벤트 보내기**

    ![[Event Hubs - 이벤트 보내기] 작업을 선택합니다.](./media/connectors-create-api-azure-event-hubs/find-event-hubs-action.png)

3.  이벤트에 필요한 정보(예: 이벤트를 보낼 Event Hub 이름)를 입력합니다. 이벤트에 대한 다른 선택적 정보(예: 이벤트에 대한 콘텐츠)를 입력합니다.

    > [!TIP]
    > 이벤트를 보낼 Event Hub 파티션을 필요에 따라 지정하려면 **고급 옵션 표시**를 선택합니다. 

    ![Event Hub 이름 및 선택적 이벤트 정보 입력](./media/connectors-create-api-azure-event-hubs/event-hubs-send-event-action.png)

6.  변경 내용을 저장합니다.

    ![논리 앱 저장](./media/connectors-create-api-azure-event-hubs/save-logic-app.png)

    이제 논리 앱에서 이벤트를 보내는 작업을 설정했습니다. 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/eventhubs/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다. 

## <a name="get-help"></a>도움말 보기

질문하고, 질문에 답변하고, 다른 Azure Logic Apps 사용자가 어떤 일을 하는지 확인하려면 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문하세요.

Logic Apps 및 커넥터 개선에 도움을 주려면 [Logic Apps 사용자 의견 사이트](http://aka.ms/logicapps-wish)에서 투표하고 아이디어를 제출하세요.

## <a name="next-steps"></a>다음 단계

*  [Azure Logic Apps용 다른 커넥터 찾기](./apis-list.md)
