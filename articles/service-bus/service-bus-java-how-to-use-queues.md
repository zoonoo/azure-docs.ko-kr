<properties
	pageTitle="Java와 함께 서비스 버스 큐를 사용하는 방법 | Microsoft Azure"
	description="Azure에서 서비스 버스 큐를 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Java로 작성되었습니다."
	services="service-bus"
	documentationCenter="java"
	authors="sethmanheim"
	manager="timlt"
	/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="sethm"/>

# 서비스 버스 큐를 사용하는 방법

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

이 문서에서는 서비스 버스 큐를 사용하는 방법을 설명합니다. 샘플은 Java로 작성되었으며 [Java용 Azure SDK][]\(영문)를 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기**, **메시지 보내기 및 받기**, **큐 삭제** 등이 포함됩니다.

## 서비스 버스 큐 정의

서비스 버스 큐는 **조정된 메시징** 통신 모델을 지원합니다. 큐를 사용하는 경우 분산 응용 프로그램의 구성 요소가 서로 직접 통신하지 않고 중간자(브로커) 역할을 하는 큐를 통해 메시지를 교환합니다. 메시지 생산자(보낸 사람)는 메시지를 큐로 전달한 후 계속해서 처리합니다. 메시지 소비자(받는 사람)는 비동기적으로 큐에서 메시지를 끌어와서 처리합니다. 생산자는 계속해서 추가 메시지를 처리하고 보내기 위해 소비자의 회신을 기다릴 필요가 없습니다. 큐는 하나 이상의 경쟁 소비자에게 **FIFO(선입선출)** 메시지 배달을 제공합니다. 즉, 일반적으로 메시지가 큐에 추가된 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리됩니다.

![QueueConcepts](./media/service-bus-java-how-to-use-queues/sb-queues-08.png)

서비스 버스 큐는 다양한 시나리오에 사용할 수 있는 범용 기술입니다.

- 다층 계층 Azure 응용 프로그램에서 웹 역할과 작업자 역할 간의 통신
- 하이브리드 솔루션에서 온-프레미스 앱과 Azure 호스티드 앱 간의 통신
- 서로 다른 조직이나 조직의 부서에서 온-프레미스로 실행되는 분산 응용 프로그램 구성 요소 간의 통신

큐를 사용하면 응용 프로그램 규모를 보다 쉽게 확장할 수 있으며 아키텍처의 복원력을 확보할 수 있습니다.

## 서비스 네임스페이스 만들기

Azure에서 서비스 버스 큐 사용을 시작하려면 먼저 네임스페이스를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다.

네임스페이스를 만들려면

1.  [Azure 클래식 포털][]에 로그온합니다.

2.  포털의 왼쪽 탐색 창에서 **서비스 버스**를 클릭합니다.

3.  포털의 아래쪽 창에서 **만들기**를 클릭합니다. ![](./media/service-bus-java-how-to-use-queues/sb-queues-03.png)

4.  **Add a new namespace** 대화 상자에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다. ![](./media/service-bus-java-how-to-use-queues/sb-queues-04.png)

5.  네임스페이스 이름이 사용 가능한지 확인한 후 해당 네임스페이스를 호스트할 국가 또는 지역을 선택합니다(계산 리소스를 배포할 국가/지역과 같아야 함).

	중요: 응용 프로그램을 배포하도록 선택할 지역과 **같은 지역**을 선택합니다. 그러면 최상의 성능을 얻을 수 있습니다.

6. 	대화 상자의 다른 필드는 기본값으로 그대로 두고(**메시징** 및 **표준 계층**) 확인 표시를 클릭합니다. 이제 시스템이 네임스페이스를 만들고 사용하도록 설정합니다. 시스템이 계정에 대한 리소스를 프로비전하는 동안 몇 분 정도 기다려야 할 수도 있습니다.

만든 네임스페이스를 활성화되는 데 약간의 시간이 걸린 후 Azure 포털에 표시됩니다. 계속하기 전에 네임스페이스가 **활성** 상태가 될 때까지 기다리세요.

## 네임스페이스에 대한 기본 관리 자격 증명 얻기

새 네임스페이스에 대해 큐 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다. 포털에서 이러한 자격 증명을 가져올 수 있습니다.

1.  왼쪽 탐색 창에서 **서비스 버스** 노드를 클릭하여 사용 가능한 네임스페이스 목록을 표시합니다. ![](./media/service-bus-java-how-to-use-queues/sb-queues-13.png)

2.  표시된 목록에서 방금 만든 네임스페이스를 클릭합니다.

3.  **구성**을 클릭하여 네임스페이스에 대한 공유 액세스 정책을 확인합니다. ![](./media/service-bus-java-how-to-use-queues/sb-queues-14.png)

4.  기본 키를 적어 두거나 클립보드에 복사해 둡니다.

## 서비스 버스를 사용하도록 응용 프로그램 구성

이 샘플을 빌드하기 전에 [Azure SDK for Java][]를 설치했는지 확인하세요. Eclipse를 사용하는 경우 Azure SDK for Java를 포함하고 있는 [Eclipse용 Azure Toolkit][]를 설치할 수 있습니다. 그런 다음 **Java용 Microsoft Azure 라이브러리**를 프로젝트에 추가할 수 있습니다.

![](./media/service-bus-java-how-to-use-queues/eclipselibs.png)

Java 파일 맨 위에 다음 `import` 문을 추가합니다.

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## 큐 만들기

**ServiceBusContract** 클래스를 통해 서비스 버스 큐에 대한 관리 작업을 수행할 수 있습니다. **ServiceBusContract** 개체는 관리에 필요한 SAS 토큰 사용 권한을 캡슐화하는 적합한 구성으로 생성되며, Azure와의 통신 지점은 **ServiceBusContract** 클래스뿐입니다.

**ServiceBusService** 클래스는 큐를 만들고 열거 및 삭제하기 위한 메서드를 제공합니다. 아래 예제에서는 **ServiceBusService** 개체를 사용하여 "HowToSample" 네임스페이스로 "TestQueue" 큐를 만드는 방법을 보여 줍니다.

		Configuration config =
			ServiceBusConfiguration.configureWithSASAuthentication(
					"HowToSample",
					"RootManageSharedAccessKey",
					"SAS_key_value",
					".servicebus.windows.net"
					);

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

**QueueInfo**에는 큐의 속성을 조정하는 데 사용할 수 있는 메서드가 있습니다. 예를 들어 큐에 전송되는 메시지에 적용할 기본 TTL(Time-To-Live) 값을 설정할 수 있습니다. 다음 예제에서는 최대 크기가 5GB인 `TestQueue` 큐를 만드는 방법을 보여 줍니다.

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateQueueResult result = service.createQueue(queueInfo);

**ServiceBusContract** 개체의 **listQueues** 메서드를 사용하여 서비스 네임스페이스 내에 지정된 이름의 큐가 이미 있는지 확인할 수 있습니다.

## 큐에 메시지 보내기

서비스 버스 큐에 메시지를 보내기 위해 응용 프로그램은 **ServiceBusContract** 개체를 얻습니다. 다음 코드는 위에서 `HowToSample` 네임스페이스 내에서 이전에 만든 `TestQueue` 큐에 대한 메시지를 보내는 방법을 보여 줍니다.

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

서비스 버스 큐로 보내고 받은 메시지는 [BrokeredMessage][] 클래스 인스턴스입니다. [BrokeredMessage][] 개체에는 표준 속성 집합(예: [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 및 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은 [BrokeredMessage][] 생성자에 직렬화 가능 개체를 전달하여 메시지 본문을 설정할 수 있으며, 적절한 직렬 변환기가 개체를 직렬화하는 데 사용됩니다. 또는 **java.IO.InputStream** 개체를 제공할 수 있습니다.

다음 예제에서는 이전 코드 조각에서 얻은 `TestQueue` **MessageSender**에 테스트 메시지 5개를 보내는 방법을 보여 줍니다.

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i);
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

서비스 버스 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다.

## 큐에서 메시지 받기

큐에서 메시지를 받는 기본 방법은 **ServiceBusContract** 개체를 사용하는 것입니다. 받은 메시지는 **ReceiveAndDelete** 및 **PeekLock**의 두 가지 모드로 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, 서비스 버스가 큐 메시지에 대한 읽기 요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. 기본 모드인 **ReceiveAndDelete** 모드는 가장 단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

**PeekLock** 모드에서는 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지에 대해 **Delete**를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스는 **Delete** 호출을 확인한 후 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

다음 예제에서는 **PeekLock** 모드(기본 모드가 아닌)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 아래 예제는 무한 루프를 만들고 메시지가 "TestQueue"에 도착하면 처리합니다.

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

## 응용 프로그램 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 **deleteMessage** 메서드 대신 **unlockMessage** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **deleteMessage** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **getMessageId** 메서드를 사용합니다.

## 다음 단계

지금까지 서비스 버스 큐의 기본 사항에 대해 알아보았습니다. 자세한 내용은 [큐, 토픽 및 구독][]을 참조하세요.

자세한 내용은 [Java개발자 센터](/develop/java/)를 참조하세요.


  [Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [Java용 Azure SDK]: http://azure.microsoft.com/develop/java/
  [Eclipse용 Azure Toolkit]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [큐, 토픽 및 구독]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [Azure 클래식 포털]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0525_2016-->