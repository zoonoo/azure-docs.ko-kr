<properties  linkid="dev-net-2-how-to-queue-service" urlDisplayName="Queue Service" pageTitle="How to use queue storage from .NET | Microsoft Azure" metaKeywords="Get started Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage   Azure queue .NET   Azure queue storage .NET   Azure queue C#   Azure queue storage C#" description="Learn how to use the Windows Azure queue storage service to create and delete queues and insert, peek, get, and delete queue messages." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title="How to use the Queue Storage Service" authors="tamram" />

# .NET에서 큐 저장소를 사용하는 방법

이 가이드에서는 Azure 큐 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C# 코드로 작성되었으며 Azure Storage Client for .NET을 사용합니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**와 **큐 만들기 및 삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

<h2>목차</h2>


* [큐 저장소 정의](#what-is)
* [개념](#concepts)
* [Azure 저장소 계정 만들기](#create-account)
* [Azure 저장소 연결 문자열 설정](#setup-connection-string)
* [방법: .NET을 사용하여 프로그래밍 방식으로 큐에 액세스](#access)
* [방법: 큐 만들기](#create-queue)
* [방법: 큐에 메시지 삽입](#insert-message)
* [방법: 다음 메시지 보기](#peek-message)
* [방법: 대기 중인 메시지의 콘텐츠 변경](#change-contents)
* [방법: 큐에서 다음 메시지 제거](#get-message)
* [방법: 큐에서 메시지를 제거하는 추가 옵션 활용](#advanced-get)
* [방법: 큐 길이 가져오기](#get-queue-length)
* [방법: 큐 삭제](#delete-queue)
* [다음 단계](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-queue-storage.md)]

<h2><a name="create-account"></a><span  class="short-header">계정 만들기</span>Azure 저장소 계정 만들기</h2>


[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

<h2><a name="setup-connection-string"></a><span  class="short-header">연결 문자열 설정</span>Azure 저장소 연결 문자열 설정</h2>


Azure Storage Client Library for .NET은 저장소 연결 문자열을 사용하여 저장소 서비스에 액세스하기 위한 끝점 및 자격 증명을 구성하는 작업을 지원합니다. 저장소 연결 문자열을 코드에 하드 코딩하는 대신 구성 파일에 지정할 수 있습니다.

* Azure 클라우드 서비스를 사용하는 경우 Azure 서비스 구성 시스템(`*.csdef` 및 `*.cscfg` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.
* Azure 웹 사이트 또는 Azure 가상 컴퓨터를 사용하거나 Azure 외부에서 실행하려는 .NET 응용 프로그램을 빌드하는 경우 .NET 구성 시스템(예: `web.config` 또는 `app.config` 파일)을 사용하여 연결 문자열을 저장하는 것이 좋습니다.

두 경우 모두, 이 가이드의 뒷부분에 표시된 대로 `CloudConfigurationManager.GetSetting` 메서드를 사용하여 연결 문자열을 검색할 수 있습니다.

### 클라우드 서비스를 사용하는 경우 연결 문자열 구성

서비스 구성 메커니즘은 Azure 클라우드 서비스 프로젝트에 고유하며, 응용 프로그램을 다시 배포하지 않고도 Azure 관리 포털에서 구성 설정을 동적으로 변경할 수 있게 해 줍니다.

Azure 서비스 구성에서 연결 문자열을 구성하려면

1.  Visual Studio의 솔루션 탐색기 내에서 Azure 배포 프로젝트의 **역할** 폴더에 있는 해당 웹 역할이나 작업자 역할을 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.
    
    ![Blob5](./media/storage-dotnet-how-to-use-queues-20/blob5.png)

2.  **설정** 탭을 클릭하고 **설정 추가** 단추를 누릅니다.
    
    ![Blob6](./media/storage-dotnet-how-to-use-queues-20/blob6.png)
    
    새로운 **Setting1** 항목이 설정 그리드에 표시됩니다.

3.  새로운 **Setting1** 항목의 **유형** 드롭다운에서 **연결 문자열**을 선택합니다.
    
    ![Blob7](./media/storage-dotnet-how-to-use-queues-20/blob7.png)

4.  **Setting1** 항목의 오른쪽 끝에 있는 **...** 단추를 클릭합니다. **저장소 계정 연결 문자열** 대화 상자가 열립니다.

5.  저장소 에뮬레이터(로컬 컴퓨터에서 시뮬레이트된 Azure 저장소)를 대상으로 지정할지 또는 클라우드의 실제 저장소 계정을 대상으로 지정할지 선택합니다. 이 가이드의 코드는 두 옵션 중 어떤 옵션을 사용하든 작동합니다. 앞에서 Azure에 만든 저장소 계정에 큐 데이터를 저장하려면 이 자습서의 이전 단계에서 복사한 **기본 액세스 키** 값을 입력합니다.
    
    ![Blob8](./media/storage-dotnet-how-to-use-queues-20/blob8.png)

6.  **이름** 항목을 **Setting1**에서 **StorageConnectionString** 등의 "친근한" 이름으로 변경합니다. 이 가이드의 코드 뒷부분에서 이 연결 문자열을 참조합니다.
    
    ![Blob9](./media/storage-dotnet-how-to-use-queues-20/blob9.png)

### .NET 구성을 사용하여 연결 문자열 구성

Azure 클라우드 서비스가 아닌 응용 프로그램을 작성하는 경우(이전 섹션 참조) .NET 구성 시스템(예:`web.config` 또는 `app.config`)을 사용하는 것이 좋습니다. 여기에는 Azure 웹 사이트 또는 Azure 가상 컴퓨터와 Azure 외부에서 실행되도록 설계된 응용 프로그램이 포함됩니다. 다음과 같이 `<a ppSettings>` 요소를 사용하여 연결 문자열을 저장합니다.

	<configuration>
  		<a ppSettings>
    		<a dd key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey" />
  		</appSettings>
	</configuration>

 저장소 연결 문자열에 대한 자세한 내용은 [연결 문자열 구성][1]을 참조하십시오.

 이제 이 가이드의 방법 작업을 수행할 준비가 되었습니다.

 <h2><a name="access"></a><span  class="short-header">프로그래밍 방식으로 액세스</span>방법: .NET을 사용하여 프로그래밍 방식으로 큐에 액세스</h2>


 <h3>어셈블리 가져오기</h3>


 NuGet을 사용하여 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져올 수 있습니다. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. 온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

 `Microsoft.WindowsAzure.Storage.dll`은 [.NET 개발자 센터][2](영문)에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다. 어셈블리는 `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk>\ref\` 디렉터리에 설치됩니다.

 <h3>네임스페이스 선언</h3>


 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

 <h3>연결 문자열 검색</h3>


**CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Azure 프로젝트 템플릿을 사용 중이거나 Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조가 있는 경우
**CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열 및 저장소 계정 정보를 다음과 같이 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

 Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는
중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우
**ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다.
System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

    using System.Configuration;
    ...
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

 <h3>ODataLib 종속성</h3>


 Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을
통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다. ODataLib 라이브러리를 직접 다운로드하거나
NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다. 특정 ODataLib 패키지는 [OData][3], [Edm][4] 및
[Spatial][5]입니다.

 <h2><a name="create-queue"></a><span  class="short-header">큐 만들기</span>방법: 큐 만들기</h2>


 **CloudQueueClient** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는
**CloudQueueClient** 개체를 만듭니다. 이 가이드의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된
저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다. 자세한 내용은
[CloudStorageAccount][6] 설명서를 참조하십시오.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

 **queueClient** 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 큐가 없는 경우 새로 만들 수
있습니다.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

 <h2><a name="insert-message"> </a><span  class="short-header">메시지 삽입</span>방법: 큐에 메시지 삽입</h2>


 기존 큐에 메시지를 삽입하려면 먼저 새 **CloudQueueMessage**를 만듭니다. 그런 다음, **AddMessage**
메서드를 호출합니다. **CloudQueueMessage**는 문자열(UTF-8 형식) 또는 **바이트** 배열에서 만들 수
있습니다. 다음은 큐가 없는 경우 새로 만들고 \'Hello, World\' 메시지를 삽입하는 코드입니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();
    
    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

 <h2><a name="peek-message"></a><span  class="short-header">다음 메시지 보기</span>방법: 다음 메시지 보기</h2>


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
    
    // Display message.
    Console.WriteLine(peekedMessage.AsString);

 <h2><a name="change-contents"></a><span  class="short-header">메시지 콘텐츠 변경</span>방법: 대기 중인 메시지의 콘텐츠 변경</h2>


 큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수
있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면
메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을
사용하면 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의
워크플로를 추적할 수 있습니다. 일반적으로, 다시 시도 수도 유지하므로, 메시지가 *n*번 넘게 다시 시도된 경우 메시지를 지울
수도 있습니다. 이 기능은 처리될 때마다 응용 프로그램 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.") ;
    queue.UpdateMessage(message, 
        TimeSpan.FromSeconds(0.0),  // Make it visible immediately.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

 <h2><a name="get-message"></a><span  class="short-header">큐에서 다음 메시지 제거</span>방법: 큐에서 다음 메시지 제거</h2>


 다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **GetMessage**를 호출하면 큐에서 다음 메시지를 가져올 수
있습니다. **GetMessage**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이
메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **DeleteMessage**도 호출해야 합니다. 메시지를
제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가
동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **DeleteMessage**를
호출합니다.

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

 <h2><a name="advanced-get"></a><span  class="short-header">큐에서 메시지를 제거하는 추가 옵션</span>방법: 큐에서 메시지를 제거하는 추가 옵션 활용</h2>


 큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수
있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을
늘리거나 줄일 수 있습니다. 다음 코드 예제는 **GetMessages** 메서드를 사용하여 한 번 호출에 20개의 메시지를
가져옵니다. 그런 다음에 **foreach** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한
시간을 5분으로 설정합니다. 5분은 모든 메시지에 대해 동시에 시작되므로, **GetMessages**에 대한 호출 이후로 5분이
지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

 <h2><a name="get-queue-length"></a><span  class="short-header">큐 길이 가져오기</span>방법: 큐 길이 가져오기</h2>


 큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **FetchAttributes** 메서드는 메시지 수를 포함하여 큐
특성을 검색하도록 큐 서비스에 요청합니다. **ApproximateMethodCount** 속성은 큐 서비스를 호출하지 않고도
**FetchAttributes** 메서드를 통해 검색된 마지막 값을 반환합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Fetch the queue attributes.
    queue.FetchAttributes();
    
    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;
    
    // Display number of messages.
    Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

 <h2><a name="delete-queue"></a><span  class="short-header">큐 삭제</span>방법: 큐 삭제</h2>


 큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 **Delete** 메서드를 호출합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));
    
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    
    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");
    
    // Delete the queue.
    queue.Delete();

 <h2><a name="next-steps"></a>다음 단계</h2>


 이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

 *  사용 가능한 API에 대한 자세한 내용은 큐 서비스 참조 설명서를 참조하십시오.
  *  [Storage Client Library for .NET 참조][7]
  *  [REST API 참조][8]
  

*  Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 [Azure에 데이터 저장 및 액세스][9]를 참조하십시오.
*  Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하십시오.
  *  [테이블 저장소](/en-us/develop/net/how-to-guides/table-services/)를 사용하여 구조화된 데이터를 저장합니다.
  *  [Blob 저장소](/en-us/develop/net/how-to-guides/blob-storage/)를 사용하여 구조화되지 않은 데이터를 저장합니다.
  *  [SQL 데이터베이스](/en-us/develop/net/how-to-guides/sql-database/)를 사용하여 관계형 데이터를 저장합니다.
  


 </appsettings>



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/ee758697.aspx
[2]: http://www.windowsazure.com/en-us/develop/net/#
[3]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
[4]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[5]: http://nuget.org/packages/System.Spatial/5.0.2
[6]: http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.cloudstorageaccount_methods.aspx
[7]: http://msdn.microsoft.com/en-us/library/windowsazure/dn495001(v=azure.10).aspx
[8]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179355
[9]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx