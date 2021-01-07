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
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816837"
---
## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.
- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 
- [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **엔드포인트** 리소스를 기록해야 합니다.
- [사용자 액세스 토큰](../../access-tokens.md). 범위를 "채팅"으로 설정하고 토큰 문자열과 userId 문자열을 기록해 둡니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `ChatQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o ChatQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

.NET용 Azure 통신 채팅 클라이언트 라이브러리 설치

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>개체 모델

다음 클래스는 C#용 Azure Communication Services 채팅 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. 사용자는 ChatClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |

## <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다. `Administration` 클라이언트 라이브러리의 `CommunicationIdentityClient` 클래스를 사용하여 사용자를 만들고 채팅 클라이언트에 전달할 토큰을 발급해야 합니다. [사용자 액세스 토큰](../../access-tokens.md)에 대해 자세히 알아보세요.

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`createChatThread` 메서드를 사용하여 채팅 스레드를 만듭니다.
- `topic`을 사용하여 채팅에 주제를 제공합니다. 주제는 `UpdateThread` 함수를 사용하여 채팅 스레드를 만든 후 업데이트할 수 있습니다.
- `members` 속성을 사용하여 채팅 스레드에 추가할 `ChatThreadMember` 개체 목록을 전달합니다. `ChatThreadMember` 개체가 `CommunicationUser` 개체로 초기화되었습니다. `CommunicationUser` 개체를 가져오려면 [사용자 생성](../../access-tokens.md#create-an-identity) 지침에 따라 만든 액세스 ID를 전달해야 합니다.

`chatThreadClient` 응답은 생성된 채팅 스레드에서 작업(예: 채팅 스레드에 멤버 추가, 메시지 보내기, 메시지 삭제 등)을 수행하는 데 사용됩니다. 여기에는 채팅 스레드의 고유 ID인 `Id` 특성이 포함되어 있습니다. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기
`GetChatThreadClient` 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 작업(예: 멤버 추가, 메시지 보내기 등)을 수행하는 데 사용할 수 있습니다. threadId는 기존 채팅 스레드의 고유 ID입니다.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`SendMessage` 메서드를 사용하여 threadId로 식별되는 스레드에 메시지를 보냅니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다. 이는 필수입니다.
- `priority`를 사용하여 '보통' 또는 '높음' 같은 메시지 우선 순위 수준을 지정합니다. 지정하지 않으면 '보통'이 사용됩니다.
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정하지 않으면 빈 이름이 사용됩니다.

`SendChatMessageResult`는 메시지 전송 후 반환된 응답이며, 메시지의 고유 ID인 id를 포함합니다.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 채팅 스레드 클라이언트에서 `GetMessages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages`는 선택적 `DateTimeOffset` 매개 변수입니다. 해당 오프셋이 지정되면 오프셋 시간 이후에 수신, 업데이트 또는 삭제된 메시지를 받게 됩니다. 오프셋 시간 이전에 받았지만 그 후 편집 또는 제거된 메시지도 반환됩니다.

`GetMessages`는 `UpdateMessage` 및 `DeleteMessage`를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `chatMessage.DeletedOn`은 메시지가 삭제된 시간을 나타내는 datetime 값을 반환합니다. 편집된 메시지의 경우 `chatMessage.EditedOn`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 `chatMessage.CreatedOn`을 사용하여 액세스할 수 있으며 메시지를 정렬하는 데 사용할 수 있습니다.

`GetMessages`는 `chatMessage.Type`으로 식별할 수 있는 다양한 유형의 메시지를 반환합니다. 이러한 유형은 다음과 같습니다.

- `Text`: 스레드 멤버가 보낸 일반 채팅 메시지

- `ThreadActivity/TopicUpdate`: 주제가 업데이트되었음을 나타내는 시스템 메시지

- `ThreadActivity/AddMember`: 한 명 이상의 멤버가 채팅 스레드에 추가되었음을 나타내는 시스템 메시지

- `ThreadActivity/DeleteMember`: 멤버가 채팅 스레드에서 제거되었음을 나타내는 시스템 메시지

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="update-a-message"></a>메시지 업데이트

`ChatThreadClient`에서 `UpdateMessage`를 호출하여 이미 전송된 메시지를 업데이트할 수 있습니다.

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>메시지 삭제

`ChatThreadClient`에서 `DeleteMessage`를 호출하여 메시지를 삭제할 수 있습니다.

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-member-to-the-chat-thread"></a>채팅 스레드에 사용자를 멤버로 추가

스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 스레드에 메시지를 보내고 다른 멤버를 추가/제거할 수 있는 액세스 권한이 해당 사용자에게 부여됩니다. `AddMembers`를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`AddMembers` 메서드를 사용하여 threadId로 식별되는 스레드에 스레드 멤버를 추가합니다.

 - `members`를 사용하여 채팅 스레드에 추가할 멤버를 나열합니다.
 - `User`(필수)는 이 새 사용자를 위해 가져온 ID입니다.
 - `DisplayName`(선택 사항)은 스레드 멤버의 표시 이름입니다.
 - `ShareHistoryTime`(선택 사항)은 채팅 기록이 멤버와 공유된 시간입니다. 채팅 스레드가 시작된 후에 기록을 공유하려면 DateTime.MinValue로 설정합니다. 멤버가 추가되기 전의 기록을 공유하지 않으려면 현재 시간으로 설정합니다. 부분 기록을 공유하려면 스레드 생성 시간과 현재 시간 사이의 특정 시점으로 설정합니다.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

스레드에 사용자를 추가하는 것과 마찬가지로 채팅 스레드에서 사용자를 제거할 수 있습니다. 이렇게 하려면 추가한 멤버의 ID(CommunicationUser)를 추적해야 합니다.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```
