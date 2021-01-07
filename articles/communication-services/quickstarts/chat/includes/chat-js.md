---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376849"
---
## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 
- [Node.js](https://nodejs.org/en/download/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장)을 설치합니다.
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **엔드포인트** 리소스를 기록해야 합니다.
- [사용자 액세스 토큰](../../access-tokens.md)입니다. 범위를 "채팅"으로 설정하고 토큰 문자열과 userId 문자열을 기록해 둡니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-web-application"></a>새 웹 애플리케이션 만들기

먼저 터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **start-chat.js**라는 파일을 만듭니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

### <a name="install-the-packages"></a>패키지 설치

`npm install` 명령을 사용하여 아래 JavaScript용 Communication Services 클라이언트 라이브러리를 설치합니다.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

이 빠른 시작에서는 webpack을 사용하여 애플리케이션 자산을 번들로 묶습니다. 다음 명령을 실행하여 webpack, webpack-cli 및 webpack-dev-server npm 패키지를 설치하고 **package.json**에서 개발 종속성으로 나열합니다.

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

프로젝트의 루트 디렉터리에 **index.html** 파일을 만듭니다. 이 파일을 템플릿으로 사용하여 JavaScript용 Azure Communication Chat 클라이언트 라이브러리를 사용하는 채팅 기능을 추가합니다.

코드는 다음과 같습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
이 빠른 시작에 대한 애플리케이션 로직을 포함하기 위해 **client.js**라는 프로젝트의 루트 디렉터리에 파일을 만듭니다. 

### <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

웹앱에서 채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다. 사용자 액세스 토큰을 사용하면 Azure Communication Services에 직접 인증되는 클라이언트 애플리케이션을 빌드할 수 있습니다. 서버에서 이러한 토큰을 생성한 후 클라이언트 디바이스에 다시 전달합니다. `Common client library`에서 `AzureCommunicationUserCredential` 클래스를 사용하여 채팅 클라이언트에 토큰을 전달해야 합니다.

프로젝트의 루트 디렉터리에 **client.js** 파일을 만듭니다. 이 파일을 사용하여 JavaScript용 Azure Communication Chat 클라이언트 라이브러리를 사용하는 채팅 기능을 추가합니다.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
**ENDPOINT**를 [Azure Communication 리소스 만들기](../../create-communication-resource.md) 설명서에 따라 이전에 만든 것으로 바꿉니다.
**USER_ACCESS_TOKEN**을 [사용자 액세스 토큰](../../access-tokens.md) 설명서에 따라 발급된 토큰으로 바꿉니다.
이 코드를 **client.js** 파일에 추가합니다.


### <a name="run-the-code"></a>코드 실행
`webpack-dev-server`를 사용하여 앱을 빌드하고 실행합니다. 다음 명령을 실행하여 로컬 웹 서버에 애플리케이션 호스트를 번들로 묶습니다.
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
브라우저를 열고 http://localhost:8080/로 이동합니다.
브라우저 내 개발자 도구 콘솔에 다음이 표시됩니다.

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>개체 모델 
다음 클래스 및 인터페이스는 JavaScript용 Azure Communication Services 채팅 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                   | 설명                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. ChatClient를 통해 인스턴스를 확보하여 메시지 보내기/받기/업데이트/삭제, 사용자 추가/제거/받기, 입력 알림 보내기, 읽음 확인, 채팅 이벤트 구독에 사용할 수 있습니다. |


## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`createThread` 메서드를 사용하여 채팅 스레드를 만듭니다.

`createThreadRequest`는 스레드 요청을 설명하는 데 사용됩니다.

- `topic`을 사용하여 채팅에 주제를 제공합니다. 주제는 채팅 스레드를 만든 후 `UpdateThread` 함수를 사용하여 업데이트할 수 있습니다. 
- `members`를 사용하여 채팅 스레드에 추가할 멤버를 나열합니다.

확인되면 `createChatThread` 메서드는 `threadId`를 반환하며, 이것은 새로 생성된 채팅 스레드에서 작업(예: 채팅 스레드에 멤버 추가, 메시지 보내기, 메시지 삭제 등)을 수행하는 데 사용됩니다.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

**USER_ID_FOR_JACK**과 **USER_ID_FOR_GEETA**를 이전 단계(사용자 생성 및 [사용자 액세스 토큰](../../access-tokens.md) 발급)에서 얻은 사용자 ID로 바꿉니다.

브라우저 탭을 새로 고치면 콘솔에 다음이 표시됩니다.
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

`getChatThreadClient` 메서드는 이미 존재하는 스레드의 `chatThreadClient`를 반환합니다. 생성된 스레드에서 작업(예: 멤버 추가, 메시지 보내기 등)을 수행하는 데 사용할 수 있습니다. threadId는 기존 채팅 스레드의 고유 ID입니다.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
**client.js**에서 `<CREATE CHAT THREAD CLIENT>` 주석 대신 이 코드를 추가하고, 브라우저 탭을 새로 고친 후 콘솔을 확인하면 다음이 보입니다.
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`sendMessage` 메서드를 사용하여 방금 만든 스레드에 threadId로 식별되는 채팅 메시지를 보냅니다.

`sendMessageRequest`는 채팅 메시지 요청의 필수 필드를 설명합니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.

`sendMessageOptions`는 채팅 메시지 요청의 선택적 필드를 설명합니다.

- `priority`를 사용하여 '보통' 또는 '높음'과 같은 채팅 메시지 우선 순위 수준을 지정합니다. 이 속성은 앱에서 수신 사용자를 위한 UI 표시기를 구현하여 메시지로 주의를 끌거나 사용자 지정 비즈니스 로직을 실행하는 데 사용할 수 있습니다.   
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정합니다.

응답 `sendChatMessageResult`는 해당 메시지의 고유 ID인 "id"를 포함합니다.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
**client.js**에서 `<SEND MESSAGE TO A CHAT THREAD>` 주석 대신 이 코드를 추가하고, 브라우저 탭을 새로 고친 후 콘솔을 확인합니다.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

실시간 신호를 통해, 새로 들어오는 메시지를 수신 대기하도록 구독하고 메모리의 현재 메시지를 적절하게 업데이트할 수 있습니다. Azure Communication Services는 [구독할 수 있는 이벤트 목록](../../../concepts/chat/concepts.md#real-time-signaling)을 지원합니다.

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
**client.js**에서 `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` 주석 대신 이 코드를 추가합니다.
브라우저 탭을 새로 고치면 콘솔에 `Notification chatMessageReceived` 메시지가 표시됩니다.

또는 지정된 간격으로 `listMessages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
**client.js**에서 `<LIST MESSAGES IN A CHAT THREAD>` 주석 대신 이 코드를 추가합니다.
탭을 새로 고치면 채팅 스레드에서 보낸 메시지 목록을 콘솔에서 찾을 수 있습니다.


`listMessages`는 `updateMessage` 및 `deleteMessage`를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다.
삭제된 메시지의 경우 `chatMessage.deletedOn`은 메시지가 삭제된 시기를 나타내는 날짜/시간 값을 반환합니다. 편집된 메시지의 경우 `chatMessage.editedOn`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 메시지를 정렬하는 데 사용할 수 있는 `chatMessage.createdOn`을 사용하여 액세스할 수 있습니다.

`listMessages`는 `chatMessage.type`으로 식별할 수 있는 다양한 유형의 메시지를 반환합니다. 그 유형은 다음과 같습니다.

- `Text`: 스레드 멤버가 보낸 일반 채팅 메시지

- `ThreadActivity/TopicUpdate`: 주제가 업데이트되었음을 나타내는 시스템 메시지

- `ThreadActivity/AddMember`: 채팅 스레드에 한 명 이상의 멤버가 추가되었음을 나타내는 시스템 메시지

- `ThreadActivity/RemoveMember`: 채팅 스레드에서 멤버가 제거되었음을 나타내는 시스템 메시지

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="add-a-user-as-member-to-the-chat-thread"></a>채팅 스레드에 사용자를 멤버로 추가

채팅 스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 채팅 스레드에 메시지를 보내고 다른 멤버를 추가/제거할 수 있는 액세스 권한이 사용자에게 부여됩니다. `addMembers` 메서드를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`addMembersRequest`는 `members`가 채팅 스레드에 추가될 멤버를 나열하는 요청 개체를 설명합니다.
- `user`(필수)는 채팅 스레드에 추가될 통신 사용자입니다.
- `displayName`(선택 사항)은 스레드 멤버의 표시 이름입니다.
- 선택 사항인 `shareHistoryTime`은 채팅 기록이 멤버와 공유된 시간입니다. 채팅 스레드가 시작된 이후의 기록을 공유하려면 이 속성을 스레드 생성 날짜와 동일한 날짜 또는 그 이전의 날짜로 설정합니다. 멤버가 추가되기 전의 기록을 공유하지 않으려면 현재 날짜로 설정합니다. 일부 기록을 공유하려면 원하는 날짜로 설정합니다.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
**NEW_MEMBER_USER_ID**를 [새 사용자 ID](../../access-tokens.md)로 바꾸고 **client.js**에서 `<ADD NEW MEMBER TO THREAD>` 주석 대신 이 코드를 추가합니다.

## <a name="list-users-in-a-chat-thread"></a>채팅 스레드에 사용자 나열
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
**client.js**에서 `<LIST MEMBERS IN A THREAD>` 주석 대신 이 코드를 추가하고, 브라우저 탭을 새로 고친 후 콘솔을 확인하면 스레드의 사용자에 대한 정보가 보입니다.

## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

멤버를 추가하는 것과 유사하게 채팅 스레드에서 멤버를 제거할 수 있습니다. 제거하려면 추가한 멤버의 ID를 추적해야 합니다.

`removeMember` 메서드를 사용합니다. 여기서 `member`는 스레드에서 제거할 통신 사용자입니다.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
**MEMBER_ID**를 이전 단계에서 사용한 사용자 ID로 바꿉니다(<NEW_MEMBER_USER_ID>).
**client.js**에서 `<REMOVE MEMBER FROM THREAD>` 주석 대신 이 코드를 추가합니다.
