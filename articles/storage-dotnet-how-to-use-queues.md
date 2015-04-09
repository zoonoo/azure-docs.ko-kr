<properties 
	pageTitle=".NET에서 큐 저장소를 사용하는 방법 | Microsoft Azure" 
	description="Microsoft Azure 큐 저장소를 사용하여 큐를 작성 및 삭제하고 큐 메시지를 삽입하고 미리 보고 가져오고 삭제하는 방법을 알아봅니다." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tamram"/>

# .NET에서 큐 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-queue-include](../includes/storage-selector-queue-include.md)]

## 개요

이 가이드에서는 Azure Blob 서비스를 사용하여
일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 C\# 코드로 작성되었으며 Azure Storage Client for .NET을 사용합니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입**,
**보기**, **가져오기** 및 **삭제**와
**큐 만들기 및 삭제**가 포함됩니다. 

> [AZURE.NOTE] 이 가이드는 Azure .NET Storage Client Library 2.x 이상을 대상으로 합니다. 권장되는 버전은 [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/)을 통해 또는 [Azure SDK for .NET](/downloads/)의 일부로 사용할 수 있는 Storage Client Library 4.x입니다. Storage Client Library 가져오기에 대한 자세한 내용은 아래의 [프로그래밍 방식으로 큐 저장소 액세스]를(#programmatically-access-queue-storage) 참조하세요.

[AZURE.INCLUDE [storage-queue-concepts-include](../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../includes/storage-configure-connection-string-include.md)]

## 프로그래밍 방식으로 큐 저장소 액세스

### 어셈블리 가져오기
NuGet을 사용하여 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 가져올 수 있습니다. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.  온라인에서 "WindowsAzure.Storage"를 검색하고 **설치**를 클릭하여 Azure 저장소 패키지와 종속성을 설치합니다.

`Microsoft.WindowsAzure.Storage.dll`은 <a href="http://azure.microsoft.com/develop/net/#">.NET 개발자 센터</a>(영문)에서 다운로드할 수 있는 Azure SDK for .NET에도 포함되어 있습니다. 이 어셈블리는 `%Program Files%\Microsoft SDKs\Azure\.NET SDK\<sdk-version>\ref\` 디렉터리에 설치됩니다.

### 네임스페이스 선언
프로그래밍 방식으로 Azure 저장소에 액세스하려는 C\# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;

 `Microsoft.WindowsAzure.Storage.dll` 어셈블리를 참조해야 합니다.

### 연결 문자열 검색
**CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. Microsoft 
Azure 프로젝트 템플릿을 사용 중이거나 
Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조가 있는 경우 **CloudConfigurationManager** 유형을 사용하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 검색할 수 있습니다.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Microsoft.WindowsAzure.CloudConfigurationManager에 대한 참조 없이 응용 프로그램을 만드는 중이며 위에 표시된 대로 연결 문자열이 `web.config` 또는 `app.config`에 있는 경우 **ConfigurationManager**를 사용하여 연결 문자열을 검색할 수 있습니다.  System.Configuration.dll에 대한 참조를 프로젝트에 추가하고 다른 네임스페이스 선언을 추가해야 합니다.

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

### ODataLib 종속성
Storage Client Library for .NET의 ODataLib 종속성은 WCF 데이터 서비스가 아니라 NuGet을 통해 사용 가능한 ODataLib(버전 5.0.2) 패키지를 통해 확인됩니다.  ODataLib 라이브러리를 직접 다운로드하거나 NuGet을 통해 코드 프로젝트에서 참조할 수 있습니다.  특정 ODataLib 패키지는 [OData], [Edm] 및 [Spatial]입니다.

## 큐 만들기

**CloudQueueClient** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다.
다음 코드는 **CloudQueueClient** 개체를 만듭니다. 이 가이드의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다. 자세한 내용은 [CloudStorageAccount][] 설명서를 참조하세요.

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

## 큐에 메시지 삽입

기존 큐에 메시지를 삽입하려면 먼저 새
**CloudQueueMessage**를 만듭니다. 그런 다음, **AddMessage** 메서드를 호출합니다. 문자열(UTF-8 형식) 또는
**바이트** 배열에서 **CloudQueueMessage**를 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 'Hello, World' 메시지를 삽입하는 코드입니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExist();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## 다음 메시지 보기

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

## 대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 다음 코드는 큐 메시지를 새로운 콘텐츠로 업데이트하고 표시 제한 시간이 60초 더 늘어나도록 설정합니다. 그러면 메시지와 연결된 작업의 상태가 저장되고 클라이언트에서 메시지에 대한 작업을 계속할 수 있는 시간이 1분 더 허용됩니다. 이 기술을 사용하여 처리 단계가 하드웨어 또는 소프트웨어 오류로 인해 실패하는 경우 처음부터 시작하지 않고도 큐 메시지에 대한 여러 단계의 워크플로를 추적할 수 있습니다. 일반적으로 다시 시도 횟수도 유지하므로 메시지가 *n*번 이상 다시 시도된 경우 메시지를 삭제합니다. 이 기능은 처리될 때마다 응용 프로그램 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

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

## 큐에서 다음 메시지 제거

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **GetMessage**를 호출하면
큐에서 다음 메시지를 가져올 수 있습니다. **GetMessage**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **DeleteMessage**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **DeleteMessage**를 호출합니다.

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

## 큐에서 메시지를 제거하는 추가 옵션 활용

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.
먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제에서는
**GetMessages** 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음 **foreach** 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다. 5분은 모든 메시지에 대해 동시에 시작되므로, **GetMessages**에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

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

## 큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **FetchAttributes**
메서드는 메시지 수를 포함하여 큐 특성을 검색하도록 큐 서비스에 요청합니다. **ApproximateMethodCount** 속성은 큐 서비스를 호출하지 않고도
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

## 큐 삭제

큐와 해당 큐에 포함된 모든 메시지를 삭제하려면 큐 개체에서
**Delete** 메서드를 호출합니다.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## 다음 단계

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업에 대해 알아보세요.

<ul>
<li>사용 가능한 API에 대한 자세한 내용은 큐 서비스 참조 설명서를 참조하세요.
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Storage Client Library for .NET 참조</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST API 참조</a></li>
  </ul>
</li>
<li>Azure 저장소를 사용하여 수행할 수 있는 고급 작업에 대한 자세한 내용은 <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Azure에 데이터 저장 및 액세스</a>를 참조하세요.</li>
<li><a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK를 사용하여 Azure 저장소 작업을 위해 작성하는 코드를 간소화하는 방법을 알아봅니다.</li>
<li>Azure에 데이터를 저장하기 위한 추가 옵션에 대한 자세한 내용은 추가 기능 가이드를 참조하세요.
  <ul>
    <li><a href="/documentation/articles/storage-dotnet-how-to-use-tables/">테이블 저장소</a>를 사용하여 구조화된 데이터를 저장합니다.</li>
    <li><a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">Blob 저장소</a>를 사용하여 구조화되지 않은 데이터를 저장합니다.</li>
    <li><a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL 데이터베이스</a>를 사용하여 관계형 데이터를 저장합니다.</li>
  </ul>
</li>
</ul>

  [Azure SDK for .NET 다운로드 및 설치] /develop/net/
  [.NET 클라이언트 라이브러리 참조]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Visual Studio에서 Azure 프로젝트 만들기]: http://msdn.microsoft.com/library/azure/ee405487.aspx 
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
  [연결 문자열 구성]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49-->