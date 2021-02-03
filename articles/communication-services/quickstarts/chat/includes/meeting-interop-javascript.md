---
title: 빠른 시작 - Teams 모임 참가
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 780ef2bbb7851d8bef5fc52a51421a7938043ecb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932321"
---
## <a name="join-the-meeting-chat"></a>미팅 채팅 참여 

Teams 상호 운용성을 사용하도록 설정하면 Communication Services 사용자가 호출 클라이언트 라이브러리를 사용하여 Teams 호출에 게스트 사용자로 조인할 수 있습니다. 통화에 조인하면 채팅 통화에 참가자로 추가됩니다. 그러면 통화 중인 다른 사용자와 메시지를 주고받을 수 있습니다. 사용자는 통화에 조인하기 전에 전송된 채팅 메시지에 액세스할 수 없습니다. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services 사용자를 위한 Teams 미팅 채팅 스레드 가져오기

먼저 미팅 채팅 스레드에 대한 `ChatThreadClient`를 인스턴스화합니다. 미팅 링크를 구문 분석하거나 미팅 ID와 함께 Graph API를 사용하여 스레드 ID를 가져옵니다. 

- Teams 미팅 링크는 `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`와 같습니다. 스레드 ID는 해당 링크의 `meeting_chat_thread_id` 위치에 있습니다. 
- 미팅 ID가 있는 경우 [Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)를 사용하여 스레드 ID를 가져올 수 있습니다. [GET API](/graph/api/onlinemeeting-get?tabs=http%22+%5c&view=graph-rest-beta) 응답에는 `threadID`를 포함하는 `chatInfo` 개체가 포함됩니다. 

채팅 스레드 ID가 있으면 JavaScript 채팅 클라이언트 라이브러리를 사용하여 채팅 스레드 클라이언트를 가져올 수 있습니다. 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient`를 사용하여 채팅 스레드의 멤버를 나열하고, 채팅 기록을 가져오고, 메시지를 보낼 수 있습니다.  

## <a name="send-and-receive-messages"></a>메시지 보내기 및 받기  

`SendMessage`를 사용하여 미팅 채팅에 메시지를 보냅니다. 들어오는 메시지를 수신하는 경우 `chatMessageReceived`와 같은 이벤트를 구독하는 기능은 이 시나리오에 대해 실시간 신호가 아직 활성화되지 않았기 때문에 지원되지 않습니다. 최신 메시지를 가져오려면 `ListMessages` API를 폴링하면 됩니다. 상호 운용성 시나리오에서 `ListMessages` API는 이제 다음과 같은 세 가지 새 메시지 유형을 반환하도록 지원합니다.
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

메시지 유형에 대한 자세한 내용은 [여기](../../../concepts/chat/concepts.md)를 참조하세요. 

**참고** - 현재는 Teams와의 상호 운용성 시나리오에 대해서만 메시지를 보내고 받을 수 있습니다. 입력 표시기 및 Communication Services 사용자와 같은 다른 기능에서는 아직 Teams 미팅에서 다른 사용자를 추가하거나 제거할 수 없습니다.  

