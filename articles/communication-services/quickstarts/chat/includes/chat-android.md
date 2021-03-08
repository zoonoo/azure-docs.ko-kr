---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 021abce5c6cd83257ad65f529833848d8f14f534
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750477"
---
## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Android Studio](https://developer.android.com/studio)를 설치합니다. Android Studio를 사용하여 종속성을 설치하기 위한 빠른 시작용 Android 애플리케이션을 만듭니다.
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [Azure Communication 리소스 만들기](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에서는 **리소스 엔드포인트를 기록** 해야 합니다.
- **2명** 의 Communication Services 사용자를 만들고 사용자 액세스 토큰 [사용자 액세스 토큰](../../access-tokens.md)을 발급합니다. 범위를 **채팅** 으로 설정하고 **토큰 문자열과 userId 문자열을 기록** 해 둡니다. 이 빠른 시작에서는 초기 참가자가 있는 스레드를 만든 다음, 두 번째 참가자를 스레드에 추가합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-android-application"></a>새 Android 애플리케이션 만들기

1. Android Studio를 열고 `Create a new project`를 선택합니다. 
2. 다음 창에서 프로젝트 템플릿으로 `Empty Activity`를 선택합니다.
3. 옵션을 선택할 때 프로젝트 이름에 `ChatQuickstart`를 입력합니다.
4. 다음을 클릭하고 프로젝트를 만들려는 디렉터리를 선택합니다.

### <a name="install-the-libraries"></a>라이브러리 설치

Gradle을 사용하여 필요한 Communication Services 종속성을 설치합니다. 명령줄에서 `ChatQuickstart` 프로젝트의 루트 디렉터리 안으로 이동합니다. 앱의 build.gradle 파일을 열고 `ChatQuickstart` 대상에 다음 종속성을 추가합니다.

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.6'
```

#### <a name="exclude-meta-files-in-packaging-options"></a>패키징 옵션에서 메타 파일 제외
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

Android Studio에서 '지금 동기화'를 클릭합니다.

#### <a name="alternative-to-install-libraries-through-maven"></a>(대안) Maven을 통해 라이브러리를 설치하려면
[Maven](https://maven.apache.org/) 빌드 시스템을 사용하여 프로젝트로 라이브러리를 가져오려면 아티팩트 ID와 사용할 버전을 지정하여 앱의 `pom.xml` 파일에 있는 `dependencies` 섹션에 라이브러리를 추가합니다.

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.6</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>자리 표시자 설정

`MainActivity.java` 파일을 열고 편집합니다. 이 빠른 시작에서는 `MainActivity`에 코드를 추가하고 콘솔에서 출력을 확인합니다. 이 빠른 시작은 UI 빌드를 다루지 않습니다. 파일 맨 위에서 `Communication common` 및 `Communication chat` 라이브러리를 가져옵니다.

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

다음 코드를 `MainActivity` 파일에 복사합니다.

```java
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.6";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

다음 단계에서는 Azure Communication Services 채팅 라이브러리를 사용하여 자리 표시자를 샘플 코드로 바꿉니다.


### <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

`<CREATE A CHAT CLIENT>` 주석을 다음 코드로 바꿉니다(파일 맨 위에 import 문 배치).

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. `ChatAsyncClient.Builder`를 사용하여 `ChatAsyncClient`의 인스턴스를 구성하고 작성합니다.
2. `<resource>`을 Communication Services 리소스로 바꿉니다.
3. `<user_access_token>`을 올바른 Communication Services 액세스 토큰으로 바꿉니다.

## <a name="object-model"></a>개체 모델
다음 클래스 및 인터페이스는 JavaScript용 Azure Communication Services 채팅 클라이언트 라이브러리의 주요 기능 중 일부를 처리합니다.

| 이름                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| ChatThreadClient/ChatThreadAsyncClient | 이 클래스는 채팅 스레드 기능에 필요합니다. ChatClient를 통해 인스턴스를 확보하여 메시지 보내기/받기/업데이트/삭제, 사용자 추가/제거/받기, 입력 알림 보내기, 읽음 확인, 채팅 이벤트 구독에 사용할 수 있습니다. |

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`ChatClient`를 사용하여 초기 사용자로 새 스레드를 만듭니다.

`<CREATE A CHAT THREAD>` 주석을 다음 코드로 바꿉니다.

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setId(id)
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

`<user_id>`를 올바른 Communication Services 사용자 ID로 바꿉니다. 이후 단계에서 완료 처리기로 반환된 응답의 `threadId`를 사용하므로 클래스의 `<thread_id>`를 이 요청에서 얻은 `threadId`로 바꾸고 앱을 다시 실행합니다.

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

이제 채팅 스레드를 만들었으므로 스레드 내에서 작업을 수행하기 위해 `ChatThreadClient`를 가져옵니다. `<CREATE A CHAT THREAD CLIENT>` 주석을 다음 코드로 바꿉니다.

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`<thread_id>`를 유효한 스레드 ID로 바꾸었는지 확인하세요. 이제 해당 스레드로 메시지를 보냅니다.

`<SEND A MESSAGE>` 주석을 다음 코드로 바꿉니다.

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
        .setType(ChatMessageType.TEXT)
        .setContent(content)
        .setSenderDisplayName(senderDisplayName);

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

`chatMessageId`를 가져온 후 나중에 빠른 시작에서 메서드 사용을 위해 `<chat_message_id>`를 `chatMessageId`로 바꾸고 앱을 다시 실행할 수 있습니다.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

`<ADD A USER>` 주석을 다음 코드로 바꿉니다.

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setId(second_user_id).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

클래스의 `<second_user_id>`를 추가할 사용자의 Communication Services 사용자 ID로 바꿉니다. 

## <a name="list-users-in-a-thread"></a>스레드에 사용자 나열

`<LIST USERS>` 주석을 다음 코드로 바꿉니다.

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

클래스에 다음과 같은 도우미 메서드를 삽입합니다.

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>채팅 스레드에서 사용자 제거

`<second_user_id>`를 유효한 사용자 ID로 바꾸어야 합니다. 이제 스레드에서 두 번째 사용자를 제거하겠습니다.

`<REMOVE A USER>` 주석을 다음 코드로 바꿉니다.

```java
threadClient.removeChatParticipant(threadId, second_user_id, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>입력 알림 보내기

`<SEND A TYPING NOTIFICATION>` 주석을 다음 코드로 바꿉니다.

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>읽음 확인 보내기

`<chat_message_id>`를 유효한 채팅 메시지 ID로 바꾸어야 합니다. 이제 이 메시지에 대한 읽음 확인을 보내겠습니다.

`<SEND A READ RECEIPT>` 주석을 다음 코드로 바꿉니다.

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>읽음 확인 나열

`<READ RECEIPTS>` 주석을 다음 코드로 바꿉니다.

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

클래스에 다음과 같은 도우미 메서드를 삽입합니다.
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>코드 실행

Android Studio에서 실행 단추를 눌러 프로젝트를 빌드하고 실행합니다. 콘솔에서 코드의 출력과 ChatClient의 로거 출력을 볼 수 있습니다.
