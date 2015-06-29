<properties 
	pageTitle="Azure 저장소 시작" 
	description="Visual Studio의 ASP.NET 5 프로젝트에서 Azure 큐 저장소를 사용하는 방법" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="patshea123"/>

# Azure 저장소 시작(ASP.NET 5 프로젝트)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet5-getting-started-queues.md)
> - [What Happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

Azure 큐 저장소는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 저장소 계정의 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다. 자세한 내용은 [.NET에서 큐 저장소를 사용하는 방법](storage-dotnet-how-to-use-queues.md/ ".NET에서 큐 저장소를 사용하는 방법(영문)")(영문)을 참조하세요.

ASP.NET 5 프로젝트에서 큐에 프로그래밍 방식으로 액세스하려면 다음 항목(아직 없는 경우)을 추가해야 합니다.

1. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. 다음 코드를 사용하여 구성 설정을 가져옵니다.

		 IConfigurationSourceRoot config = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####저장소 연결 문자열 가져오기
큐를 가지고 작업을 수행하려면, 큐가 저장될 저장소 계정에 대한 연결 문자열을 가져와야 합니다. **CloudStorageAccount** 유형을 사용하여 저장소 계정 정보를 나타낼 수 있습니다. ASP.NET 5 프로젝트를 사용하는 경우 다음 코드에 나온 대로 구성 개체의 get 메서드를 호출하여 Azure 서비스 구성에서 저장소 연결 문자열과 저장소 계정 정보를 가져올 수 있습니다.

**참고:** ASP.NET 5에서 Azure 저장소에 대한 호출을 수행하는 API는 비동기적입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](http://msdn.microsoft.com/library/hh191443.aspx)을 참조하세요. 아래 코드에서는 비동기 프로그래밍 메서드를 사용한다고 가정합니다.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####큐 만들기
**CloudQueueClient** 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 **CloudQueueClient** 개체를 만듭니다. 이 항목의 모든 코드는 Azure 응용 프로그램의 서비스 구성에 저장된 저장소 연결 문자열을 사용합니다. **CloudStorageAccount** 개체를 만드는 다른 방법도 있습니다. 자세한 내용은 [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") 설명서를 참조하세요.

**참고:** ASP.NET 5에서 Azure 저장소에 대한 호출을 수행하는 API는 비동기적입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](http://msdn.microsoft.com/library/hh191443.aspx)을 참조하세요. 아래 코드에서는 비동기 프로그래밍 메서드를 사용한다고 가정합니다.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

**queueClient** 개체를 사용하여 사용할 큐에 대한 참조를 가져올 수 있습니다. 코드가 “myqueue”라는 이름의 큐를 참조하려고 합니다. 이 이름의 큐를 찾을 수 없으면 새로 만듭니다.

	// Get a reference to a queue named “myqueue”.
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn’t already there, then create it.
	await queue.CreateIfNotExistsAsync();

**참고:** 다음 섹션의 코드 앞에서 이 코드를 모두 사용합니다.

#####큐에 메시지 삽입
기존 큐에 메시지를 삽입하려면 먼저 새 **CloudQueueMessage** 개체를 만듭니다. 그런 다음, AddMessageAsync() 메서드를 호출합니다. **CloudQueueMessage** 개체는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 새로 만들고 'Hello, World' 메시지를 삽입하는 코드입니다.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####다음 메시지 보기
큐에서 메시지를 제거하지 않고도 PeekMessageAsync() 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다.

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####다음 메시지 제거
이 코드에서는 2단계를 거쳐 큐에서 메시지를 제거할 수 있습니다.


1. GetMessageAsync()를 호출하여 큐에서 다음 메시지를 가져옵니다. GetMessageAsync()에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 
2.	큐에서 메시지 제거를 완료하려면 DeleteMessageAsync()를 호출합니다. 

메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 다음 코드에서는 메시지가 처리된 직후에 DeleteMessageAsync()를 호출합니다.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Azure 저장소에 대한 자세한 정보](http://azure.microsoft.com/documentation/services/storage/) [서버 탐색기에서 저장소 리소스 탐색](http://msdn.microsoft.com/library/azure/ff683677.aspx) 및 [ASP.NET 5](http://www.asp.net/vnext)도 참조하세요.
 

<!---HONumber=58_postMigration-->