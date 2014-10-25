<properties linkid="dev-java-how-to-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Java) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Java" description="Learn how to use Service Bus queues in Azure. Code samples written in Java." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Queues" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# 서비스 버스 큐를 사용하는 방법

이 가이드에서는 서비스 버스 큐를 사용하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure SDK][]를 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기**, **메시지 보내기 및 받기**, **큐 삭제** 등이 포함됩니다.

## 목차

-   [서비스 버스 큐 정의][]
-   [서비스 네임스페이스 만들기][]
-   [네임스페이스에 대한 기본 관리 자격 증명 얻기][]
-   [서비스 버스를 사용하도록 응용 프로그램 구성][]
-   [방법: 보안 토큰 공급자 만들기][]
-   [방법: 큐 만들기][방법: 보안 토큰 공급자 만들기]
-   [방법: 큐에 메시지 보내기][]
-   [방법: 큐에서 메시지 받기][]
-   [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리][]
-   [다음 단계][]

[WACOM.INCLUDE [howto-service-bus-queues][]]

## <a name="bkmk_ConfigApp"> </a> 서비스 버스를 사용하도록 응용 프로그램 구성

Java 파일 맨 위에 다음 import 문을 추가합니다.

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*; 
    import com.microsoft.windowsazure.services.core.*; 
    import javax.xml.datatype.*;

## <a name="bkmk_HowToCreateQueue"> </a>큐를 만드는 방법

**ServiceBusContract** 클래스를 통해 서비스 버스 큐에 대한 관리 작업을 수행할 수 있습니다. **ServiceBusContract** 개체는 관리에 필요한 토큰 사용 권한을 캡슐화하는 적절한 구성으로 생성되고 **ServiceBusContract** 클래스는 Azure와의 단일 통신 지점입니다.

**ServiceBusService** 클래스는 큐를 만들고 열거 및 삭제하기 위한 메서드를 제공합니다. 아래 예제에서는 **ServiceBusService** 개체를 사용하여 "HowToSample" 네임스페이스로 "TestQueue" 큐를 만드는 방법을 보여 줍니다.

    Configuration config = 
        ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {     
        CreateQueueResult result = service.createQueue(queueInfo);
    }
    catch (ServiceException e)
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

큐의 속성을 조정할 수 있는 QueueInfo의 메서드가 있습니다(예: 큐로 전송된 메시지에 적용할 기본 "TTL(Time-to-Live)" 값 설정). 다음 예제에서는 최대 크기가 5GB인 "TestQueue" 큐를 만드는 방법을 보여 줍니다.

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateQueueResult result = service.createQueue(queueInfo);

**ServiceBusContract** 개체의 **listQueues** 메서드를 사용하여 서비스 네임스페이스 내에 지정된 이름의 큐가 이미 있는지
확인할 수 있습니다.

## <a name="bkmk_HowToSendMsgs"> </a>큐에 메시지를 보내는 방법

서비스 버스 큐에 메시지를 보내기 위해 응용 프로그램은 **ServiceBusContract** 개체를 얻습니다. 아래 코드는 위의 "HowToSample" 서비스 네임스페이스 내에서 만든 "TestQueue" 큐에 대한 메시지를 보내는 방법을 보여 줍니다.

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e) 
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

서비스 버스 큐로 보내고 받은 메시지는 **BrokeredMessage** 클래스의 인스턴스입니다. **BrokeredMessage** 개체에는 표준 속성 집합(예: **getLabel**, **getTimeToLive**, **setLabel** 및 **setTimeToLive**), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은 **BrokeredMessage** 생성자에 직렬화된 개체를 전달하여 메시지 본문을 설정할 수 있으며, 적절한 serializer가 개체를 직렬화하는 데 사용됩니다. 또는 **java.IO.InputStream**을 제공할 수 있습니다.

다음 예제는 위의 코드 조각에서 얻은 "TestQueue" **MessageSender**에 테스트 메시지 5개를 보내는 방법을 보여 줍니다.

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i); 
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

서비스 버스 큐는 256KB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB임). 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="bkmk_HowToReceiveMsgs"> </a>큐에서 메시지를 받는 방법

큐에서 메시지를 받는 기본 방법은 **ServiceBusContract** 개체를 사용하는 것입니다. 받은 메시지는 **ReceiveAndDelete** 및 **PeekLock**의 두 가지 모드로 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, 서비스 버스가 큐 메시지에 대한 읽기 요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. 기본 모드인 **ReceiveAndDelete** 모드는 가장 단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

**PeekLock** 모드에서는 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 **Delete**를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스는 **Delete** 호출을 확인한 후 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

아래 예에서는 **PeekLock** 모드(기본 모드 아님)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 아래 예에서는 무한 루프를 만들고 메시지가 "TestQueue"에 도착하면 처리합니다.

        try
    {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

        while(true)  { 
             ReceiveQueueMessageResult resultQM = 
                    service.receiveQueueMessage("TestQueue", opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null)
            {
                System.out.println("MessageID: " + message.getMessageId());    
                // Display the queue message.
                System.out.print("From queue: ");
                byte[] b = new byte[200];
                String s = null;
                int numRead = message.getBody().read(b);
                while (-1 != numRead)
                {
                    s = new String(b);
                    s = s.trim();
                    System.out.print(s);
                    numRead = message.getBody().read(b);
                }
                System.out.println();
                System.out.println("Custom Property: " + 
                    message.getProperty("MyProperty"));
                // Remove message from queue.
                System.out.println("Deleting this message.");
                //service.deleteMessage(message);
            }  
            else  
            {        
                System.out.println("Finishing up - no more messages.");        
                break; 
                // Added to handle no more messages.
                // Could instead wait for more messages to be added.
            }
        }
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }
    catch (Exception e) {
        System.out.print("Generic exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }   

## <a name="bkmk_HowToHandleAppCrashes"> </a> 응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 **deleteMessage** 메서드 대신 **unlockMessage** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **deleteMessage** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **getMessageId** 메서드를 사용합니다.

## <a name="bkmk_NextSteps"> </a>다음 단계

지금까지 서비스 버스 큐의 기본 사항에 대해 알아보았습니다. 자세한 내용은 MSDN 항목 [큐, 토픽 및 구독][]을 참조하세요.

  [Java용 Azure SDK]: http://azure.microsoft.com/ko-KR/develop/java/
  [서비스 버스 큐 정의]: #what-are-service-bus-queues
  [서비스 네임스페이스 만들기]: #create-a-service-namespace
  [네임스페이스에 대한 기본 관리 자격 증명 얻기]: #obtain-default-credentials
  [서비스 버스를 사용하도록 응용 프로그램 구성]: #bkmk_ConfigApp
  [방법: 보안 토큰 공급자 만들기]: #bkmk_HowToCreateQueue
  [방법: 큐에 메시지 보내기]: #bkmk_HowToSendMsgs
  [방법: 큐에서 메시지 받기]: #bkmk_HowToReceiveMsgs
  [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리]: #bkmk_HowToHandleAppCrashes
  [다음 단계]: #bkmk_NextSteps
  [howto-service-bus-queues]: ../includes/howto-service-bus-queues.md
  [큐, 토픽 및 구독]: http://msdn.microsoft.com/ko-KR/library/windowsazure/hh367516.aspx
