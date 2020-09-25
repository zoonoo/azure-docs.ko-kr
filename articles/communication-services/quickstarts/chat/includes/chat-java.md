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
ms.openlocfilehash: f105fd3d3d760d4173c5a271b1bb16942a634dfd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945632"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK(Java Development Kit)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true), 버전 8 이상.
- [Apache Maven](https://maven.apache.org/download.cgi).
- 배포된 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스 만들기](../../create-communication-resource.md)
- [사용자 액세스 토큰](../../access-tokens.md). 범위를 "채팅"으로 설정하고 토큰 문자열과 userId 문자열을 기록해 둡니다.


## <a name="setting-up"></a>설치

### <a name="create-a-new-java-application"></a>새 Java 애플리케이션 만들기

터미널 또는 명령 창을 열고 Java 애플리케이션을 만들 디렉터리로 이동합니다. 아래 명령을 실행하여 maven-archetype-quickstart 템플릿에서 Java 프로젝트를 생성합니다.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

'생성' 목표에서 artifactId와 동일한 이름으로 디렉터리를 만들었습니다. 이 디렉터리 아래의 `src/main/java directory` 디렉터리는 프로젝트 소스 코드를 포함하고, `src/test/java` 디렉터리는 테스트 원본을 포함하고, pom.xml 파일은 프로젝트의 [POM(프로젝트 개체 모델)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)입니다.

Java 8 이상을 사용하도록 애플리케이션의 POM 파일을 업데이트합니다.

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-client-library"></a>채팅 클라이언트 라이브러리에 대한 패키지 참조 추가

POM 파일에서 채팅 API를 사용하여 `azure-communication-chat` 패키지를 참조합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.1</version> 
</dependency>
```

인증을 위해 클라이언트가 `azure-communication-common` 패키지를 참조해야 합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.1</version> 
</dependency>

```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Java용 Azure Communication Services 채팅 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatAsyncClient | 이 클래스는 비동기 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. 사용자는 ChatClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |
| ChatThreadAsyncClient | 이 클래스는 비동기 채팅 스레드 기능에 필요합니다. 사용자는 ChatAsyncClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |

## <a name="create-a-chat-client"></a>채팅 클라이언트 만들기
채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다. 사용자 액세스 토큰을 사용하면 Azure Communication Service에 직접 인증되는 클라이언트 애플리케이션을 빌드할 수 있습니다. 서버에서 이러한 토큰을 생성한 후 클라이언트 디바이스에 다시 전달합니다. 공통 클라이언트 라이브러리의 CommunicationUserCredential 클래스를 사용하여 토큰을 채팅 클라이언트에 전달해야 합니다. 

Import 문을 추가할 때 com.azure.communication.chat 및 com.azure.communication.chat.models namespaces의 import 문만 추가하고 com.azure.communication.chat.implementation 네임스페이스의 import 문은 추가하지 마세요. Maven을 통해 생성된 App.java 파일에서 다음 코드를 사용하여 다음 작업을 시작할 수 있습니다.

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");
        
        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```


## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`createChatThread` 메서드를 사용하여 채팅 스레드를 만듭니다.
`createChatThreadOptions`는 스레드 요청을 설명하는 데 사용됩니다.

- `topic`을 사용하여 이 채팅의 주제를 제공합니다. 주제는 `UpdateThread` 함수를 사용하여 채팅 스레드를 만든 후 업데이트할 수 있습니다.
- `members`를 사용하여 채팅 스레드에 추가할 멤버를 나열합니다. `ChatThreadMember`는 [사용자 액세스 토큰](../../access-tokens.md) 빠른 시작에서 만든 사용자를 가져옵니다.

`chatThreadClient` 응답은 생성된 채팅 스레드에서 작업(예: 채팅 스레드에 멤버 추가, 메시지 보내기, 메시지 삭제 등)을 수행하는 데 사용됩니다. 여기에는 채팅 스레드의 고유 ID인 `chatThreadId` 속성이 포함되어 있습니다. 이 속성은 공용 메서드 .getChatThreadId()에서 액세스할 수 있습니다.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`sendMessage` 메서드를 사용하여 chatThreadId로 식별되는 방금 만든 스레드에 채팅 메시지를 보냅니다.
`sendChatMessageOptions`는 채팅 메시지 요청을 설명하는 데 사용됩니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.
- `priority`를 사용하여 '보통' 또는 '높음'과 같은 채팅 메시지 우선 순위 수준을 지정합니다. 이 속성은 앱에서 수신 사용자를 위한 UI 표시기를 구현하여 메시지로 주의를 끌거나 사용자 지정 비즈니스 로직을 실행하는 데 사용할 수 있습니다.
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정합니다.

응답 `sendChatMessageResult`에는 메시지의 고유 ID인 `id`가 포함됩니다.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

`getChatThreadClient` 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 멤버 추가, 메시지 보내기 등의 작업을 수행하는 데 사용할 수 있습니다. `chatThreadId`는 기존 채팅 스레드의 고유 ID입니다.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 채팅 스레드 클라이언트에서 `listMessages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages`는 .editMessage() 및 .deleteMessage()를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `chatMessage.getDeletedOn()`은 메시지가 삭제된 시간을 나타내는 datetime 값을 반환합니다. 편집된 메시지의 경우 `chatMessage.getEditedOn()`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 `chatMessage.getCreatedOn()`을 사용하여 액세스할 수 있으며 메시지를 정렬하는 데 사용할 수 있습니다.

`listMessages`는 `chatMessage.getType()`으로 식별할 수 있는 다양한 유형의 메시지를 반환합니다. 그 유형은 다음과 같습니다.

- `Text`: 스레드 멤버가 보낸 일반 채팅 메시지

- `ThreadActivity/TopicUpdate`: 주제가 업데이트되었음을 나타내는 시스템 메시지

- `ThreadActivity/AddMember`: 채팅 스레드에 한 명 이상의 멤버가 추가되었음을 나타내는 시스템 메시지

- `ThreadActivity/DeleteMember`: 채팅 스레드에서 멤버가 제거되었음을 나타내는 시스템 메시지

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="add-a-user-as-member-to-the-chat-thread"></a>채팅 스레드에 사용자를 멤버로 추가

채팅 스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 채팅 스레드에 메시지를 보내고 다른 멤버를 추가/제거할 수 있는 액세스 권한이 사용자에게 부여됩니다. 해당 사용자의 새 액세스 토큰과 ID를 가져오는 것부터 시작해야 합니다. addMembers 메서드를 호출하기 전에, 해당 사용자의 새 액세스 토큰과 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`addMembers` 메서드를 사용하여 threadId로 식별되는 스레드에 스레드 멤버를 추가합니다.

- `members`를 사용하여 채팅 스레드에 추가할 멤버를 나열합니다.
- 필수인 `user`는 [사용자 액세스 토큰](../../access-tokens.md) 빠른 시작에서 CommunicationIdentityClient를 통해 만든 CommunicationUser입니다.
- 선택 사항인 `display_name`은 스레드 멤버의 표시 이름입니다.
- 선택 사항인 `share_history_time`은 채팅 기록이 멤버와 공유된 시간입니다. 채팅 스레드가 시작된 이후의 기록을 공유하려면 이 속성을 스레드 생성 날짜와 동일한 날짜 또는 그 이전의 날짜로 설정합니다. 멤버가 추가되기 전의 기록을 공유하지 않으려면 현재 날짜로 설정합니다. 일부 기록을 공유하려면 필요한 날짜로 설정합니다.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

스레드에 사용자를 추가하는 것과 마찬가지로 채팅 스레드에서 사용자를 제거할 수 있습니다. 사용자를 제거하려면 추가한 멤버의 사용자 ID를 추적해야 합니다.

`removeMember`를 사용합니다. 여기서 `user`는 이전에 만든 CommunicationUser입니다.

```Java
chatThreadClient.removeMember(user);
```

## <a name="run-the-code"></a>코드 실행

*pom.xml* 파일이 포함된 디렉터리로 이동한 후 다음 `mvn` 명령을 사용하여 프로젝트를 컴파일합니다.

```console
mvn compile
```

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 실행하여 앱을 실행합니다.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
