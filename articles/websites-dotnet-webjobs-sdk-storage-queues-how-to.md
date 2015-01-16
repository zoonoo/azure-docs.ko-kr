<properties linkid="" pageTitle="WebJobs SDK를 사용하여 Azure 큐 저장소 작업을 하는 방법" metaKeywords="WebJobs SDK Azure 큐 저장소 .NET C#" description="WebJobs SDK를 사용하여 Azure 큐 저장소 작업을 하는 방법에 대해 알아봅니다. 큐 만들기 및 삭제, 큐 메시지 삽입, 미리 보기, 가져오기 및 삭제 등의 작업을 알아봅니다." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# WebJobs SDK를 사용하여 Azure 큐 저장소 작업을 하는 방법

이 가이드에서는 Azure 큐 저장소 서비스와 Azure WebJobs SDK 버전
1.0.0을 사용하여 일반적인 시나리오에 대한 C# 코드를 작성하는 방법을 보여 줍니다.

이 가이드에서는 사용자가 이미 [Webjobs SDK가 무엇인지](../websites-webjobs-sdk-storage-queues-how-to), WebJobs SDK NuGet 패키지 설치, Azure 저장소 계정 만들기, 저장소 계정을 가리키는 WebJobs SDK용 연결 문자열 만들기 등의 [기본 작업을 하는 방법](../websites-dotnet-webjobs-sdk-get-started/)을 알고 있다고 가정합니다.

대부분 코드 조각은 이 예제와 같이 'JobHost' 개체를 만드는 코드가 아니라 함수만을 보여 줍니다.

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 목차

-   [큐 메시지를 받을 때 함수를 트리거하는 방법](#trigger)
	- 문자열 큐 메시지
	- POCO 개체 큐 메시지
	- Async 함수
	- 폴링 알고리즘
	- 병렬 실행
	- 큐 또는 큐 메시지 메타데이터 가져오기
	- 정상 종료
-   [큐 메시지를 처리하는 동안 큐 메시지를 만드는 방법](#createqueue)
	- 문자열 큐 메시지
	- POCO 개체 큐 메시지
	- 여러 메시지 만들기
	- 함수 본문에서 큐 특성 사용
-   [큐 메시지를 처리하는 동안 Blob을 읽고 쓰는 방법](#blobs)
	- 문자열 큐 메시지
	- POCO 개체 큐 메시지
	- 함수 본문에서 Blob 특성 사용
-   [포이즌 메시지를 처리하는 방법](#poison)
	- 자동 포이즌 메시지 처리
	- 수동 포이즌 메시지 처리
-   [구성 옵션을 설정하는 방법](#config)
	- 코드에서 SDK 연결 문자열 설정
	- QueueTrigger 설정 구성
	- 구성에서 큐 이름 가져오기
-   [수동으로 함수를 트리거하는 방법](#manual)
-   [로그를 기록하는 방법](#logs)
-   [다음 단계](#nextsteps)

## <a id="trigger"></a> 큐 메시지를 받을 때 함수를 트리거하는 방법

큐 메시지를 받을 때 SDK가 호출하는 함수를 작성하려면 메시지에서 가져올 것으로 예상하는 대상에 따라 문자열이나 POCO 매개 변수와 함께 'QueueTrigger' 특성을 사용합니다. 특성 생성자는 폴링할 큐의 이름을 지정하는 문자열 매개 변수를 사용합니다. [큐 이름을 동적으로 설정](#config)할 수도 있습니다.

* 웹 사이트가 여러 VM에서 실행되는 경우 WebJob은 각 컴퓨터에서 실행되고, 각 컴퓨터는 트리거를 기다렸다가 함수 실행을 시도합니다. 일부 시나리오에서는 이로 인해 일부 함수가 동일한 데이터를 두 번 처리하게 될 수 있으므로 함수는 idempotent로, 같은 입력 데이터로 반복 호출해도 중복된 결과가 나오지 않도록 작성되어야 합니다.  

### 문자열 큐 메시지

다음 예제에서는 큐에 문자열 메시지가 포함되므로 큐 메시지의 콘텐츠를 포함하는 ' logMessage'라는 문자열 매개 변수에 'QueueTrigger'가 적용됩니다. 함수는 [로그 메시지를 대시보드에 씁니다](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

'string' 외에도 매개 변수는 바이트 배열, 'CloudQueueMessage' 개체 또는 사용자가 정의한 POCO 개체일 수 있습니다.

### POCO 개체 큐 메시지

다음 예제에서 큐 메시지에는 'BlobName' 속성을 포함하는 'BlobInformation' 개체에 대한 JSON이 포함됩니다. SDK는 자동으로 개체를 역직렬화합니다.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

다음 예제에는 WebJobs SDK를 사용하지 않고 SDK가 구문 분석할 수 있는 POCO 큐 메시지를 만드는 방법을 보여 줍니다. SDK에서는 [Newtonsoft.Json NuGet 패키지](http://www.nuget.org/packages/Newtonsoft.Json)를 사용하여 메시지를 직렬화 및 역직렬화합니다.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Async 함수

다음 async 함수는 [로그를 대시보드에 씁니다](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Blob을 복사하는 다음 예제와 같이 async 함수는 취소 토큰을 사용할 수 있습니다. `queueTrigger` 자리 표시자에 대한 자세한 내용은 [Blob](#blobs) 섹션을 참조하세요.

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### 폴링 알고리즘

SDK는 무작위 지수 백오프 알고리즘을 구현하여 유휴 큐 폴링이 저장소 트랜잭션 비용에 미치는 영향을 줄입니다.  메시지가 발견되면 SDK는 2초 대기하고 다른 메시지가 있는지 확인하며, 메시지가 발견되지 않으면 4초 정도 대기하고 나서 다시 시도합니다. 후속 시도로 큐 메시지를 가져오지 못하면 최대 대기 시간(기본값 1분)에 도달할 때까지 대기 시간이 계속 증가합니다. [최대 대기 시간은 구성할 수 있습니다](#config).

### 병렬 실행

여러 함수가 서로 다른 큐에서 수신 대기하면 SDK는 메시지를 동시에 받을 때 함수를 병렬로 호출합니다. 

단일 큐에 대한 여러 메시지를 받을 때도 마찬가지입니다. 기본적으로 SDK는 한 번에 16개의 큐 메시지를 일괄 처리로 가져오고 나서 이 큐 메시지를 병렬로 처리하는 함수를 실행합니다. [일괄 처리 크기는 구성할 수 있습니다](#config). 처리 중인 개수가 일괄 처리 크기의 절반으로 감소하면 SDK는 또 다른 일괄 처리 큐 메시지를 가져오고 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리 중인 최대 동시 메시지 수는 일괄 처리 크기의 1.5배입니다. 이 제한은 'QueueTrigger' 특성이 있는 각 함수에 개별적으로 적용됩니다. 하나의 큐에 수신된 메시지를 병렬로 실행하지 않으려면 일괄 처리 크기를 1로 설정합니다.

### <a id="queuemetadata"></a>큐 또는 큐 메시지 메타데이터 가져오기

메서드 서명에 매개 변수를 추가하여 다음 메시지 속성을 가져올 수 있습니다.

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger(메시지 텍스트 포함)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Azure 저장소 API에서 직접 작업하려면 'CloudStorageAccount' 매개 변수를 추가하면 됩니다.

다음 예제에서는 이 메타데이터를 모두 INFO 응용 프로그램 로그에 씁니다. 예제에서 logMessage 및 queueTrigger에는 모두 큐 메시지의 내용이 포함됩니다.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

샘플 코드로 기록된 샘플 로그는 다음과 같습니다.

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>정상 종료

연속 WebJob으로 실행되는 함수는 WebJob이 종료되려고 할 때 운영 체제에서 이를 함수에 알릴 수 있는 'CancellationToken' 매개 변수를 수락할 수 있습니다. 이 알림을 사용하면 데이터를 일관성 없는 상태로 남겨 두는 방식으로 함수가 예기치 않게 종료하지 않도록 할 수 있습니다.

다음 예제에서는 함수에서 WebJob이 곧 종료할 것인지 확인하는 방법을 보여 줍니다.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**참고:** 대시보드에는 종료된 함수의 상태와 출력이 제대로 표시되지 않을 수 있습니다.
 
자세한 내용은 [WebJob 정상 종료](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)(영문)를 참조하세요.   

## <a id="createqueue"></a> 큐 메시지를 처리하는 동안 큐 메시지를 만드는 방법

새 큐 메시지를 만드는 함수를 작성하려면 출력 문자열이나 POCO 매개 변수에서 'Queue' 특성을 사용합니다. `QueueTrigger`와 마찬가지로 큐 이름을 문자열로 전달하거나 [큐 이름을 동적으로 설정](#config)할 수 있습니다.

### 문자열 큐 메시지

다음 예제에서는 "inputqueue" 큐에 수신된 큐 메시지와 같은 내용이 포함된 새 큐 메시지를 "outputqueue" 큐에서 만듭니다.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

출력 매개 변수 형식이 다음 형식 중 하나이고 함수가 종료될 때 개체가 null이 아니면 SDK에서 큐 메시지를 만듭니다.

* `string` 
* `byte[]`
* 사용자가 정의한 직렬화 가능 POCO 유형
* `CloudQueueMessage`

여러 메시지를 만들려면 이 섹션의 뒷부분에서 **여러 메시지 만들기**를 참조하세요.

메시지를 수동으로 보내려는 경우 'CloudQueue'를 출력 매개 변수 형식으로 사용할 수도 있습니다.

### POCO 개체 큐 메시지

문자열 대신 POCO 개체를 포함하는 큐 메시지를 만들려면 POCO 유형을 출력 매개 변수로 'Queue' 특성 생성자에 전달합니다. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK가 개체를 JSON에 자동으로 직렬화합니다. 개체가 null이더라도 큐 메시지는 항상 만들어집니다.

### 여러 메시지 만들기

여러 메시지를 만들려면 `ICollector<T>` 또는 `IAsyncCollector` 출력 큐에 대한 매개 변수 형식을 다음 예제와 같이 만듭니다.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

각 큐 메시지는 'Add' 메서드가 호출되면 바로 만들어집니다.

### <a id="queuebody"></a>함수 본문에서 큐 특성 사용

`Queue` 특성을 사용하여 큐 메시지를 만들기 전에 함수에서 몇몇 작업을 해야 하면 'IBinder' 인터페이스를 사용하여 직접 큐를 사용할 수 있도록 하는 'CloudQueue' 개체를 가져올 수 있습니다. 

다음 예제에서는 입력 큐 메시지를 사용하고 출력 큐에 같은 내용이 포함된 새 메시지를 만듭니다. 출력 큐 이름은 함수 본문의 코드를 통해 설정됩니다.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> 큐 메시지를 처리하는 동안 Blob 및 테이블을 읽고 쓰는 방법

'Blob' 및 'Table' 특성을 사용하여 Blob과 테이블을 읽고 쓸 수 있습니다. 이 섹션의 샘플은 Blob에 적용됩니다.

'Blob' 특성과 함께 사용할 수 있는 몇 가지 형식에는 `Stream`, `TextReader`, `TextWriter`, `CloudBlockBlob`이 포함됩니다. 특성 생성자는 컨테이너 및 Blob 이름을 지정하는 'blobPath' 매개 변수를 사용하고, 특성이 `Stream` 개체를 장식하면 또 다른 생성자 매개 변수가 'FileAccess' 모드를 읽기, 쓰기 또는 읽기/쓰기로 지정합니다. Blob을 개체에 바인드하기 전에 함수에서 몇 가지 작업을 해야 하면 [앞의 Queue 특성처럼](#queuebody) 함수 본문에서 특성을 사용합니다.

### 문자열 큐 메시지

문자열을 포함하는 큐 메시지의 경우 'queueTrigger'는 메시지의 내용을 포함하는 'Blob' 특성의 'blobPath' 매개 변수에서 사용할 수 있는 자리 표시자입니다. 

다음 예제에서는 'Stream' 개체를 사용하여 Blob을 읽고 씁니다. 큐 메시지는 textblobs 컨테이너에 있는 Blob의 이름을 제공합니다. 같은 컨테이너에는 이름에 "-new"가 추가된 Blob 복사본이 만들어집니다. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

다음 예제에서는 'CloudBlockBlob' 개체를 사용하여 Blob을 삭제합니다.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### POCO 개체 큐 메시지

큐 메시지에 JSON으로 저장된 POCO 개체의 경우 'Queue' 특성의 'blobPath' 매개 변수에서 개체 속성의 이름을 지정하는 자리 표시자를 사용할 수 있습니다. [큐 메타데이터 속성 이름](#queuemetadata)을 자리 표시자로 사용할 수도 있습니다. 

다음 예제에서는 Blob을 확장명이 다른 새 Blob으로 복사하여 'BlobInformation' 개체의 속성을 통해 입력 및 출력 Blob의 이름을 지정합니다. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

다음 예제에는 WebJobs SDK를 사용하지 않고 SDK가 구문 분석할 수 있는 POCO 큐 메시지를 만드는 방법을 보여 줍니다. SDK에서는 [Newtonsoft.Json NuGet 패키지](http://www.nuget.org/packages/Newtonsoft.Json)를 사용하여 메시지를 직렬화 및 역직렬화합니다.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> 포이즌 메시지를 처리하는 방법

내용 때문에 함수가 실패하는 메시지를 *포이즌 메시지*라고 합니다. 함수가 실패하면 큐 메시지가 삭제되지 않으므로, 결국 해당 큐 메시지가 다시 선택되어 주기가 반복됩니다. SDK가 제한된 횟수만큼 반복한 후 주기를 자동으로 중단하거나, 사용자가 수동으로 중단할 수 있습니다.

### 자동 포이즌 메시지 처리

SDK는 큐 메시지를 처리하기 위해 함수를 최대 5회 호출합니다. 다섯 번째 시도가 실패하면 메시지가 포이즌 큐로 이동합니다. [최대 재시도 횟수는 구성 가능합니다](#config). 

포이즌 큐 이름은 *{originalqueuename}*-poison으로 지정됩니다. 메시지를 기록하거나 수동 처리 시 주의가 필요하다는 알림을 보내는 방식으로 포이즌 큐에서 메시지를 처리하는 함수를 작성할 수 있습니다. 

다음 예제에서는 큐 메시지에 존재하지 않는 Blob의 이름이 포함될 때 'CopyBlob' 함수가 실패합니다. 이 경우 메시지가 copyblobqueue 큐에서 copyblobqueue-poison 큐로 이동합니다. 그런 다음 'ProcessPoisonMessage'가 포이즌 메시지를 기록합니다.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

다음 그림에서는 포이즌 메시지를 처리할 때 이러한 함수의 콘솔 출력을 보여 줍니다.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### 수동 포이즌 메시지 처리

이름이 'dequeueCount'인 'int' 매개 변수를 함수에 추가하여 처리를 위해 메시지가 선택된 횟수를 가져올 수 있습니다. 그리고 나서 함수 코드에서 큐에서 제거 횟수를 확인하고 다음 예제와 같이 횟수가 임계값을 초과하면 직접 포이즌 메시지를 처리할 수 있습니다.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

이 코드가 예상대로 작동하면 자동 포이즌 처리를 위해 최대 재시도 횟수를 늘려야 합니다. 그렇지 않으면 이 예제의 큐에서 제거 횟수는 5회를 초과하지 않습니다.

## <a id="config"></a> 구성 옵션을 설정하는 방법

'JobHostConfiguration' 형식을 사용하여 다음 구성 옵션을 설정할 수 있습니다.

* 코드에서 SDK 연결 문자열을 설정합니다.
* 최대 큐에서 제거 횟수와 같은 'QueueTrigger' 설정을 구성합니다.
* 구성에서 큐 이름을 가져옵니다.

### <a id="setconnstr"></a>코드에서 SDK 연결 문자열 설정

코드에서 SDK 연결 문자열을 설정하면 다음 예제와 같이 구성 파일이나 환경 변수에서 고유한 연결 문자열 이름을 사용할 수 있습니다.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>큐 설정 구성

큐 메시지 처리에 적용되는 다음 설정을 구성할 수 있습니다.

- 병렬로 실행하도록 동시에 선택되는 최대 큐 메시지 수(기본값은 16).
- 큐 메시지가 포이즌 큐로 전송되기 전의 최대 재시도 횟수(기본값은 5).
- 큐가 비어 있을 때 다시 폴링하기 전의 최대 대기 시간(기본값은 1분).

다음 예제에서는 이러한 설정을 구성하는 방법을 보여 줍니다.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>구성에서 큐 이름 가져오기

'JobHostConfiguration' 형식을 사용하여 큐 이름을 제공하는 'NameResolver' 개체를 'QueueTrigger' 및 'Queue' 특성에 대한 SDK에 전달할 수 있습니다.

예를 들어, 테스트 환경에서 logqueuetest 큐를 사용하고 프로덕션 환경에서 logqueueprod 큐를 사용하려고 한다고 가정합니다. 하드 코드된 큐 이름 대신 실제 큐 이름이 있는 'appSettings' 컬렉션에서 항목의 이름을 지정하려고 합니다. 'appSettings' 키는 logqueue이므로 함수는 다음 예제처럼 표시될 수 있습니다.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

그런 다음 'NameResolver' 클래스가 다음 예제와 같이 'appSettings'에서 큐 이름을 가져올 수 있습니다.

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

다음 예제와 같이 'JobHost' 개체에 'NameResolver' 클래스를 전달합니다.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>수동으로 함수를 트리거하는 방법

함수를 수동으로 트리거하려면 다음 예제와 같이 'JobHost' 개체에서 'Call' 또는 'CallAsync' 메서드를 사용하고 함수에서 'NoAutomaticTrigger' 특성을 사용합니다. 

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>로그를 기록하는 방법

특정 함수 호출에 연결된 WebJob 대시보드 페이지에 표시되는 로그를 기록하려면 메서드 서명의 매개 변수에서 얻은 'TextWriter' 개체를 사용합니다.

[응용 프로그램 추적 로그](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)를 기록하려면 INFO로 표시되는 로그를 만드는 `Console.Out` 및 ERROR로 표시되는 로그를 만드는 `Console.Error`를 사용합니다. [Trace 또는 TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)를 사용할 수도 있습니다.

응용 프로그램 로그는 Azure 웹 사이트 구성 방법에 따라 웹 사이트 로그 파일, Azure 테이블 또는 Azure Blob에 표시됩니다. 프로그램이 Azure WebJob으로 실행 중이면 가장 최근 100개 응용 프로그램 로그가 대시보드에도 나타납니다. 로컬에서 실행되거나 몇몇 다른 환경에서 실행되는 프로그램의 응용 프로그램 로그는 대시보드에 표시되지 않습니다.   

[대시보드 연결 문자열을 null로 설정](#config)하면 로깅을 사용하지 않도록 설정할 수 있습니다.

다음 예제에서는 로그를 기록하는 여러 가지 방법을 보여 줍니다.

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

WebJobs SDK 대시보드에서 `TextWriter` 개체의 출력은 특정 함수 호출에 해당하는 페이지로 이동하여 **Toggle Output**을 클릭할 때 표시됩니다.

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

WebJobs SDK 대시보드에서 응용 프로그램 로그의 가장 최근 100줄은 함수 호출이 아니라 WebJob에 해당하는 페이지로 이동하여 **Toggle Output**을 클릭할 때 표시됩니다.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

연속 WebJob에서 응용 프로그램 로그는 웹 사이트 파일 시스템의 /data/jobs/continuous/*{webjobname}*/job_log.txt에 표시됩니다.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Azure Blob에서 응용 프로그램 로그는 다음과 같이 표시됩니다.
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

그리고 Azure 테이블에서 'Console.Out' 및 'Console.Error' 로그는 다음과 같이 표시됩니다.

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 다음 단계

이 항목에서는 Azure 큐 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 예제를 제공했습니다. Azure WebJob 및 WebJobs SDK에 대한 자세한 내용은 [Azure WebJob - 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)(영문)를 참조하세요.
