---
title: "WebJob SDK를 사용하여 Azure 서비스 버스로 작업하는 방법"
description: "WebJobs SDK를 사용하여 Azure 서비스 버스 큐 및 항목으로 작업하는 방법에 대해 알아봅니다."
services: app-service\web, service-bus
documentationcenter: .net
author: tdykstra
manager: erikre
editor: jimbe
ms.assetid: 2114a934-135b-42b8-871c-6cc040214e76
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/01/2016
ms.author: tdykstra
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4d5205b1c693b8c1f199235784263f3f7b6189ff
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-azure-service-bus-with-the-webjobs-sdk"></a>WebJob SDK를 사용하여 Azure 서비스 버스로 작업하는 방법
## <a name="overview"></a>개요
이 가이드에서는 Azure 서비스 버스 메시지를 받았을 때 프로세스를 트리거하는 방법을 보여 주는 C# 코드 샘플을 제공합니다. 코드 샘플에서는 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 버전 1.x를 사용합니다.

이 가이드에서는 [저장소 계정을 가리키는 연결 문자열을 사용하여 Visual Studio에서 WebJob 프로젝트를 만드는 방법](websites-dotnet-webjobs-sdk-get-started.md)을 알고 있는 것으로 가정합니다.

코드 조각은 다음 예제와 같이 `JobHost` 개체를 만드는 코드가 아니라 함수만 보여 줍니다.

```
public class Program
{
   public static void Main()
   {
      JobHostConfiguration config = new JobHostConfiguration();
      config.UseServiceBus();
      JobHost host = new JobHost(config);
      host.RunAndBlock();
   }
}
```

[전체 서비스 버스 코드 예제](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Program.cs) 는 GitHub.com의 azure-webjobs-sdk-samples 리포지토리에 있습니다.

## <a name="a-idprerequisitesa-prerequisites"></a><a id="prerequisites"></a> 필수 조건
서비스 버스로 작업하려면 다른 WebJobs SDK 패키지와 함께 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet 패키지를 설치해야 합니다. 

또한 저장소 연결 문자열과 함께 AzureWebJobsServiceBus 연결 문자열을 설정해야 합니다.  다음 예제와 같이 App.config 파일의 `connectionStrings` 섹션에서 이 작업을 수행할 수 있습니다.

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

App.config 파일에 서비스 버스 연결 문자열 설정을 포함하는 샘플 프로젝트의 경우 [서비스 버스 예제](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus)를 참조하세요. 

Azure 런타임 환경에서 연결 문자열을 설정할 수도 있으며, 이 문자열은 Azure에서 WebJob이 실행될 때 App.config 설정을 재정의합니다. 자세한 내용은 [WebJobs SDK 시작](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account)을 참조하세요.

## <a name="a-idtriggera-how-to-trigger-a-function-when-a-service-bus-queue-message-is-received"></a><a id="trigger"></a> 서비스 버스 큐 메시지가 수신될 때 함수를 트리거하는 방법
큐 메시지가 수신될 때 WebJobs SDK에서 호출하는 함수를 작성하려면 `ServiceBusTrigger` 특성을 사용합니다. 특성 생성자는 폴링할 큐의 이름을 지정하는 매개 변수를 사용합니다.

### <a name="how-servicebustrigger-works"></a>ServiceBusTrigger 작동 방식
SDK는 `PeekLock` 모드로 메시지를 받아 함수가 성공적으로 완료된 경우 메시지에서 `Complete`를 호출하고, 함수가 실패한 경우 `Abandon`을 호출합니다. 함수가 `PeekLock` 시간 제한보다 오래 실행되는 경우 잠금이 자동으로 갱신됩니다.

서비스 버스는 WebJobs SDK에 의해 제어 또는 구성할 수 없는 자체 포이즌 큐 처리를 수행합니다. 

### <a name="string-queue-message"></a>문자열 큐 메시지
다음 코드 샘플은 문자열이 포함된 큐 메시지를 읽고 WebJobs SDK 대시보드에 씁니다.

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**참고:** WebJobs SDK를 사용하지 않는 응용 프로그램에서 큐 메시지를 만드는 경우 [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) 을 "text/plain"으로 설정해야 합니다.

### <a name="poco-queue-message"></a>POCO 큐 메시지
SDK에서는 POCO( [Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 유형에 대한 JSON이 포함된 큐 메시지를 자동으로 deserialize합니다. 다음 코드 샘플은 `BlobName` 속성을 가진 `BlobInformation` 개체가 포함된 큐 메시지를 읽습니다.

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

POCO 속성을 사용하여 동일한 함수의 Blob 및 테이블로 작업하는 방법을 보여 주는 코드 샘플은 [이 문서의 저장소 큐 버전](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs)을 참조하세요.

큐 메시지를 만든 코드가 WebJobs SDK를 사용하지 않을 경우 다음 예제와 유사한 코드를 사용합니다.

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### <a name="types-servicebustrigger-works-with"></a>ServiceBusTrigger가 작동하는 유형
`string` 및 POCO 유형 외에 바이트 배열 또는 `BrokeredMessage` 개체에서 `ServiceBusTrigger` 특성을 사용할 수 있습니다.

## <a name="a-idcreatea-how-to-create-service-bus-queue-messages"></a><a id="create"></a> 서비스 버스 큐 메시지를 만드는 방법
새 큐 메시지를 만드는 함수를 작성하려면 `ServiceBus` 특성을 사용하고 특성 생성자로 큐 이름을 전달합니다. 

### <a name="create-a-single-queue-message-in-a-non-async-function"></a>비동기가 아닌 함수로 단일 큐 메시지를 만들기
다음 코드 샘플은 출력 매개 변수를 사용하여 "inputqueue"라는 큐에 수신된 메시지와 동일한 콘텐츠를 가진 새 메시지를 "outputqueue"라는 큐에 만듭니다.

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

단일 큐 메시지를 만들기 위한 출력 매개 변수는 다음 유형 중 하나일 수 있습니다.

* `string`
* `byte[]`
* `BrokeredMessage`
* 사용자가 정의한 serialize 가능 POCO 유형. JSON으로 자동으로 serialize됨

POCO 유형 매개 변수의 경우 함수가 종료되면 큐 메시지가 항상 만들어집니다. 매개 변수가 null인 경우에는 SDK에서 메시지가 수신되고 deserialize될 때 null을 반환하는 큐 메시지를 만듭니다. 다른 유형의 경우 매개 변수가 null이면 큐 메시지가 만들어지지 않습니다.

### <a name="create-multiple-queue-messages-or-in-async-functions"></a>여러 큐 메시지 만들기 또는 비동기 함수로 큐 메시지 만들기
여러 메시지를 만들려면 다음 코드 샘플과 같이 `ICollector<T>` 또는 `IAsyncCollector<T>`에서 `ServiceBus` 특성을 사용합니다.

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

`Add` 메서드를 호출하면 각 큐 메시지가 즉시 생성됩니다.

## <a name="a-idtopicsahow-to-work-with-service-bus-topics"></a><a id="topics"></a>서비스 버스 항목으로 작업하는 방법
서비스 버스 항목에 메시지가 수신될 때 SDK에서 호출하는 함수를 작성하려면 다음 코드 샘플과 같이 항목 이름 및 구독 이름을 가져오는 생성자와 함께 `ServiceBusTrigger` 특성을 사용합니다.

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

항목에 대한 메시지를 만들려면 큐 이름과 동일한 방식으로 항목 이름에서 `ServiceBus` 특성을 사용합니다.

## <a name="features-added-in-release-11"></a>버전 1.1에 추가된 기능
릴리스 1.1에서 다음과 같은 기능이 추가되었습니다.

* `ServiceBusConfiguration.MessagingProvider`를 통해 메시지 처리를 상세하게 사용자 지정할 수 있습니다.
* `MessagingProvider`는 서비스 버스 `MessagingFactory` 및 `NamespaceManager`의 사용자 지정을 지원합니다.
* `MessageProcessor` 전략 패턴을 통해 큐/토픽별로 프로세서를 지정할 수 있습니다.
* 메시지 처리 동시성이 기본적으로 지원됩니다. 
* `ServiceBusConfiguration.MessageOptions`를 통해 `OnMessageOptions`를 쉽게 사용자 지정할 수 있습니다.
* 관리 권한이 없을 수 있는 시나리오에 대해 `ServiceBusTriggerAttribute`/`ServiceBusAttribute`에서 [AccessRights](https://github.com/Azure/azure-webjobs-sdk-samples/blob/master/BasicSamples/ServiceBus/Functions.cs#L71)를 지정하는 것을 허용합니다. 

## <a name="a-idqueuesarelated-topics-covered-by-the-storage-queues-how-to-article"></a><a id="queues"></a>저장소 큐 방법 문서에서 다루는 관련 항목
서비스 버스에 특정하지 않은 WebJobs SDK 시나리오에 대한 자세한 내용은 [WebJobs SDK를 사용하여 Azure 큐 저장소로 작업하는 방법](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)을 참조하세요. 

이 문서에서 다루는 항목은 다음과 같습니다.

* 비동기 함수
* 여러 인스턴스
* 정상 종료
* 함수 본문에 WebJobs SDK 특성 사용
* 코드에서 SDK 연결 문자열 설정
* 코드에서 WebJobs SDK 생성자 매개 변수 값 설정
* 수동으로 함수 트리거
* 로그 작성

## <a name="a-idnextstepsa-next-steps"></a><a id="nextsteps"></a> 다음 단계
이 가이드에서는 Azure 서비스 버스 작업에 대한 일반적인 시나리오를 처리하는 방법을 보여 주는 코드 샘플을 제공했습니다. Azure WebJob 및 WebJob SDK를 사용하는 방법에 대한 자세한 내용은 [Azure WebJob 권장 리소스](http://go.microsoft.com/fwlink/?linkid=390226)를 참조하세요.


