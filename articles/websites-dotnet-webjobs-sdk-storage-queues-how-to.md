<properties linkid="" pageTitle="WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법" metaKeywords="WebJobs SDK Azure 큐 저장소 .NET C#" description="WebJobs SDK를 사용하여 Azure 큐 저장소 작업을 하는 방법에 대해 알아봅니다. 큐 만들기 및 삭제, 큐 메시지 삽입, 미리 보기, 가져오기 및 삭제 등의 작업을 알아봅니다." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법

이 가이드에서는 Azure 큐 저장소 서비스와 Azure WebJobs SDK 버전 1.0.0을 사용하는 일반적인 시나리오에 대한 C# 코드를 작성하는 방법을
보여 줍니다.

[Webjobs SDK 정의](../websites-webjobs-sdk-storage-queues-how-to) 및 WebJobs SDK NuGet 패키지 설치, Azure 저장소 계정 만들기, 저장소 계정을 가리키는 WebJobs SDK에 대한 연결 문자열 만들기 등의 [기본적인 작업을 수행하는 방법]을(../websites-dotnet-webjobs-sdk-get-started/)이미 알고 있다고 가정합니다.

대부분의 코드 조각은 다음 예제와 같이 'JobHost' 개체를 만드는 코드가 아니라 함수만 보여 줍니다.

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## 목차

-   [큐 메시지가 수신될 때 함수를 트리거하는 방법](#trigger)
	- 문자열 큐 메시지
	- POCO 개체 큐 메시지
	- 비동기 함수
	- 폴링 알고리즘
	- 병렬 실행
	- 큐 또는 큐 메시지 메타데이터 가져오기
	- 정상 종료
-   [큐 메시지를 처리하는 동안 큐 메시지를 만드는 방법](#createqueue)
	- 문자열 큐 메시지
	- POCO 개체 큐 메시지
	- 여러 개의 메시지 만들기
	- 함수 본문에 큐 특성 사용
-   [큐 메시지를 처리하는 동안 Blob을 읽고 쓰는 방법](#blobs)
	- 문자열 큐 메시지
	- POCO 개체 큐 메시지
	- 함수 본문에 Blob 특성 사용
-   [포이즌 메시지를 처리하는 방법](#poison)
	- 자동 포이즌 메시지 처리
	- 수동 포이즌 메시지 처리
-   [구성 옵션을 설정하는 방법](#config)
	- 코드에서 SDK 연결 문자열 설정
	- QueueTrigger 설정 구성
	- 구성에서 큐 이름 가져오기
-   [수동으로 함수를 트리거하는 방법](#manual)
-   [로그를 작성하는 방법](#logs)
-   [다음 단계](#nextsteps)

## <a id="trigger"></a> 큐 메시지가 수신될 때 함수를 트리거하는 방법

큐 메시지가 수신될 때 SDK에서 호출하는 함수를 작성하려면 메시지에 포함된 내용에 따라 문자열 또는 POCO 매개 변수와 함께 'QueueTrigger' 특성을 사용합니다. 특성 생성자는 폴링할 큐의 이름을 지정하는 문자열 매개 변수를 사용합니다. [큐 이름을 동적으로 설정]할 수도 있습니다(#config).

웹 사이트가 여러 VM에서 실행되는 경우 WebJob이 각 컴퓨터에서 실행되고, 각 컴퓨터는 트리거를 기다렸다가 함수 실행을 시도합니다. 일부 시나리오에서는 이로 인해 일부 함수가 동일한 데이터를 두 번 처리하게 될 수 있으므로 함수는 역등원이어야 합니다(같은 입력 데이터로 반복 호출해도 중복된 결과가 나오지 않도록 작성).  

### 문자열 큐 메시지

다음 예제에서는 큐에 문자열 메시지가 포함되므로 큐 메시지의 내용을 포함하는 'logMessage'라는 문자열 매개 변수에 'QueueTrigger'가 적용됩니다. 이 함수는 [대시보드에 로그 메시지를 씁니다](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

'문자열' 외에도 이 매개 변수는 바이트 배열, 'CloudQueueMessage' 개체 또는 정의한 POCO 개체일 수 있습니다.

### POCO 개체 큐 메시지

다음 예제에서는 큐 메시지에 'BlobName' 속성을 포함하는 'BlobInformation' 개체에 대한 JSON이 포함됩니다. SDK에서 자동으로 개체를 역직렬화합니다.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

다음 예제에서는 WebJobs SDK를 사용하지 않고 SDK가 구문 분석할 수 있는 POCO 큐 메시지를 만드는 방법을 보여 줍니다. 이 SDK는 [Newtonsoft.Json NuGet 패키지](http://www.nuget.org/packages/Newtonsoft.Json)를 사용하여 메시지를 직렬화 및 역직렬화합니다.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### 비동기 함수

다음 비동기 함수는 [대시보드에 로그를 씁니다](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Blob을 복사하는 다음 예제와 같이 비동기 함수는 취소 토큰을 사용할 수 있습니다. 'queueTrigger' 자리 표시자에 대한 설명은 [Blob](#blobs) 섹션을 참조하세요.

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### 폴링 알고리즘

이 SDK는 임의 지수적 백오프 알고리즘을 구현하여 유휴 큐 폴링이 저장소 트랜잭션 비용에 미치는 영향을 줄입니다.  메시지가 있으면 SDK는 2초 동안 기다린 후 다른 메시지를 확인합니다. 메시지가 없으면 4초 정도 기다린 후 다시 시도합니다. 큐 메시지를 가져오려는 후속 시도가 실패한 후 대기 시간은 최대 대기 시간(기본적으로 1분으로 설정됨)에 도달할 때까지 계속 증가합니다. [최대 대기 시간은 구성 가능합니다](#config).

### 병렬 실행

여러 함수가 서로 다른 큐에서 수신 대기 중이면 메시지가 동시에 수신될 경우 SDK에서 병렬로 호출합니다. 

단일 큐에 대해 여러 메시지가 수신되는 경우에도 마찬가지입니다. 기본적으로 SDK는 한 번에 16개의 큐 메시지를 일괄로 가져오고 해당 메시지를 병렬로 처리하는 함수를 실행합니다. [일괄 처리 크기는 구성 가능합니다](#config). 처리되는 개수가 일괄 처리 크기의 절반으로 감소하면 SDK에서 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리되는 최대 동시 메시지 수는 일괄 처리 크기의 1.5배입니다. 이 제한은 'QueueTrigger' 특성이 있는 각 함수에 개별적으로 적용됩니다. 하나의 큐에 수신된 메시지에 대해 병렬 실행을 사용하지 않으려면 일괄 처리 크기를 1로 설정합니다.

### <a id="queuemetadata"></a>큐 또는 큐 메시지 메타데이터 가져오기

메서드 서명에 매개 변수를 추가하여 다음 메시지 속성을 가져올 수 있습니다.

* 'DateTimeOffset' expirationTime
* 'DateTimeOffset' insertionTime
* 'DateTimeOffset' nextVisibleTime
* 'string' queueTrigger(메시지 텍스트 포함)
* 'string' id
* 'string' popReceipt
* 'int' dequeueCount

Azure 저장소 API로 직접 작업하려는 경우 'CloudStorageAccount' 매개 변수를 추가할 수도 있습니다.

다음 예제에서는 이러한 모든 메타데이터를 INFO 응용 프로그램 로그에 씁니다. 예제에서 logMessage와 queueTrigger에는 둘 다 큐 메시지의 내용이 포함됩니다.

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
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
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

다음은 샘플 코드에 의해 작성된 샘플 로그입니다.

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

연속 WebJob에서 실행되는 함수는 WebJob이 종료될 때 운영 체제가 함수에 알릴 수 있게 해주는 'CancellationToken' 매개 변수를 사용할 수 있습니다. 이 알림을 통해 함수가 예기치 않게 종료되어 데이터가 일관되지 않은 상태가 되는 것을 방지할 수 있습니다.

다음 예제에서는 함수에서 임박한 WebJob 종료를 확인하는 방법을 보여 줍니다.

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

**참고**: 대시보드에 종료된 함수의 상태와 출력이 올바르게 표시되지 않을 수도 있습니다.
 
자세한 내용은 [WebJobs 정상 종료](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)를 참조하세요.   

## <a id="createqueue"></a> 큐 메시지를 처리하는 동안 큐 메시지를 만드는 방법

새 큐 메시지를 만드는 함수를 작성하려면 출력 문자열의 'Queue' 특성이나 POCO 매개 변수를 사용합니다. 'QueueTrigger'와 마찬가지로 큐 이름을 문자열로 전달하거나, [동적으로 큐 이름을 설정]할 수 있습니다(#config).

### 문자열 큐 메시지

다음 예제에서는 "inputqueue"라는 큐에 수신된 큐 메시지와 동일한 콘텐츠를 가진 새로운 큐 메시지를 "outputqueue"라는 큐에 만듭니다.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

출력 매개 변수 유형이 다음 유형 중 하나이고 함수 종료 시 개체가 null이 아니면 SDK에서 큐 메시지를 만듭니다.

* 'string' 
* 'byte[]'
* 정의한 직렬화 가능 POCO 유형
* 'CloudQueueMessage'

여러 개의 메시지를 만들려면 이 섹션의 뒷부분에 있는 **여러 개의 메시지 만들기**를 참조하세요.

수동으로 메시지를 보내려는 경우 'CloudQueue'를 출력 매개 변수 유형으로 사용할 수도 있습니다.

### POCO 개체 큐 메시지

문자열 대신 POCO 개체가 포함된 큐 메시지를 만들려면 POCO 유형을 출력 매개 변수로 'Queue' 특성 생성자에 전달합니다. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

SDK에서 자동으로 개체를 JSON으로 직렬화합니다. 개체가 null인 경우에도 항상 큐 메시지가 생성됩니다.

### 여러 개의 메시지 만들기

여러 개의 메시지를 만들려면 다음 예제와 같이 출력 큐의 매개 변수 유형을 'ICollector<T>' 또는 'IAsyncCollector'로 설정합니다.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

'Add' 메서드를 호출하면 각 큐 메시지가 즉시 생성됩니다.

### <a id="queuebody"></a>함수 본문에 큐 특성 사용

'Queue' 특성을 사용하여 큐 메시지를 만들기 전에 함수에서 일부 작업을 수행해야 하는 경우 'IBinder' 인터페이스를 사용하여 직접 큐에 대해 작업할 수 있게 해주는 'CloudQueue' 개체를 가져올 수 있습니다. 

다음 예제에서는 입력 큐 메시지를 사용하여 동일한 내용의 새 메시지를 출력 큐에 만듭니다. 출력 큐 이름은 함수 본문에서 코드로 설정됩니다.

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

'Blob' 및 'Table' 특성을 사용하여 Blob 및 테이블을 읽고 쓸 수 있습니다. 이 섹션의 샘플은 Blob에 적용됩니다.

'Blob' 특성을 사용할 수 있는 유형에는 'Stream', 'TextReader', 'TextWriter' 및 'CloudBlockBlob'가 있습니다. 특성 생성자는 컨테이너 및 Blob 이름을 지정하는 'blobPath' 매개 변수를 사용합니다. 특성이 'Stream' 개체를 데코레이트하는 경우 다른 생성자 매개 변수가 'FileAccess' 모드를 읽기, 쓰기 또는 읽기/쓰기로 지정합니다. Blob을 개체에 바인딩하기 전에 함수에서 일부 작업을 수행해야 하는 경우 [앞의 Queue 특성처럼] 함수 본문에 특성을 사용할 수 있습니다(#queuebody).

### 문자열 큐 메시지

문자열이 포함된 큐 메시지의 경우 'queueTrigger'는 메시지 내용이 포함된 'Blob' 특성의 'blobPath' 매개 변수에 사용할 수 있는 자리 표시자입니다. 

다음 예제에서는 'Stream' 개체를 사용하여 Blob을 읽고 씁니다. 큐 메시지는 textblobs 컨테이너에 있는 Blob의 이름을 제공합니다. 이름에 "-new"가 추가된 Blob 복사본이 동일한 컨테이너에 생성됩니다. 

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

큐 메시지에 JSON으로 저장된 POCO 개체의 경우 개체 속성의 이름을 지정하는 자리 표시자를 'Queue' 특성의 'blobPath' 매개 변수에 사용할 수 있습니다. [큐 메타데이터 속성 이름]을(#queuemetadata) 자리 표시자로 사용할 수도 있습니다. 

다음 예제에서는 'BlobInformation' 개체의 속성을 통해 입력 및 출력 Blob의 이름을 지정하여 다른 확장명을 가진 새 Blob에 Blob을 복사합니다. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

다음 예제에서는 WebJobs SDK를 사용하지 않고 SDK가 구문 분석할 수 있는 POCO 큐 메시지를 만드는 방법을 보여 줍니다. 이 SDK는 [Newtonsoft.Json NuGet 패키지](http://www.nuget.org/packages/Newtonsoft.Json)를 사용하여 메시지를 직렬화 및 역직렬화합니다.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> 포이즌 메시지를 처리하는 방법

함수가 실패하게 만드는 내용이 포함된 메시지를 *포이즌 메시지*라고 합니다. 함수가 실패할 경우 큐 메시지가 삭제되지 않고 결국 다시 선택되어 주기가 반복됩니다. SDK에서 제한된 반복 횟수 후에 자동으로 주기를 중단하거나 수동으로 중단할 수 있습니다.

### 자동 포이즌 메시지 처리

SDK는 최대 5회까지 함수를 호출하여 큐 메시지를 처리합니다. 다섯 번째 시도가 실패하면 메시지가 포이즌 큐로 이동됩니다. [최대 다시 시도 횟수는 구성 가능합니다](#config). 

포이즌 큐의 이름은 *{originalqueuename}*-poison으로 지정됩니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다. 

다음 예제에서는 큐 메시지에 존재하지 않는 Blob 이름이 포함되어 있을 경우 'CopyBlob' 함수가 실패합니다. 이 경우 메시지가 copyblobqueue 큐에서 copyblobqueue-poison 큐로 이동됩니다. 'ProcessPoisonMessage'에서 포이즌 메시지를 기록합니다.

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

다음 그림은 포이즌 메시지가 처리될 때 이러한 함수의 콘솔 출력을 보여 줍니다.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### 수동 포이즌 메시지 처리

'dequeueCount'라는 'int' 매개 변수를 함수에 추가하여 메시지가 처리를 위해 선택된 횟수를 가져올 수 있습니다. 함수 코드를 통해 큐에서 제거한 횟수를 확인하고 다음 예제와 같이 횟수가 임계값을 초과할 경우 고유한 포이즌 메시지 처리를 수행할 수 있습니다.

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

이 코드가 예상대로 작동하려면 자동 포이즌 처리에 대한 최대 다시 시도 횟수를 늘려야 합니다. 그렇지 않으면 이 예제의 큐에서 제거 횟수가 5를 초과하지 않습니다.

## <a id="config"></a> 구성 옵션을 설정하는 방법

'JobHostConfiguration' 유형을 사용하여 다음 구성 옵션을 설정할 수 있습니다.

* 코드에서 SDK 연결 문자열을 설정합니다.
* 최대 큐에서 제거 횟수와 같은 'QueueTrigger' 설정을 구성합니다.
* 구성에서 큐 이름을 가져옵니다.

### <a id="setconnstr"></a>코드에서 SDK 연결 문자열 설정

코드에서 SDK 연결 문자열을 설정하면 다음 예제와 같이 구성 파일이나 환경 변수에 고유한 연결 문자열 이름을 사용할 수 있습니다.

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

- 병렬로 실행되도록 동시에 선택되는 최대 큐 메시지 수(기본값: 16)
- 큐 메시지가 포이즌 큐로 전송되기 전의 최대 다시 시도 횟수(기본값: 5)
- 큐가 비어 있을 때 다시 폴링하기 전의 최대 대기 시간(기본값: 1분)

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

'JobHostConfiguration' 유형을 사용하면 'QueueTrigger' 및 'Queue' 특성에 대한 큐 이름을 SDK에 제공하는 'NameResolver' 개체를 전달할 수 있습니다.

예를 들어 테스트 환경에 logqueuetest라는 큐를 사용하고 프로덕션에 logqueueprod라는 큐를 사용한다고 가정합니다. 하드 코드된 큐 이름 대신 실제 큐 이름을 포함하는 'appSettings' 컬렉션의 항목 이름을 지정하려고 합니다. 'appSettings' 키가 logqueue이면 함수가 다음 예제처럼 표시될 수 있습니다.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

'NameResolver' 클래스는 다음 예제와 같이 'appSettings'에서 큐 이름을 가져올 수 있습니다.

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

다음 예제와 같이 'NameResolver' 클래스를 'JobHost' 개체에 전달합니다.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>수동으로 함수를 트리거하는 방법

수동으로 함수를 트리거하려면 다음 예제와 같이 'JobHost' 개체의 'Call' 또는 'CallAsync' 메서드와 함수의 'NoAutomaticTrigger' 특성을 사용합니다. 

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

## <a id="logs"></a>로그를 작성하는 방법

특정 함수 호출에 연결된 WebJobs 대시보드 페이지에 표시되는 로그를 작성하려면 메서드 서명의 매개 변수에서 가져온 'TextWriter' 개체를 사용합니다.

[응용 프로그램 추적 로그]를 작성하려면(../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)'Console.Out'(INFO로 표시된 로그 생성) 및 'Console.Error'(ERROR로 표시된 로그 생성)를 사용합니다. 대신 [Trace 또는 TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)를 사용할 수도 있습니다.

응용 프로그램 로그는 Azure 웹 사이트를 구성한 방식에 따라 웹 사이트 로그 파일, Azure 테이블 또는 Azure Blob에 표시됩니다. 프로그램이 Azure WebJob에서 실행 중인 경우 대시보드에도 최근 100개 응용 프로그램 로그가 표시됩니다. 다른 환경이나 로컬에서 실행 중인 프로그램의 대시보드에는 응용 프로그램 로그가 표시되지 않습니다.   

[대시보드 연결 문자열을 null로 설정]하여 로깅을 사용하지 않도록 설정할 수 있습니다(#config).

다음 예제에서는 로그를 작성하는 여러 가지 방법을 보여 줍니다.

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

WebJobs SDK 대시보드에서 'TextWriter' 개체 출력은 특정 함수 호출에 대한 페이지로 이동하여 **Toggle Output**을 클릭할 때 표시됩니다.

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

WebJobs SDK 대시보드에서 응용 프로그램 로그의 최근 100줄은 함수 호출이 아니라 WebJob에 대한 페이지로 이동하여 **Toggle Output**을 클릭할 때 표시됩니다.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

연속 WebJob에서는 응용 프로그램 로그가 웹 사이트 파일 시스템의 /data/jobs/continuous/*{webjobname}*/job_log.txt에 표시됩니다.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Azure Blob에서 응용 프로그램 로그는 다음과 같이 표시됩니다.
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Azure 테이블에서 'Console.Out' 및 'Console.Error' 로그는 다음과 같이 표시됩니다.

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> 다음 단계

이 항목에서는 Azure 큐 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 샘플을 제공했습니다. Azure WebJob 및 WebJob SDK를 사용하는 방법에 대한 자세한 내용은 [Azure WebJob - 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)(영문)를 참조하세요.

<!--HONumber=35.2-->
