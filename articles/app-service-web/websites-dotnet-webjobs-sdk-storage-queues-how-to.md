---
title: "WebJob SDK를 사용하여 Azure 큐 저장소로 작업하는 방법"
description: "WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법에 대해 알아봅니다. 큐 만들기 및 삭제, 큐 메시지 삽입, 미리 보기, 가져오기 및 삭제 등의 작업을 알아봅니다."
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: wpickett
editor: jimbe
ms.assetid: dbfac5d9-f4a0-4e3e-9ecc-af3d7bf80463
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 154d2cd9b7f4ea51d3fd4c1995b67a25816b28a2
ms.openlocfilehash: 67bb54e8a573607199143621451e598bf11947fe


---
# <a name="how-to-use-azure-queue-storage-with-the-webjobs-sdk"></a>WebJob SDK를 사용하여 Azure 큐 저장소로 작업하는 방법
## <a name="overview"></a>개요
이 가이드에서는 Azure 큐 저장소 서비스에서 Azure WebJobs SDK 버전 1.x를 사용하는 방법을 보여 주는 C# 코드 샘플을 제공합니다.

이 가이드에서는 [저장소 계정 또는 [여러 저장소 계정](https://github.com/Azure/azure-webjobs-sdk/blob/master/test/Microsoft.Azure.WebJobs.Host.EndToEndTests/MultipleStorageAccountsEndToEndTests.cs)을 가리키는 연결 문자열을 사용하여 Visual Studio에서 WebJob 프로젝트를 만드는 방법](websites-dotnet-webjobs-sdk-get-started.md)을 알고 있는 것으로 가정합니다.

대부분의 코드 조각은 다음 예제와 같이 `JobHost` 개체를 만드는 코드가 아니라 함수만 보여 줍니다.

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

이 가이드에는 다음 항목이 포함되어 있습니다.

* [큐 메시지가 수신될 때 함수를 트리거하는 방법](#trigger)
  * 문자열 큐 메시지
  * POCO 큐 메시지
  * 비동기 함수
  * QueueTrigger 특성이 작동하는 유형
  * 폴링 알고리즘
  * 여러 인스턴스
  * 병렬 실행
  * 큐 또는 큐 메시지 메타데이터 가져오기
  * 정상 종료
* [큐 메시지를 처리하는 동안 큐 메시지를 만드는 방법](#createqueue)
  * 문자열 큐 메시지
  * POCO 큐 메시지
  * 여러 메시지 만들기 또는 비동기 함수로 큐 메시지 만들기
  * 큐 특성이 작동하는 유형
  * 함수 본문에 WebJobs SDK 특성 사용
* [큐 메시지를 처리하는 동안 Blob을 읽고 쓰는 방법](#blobs)
  * 문자열 큐 메시지
  * POCO 큐 메시지
  * Blob 특성이 작동하는 유형
* [포이즌 메시지를 처리하는 방법](#poison)
  * 자동 포이즌 메시지 처리
  * 수동 포이즌 메시지 처리
* [구성 옵션을 설정하는 방법](#config)
  * 코드에서 SDK 연결 문자열 설정
  * QueueTrigger 설정 구성
  * 코드에서 WebJobs SDK 생성자 매개 변수 값 설정
* [수동으로 함수를 트리거하는 방법](#manual)
* [로그를 작성하는 방법](#logs)
* [오류를 처리하고 시간 제한을 구성하는 방법](#errors)
* [다음 단계](#nextsteps)

## <a name="a-idtriggera-how-to-trigger-a-function-when-a-queue-message-is-received"></a><a id="trigger"></a> 큐 메시지가 수신될 때 함수를 트리거하는 방법
큐 메시지가 수신될 때 WebJobs SDK에서 호출하는 함수를 작성하려면 `QueueTrigger` 특성을 사용합니다. 특성 생성자는 폴링할 큐의 이름을 지정하는 문자열 매개 변수를 사용합니다. [큐 이름을 동적으로 설정](#config)할 수도 있습니다

### <a name="string-queue-messages"></a>문자열 큐 메시지
다음 예제에서는 큐에 문자열 메시지가 포함되므로 큐 메시지의 내용을 포함하는 `logMessage`라는 문자열 매개 변수에 `QueueTrigger`가 적용됩니다. 이 함수는 [대시보드에 로그 메시지를 씁니다](#logs).

        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

`string` 외에도 이 매개 변수는 바이트 배열, `CloudQueueMessage` 개체 또는 정의한 POCO 개체일 수 있습니다.

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 큐 메시지
다음 예제에서는 큐 메시지에 `BlobName` 속성을 포함하는 `BlobInformation` 개체에 대한 JSON이 포함됩니다. SDK에서 자동으로 개체를 역직렬화합니다.

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

이 SDK는 [Newtonsoft.Json NuGet 패키지](http://www.nuget.org/packages/Newtonsoft.Json) 를 사용하여 메시지를 직렬화 및 역직렬화합니다. WebJobs SDK를 사용하지 않는 프로그램에서 큐 메시지를 만드는 경우 다음 예제와 같은 코드를 작성하여 SDK에서 구문 분석할 수 있는 POCO 큐 메시지를 만들 수 있습니다.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### <a name="async-functions"></a>비동기 함수
다음 비동기 함수는 [대시보드에 로그를 씁니다](#logs).

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Blob을 복사하는 다음 예제와 같이 비동기 함수는 [취소 토큰](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken)을 사용할 수 있습니다. `queueTrigger` 자리 표시자에 대한 자세한 내용은 [Blob](#blobs) 섹션을 참조하세요.

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

### <a name="a-idqtattributetypesa-types-the-queuetrigger-attribute-works-with"></a><a id="qtattributetypes"></a> QueueTrigger 특성이 작동하는 유형
다음 유형에서 `QueueTrigger` 를 사용할 수 있습니다.

* `string`
* JSON으로 serialize된 POCO 유형
* `byte[]`
* `CloudQueueMessage`

### <a name="a-idpollinga-polling-algorithm"></a><a id="polling"></a> 폴링 알고리즘
SDK는 무작위 지수 백오프 알고리즘을 구현하여 유휴 큐 폴링이 저장소 트랜잭션 비용에 미치는 영향을 줄입니다.  메시지가 발견되면 SDK는 2초 대기하고 다른 메시지가 있는지 확인하며, 메시지가 발견되지 않으면 4초 정도 대기하고 나서 다시 시도합니다. 후속 시도로 큐 메시지를 가져오지 못하면 최대 대기 시간(기본값 1분)에 도달할 때까지 대기 시간이 계속 증가합니다. [최대 대기 시간은 구성 가능합니다](#config).

### <a name="a-idinstancesa-multiple-instances"></a><a id="instances"></a> 여러 인스턴스
웹앱이 여러 인스턴스에서 실행되는 경우 연속적인 WebJob이 각 컴퓨터에서 실행되고, 각 컴퓨터는 트리거를 기다렸다가 함수 실행을 시도합니다. WebJobs SDK 큐 트리거는 함수가 큐 메시지를 여러 번 처리하는 것을 방지합니다. 함수를 idempotent로 작성할 필요가 없습니다. 그러나 호스트 웹앱의 여러 인스턴스가 있는 경우에는 함수는 하나의 인스턴스만 실행되도록 하려는 경우 `Singleton` 특성을 사용할 수 있습니다.

### <a name="a-idparallela-parallel-execution"></a><a id="parallel"></a> 병렬 실행
여러 함수가 서로 다른 큐에서 수신 대기 중이면 메시지가 동시에 수신될 경우 SDK에서 병렬로 호출합니다.

단일 큐에 대해 여러 메시지가 수신되는 경우에도 마찬가지입니다. 기본적으로 SDK는 한 번에 16개의 큐 메시지를 일괄로 가져오고 해당 메시지를 병렬로 처리하는 함수를 실행합니다. [일괄 처리 크기는 구성 가능합니다](#config). 처리되는 개수가 일괄 처리 크기의 절반으로 감소하면 SDK에서 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리되는 최대 동시 메시지 수는 일괄 처리 크기의 1.5배입니다. 이 제한은 `QueueTrigger` 특성이 있는 각 함수에 개별적으로 적용됩니다.

하나의 큐에 수신된 메시지에 대해 병렬 실행을 사용하지 않으려면 일괄 처리 크기를 1로 설정하면 됩니다. **Azure WebJobs SDK 1.1.0 RTM** 에서 [큐 처리에 대한 제어 강화](/blog/azure-webjobs-sdk-1-1-0-rtm/)를 참조하세요.

### <a name="a-idqueuemetadataaget-queue-or-queue-message-metadata"></a><a id="queuemetadata"></a>큐 또는 큐 메시지 메타데이터 가져오기
메서드 서명에 매개 변수를 추가하여 다음 메시지 속성을 가져올 수 있습니다.

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` queueTrigger(메시지 텍스트 포함)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Azure 저장소 API로 직접 작업하려는 경우 `CloudStorageAccount` 매개 변수를 추가할 수도 있습니다.

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

### <a name="a-idgracefulagraceful-shutdown"></a><a id="graceful"></a>정상 종료
연속 WebJob에서 실행되는 함수는 WebJob이 종료될 때 운영 체제가 함수에 알릴 수 있게 해주는 `CancellationToken` 매개 변수를 사용할 수 있습니다. 이 알림을 통해 함수가 예기치 않게 종료되어 데이터가 일관되지 않은 상태가 되는 것을 방지할 수 있습니다.

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

**참고:** 대시보드에 종료된 함수의 상태와 출력이 올바르게 표시되지 않을 수도 있습니다.

자세한 내용은 [WebJobs 정상 종료](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)를 참조하세요.   

## <a name="a-idcreatequeuea-how-to-create-a-queue-message-while-processing-a-queue-message"></a><a id="createqueue"></a> 큐 메시지를 처리하는 동안 큐 메시지를 만드는 방법
새 큐 메시지를 만드는 함수를 작성하려면 `Queue` 특성을 사용합니다. `QueueTrigger`와 마찬가지로 큐 이름을 문자열로 전달하거나, [동적으로 큐 이름을 설정](#config)할 수 있습니다

### <a name="string-queue-messages"></a>문자열 큐 메시지
다음 비동기가 아닌 코드 샘플에서는 "inputqueue"라는 큐에 수신된 큐 메시지와 동일한 콘텐츠를 가진 새로운 큐 메시지를 "outputqueue"라는 큐에 만듭니다. 비동기 함수는 이 섹션의 뒷부분에 나와 있는 것처럼 `IAsyncCollector<T>` 를 사용합니다.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### <a name="poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a>POCO( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 큐 메시지
문자열 대신 POCO가 포함된 큐 메시지를 만들려면 POCO 유형을 출력 매개 변수로 `Queue` 특성 생성자에 전달합니다.

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

SDK에서 자동으로 개체를 JSON으로 serialize합니다. 개체가 null인 경우에도 항상 큐 메시지가 생성됩니다.

### <a name="create-multiple-messages-or-in-async-functions"></a>여러 메시지 만들기 또는 비동기 함수로 큐 메시지 만들기
여러 개의 메시지를 만들려면 다음 예제와 같이 출력 큐의 매개 변수 유형을 `ICollector<T>` 또는 `IAsyncCollector<T>`로 설정합니다.

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

`Add` 메서드를 호출하면 각 큐 메시지가 즉시 생성됩니다.

### <a name="types-that-the-queue-attribute-works-with"></a>큐 특성이 작동하는 유형
다음 매개 변수 유형에서 `Queue` 특성을 사용할 수 있습니다.

* `out string`(함수가 종료될 때 매개 변수 값이 null이 아닌 경우 큐 메시지 생성)
* `out byte[]`(`string`처럼 작동)
* `out CloudQueueMessage`(`string`처럼 작동)
* `out POCO` (serialize 가능한 유형, 함수가 종료될 때 매개 변수가 null인 경우 null 개체가 포함된 메시지 생성)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (Azure 저장소 API를 직접 사용하여 수동으로 메시지 생성)

### <a name="a-idibinderause-webjobs-sdk-attributes-in-the-body-of-a-function"></a><a id="ibinder"></a>함수 본문에 WebJobs SDK 특성 사용
`Queue`, `Blob` 또는 `Table`과 같은 WebJobs SDK 특성을 사용하기 전에 함수에서 일부 작업을 수행해야 하는 경우 `IBinder` 인터페이스를 사용할 수 있습니다.

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

`IBinder` 인터페이스를 `Table` 및 `Blob` 특성과 함께 사용할 수도 있습니다.

## <a name="a-idblobsa-how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a><a id="blobs"></a> 큐 메시지를 처리하는 동안 Blob 및 테이블을 읽고 쓰는 방법
`Blob` 및 `Table` 특성을 사용하여 Blob 및 테이블을 읽고 쓸 수 있습니다. 이 섹션의 샘플은 Blob에 적용됩니다. Blob이 생성되거나 업데이트될 때 프로세스를 트리거하는 방법을 보여 주는 코드 샘플은 [WebJobs SDK를 사용하여 Azure Blob 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)을 참조하고, 테이블을 읽고 쓰는 코드 샘플은 [WebJobs SDK를 사용하여 Azure 테이블 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)을 참조하세요.

### <a name="string-queue-messages-triggering-blob-operations"></a>Blob 작업을 트리거하는 문자열 큐 메시지
문자열이 포함된 큐 메시지의 경우 `queueTrigger`는 메시지 내용이 포함된 `Blob` 특성의 `blobPath` 매개 변수에 사용할 수 있는 자리 표시자입니다.

다음 예제에서는 `Stream` 개체를 사용하여 Blob를 읽고 씁니다. 큐 메시지는 textblobs 컨테이너에 있는 Blob의 이름입니다. 이름에 "-new"가 추가된 Blob 복사본이 동일한 컨테이너에 생성됩니다.

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

`Blob` 특성 생성자는 컨테이너 및 Blob 이름을 지정하는 `blobPath` 매개 변수를 가져옵니다. 이 자리 표시자에 대한 자세한 내용은 [WebJob SDK를 사용하여 Azure Blob 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)을 참조하세요

특성이 `Stream` 개체를 데코레이팅하는 경우 또 다른 생성자 매개 변수가 `FileAccess` 모드를 읽기, 쓰기 또는 읽기/쓰기로 지정합니다.

다음 예제에서는 `CloudBlockBlob` 개체를 사용하여 Blob를 삭제합니다. 큐 메시지는 Blob의 이름입니다.

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### <a name="a-idpocoblobsa-poco-plain-old-clr-objecthttpenwikipediaorgwikiplainoldclrobject-queue-messages"></a><a id="pocoblobs"></a> POCO( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 큐 메시지
큐 메시지에 JSON으로 저장된 POCO의 경우 개체 속성의 이름을 지정하는 자리 표시자를 `Queue` 특성의 `blobPath` 매개 변수에 사용할 수 있습니다. [큐 메타데이터 속성 이름](#queuemetadata) 을 자리 표시자로 사용할 수도 있습니다.

다음 예제에서는 Blob을 확장명이 다른 새 Blob에 복사합니다. 큐 메시지는 `BlobInformation` 및 `BlobName` 속성이 포함된 `BlobNameWithoutExtension` 개체입니다. 속성 이름은 `Blob` 특성에 대한 Blob 경로에서 자리 표시자로 사용됩니다.

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

이 SDK는 [Newtonsoft.Json NuGet 패키지](http://www.nuget.org/packages/Newtonsoft.Json) 를 사용하여 메시지를 직렬화 및 역직렬화합니다. WebJobs SDK를 사용하지 않는 프로그램에서 큐 메시지를 만드는 경우 다음 예제와 같은 코드를 작성하여 SDK에서 구문 분석할 수 있는 POCO 큐 메시지를 만들 수 있습니다.

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

Blob을 개체에 바인딩하기 전에 함수에서 일부 작업을 수행해야 하는 경우 [앞의 Queue 특성처럼](#ibinder)함수 본문에 특성을 사용할 수 있습니다

### <a name="a-idblobattributetypesa-types-you-can-use-the-blob-attribute-with"></a><a id="blobattributetypes"></a> Blob 특성을 사용할 수 있는 유형
`Blob` 특성은 다음 유형에서 사용할 수 있습니다.

* `Stream` (읽기 또는 쓰기, FileAccess 생성자 매개 변수를 통해 지정)
* `TextReader`
* `TextWriter`
* `string` (읽기)
* `out string` (쓰기, 함수가 반환될 때 문자열 매개 변수가 null이 아닌 경우에만 Blob 생성)
* POCO(읽기)
* out POCO(쓰기, 항상 Blob 생성, 함수가 반환될 때 POCO 매개 변수가 null인 경우 null 개체로 생성)
* `CloudBlobStream` (쓰기)
* `ICloudBlob` (읽기 또는 쓰기)
* `CloudBlockBlob` (읽기 또는 쓰기)
* `CloudPageBlob` (읽기 또는 쓰기)

## <a name="a-idpoisona-how-to-handle-poison-messages"></a><a id="poison"></a> 포이즌 메시지를 처리하는 방법
함수가 실패하게 만드는 내용이 포함된 메시지를 *포이즌 메시지*라고 합니다. 함수가 실패할 경우 큐 메시지가 삭제되지 않고 결국 다시 선택되어 주기가 반복됩니다. SDK에서 제한된 반복 횟수 후에 자동으로 주기를 중단하거나 수동으로 중단할 수 있습니다.

### <a name="automatic-poison-message-handling"></a>자동 포이즌 메시지 처리
SDK는 최대 5회까지 함수를 호출하여 큐 메시지를 처리합니다. 다섯 번째 시도가 실패하면 메시지가 포이즌 큐로 이동됩니다. [최대 다시 시도 횟수는 구성 가능합니다](#config).

포이즌 큐의 이름은 *{originalqueuename}*-poison으로 지정됩니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다.

다음 예제에서는 큐 메시지에 존재하지 않는 Blob 이름이 포함되어 있을 경우 `CopyBlob` 함수가 실패합니다. 이 경우 메시지가 copyblobqueue 큐에서 copyblobqueue-poison 큐로 이동됩니다. 그런 다음 `ProcessPoisonMessage` 가 포이즌 메시지를 기록합니다.

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

![포이즌 메시지 처리에 대한 콘솔 출력](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### <a name="manual-poison-message-handling"></a>수동 포이즌 메시지 처리
`dequeueCount`라는 `int` 매개 변수를 함수에 추가하여 메시지가 처리를 위해 선택된 횟수를 가져올 수 있습니다. 함수 코드를 통해 큐에서 제거한 횟수를 확인하고 다음 예제와 같이 횟수가 임계값을 초과할 경우 고유한 포이즌 메시지 처리를 수행할 수 있습니다.

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## <a name="a-idconfiga-how-to-set-configuration-options"></a><a id="config"></a> 구성 옵션을 설정하는 방법
`JobHostConfiguration` 유형을 사용하여 다음 구성 옵션을 설정할 수 있습니다.

* 코드에서 SDK 연결 문자열 설정
* 최대 큐에서 제거 횟수와 같은 `QueueTrigger` 설정을 구성합니다.
* 구성에서 큐 이름을 가져옵니다.

### <a name="a-idsetconnstraset-sdk-connection-strings-in-code"></a><a id="setconnstr"></a>코드에서 SDK 연결 문자열 설정
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

### <a name="a-idconfigqueueaconfigure-queuetrigger-settings"></a><a id="configqueue"></a>QueueTrigger 설정 구성
큐 메시지 처리에 적용되는 다음 설정을 구성할 수 있습니다.

* 병렬로 실행하도록 동시에 선택되는 최대 큐 메시지 수(기본값은 16).
* 큐 메시지가 포이즌 큐로 전송되기 전의 최대 재시도 횟수(기본값은 5).
* 큐가 비어 있을 때 다시 폴링하기 전의 최대 대기 시간(기본값은 1분).

다음 예제에서는 이러한 설정을 구성하는 방법을 보여 줍니다.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### <a name="a-idsetnamesincodeaset-values-for-webjobs-sdk-constructor-parameters-in-code"></a><a id="setnamesincode"></a>코드에서 WebJobs SDK 생성자 매개 변수 값 설정
경우에 따라 큐 이름, Blob 이름 또는 컨테이너, 테이블 이름을 하드 코드하지 않고 코드에서 지정할 수 있습니다. 예를 들어 구성 파일 또는 환경 변수에서 `QueueTrigger`에 대한 큐 이름을 지정할 수 있습니다.

이렇게 하려면 `NameResolver` 개체를 `JobHostConfiguration` 유형으로 전달하면 됩니다. WebJobs SDK 특성 생성자 매개 변수에 백분율(%) 기호로 묶어 특정 자리 표시자를 포함하면 `NameResolver` 코드가 해당 자리 표시자 위치에서 사용할 실제 값을 지정합니다.

예를 들어 테스트 환경에 logqueuetest라는 큐를 사용하고 프로덕션에 logqueueprod라는 큐를 사용한다고 가정해 보겠습니다. 하드 코드된 큐 이름 대신 실제 큐 이름을 포함하는 `appSettings` 컬렉션의 항목 이름을 지정하려고 합니다. `appSettings` 키가 logqueue이면 함수가 다음 예제처럼 표시될 수 있습니다.

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

그런 다음 `NameResolver` 클래스가 다음 예제와 같이 `appSettings`에서 큐 이름을 가져올 수 있습니다.

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

다음 예제와 같이 `NameResolver` 클래스를 `JobHost` 개체에 전달합니다.

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**참고:** 큐, 테이블 및 Blob 이름은 함수가 호출될 때마다 확인되지만 Blob 컨테이너 이름은 응용 프로그램이 시작될 때만 확인됩니다. 작업이 실행되는 동안에는 Blob 컨테이너 이름을 변경할 수 없습니다.

## <a name="a-idmanualahow-to-trigger-a-function-manually"></a><a id="manual"></a>수동으로 함수를 트리거하는 방법
함수를 수동으로 트리거하려면 다음 예제와 같이 `JobHost` 개체에서 `Call` 또는 `CallAsync` 메서드를 사용하고 함수에서 `NoAutomaticTrigger` 특성을 사용합니다.

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

## <a name="a-idlogsahow-to-write-logs"></a><a id="logs"></a>로그를 작성하는 방법
대시보드의 두 곳, 즉 WebJob의 페이지 및 특정 WebJob 호출의 페이지에 로그가 표시됩니다.

![WebJob 페이지에서 로그](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![함수 호출 페이지에서 로그](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

함수 또는 `Main()` 메서드에서 호출한 콘솔 메서드의 출력은 특정 메서드 호출에 대한 페이지가 아니라 WebJob에 대한 대시보드 페이지에 표시됩니다. 메서드 서명의 매개 변수에서 가져오는 TextWriter 개체의 출력은 메서드 호출에 대한 대시보드 페이지에 표시됩니다.

많은 작업 기능이 동시에 실행될 수 있지만 콘솔은 단일 스레드이므로 콘솔 출력을 특정 메서드 호출에 연결할 수 없습니다. 따라서 SDK에서는 각 함수 호출에 고유한 로그 작성기 개체를 제공합니다.

[응용 프로그램 추적 로그](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview)를 기록하려면 INFO로 표시되는 로그를 만드는 `Console.Out` 및 ERROR로 표시되는 로그를 만드는 `Console.Error`를 사용합니다. 그렇지 않으면 Info 및 Error 외에 Verbose, Warning 및 Critical 수준을 제공하는 [추적 또는 TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx)를 사용합니다. 응용 프로그램 추적 로그는 Azure 웹 앱을 구성한 방식에 따라 웹 앱 로그 파일, Azure 테이블 또는 Azure Blob에 표시됩니다. 모든 콘솔 출력과 마찬가지로 가장 최근 100개의 응용 프로그램 로그도 함수 호출에 대한 페이지가 아니라 WebJob에 대한 대시보드 페이지에 표시됩니다.

콘솔 출력은 프로그램이 Azure WebJob에서 실행되는 경우에만 대시보드에 표시되고, 프로그램이 로컬로 실행되거나 다른 환경에서 실행되는 경우에는 표시되지 않습니다.

처리량이 많은 시나리오에 대해 대시보드 로깅을 사용하지 않도록 설정합니다. 기본적으로 SDK는 저장소에 로그를 쓰며, 많은 메시지를 처리하는 경우 이 작업으로 인해 성능이 저하될 수 있습니다. 로깅을 사용하지 않도록 설정하려면 다음 예제와 같이 대시보드 연결 문자열을 null로 설정합니다.

        JobHostConfiguration config = new JobHostConfiguration();       
        config.DashboardConnectionString = "";        
        JobHost host = new JobHost(config);
        host.RunAndBlock();

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

WebJobs SDK 대시보드에서 `TextWriter` 개체 출력은 특정 함수 호출에 대한 페이지로 이동하여 **Toggle Output**을 클릭할 때 표시됩니다.

![함수 호출 링크 클릭](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![함수 호출 페이지에서 로그](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

WebJobs SDK 대시보드에서 콘솔 출력의 최근 100줄은 함수 호출이 아니라 WebJob에 대한 페이지로 이동하여 **Toggle Output**을 클릭할 때 표시됩니다.

![출력 설정/해제 클릭](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

연속 WebJob에서는 응용 프로그램 로그가 웹앱 파일 시스템의 /data/jobs/continuous/*{webjobname}*/job_log.txt에 표시됩니다.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Azure Blob에서 응용 프로그램 로그는 다음과 같습니다. 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

또한 Azure 테이블에서 `Console.Out` 및 `Console.Error` 로그는 다음과 같이 표시됩니다.

![테이블에 대한 정보 로그](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![테이블에 대한 오류 로그](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

사용자 고유의 로거를 연결하려면 [이 예제](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Program.cs)를 참조하세요.

## <a name="a-iderrorsahow-to-handle-errors-and-configure-timeouts"></a><a id="errors"></a>오류를 처리하고 시간 제한을 구성하는 방법
WebJobs SDK에는 지정된 기간 내에 완료되지 않는 경우 함수를 취소하는 데 사용할 수 있는 [Timeout](http://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs) 특성도 포함되어 있습니다. 또한 지정된 기간 내에 너무 많은 오류가 발생한 경우 경고를 표시하는 데 `ErrorTrigger` 특성을 사용할 수 있습니다. 다음은 [ErrorTrigger 예제](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Error-Monitoring)입니다.

```
public static void ErrorMonitor(
[ErrorTrigger("00:01:00", 1)] TraceFilter filter, TextWriter log,
[SendGrid(
    To = "admin@emailaddress.com",
    Subject = "Error!")]
 SendGridMessage message)
{
    // log last 5 detailed errors to the Dashboard
   log.WriteLine(filter.GetDetailedMessage(5));
   message.Text = filter.GetDetailedMessage(1);
}
```

구성 스위치(앱 설정이거나 환경 변수 이름일 수 있음)를 사용하여 함수를 동적으로 사용하거나 사용하지 않도록 설정함으로써 함수가 트리거될 수 있는지 여부를 제어할 수도 있습니다. 샘플 코드는 [WebJobs SDK 샘플 리포지토리](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/MiscOperations/Functions.cs)에서 `Disable` 특성을 참조하세요.

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a> 다음 단계
이 가이드에서는 Azure 큐 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 샘플을 제공했습니다. Azure WebJob 및 WebJob SDK를 사용하는 방법에 대한 자세한 내용은 [Azure WebJob 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.



<!--HONumber=Nov16_HO3-->


