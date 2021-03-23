---
title: 파일 포함
description: 포함 파일
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 2d1c3d3be412f6f11f9d2e300b3a97cf5634f5e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495457"
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

텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 **start-chat.py** 라는 파일을 만들고 기본 예외 처리를 비롯한 프로그램의 구조를 추가합니다. 이 빠른 시작의 모든 소스 코드를 다음 섹션의 이 파일에 추가합니다.

```python
import os
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
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

이 빠른 시작에서는 권장되는 사항이기는 하지만 채팅 애플리케이션에 대한 토큰을 관리하는 서비스 계층을 만드는 방법을 다루지 않습니다. [채팅 아키텍처](../../../concepts/chat/concepts.md)에 대한 자세한 내용은 다음 설명서를 참조하세요.

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`create_chat_thread` 메서드를 사용하여 채팅 스레드를 만듭니다.

- `topic`을 사용하여 스레드 주제를 지정합니다. 주제는 `update_thread` 함수를 사용하여 채팅 스레드를 만든 후 업데이트할 수 있습니다.
- `thread_participants`를 사용하여 채팅 스레드에 추가할 `ChatThreadParticipant`를 나열합니다. `ChatThreadParticipant`는 `user`로 `CommunicationUserIdentifier` 유형을 사용합니다. 이는 [사용자 생성](../../access-tokens.md#create-an-identity)으로 사용자가 생성된 후에 가져올 수 있습니다.
- `repeatability_request_id`를 사용하여 요청이 반복 가능하도록 지시합니다. 클라이언트는 동일한 반복성 요청 ID를 사용하여 요청을 여러 번 수행하고 서버에서 요청을 여러 번 실행하지 않으면서 적절한 응답을 다시 가져올 수 있습니다.

`CreateChatThreadResult`는 스레드를 만들 때 반환되는 결과이며, 생성된 채팅 스레드의 `id`를 페치하는 데 사용할 수 있습니다. 이 `id`는 `get_chat_thread_client` 메서드를 사용하여 `ChatThreadClient` 개체를 페치하는 데 사용할 수 있습니다. `ChatThreadClient`는 이 채팅 스레드에 대해 다른 채팅 작업을 수행하는 데 사용할 수 있습니다.

#### <a name="without-repeatability-request-id"></a>반복성 요청 ID 미사용
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>반복성 요청 ID 사용
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기
`get_chat_thread_client` 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 참가자 추가, 메시지 보내기 등의 작업을 수행하는 데 사용할 수 있습니다. thread_id는 기존 채팅 스레드의 고유 ID입니다.

`ChatThreadClient`는 이 채팅 스레드에 대해 다른 채팅 작업을 수행하는 데 사용할 수 있습니다.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>채팅 스레드 가져오기

`get_chat_thread` 메서드를 통해 서비스에서 `ChatThread`를 검색합니다. `thread_id`는 스레드의 고유 ID입니다.
- 필수 `thread_id`를 사용하여 스레드의 고유 ID를 지정합니다. 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>모든 채팅 스레드 나열
`list_chat_threads` 메서드는 `ChatThreadInfo` 형식의 반복기를 반환합니다. 모든 채팅 스레드를 나열하는 데 사용할 수 있습니다.

- `start_time`을 사용하여 채팅 스레드를 가져올 가장 이른 시간을 지정합니다.
- `results_per_page`를 사용하여 페이지당 반환되는 최대 채팅 스레드 수를 지정합니다.

`[ChatThreadInfo]`의 반복기는 스레드 목록에서 반환된 응답입니다.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>채팅 스레드 삭제
`delete_chat_thread`는 채팅 스레드를 삭제하는 데 사용됩니다.

- `thread_id`를 사용하여 삭제해야 하는 기존 채팅 스레드의 thread_id를 지정합니다.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`send_message` 메서드를 사용하여 방금 만든 채팅 스레드에 thread_id로 식별되는 메시지를 보냅니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.
- `chat_message_type`을 사용하여 메시지 콘텐츠 형식을 지정합니다. 가능한 값은 'text'와 'html'입니다. 지정되지 않는 경우 기본값인 'text'가 할당됩니다.
- `sender_display_name`을 사용하여 보낸 사람의 표시 이름을 지정합니다.

응답은 `str` 형식의 ‘id’로, 메시지의 고유 ID입니다.

#### <a name="message-type-not-specified"></a>메시지 유형이 지정되지 않음
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>메시지 유형이 지정됨
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>채팅 스레드에서 특정 채팅 메시지 가져오기
`get_message` 함수를 사용하여 message_id로 식별되는 특정 메시지를 검색할 수 있습니다.

- `message_id`를 사용하여 메시지 ID를 지정합니다.

`ChatMessage` 형식의 응답에는 단일 메시지와 관련된 모든 정보가 포함됩니다.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 `list_messages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

- `results_per_page`를 사용하여 페이지당 반환되는 최대 메시지 수를 지정합니다.
- `start_time`을 사용하여 메시지를 가져올 가장 이른 시간을 지정합니다.

`[ChatMessage]`의 반복기는 메시지 목록에서 반환된 응답입니다.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages`는 `update_message` 및 `delete_message`를 사용하여 메시지에 발생한 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `ChatMessage.deleted_on`은 메시지가 삭제된 시기를 나타내는 날짜/시간 값을 반환합니다. 편집된 메시지의 경우 `ChatMessage.edited_on`은 메시지가 편집된 시간을 나타내는 datetime을 반환합니다. 원래 메시지 생성 시간은 메시지를 정렬하는 데 사용할 수 있는 `ChatMessage.created_on`을 사용하여 액세스할 수 있습니다.

`list_messages`는 `ChatMessage.type`으로 식별할 수 있는 다양한 유형의 메시지를 반환합니다. 그 유형은 다음과 같습니다.

- `ChatMessageType.TEXT`: 스레드 참가자가 보낸 일반 채팅 메시지입니다.

- `ChatMessageType.HTML`: 스레드 참가자가 보낸 HTML 채팅 메시지입니다.

- `ChatMessageType.TOPIC_UPDATED`: 주제가 업데이트되었음을 나타내는 시스템 메시지입니다.

- `ChatMessageType.PARTICIPANT_ADDED`: 한 명 이상의 참가자가 채팅 스레드에 추가되었음을 나타내는 시스템 메시지입니다.

- `ChatMessageType.PARTICIPANT_REMOVED`: 참가자가 채팅 스레드에서 제거되었음을 나타내는 시스템 메시지입니다.

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="update-topic-of-a-chat-thread"></a>채팅 스레드의 토픽 업데이트
`update_topic` 메서드를 사용하여 채팅 스레드의 토픽을 업데이트할 수 있습니다.

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>메시지 업데이트
`update_message` 메서드를 사용하여 message_id로 식별되는 기존 메시지의 콘텐츠를 업데이트할 수 있습니다.

- 필수 `message_id`는 메시지의 고유 ID입니다.
- 필요에 따라 `content`를 사용하여 업데이트할 메시지 콘텐츠를 지정합니다. 지정하지 않으면 비어 있는 것으로 할당됩니다.

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>메시지 읽음 확인 보내기
`send_read_receipt` 메서드를 사용하여 사용자 대신 스레드에 읽음 확인 이벤트를 게시할 수 있습니다.

- `message_id`를 사용하여 현재 사용자가 읽은 최신 메시지의 ID를 지정합니다.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>채팅 스레드의 읽음 확인 나열
`list_read_receipts` 메서드를 사용하여 스레드에 대한 읽음 확인을 가져올 수 있습니다.

- `results_per_page`를 사용하여 페이지당 반환되는 최대 채팅 메시지 읽음 확인 수를 지정합니다.
- `skip`을 사용하여 응답의 지정된 위치로 채팅 메시지 읽음 확인을 건너뜁니다.

`[ChatMessageReadReceipt]`의 반복기는 읽음 확인 목록에서 반환된 응답입니다.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
```

## <a name="send-typing-notification"></a>입력 알림 보내기
`send_typing_notification` 메서드를 사용하여 사용자 대신 스레드에 입력 이벤트를 게시할 수 있습니다.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>메시지 삭제
`delete_message` 메서드를 사용하여 message_id로 식별되는 메시지를 삭제할 수 있습니다.

- `message_id`를 사용하여 message_id를 지정합니다.

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

채팅 스레드가 생성되면 스레드에서 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 채팅 스레드에 메시지를 보내고 다른 참가자를 추가/제거할 수 있는 액세스 권한이 사용자에게 부여됩니다. `add_participant` 메서드를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자가 채팅 클라이언트를 초기화하려면 액세스 토큰이 필요합니다.

`add_participant` 메서드를 사용하여 thread_id로 식별되는 스레드에 스레드 참가자를 추가합니다.

- `thread_participant`를 사용하여 채팅 스레드에 추가할 참가자를 지정합니다.
- `user`(필수)는 [사용자 생성](../../access-tokens.md#create-an-identity) 시 `CommunicationIdentityClient`로 만든 `CommunicationUserIdentifier`입니다.
- 선택 사항인 `display_name`은 스레드 참가자의 표시 이름입니다.
- 선택 사항인 `share_history_time`은 채팅 기록이 참가자와 공유된 시간입니다. 채팅 스레드가 시작된 이후의 기록을 공유하려면 이 속성을 스레드 생성 날짜와 동일한 날짜 또는 그 이전의 날짜로 설정합니다. 참가자가 추가되기 전의 기록을 공유하지 않으려면 현재 날짜로 설정합니다. 부분 기록을 공유하려면 이를 중간 날짜로 설정합니다.

참가자가 성공적으로 추가되면 오류가 throw되지 않습니다. 참가자를 추가하는 동안 오류가 발생하면 `RuntimeError`가 throw됩니다.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

새 액세스 토큰과 ID가 모든 사용자에게 제공되는 경우 `add_participants` 메서드를 사용하여 여러 사용자를 채팅 스레드에 추가할 수 있습니다.

`list(tuple(ChatThreadParticipant, CommunicationError))`가 반환됩니다. 참가자가 성공적으로 추가되면 빈 목록이 필요합니다. 참가자를 추가하는 동안 오류가 발생한 경우 발생한 오류와 함께 실패한 참가자로 목록이 채워집니다.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

참가자를 추가하는 방법과 유사하게 스레드에서 참가자를 제거할 수 있습니다. 제거하려면 추가한 참가자의 ID를 추적해야 합니다.

`remove_participant` 메서드를 사용하여 threadId로 식별되는 스레드에서 스레드 참가자를 제거합니다.
- `user`는 스레드에서 제거할 `CommunicationUserIdentifier`입니다.

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>코드 실행

`python` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
python start-chat.py
```
