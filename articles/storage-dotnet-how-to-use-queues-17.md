<properties linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service (2.0)" pageTitle="How to use the queue storage service | Microsoft Azure" metaKeywords="Get started Azure queue, Azure asynchronous processing, Azure queue, Azure queue storage, Azure queue .NET, Azure queue storage .NET, Azure queue C#, Azure queue storage C#" description="Learn how to use the Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="" solutions="" manager="paulettm" editor="cgronlun" />

큐 저장소 서비스를 사용하는 방법
================================

[버전 1.7](/en-us/develop/net/how-to-guides/queue-service-v17/ "버전 1.7") [버전 2.0](/en-us/develop/net/how-to-guides/queue-service/ "버전 2.0")

이 가이드에서는 Azure 큐 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C\# 코드로 작성되었고 .NET API를 사용합니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**와 **큐 만들기 및 삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

-   [큐 저장소 정의](#what-is)
-   [개념](#concepts)
-   [Azure 저장소 계정 만들기](#create-account)
-   [Azure 저장소 연결 문자열 설정](#setup-connection-string)
-   [방법: .NET을 사용하여 프로그래밍 방식으로 큐에 액세스](#access)
-   [방법: 큐 만들기](#create-queue)
-   [방법: 큐에 메시지 삽입](#insert-message)
-   [방법: 다음 메시지 보기](#peek-message)
-   [방법: 대기 중인 메시지의 콘텐츠 변경](#change-contents)
-   [방법: 큐에서 다음 메시지 제거](#get-message)
-   [방법: 큐에서 메시지를 제거하는 추가 옵션 활용](#advanced-get)
-   [방법: 큐 길이 가져오기](#get-queue-length)
-   [방법: 큐 삭제](#delete-queue)
-   [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

계정 만들기Azure 저장소 계정 만들기
-----------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

연결 문자열 설정Azure 저장소 연결 문자열 설정
---------------------------------------------

Azure .NET 저장소 API는 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 저장소 연결 문자열을 코드에 하드 코딩하는 대신 구성 파일에 지정할 수 있습니다.

-   Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
-   Azure 웹 사이트나 Azure 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 가이드의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를 사용하여 연결 문자열을 검색할 수 있습니다.

### 클라우드 서비스를 사용하는 경우 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리 포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나 작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.

	![Blob5](./media/storage-dotnet-how-to-use-queues-17/blob5.png)

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다.

	![Blob6](./media/storage-dotnet-how-to-use-queues-17/blob6.png)

    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다.

	![Blob7](./media/storage-dotnet-how-to-use-queues-17/blob7.png)

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화 상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Azure 저장소)를 대상으로 지정할지 또는 클라우드의 실제 저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 어떤 옵션을 사용하든 작동합니다. 앞에서 Azure에 만든 저장소 계정에 Blob 데이터를 저장하려면 이 자습서의 이전 단계에서 복사한 **기본 액세스 키** 값을 입력합니다.

	![Blob8](./media/storage-dotnet-how-to-use-queues-17/blob8.png)

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 "친근한" 이름으로 변경합니다. 이 가이드의 코드 뒷부분에서 이 연결 문자열을 참조합니다.

	![Blob9](./media/storage-dotnet-how-to-use-queues-17/blob9.png)

### 웹 사이트 또는 가상 컴퓨터를 사용하는 경우 연결 문자열 구성

웹 사이트나 가상 컴퓨터를 사용하는 경우 .NET 구성 시스템(예: `web.config`)을 사용하는 것이 좋습니다. `<appSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

    <configuration>
        <appSettings>
            <add key="StorageConnectionString"
                 value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
        </appSettings>
    </configuration>

저장소 연결 문자열에 대한 자세한 내용은 [연결 문자열 구성](http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx)을 참조하십시오.

이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

프로그래밍 방식으로 액세스방법: .NET을 사용하여 프로그래밍 방식으로 큐에 액세스
-------------------------------------------------------------------------------

프로그래밍 방식으로 Azure 저장소에 액세스하려는 C\# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.StorageClient;

**CloudStorageAccount** 유형과 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

큐 만들기방법: 큐 만들기
------------------------

**CloudQueueClient** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudQueueClient** 개체를 만듭니다. 이 가이드의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다. 자세한 내용은 [CloudStorageAccount](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx) 설명서를 참조하십시오.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 큐가 없는 경우 새로 만들 수 있습니다.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

메시지 삽입방법: 큐에 메시지 삽입
---------------------------------

기존 큐에 메시지를 삽입하려면 먼저 새 **CloudQueueMessage**를 만듭니다. 그런 다음, **AddMessage** 메서드를 호출합니다. **CloudQueueMessage**는 문자열(UTF-8 형식) 또는 **바이트** 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 'Hello, World' 메시지를 삽입하는 코드입니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExist();

    // Create a message and add it to the queue
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

다음 메시지 보기방법: 다음 메시지 보기
--------------------------------------

큐에서 메시지를 제거하지 않고도 **PeekMessage** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

메시지 콘텐츠 변경방법: 대기 중인 메시지의 콘텐츠 변경
------------------------------------------------------

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로, 다시 시도 수도 유지하므로, 메시지가 *n*번 넘게 다시 시도된 경우 메시지를 지울 수도 있습니다. 이 기능은 처리될 때마다 응용 프로그램 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // visible immediately
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

큐에서 다음 메시지 제거방법: 큐에서 다음 메시지 제거
----------------------------------------------------

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **GetMessage**를 호출하면 큐에서 다음 메시지를 가져올 수 있습니다. **GetMessage**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **DeleteMessage**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **DeleteMessage**를 호출합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

큐에서 메시지를 제거하는 추가 옵션방법: 큐에서 메시지를 제거하는 추가 옵션 활용
-------------------------------------------------------------------------------

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **GetMessages** 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 **foreach** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다. 5분은 모든 메시지에 대해 동시에 시작되므로, **GetMessages**에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Do processing for all messages in less than 5 minutes, deleting each message after processing
        queue.DeleteMessage(message);
    }

큐 길이 가져오기방법: 큐 길이 가져오기
--------------------------------------

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **RetrieveApproximateMessageCount** 메서드는 큐에 있는 메시지 수를 계산하도록 큐 서비스에 요청합니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 메시지 수는 근사치일 뿐입니다. **ApproximateMethodCount** 속성은 큐 서비스를 호출하지 않고도 **RetrieveApproximateMessageCount** 메서드를 통해 검색된 마지막 값을 반환합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Retrieve the approximate message count
    int freshMessageCount = queue.RetrieveApproximateMessageCount();

    // Retrieve the cached approximate message count
    int? cachedMessageCount = queue.ApproximateMessageCount;

큐 삭제방법: 큐 삭제
--------------------

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 **Delete** 메서드를 호출합니다.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue
    queue.Delete();

다음 단계
---------

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   사용 가능한 API에 대한 자세한 내용은 큐 서비스 참조 설명서를 참조하십시오.
    -   [.NET 클라이언트 라이브러리 참조](http://msdn.microsoft.com/en-us/library/windowsazure/wl_svchosting_mref_reference_home)
    -   [REST API 참조](http://msdn.microsoft.com/en-us/library/windowsazure/dd179355)
-   Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)를 참조하십시오.
-   Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
    -   [테이블 저장소](/en-us/develop/net/how-to-guides/table-services/)를 사용하여 구조화된 데이터를 저장합니다.
    -   [Blob 저장소](/en-us/develop/net/how-to-guides/blob-storage/)를 사용하여 구조화되지 않은 데이터를 저장합니다.
    -   [SQL 데이터베이스](/en-us/develop/net/how-to-guides/sql-database/)를 사용하여 관계형 데이터를 저장합니다.

