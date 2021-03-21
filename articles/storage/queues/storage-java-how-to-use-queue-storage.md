---
title: Java에서 Queue Storage를 사용 하는 방법-Azure Storage
description: Queue Storage를 사용 하 여 큐를 만들고 삭제 하는 방법에 대해 알아봅니다. Java 용 Azure Storage 클라이언트 라이브러리를 사용 하 여 메시지를 삽입, 피킹, 가져오기 및 삭제 하는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 997a37ac4252813abf1b35877cd34e192ec3e2ae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585720"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java에서 Queue Storage를 사용 하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>개요

이 가이드에서는 Azure Queue Storage 서비스를 사용 하는 일반적인 시나리오를 코딩 하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure Storage SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)(영문)를 사용합니다. 시나리오에는 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제** 가 포함 됩니다. 큐를 **만들고** **삭제 하** 는 코드도 다룹니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기

# <a name="java-v12"></a>[Java v12](#tab/java)

먼저 개발 시스템이 [Azure Queue Storage client library v12 For Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)에 나열 된 필수 구성 요소를 충족 하는지 확인 합니다.

이라는 Java 응용 프로그램을 만들려면 `queues-how-to-v12` 다음을 수행 합니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 Maven을 사용하여 `queues-how-to-v12`라는 새 콘솔 앱을 만듭니다. 다음 `mvn` 명령을 입력하여 "hello world" Java 프로젝트를 만듭니다.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.howto `
        --define artifactId=queues-howto-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

1. 프로젝트를 생성하는 출력은 다음과 같습니다.

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.howto
    [INFO] Parameter: groupId, Value: com.queues.howto
    [INFO] Parameter: artifactId, Value: queues-howto-v12
    [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.775 s
    [INFO] Finished at: 2020-08-17T15:27:31-07:00
    [INFO] ------------------------------------------------------------------------
        ```

1. Switch to the newly created `queues-howto-v12` directory.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 `pom.xml` 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8"></a>[Java v8](#tab/java8)

먼저, 개발 시스템이 [AZURE STORAGE SDK For Java v8](https://github.com/azure/azure-storage-java)에 나열 된 필수 구성 요소를 충족 하는지 확인 합니다. Java 용 Azure Storage 라이브러리를 다운로드 하 고 설치 하는 방법에 대 한 지침을 따르세요. 그런 다음이 문서의 예제를 사용 하 여 Java 응용 프로그램을 만들 수 있습니다.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 애플리케이션 구성

Azure Storage Api를 사용 하 여 큐에 액세스 하려는 Java 파일의 맨 위에 다음 import 문을 추가 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 설정

Azure Storage 클라이언트는 데이터 관리 서비스에 액세스 하기 위해 저장소 연결 문자열을 사용 합니다. [Azure Portal](https://portal.azure.com)에 나열 된 저장소 계정에 대 한 이름 및 기본 선택 키를 가져옵니다. `AccountName` `AccountKey` 연결 문자열에서 및 값으로 사용 합니다. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

이라는 서비스 구성 파일에이 문자열을 저장할 수 있습니다 `ServiceConfiguration.cscfg` . Microsoft Azure 역할 내에서 실행 되는 앱의 경우를 호출 하 여 연결 문자열에 액세스 합니다 `RoleEnvironment.getConfigurationSettings` . 다음은 라는 요소에서 연결 문자열을 가져오는 예제입니다 `Setting` `StorageConnectionString` .

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

다음 샘플에서는 저장소 연결 문자열을 포함 하는 개체가 있다고 가정 합니다 `String` .

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기

# <a name="java-v12"></a>[Java v12](#tab/java)

`QueueClient`개체는 큐와 상호 작용 하기 위한 작업을 포함 합니다. 다음 코드에서는 개체를 만듭니다 `QueueClient` . 개체를 사용 하 여 `QueueClient` 사용 하려는 큐를 만듭니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

`CloudQueueClient`개체를 사용 하면 큐에 대 한 참조 개체를 가져올 수 있습니다. 다음 코드는 사용 하려는 `CloudQueueClient` 큐에 대 한 참조를 제공 하는 개체를 만듭니다. 큐가 없는 경우 새로 만들 수 있습니다.

> [!NOTE]
> 개체를 만드는 다른 방법이 있습니다 `CloudStorageAccount` . 자세한 내용은 `CloudStorageAccount` [AZURE STORAGE 클라이언트 SDK 참조](https://azure.github.io/azure-sdk-for-java/storage.html)에서를 참조 하세요.)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>큐에 메시지 추가 방법

# <a name="java-v12"></a>[Java v12](#tab/java)

기존 큐에 메시지를 삽입 하려면 메서드를 호출 `sendMessage` 합니다. 메시지는 문자열 (UTF-8 형식) 또는 바이트 배열 중 하나일 수 있습니다. 다음은 문자열 메시지를 큐로 보내는 코드입니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

기존 큐에 메시지를 삽입 하려면 먼저 새를 만듭니다 `CloudQueueMessage` . 그런 다음 메서드를 호출 `addMessage` 합니다. 는 `CloudQueueMessage` 문자열 (utf-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 큐를 만들고 (없는 경우) 메시지를 삽입 하는 코드는 다음과 같습니다 `Hello, World` .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 보기

를 호출 하 여 큐에서 메시지를 제거 하지 않고 큐의 맨 앞에 있는 메시지를 볼 수 있습니다 `peekMessage` .

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업 태스크를 나타내는 경우이 기능을 사용 하 여 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새 콘텐츠로 업데이트 하 고 표시 제한 시간을 30 초 연장 하도록 설정 합니다. 표시 제한 시간을 확장 하면 클라이언트는 메시지에 대 한 작업을 계속 하는 데 30 초 정도 걸립니다. 다시 시도 횟수도 유지할 수 있습니다. 메시지를 *n* 번 넘게 다시 시도 하면 해당 메시지를 삭제 합니다. 이 시나리오는 처리 될 때마다 응용 프로그램 오류를 트리거하는 메시지를 방지 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 코드 샘플에서는 메시지 큐를 검색 하 고, 검색 문자열과 일치 하는 첫 번째 메시지 내용을 찾고, 메시지 내용을 수정 하 고, 종료 합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

다음 코드 샘플에서는 메시지 큐를 검색 하 고, 일치 하는 첫 번째 메시지 내용을 찾고, `Hello, world` 메시지 내용을 수정 하 고, 종료 합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

다음 코드 샘플에서는 큐에 표시 되는 첫 번째 메시지만 업데이트 합니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

`getProperties`메서드는 현재 큐에 있는 메시지 수를 포함 하 여 여러 값을 반환 합니다. 요청 후에 메시지를 추가 하거나 제거할 수 있으므로이 수는 근사값입니다. `getApproximateMessageCount`메서드는 Queue Storage를 호출 하지 않고 호출을 통해 검색 된 마지막 값을 반환 `getProperties` 합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

`downloadAttributes`메서드는 현재 큐에 있는 메시지 수를 포함 하 여 여러 값을 검색 합니다. 요청 후에 메시지를 추가 하거나 제거할 수 있으므로이 수는 근사값입니다. `getApproximateMessageCount`메서드는 Queue Storage를 호출 하지 않고 호출을 통해 검색 된 마지막 값을 반환 `downloadAttributes` 합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>큐에서 다음 메시지를 제거하는 방법

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. 를 호출 하면 `receiveMessage` 큐에서 다음 메시지를 가져옵니다. `receiveMessage`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료 하려면도 호출 해야 `deleteMessage` 합니다. 코드에서 메시지를 처리 하지 못하는 경우이 두 단계 프로세스를 통해 동일한 메시지를 받고 다시 시도할 수 있습니다. 코드 `deleteMessage` 는 메시지가 처리 된 직후에 호출 됩니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. 를 호출 하면 `retrieveMessage` 큐에서 다음 메시지를 가져옵니다. `retrieveMessage`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료 하려면도 호출 해야 `deleteMessage` 합니다. 코드에서 메시지를 처리 하지 못하는 경우이 두 단계 프로세스를 통해 동일한 메시지를 받고 다시 시도할 수 있습니다. 코드 `deleteMessage` 는 메시지가 처리 된 직후에 호출 됩니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>큐에서 메시지를 제거하는 추가 옵션

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저 메시지 일괄 처리 (최대 32)를 가져옵니다. 둘째, 더 길거나 짧은 표시 안 함 시간 제한을 설정 하 여 코드에서 각 메시지를 완전히 처리 하는 시간을 늘리거나 줄입니다.

# <a name="java-v12"></a>[Java v12](#tab/java)

다음 코드 예제는 `receiveMessages` 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분(300초)으로 설정합니다. 모든 메시지에 대 한 시간 제한은 동시에 시작 됩니다. 를 호출한 후 5 분이 경과 되 면 `receiveMessages` 삭제 되지 않은 모든 메시지가 다시 표시 됩니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

다음 코드 예제는 `retrieveMessages` 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분(300초)으로 설정합니다. 모든 메시지에 대 한 시간 제한은 동시에 시작 됩니다. 를 호출한 후 5 분이 경과 되 면 `retrieveMessages` 삭제 되지 않은 모든 메시지가 다시 표시 됩니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>큐 나열하는 방법

# <a name="java-v12"></a>[Java v12](#tab/java)

현재 큐의 목록을 가져오려면 메서드를 호출 합니다 `QueueServiceClient.listQueues()` .이 메서드는 개체의 컬렉션을 반환 `QueueItem` 합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

현재 큐의 목록을 가져오려면 메서드를 호출 합니다 `CloudQueueClient.listQueues()` .이 메서드는 개체의 컬렉션을 반환 `CloudQueue` 합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제

# <a name="java-v12"></a>[Java v12](#tab/java)

큐 및 해당 큐에 포함 된 모든 메시지를 삭제 하려면 `delete` 개체에서 메서드를 호출 합니다 `QueueClient` .

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8"></a>[Java v8](#tab/java8)

큐 및 해당 큐에 포함 된 모든 메시지를 삭제 하려면 `deleteIfExists` 개체에서 메서드를 호출 합니다 `CloudQueue` .

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>다음 단계

Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡 한 저장소 작업에 대해 알아보세요.

- [Java용 Azure Storage SDK](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure Storage 클라이언트 SDK 참조](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure Storage services REST API](/rest/api/storageservices/)
- [Azure Storage 팀 블로그](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
