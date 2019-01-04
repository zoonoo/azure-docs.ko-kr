---
title: Java를 사용하여 이벤트 수신 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에 이벤트를 받는 Java 애플리케이션을 만드는 연습을 할 수 있습니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e29cf43f490bf5e8bac5e5c36b16476f93d80bfa
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081964"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Java를 사용하여 Azure Event Hubs에서 이벤트 수신

Event Hubs는 연결된 디바이스와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. Event Hubs로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Java로 작성된 콘솔 응용 프로그램을 사용하여 이벤트 허브로 이벤트를 수신하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.
* 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만듭니다.

이 자습서의 코드는 전체 작동 중인 응용 프로그램을 확인하기 위해 검사할 수 있는 [GitHub의 EventProcessorSample 코드](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample)를 기반으로 합니다.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java에서 EventProcessorHost를 사용하여 메시지 수신

**EventProcessorHost**는 영구적 검사점을 관리하여 Event Hubs의 이벤트 수신을 간소화하고 이러한 Event Hubs에서 병렬 수신하는 Java 클래스입니다. EventProcessorHost를 사용하면 다른 노드에 호스트된 경우라도 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 EventProcessorHost를 사용하는 방법을 보여 줍니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기

EventProcessorHost를 사용하려면 [Azure Storage 계정][Azure Storage account]이 있어야 합니다.

1. [Azure Portal][Azure portal]에 로그온하고 화면 왼쪽에서 **+리소스 만들기**를 클릭합니다.
2. **저장소**를 클릭한 다음, **저장소 계정**을 클릭합니다. **저장소 계정 만들기** 창에서 저장소 계정에 사용할 이름을 입력합니다. 필드의 나머지 부분을 입력하고 원하는 지역을 선택한 다음 **만들기**를 클릭합니다.
   
    ![저장소 계정 만들기](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. 새로 만든 저장소 계정을 클릭한 다음, **액세스 키**를 클릭합니다.
   
    ![액세스 키 가져오기](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Key1 값을 임시 위치로 복사합니다. 이 자습서의 뒷부분에서 사용합니다.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor 호스트를 사용하여 Java 프로젝트 만들기

Event Hubs에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리][Maven Package]의 Maven 프로젝트에 사용할 수 있으며 Maven 프로젝트 파일 내의 다음 종속성 선언을 사용하여 참조할 수 있습니다. 아티팩트 azure-eventhubs-eph의 현재 버전은 2.0.1이고 아티팩트 azure-eventhubs의 현재 버전은 1.0.2입니다.    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.0.1</version>
</dependency>
```

다양한 형식의 빌드 환경을 위해, [Maven 중앙 리포지토리][Maven Package]에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.  

1. 다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 응용 프로그램에 대한 새 Maven 프로젝트를 만듭니다. 클래스는 `ErrorNotificationHandler`라고 합니다.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. 다음 코드를 사용하여 `EventProcessorSample`(이)라는 클래스를 새로 만듭니다. 자리 표시자를 이벤트 허브 및 저장소 계정을 만들 때 사용한 값으로 바꿉니다.
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. 다음 코드를 사용하여 `EventProcessor`라는 클래스를 하나 이상 만듭니다.
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

이 자습서에서는 EventProcessorHost의 단일 인스턴스를 사용합니다. 처리량을 늘리려면 EventProcessorHost의 여러 인스턴스를 개별 머신에서 실행하는 것이 좋습니다.  그러면 중복성도 함께 제공됩니다. 이러한 경우 다양한 인스턴스가 자동으로 서로 조정하여 수신된 이벤트의 부하를 분산합니다. 여러 수신기가 각각 이벤트를 *모두* 처리하도록 하려면 **ConsumerGroup** 개념을 사용해야 합니다. 서로 다른 컴퓨터에서 이벤트를 수신하는 경우 EventProcessorHost 인스턴스의 이름을 해당 인스턴스가 배포된 컴퓨터 또는 역할을 기준으로 지정하면 유용할 수 있습니다.

## <a name="publishing-messages-to-eventhub"></a>EventHub에 메시지 게시

소비자가 메시지를 검색할 수 있으려면 먼저 게시자가 파티션에 메시지를 게시해야 합니다. com.microsoft.azure.eventhubs.EventHubClient 개체에서 sendSync() 메서드를 사용하여 메시지를 이벤트 허브에 동기적으로 게시하면 파티션 키의 지정 여부에 따라 라운드 로빈 방식으로 메시지를 특정 파티션으로 보내거나 사용 가능한 모든 파티션에 분산할 수 있다는 점이 중요합니다.

파티션 키를 나타내는 문자열이 지정되면 이벤트로 보낼 파티션을 결정하기 위해 키가 해시됩니다.

파티션 키를 설정하지 않으면 사용 가능한 모든 파티션으로 메시지가 라운드 로빈됩니다.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>EventProcessorHost(EPH)에 대한 사용자 지정 CheckpointManager 구현

API는 기본 구현이 사용 사례와 호환되지 않는 시나리오를 위해 사용자 지정 검사점 관리자를 구현하는 메커니즘을 제공합니다.

기본 검사점 관리자는 Blob 저장소를 사용하지만, EPH에서 사용하는 검사점 관리자를 사용자 고유의 구현으로 재정의하는 경우 원하는 저장소를 사용하여 검사점 관리자 구현을 지원할 수 있습니다.

com.microsoft.azure.eventprocessorhost.ICheckpointManager 인터페이스를 구현하는 클래스를 만듭니다.

사용자 지정 검사점 관리자(com.microsoft.azure.eventprocessorhost.ICheckpointManager) 구현 사용

구현 내에서, 기본 검사점 메커니즘을 재정의하여 사용자 고유의 데이터 저장소(SQL Server, CosmosDB, Azure Cache for Redis 등)에 따라 고유한 검사점을 구현할 수 있습니다. 소비자 그룹의 이벤트를 처리하는 모든 EPH 인스턴스가 검사점 관리자 구현을 지원하는 데 사용된 저장소에 액세스할 수 있도록 하는 것이 좋습니다.

사용자 환경에 제공되는 모든 데이터 저장소를 사용할 수 있습니다.

com.microsoft.azure.eventprocessorhost.EventProcessorHost 클래스는 EventProcessorHost의 검사점 관리자를 재정의할 수 있는 2가지 생성자를 제공합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 이벤트 허브에서 메시지를 받는 Java 애플리케이션을 만들었습니다. Java를 사용하여 이벤트 허브에 이벤트를 보내는 방법을 알아보려면 [이벤트 허브에서 이벤트 보내기 - Java](event-hubs-java-get-started-send.md)를 참조하세요.

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
