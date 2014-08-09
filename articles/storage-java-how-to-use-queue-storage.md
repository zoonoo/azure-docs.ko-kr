<properties linkid="dev-net-how-to-use-queue-storage-service-java" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Java) | Microsoft Azure" metaKeywords="Azure Queue Service, Azure Queue storage service, queues peeking, queues insert messages, queues get messages, queues delete messages, create queues, delete queues, Queue service Java" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Java." metaCanonical="" services="storage" documentationCenter="Java" title="How to use the Queue storage service from Java" authors="" solutions="" manager="" editor="" />

Java에서 큐 저장소 서비스를 사용하는 방법
=========================================

이 가이드에서는 Azure 큐 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure SDK](http://www.windowsazure.com/ko-kr/develop/java/)(영문)를 사용합니다. 여기서 다루는 시나리오에는 큐 메시지 삽입, 보기, 가져오기 및 삭제와 큐 만들기 및 삭제가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

목차
----

-   [큐 저장소 정의](#what-is)
-   [개념](#Concepts)
-   [Azure 저장소 계정 만들기](#CreateAccount)
-   [Java 응용 프로그램 만들기](#CreateApplication)
-   [큐 저장소에 액세스하도록 응용 프로그램 구성](#ConfigureStorage)
-   [Azure 저장소 연결 문자열 설정](#ConnectionString)
-   [방법: 큐 만들기](#create-queue)
-   [방법: 큐에 메시지 추가](#add-message)
-   [방법: 다음 메시지 보기](#peek-message)
-   [방법: 큐에서 다음 메시지 제거](#dequeue-message)
-   [방법: 대기 중인 메시지의 콘텐츠 변경](#change-message)
-   [큐에서 메시지를 제거하는 추가 옵션](#additional-options)
-   [방법: 큐 길이 가져오기](#get-queue-length)
-   [방법: 큐 삭제](#delete-queue)
-   [다음 단계](#NextSteps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

Azure 저장소 계정 만들기
------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Java 응용 프로그램 만들기
-------------------------

이 가이드에서는 Java 응용 프로그램 내에서 로컬로 또는 Azure의 웹 역할 또는 작업자 역할 내에서 실행되는 코드에서 실행할 수 있는 저장소 기능을 사용합니다. JDK(Java 개발 키트)를 다운로드하여 설치했으며 [Java용 Azure SDK 다운로드](http://www.windowsazure.com/ko-kr/develop/java/)(영문)의 지침에 따라 Java용 Azure 라이브러리 및 Azure SDK를 설치하고 Azure 구독에서 Azure 저장소 계정을 만들었다고 가정합니다. 응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다. 또한 Java 프로젝트를 컴파일하고 Java용 Azure 라이브러리를 참조할 수 있어야 합니다.

큐 저장소에 액세스하도록 응용 프로그램 구성
-------------------------------------------

Azure 저장소 API를 사용하여 큐에 액세스하려는 Java 파일의 맨 위에 다음 import 문을 추가합니다.

    // Include the following imports to use queue APIs
    import com.microsoft.windowsazure.services.core.storage.*;
    import com.microsoft.windowsazure.services.queue.client.*;

Azure 저장소 연결 문자열 설정
-----------------------------

Azure 저장소 클라이언트는 저장소 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하는 데 사용할 끝점 및 자격 증명을 저장합니다. 클라이언트 응용 프로그램에서 실행 중인 경우 관리 포털에 나열된 저장소 계정의 이름 및 저장소 계정의 기본 액세스 키를 *AccountName* 및 *AccountKey* 값에 사용하여 저장소 연결 문자열을 다음 형식으로 지정해야 합니다. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

    // Define the connection-string with your values
    public static final String storageConnectionString = 
        "DefaultEndpointsProtocol=http;" + 
        "AccountName=your_storage_account;" + 
        "AccountKey=your_storage_account_key";

Azure의 역할 내에서 실행되는 응용 프로그램에서, 이 문자열은 서비스 구성 파일인 ServiceConfiguration.cscfg에 저장할 수 있으며 RoleEnvironment.getConfigurationSettings 메서드에 대한 호출을 통해 액세스될 수 있습니다. 다음은 서비스 구성 파일에서 이름이 *StorageConnectionString*인 **설정** 요소에서 연결 문자열을 가져오는 예제입니다.

    // Retrieve storage account from connection-string
    String storageConnectionString = 
        RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");

방법: 큐 만들기
---------------

CloudQueueClient 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 CloudQueueClient 개체를 만듭니다.

이 가이드의 모든 코드는 위에서 보여 준 두 가지 방법 중 하나로 선언한 저장소 연결 문자열을 사용합니다. CloudStorageAccount 개체를 만드는 다른 방법도 있습니다. 자세한 내용은 CloudStorageAccount의 Javadocs 설명서를 참조하십시오.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

CloudQueueClient 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 큐가 없는 경우 새로 만들 수 있습니다.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

방법: 큐에 메시지 추가
----------------------

기존 큐에 메시지를 삽입하려면 먼저 새 CloudQueueMessage를 만듭니다. 그런 다음, addMessage 메서드를 호출합니다. CloudQueueMessage는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 "Hello, World" 메시지를 삽입하는 코드입니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.createIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);

방법: 다음 메시지 보기
----------------------

큐에서 메시지를 제거하지 않고도 peekMessage를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.peekMessage();

방법: 큐에서 다음 메시지 제거
-----------------------------

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. retrieveMessage를 호출하면 큐에서 다음 메시지를 가져올 수 있습니다. retrieveMessage에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 deleteMessage도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 deleteMessage를 호출합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    // Process the message in less than 30 seconds, and then delete the message.
    queue.deleteMessage(retrievedMessage);

방법: 대기 중인 메시지의 콘텐츠 변경
------------------------------------

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로, 다시 시도 수도 유지하므로, 메시지가 n번 넘게 다시 시도된 경우 메시지를 지울 수도 있습니다. 이 기능은 처리될 때마다 응용 프로그램 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue
    CloudQueueMessage message = queue.retrieveMessage();

    // Modify the message content and set it to be visible in 60 seconds
    message.setMessageContent("Updated contents.");
    EnumSet<MessageUpdateFields> updateFields = 
        EnumSet.of(MessageUpdateFields.CONTENT, MessageUpdateFields.VISIBILITY);
    queue.updateMessage(message, 60, updateFields, null, null);

큐에서 메시지를 제거하는 추가 옵션
----------------------------------

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

다음 코드 예제는 retrieveMessages 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 **for** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분(300초)으로 설정합니다. 5분은 모든 메시지에 대해 동시에 시작되므로, retrieveMessages에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes, 
        // deleting each message after processing.
        queue.deleteMessage(message);
    }

방법: 큐 길이 가져오기
----------------------

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. downloadAttributes 메서드는 큐에 있는 메시지 수를 포함한 일부 현재 값을 큐 서비스에 요청합니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 메시지 수는 근사치일 뿐입니다. getApproximateMethodCount 메서드는 큐 서비스를 호출하지 않고도 downloadAttributes를 호출하여 검색된 마지막 값을 반환합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Download the approximate message count from the server
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count
    long cachedMessageCount = queue.getApproximateMessageCount();

방법: 큐 삭제
-------------

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 delete 메서드를 호출합니다.

    // Retrieve storage account from connection-string
    CloudStorageAccount storageAccount = 
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue
    queue.delete();

다음 단계
---------

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-	다음 MSDN 참조를 확인하십시오. http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx
-   다음 Azure 저장소 팀 블로그(영문)를 방문하십시오. http://blogs.msdn.com/b/windowsazurestorage/

