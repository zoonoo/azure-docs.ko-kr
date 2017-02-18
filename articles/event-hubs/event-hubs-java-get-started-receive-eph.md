---
title: "Java를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs"
description: "Java를 사용하여 Event Hubs에서 수신 시작"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 38e3be53-251c-488f-a856-9a500f41b6ca
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: 852a0fab2fa7a50eb315e7107749e391031b463e


---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Java를 사용하여 Azure Event Hubs에서 이벤트 수신

## <a name="introduction"></a>소개
이벤트 허브는 연결된 장치와 응용 프로그램에서 생성되는 엄청난 양의 데이터를 처리 및 분석할 수 있도록 초당 수백만 개의 이벤트를 수용할 수 있는 확장성이 뛰어난 수집 시스템입니다. 이벤트 허브로 수집된 데이터는 실시간 분석 공급자나 저장소 클러스터를 사용하여 변환하고 저장할 수 있습니다.

자세한 내용은 [이벤트 허브 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Java로 작성된 콘솔 응용 프로그램을 사용하여 이벤트 허브로 이벤트를 수신하는 방법을 보여 줍니다.

이 자습서를 완료하려면 다음이 필요합니다.

* Java 개발 환경. 이 자습서에서는 [Eclipse](https://www.eclipse.org/)를 사용한다고 가정합니다.
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 체험</a>을 참조하세요.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Java에서 EventProcessorHost를 사용하여 메시지 수신

EventProcessorHost는 영구적 검사점을 관리하여 이벤트 허브의 이벤트 수신을 간소화하고 이러한 이벤트에서 병렬 수신하는 Java 클래스입니다. EventProcessorHost를 사용하면 다른 노드에 호스트된 수신기를 비롯한 여러 수신기 간에 이벤트를 분할할 수 있습니다. 이 예제에서는 단일 수신기에 대해 EventProcessorHost를 사용하는 방법을 보여 줍니다.

### <a name="create-a-storage-account"></a>저장소 계정 만들기
EventProcessorHost를 사용하려면 [Azure Storage 계정][Azure Storage account]이 있어야 합니다.

1. [Azure 클래식 포털][Azure classic portal]에 로그온한 다음 화면 맨 아래에서 **새로 만들기**를 클릭합니다.
2. **Data Services**, **Storage**, **빠른 생성**을 차례로 클릭한 다음 저장소 계정 이름을 입력합니다. 원하는 지역을 선택하고 **저장소 계정 만들기**를 클릭합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. 새로 만든 저장소 계정을 클릭한 후 **액세스 키 관리**를 클릭합니다.
   
    ![](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    이 자습서에서 나중에 사용할 기본 선택키를 복사합니다.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>EventProcessor 호스트를 사용하여 Java 프로젝트 만들기
Event Hubs에 대한 Java 클라이언트 라이브러리는 [Maven 중앙 리포지토리][Maven Package]의 Maven 프로젝트에 사용할 수 있으며 Maven 프로젝트 파일 내의 다음 종속성 선언을 사용하여 참조할 수 있습니다.    

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```

다양한 유형의 빌드 환경을 위해, [Maven 중앙 리포지토리][Maven Package] 또는 [GitHub의 릴리스 배포 지점](https://github.com/Azure/azure-event-hubs/releases)에서 최근에 릴리스된 JAR 파일을 명시적으로 가져올 수 있습니다.  

1. 다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 응용 프로그램에 대한 새 Maven 프로젝트를 만듭니다. 이 클래스를 ```ErrorNotificationHandler```라고 합니다.     
   
    ```Java
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
2. 다음 코드를 사용하여 ```EventProcessor```(이)라는 클래스를 새로 만듭니다.
   
    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;
   
    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;
   
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }
   
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
   
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }
   
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
   
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```
3. 다음 코드를 사용하여 ```EventProcessorSample```(이)라는 최종 클래스 하나를 만듭니다.
   
    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;
   
    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";
   
            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
   
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
   
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
   
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }
   
            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
   
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
   
            System.out.println("End of sample");
        }
    }
    ```
4. 다음 필드를 이벤트 허브 및 저장소 계정을 만들 때 사용한 값으로 바꿉니다.
   
    ```Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
   
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
   
    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [!NOTE]
> 이 자습서에서는 EventProcessorHost의 단일 인스턴스를 사용합니다. 처리량을 늘리려면 EventProcessorHost의 여러 인스턴스를 사용하는 것이 좋습니다. 이러한 경우 다양한 인스턴스가 자동으로 서로 조정하여 수신된 이벤트의 부하를 분산합니다. 여러 수신기가 각각 이벤트를 *모두* 처리하도록 하려면 **ConsumerGroup** 개념을 사용해야 합니다. 서로 다른 컴퓨터에서 이벤트를 수신하는 경우 EventProcessorHost 인스턴스의 이름을 해당 인스턴스가 배포된 컴퓨터 또는 역할을 기준으로 지정하면 유용할 수 있습니다.
> 
> 

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[Azure Storage account]: ../storage/storage-create-storage-account.md
[Azure classic portal]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [이벤트 허브 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md


<!--HONumber=Feb17_HO1-->


