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
ms.openlocfilehash: 3c05596e16edc5243b8a97002a5cc5990c69ec43
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945624"
---
## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요. 
- [Python 설치](https://www.python.org/downloads/)
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에 대한 **엔드포인트** 리소스를 기록해야 합니다.
- [사용자 액세스 토큰](../../access-tokens.md). 범위를 "채팅"으로 설정하고 토큰 문자열과 userId 문자열을 기록해 둡니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir chat-quickstart && cd chat-quickstart
```

텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **start-chat.py**라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>클라이언트 라이브러리 설치

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Python용 Azure Communication Services 채팅 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                  | 설명                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient | 이 클래스는 채팅 스레드 기능에 필요합니다. 사용자는 ChatClient를 통해 인스턴스를 가져오고, 이를 사용하여 메시지 전송/수신/업데이트/삭제, 사용자 추가/제거/가져오기, 입력 알림 및 읽음 확인 보내기를 수행할 수 있습니다. |

## <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 `Access Token`을 사용합니다. [사용자 액세스 토큰](../../access-tokens.md)에 대해 자세히 알아보세요.

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`create_chat_thread` 메서드를 사용하여 채팅 스레드를 만듭니다.

- `topic`을 사용하여 스레드 주제를 지정합니다. 주제는 `update_thread` 함수를 사용하여 채팅 스레드를 만든 후 업데이트할 수 있습니다.
- `members`를 사용하여 채팅 스레드에 추가할 `ChatThreadMember`를 나열합니다. `ChatThreadMember`는 `user`로 `CommunicationUser` 유형을 사용합니다. 이는 [사용자 생성](../../access-tokens.md#create-a-user)으로 사용자가 생성된 후에 가져올 수 있습니다.

`chat_thread_client` 응답은 새로 생성된 채팅 스레드에서 작업(예: 채팅 스레드에 멤버 추가, 메시지 보내기, 메시지 삭제 등)을 수행하는 데 사용됩니다. 여기에는 채팅 스레드의 고유 ID인 `thread_id` 속성이 포함되어 있습니다.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기
get_chat_thread_client 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 작업(예: 멤버 추가, 메시지 보내기 등)을 수행하는 데 사용할 수 있습니다. thread_id는 기존 채팅 스레드의 고유 ID입니다.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`send_message` 메서드를 사용하여 방금 만든 채팅 스레드에 threadId로 식별되는 메시지를 보냅니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.
- `priority`를 사용하여 '보통' 또는 '높음'과 같은 메시지 우선 순위 수준을 지정합니다. 이 속성은 앱에서 수신 사용자를 위한 UI 표시기를 구현하여 메시지로 주의를 끌거나 사용자 지정 비즈니스 로직을 실행하는 데 사용할 수 있습니다.
- `senderDisplayName`을 사용하여 보낸 사람의 표시 이름을 지정합니다.

응답 `SendChatMessageResult`는 해당 메시지의 고유 ID인 "id"를 포함합니다.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 `list_messages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages`는 `update_message` 및 `delete_message`를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `ChatMessage.deleted_on`은 메시지가 삭제된 시기를 나타내는 날짜/시간 값을 반환합니다. 편집된 메시지의 경우 `ChatMessage.edited_on`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 메시지를 정렬하는 데 사용할 수 있는 `ChatMessage.created_on`을 사용하여 액세스할 수 있습니다.

`list_messages`는 `ChatMessage.type`으로 식별할 수 있는 다양한 유형의 메시지를 반환합니다. 이러한 유형은 다음과 같습니다.

- `Text`: 스레드 멤버가 보낸 일반 채팅 메시지

- `ThreadActivity/TopicUpdate`: 주제가 업데이트되었음을 나타내는 시스템 메시지

- `ThreadActivity/AddMember`: 한 명 이상의 멤버가 채팅 스레드에 추가되었음을 나타내는 시스템 메시지

- `ThreadActivity/DeleteMember`: 멤버가 채팅 스레드에서 제거되었음을 나타내는 시스템 메시지

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="add-a-user-as-member-to-the-chat-thread"></a>채팅 스레드에 사용자를 멤버로 추가

채팅 스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 채팅 스레드에 메시지를 보내고 다른 멤버를 추가/제거할 수 있는 액세스 권한이 해당 사용자에게 부여됩니다. `add_members` 메서드를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`add_members` 메서드를 사용하여 threadId로 식별되는 스레드에 스레드 멤버를 추가합니다.

- `members`를 사용하여 채팅 스레드에 추가할 멤버를 나열합니다.
- `user`(필수)는 [사용자 생성](../../access-tokens.md#create-a-user) 시 `CommunicationIdentityClient`로 만든 `CommunicationUser`입니다.
- `display_name`(선택 사항)은 스레드 멤버의 표시 이름입니다.
- `share_history_time`(선택 사항)은 채팅 기록이 멤버와 공유된 시간입니다. 채팅 스레드가 시작된 이후의 기록을 공유하려면 이 속성을 스레드 생성 날짜와 동일한 날짜 또는 그 이전의 날짜로 설정합니다. 멤버가 추가되기 전의 기록을 공유하지 않으려면 현재 날짜로 설정합니다. 부분 기록을 공유하려면 이를 중간 날짜로 설정합니다.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

멤버를 추가하는 것과 마찬가지로 스레드에서 멤버를 제거할 수도 있습니다. 제거하려면 추가한 멤버의 ID를 추적해야 합니다.

`remove_member` 메서드를 사용하여 threadId로 식별되는 스레드에서 스레드 멤버를 제거합니다.
- `user`는 스레드에서 제거할 CommunicationUser입니다.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>코드 실행

`python` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
python start-chat.py
```
