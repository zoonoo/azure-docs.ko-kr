<properties urlDisplayName="Service Bus Topics" pageTitle="서비스 버스 토픽을 사용하는 방법(Java)-Azure" metaKeywords="Azure 서비스 버스 토픽 시작, 서비스 토픽 시작, Azure 게시 구독 메시징, Azure 메시징 토픽 및 구독, 서비스 버스 토픽 Java" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Java applications." metaCanonical="" services="service-bus" documentationCenter="Java" title="How to Use Service Bus Topics/Subscriptions" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="robmcm" />

# 서비스 버스 토픽/구독을 사용하는 방법

이 가이드에서는 서비스 버스 토픽과 구독을 사용하는 방법을
보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure SDK][](영문)를 사용합니다. 여기서 다루는 시나리오에는 **토픽 및 구독
만들기**, **구독 필터 판들기**, **토픽에
메시지 보내기**, **구독에서 메시지 받기**, 
**토픽 및 구독 삭제** 등이 포함됩니다.

## 목차

-   [서비스 버스 토픽 및 구독 정의][]
-   [서비스 네임스페이스 만들기][]
-   [네임스페이스에 대한 기본 관리 자격 증명 얻기][]
-   [서비스 버스를 사용하도록 응용 프로그램 구성][]
-   [방법: 토픽 만들기][]
-   [방법: 구독 만들기][]
-   [방법: 토픽에 메시지 보내기][]
-   [방법: 구독에서 메시지 받기][]
-   [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리][]
-   [방법: 토픽 및 구독 삭제][]
-   [다음 단계][]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <a name="bkmk_ConfigYourApp"> </a>서비스 버스를 사용하도록 응용 프로그램 구성

Java 파일 맨 위에 다음 import 문을 추가합니다.

    // Include the following imports to use service bus APIs
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;
    import javax.xml.datatype.*;

Java용 Azure 라이브러리를 빌드 경로에 추가하고 프로젝트 배포 어셈블리에 포함합니다.

## <a name="bkmk_HowToCreateTopic"> </a>토픽을 만드는 방법

서비스 버스 토픽에 대한 관리 작업은
**ServiceBusContract** 클래스를 사용하여 수행할 수 있습니다. **ServiceBusContract** 개체는
관리에 필요한 토큰 사용 권한을 캡슐화하는
적절한 구성으로 생성되고 **ServiceBusContract** 클래스는
Azure와의 단일 통신 지점입니다.

**ServiceBusService** 클래스는 토픽을 만들고 열거 및 삭제하기 위한
메서드를 제공합니다. 아래 예제에서는 **ServiceBusService** 개체를
사용하여 "HowToSample" 네임스페이스로 "TestTopic" 토픽을 만드는 방법을 보여 줍니다.

    Configuration config = 
    	ServiceBusConfiguration.configureWithWrapAuthentication(
          "HowToSample",
          "your_service_bus_owner",
          "your_service_bus_key",
          ".servicebus.windows.net",
          "-sb.accesscontrol.windows.net/WRAPv0.9");

	ServiceBusContract service = ServiceBusService.create(config);
    TopicInfo topicInfo = new TopicInfo("TestTopic");
	try  
	{
    	CreateTopicResult result = service.createTopic(topicInfo);
	}
	catch (ServiceException e) {
		System.out.print("ServiceException encountered: ");
	    System.out.println(e.getMessage());
		System.exit(-1);
	}

토픽의 속성을 조정할 수 있는 **TopicInfo**의 메서드가
있습니다. 예를 들어 토픽에 전송되는 메시지에 적용할 기본 "TTL(Time-To-Live)" 값을
설정할 수 있습니다. 아래 예제에서는
최대 크기가 5GB이인 "TestTopic"이라는 토픽을 만드는 방법을 보여 줍니다.

    long maxSizeInMegabytes = 5120;  
	TopicInfo topicInfo = new TopicInfo("TestTopic");  
    topicInfo.setMaxSizeInMegabytes(maxSizeInMegabytes); 
    CreateTopicResult result = service.createTopic(topicInfo);

**listTopics** 메서드를
**ServiceBusContract** 개체에 사용하여 서비스 네임스페이스 내에 지정된 이름의
토픽이 이미 있는지 확인할 수 있습니다.

## <a name="bkmk_HowToCreateSubscrip"> </a>구독을 만드는 방법

토픽 구독은 **ServiceBusService**
클래스로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는
메시지 집합을 제한하는 선택적 필터가 있을 수
있습니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우
사용되는 기본 필터입니다. **MatchAll**
필터를 사용하면 토픽에 게시된 모든 메시지가 구독의
가상 큐에 배치됩니다. 아래 예제에서는
"AllMessages"라는 구독을 만들고 기본 **MatchAll**
필터를 사용합니다.

    SubscriptionInfo subInfo = new SubscriptionInfo("AllMessages");
    CreateSubscriptionResult result = 
        service.createSubscription("TestTopic", subInfo);

### 필터를 사용하여 구독 만들기

토픽에 전송된 메시지 중 특정 토픽 구독 내에 나타나야 하는
메시지의 범위를 지정하는 필터를 설정할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는
**SqlFilter**입니다. SQL 필터는
토픽에 게시된 메시지의 속성에 적용됩니다. SQL
필터와 함께 사용할 수 있는 식에 대한 자세한 내용은
SqlFilter.SqlExpression 구문을 참조하세요.

아래 예제에서는
**SqlFilter** 속성이 3보다 큰 메시지만 선택하는
**MessageNumber**를 사용하여 "HighMessages"라는 구독을 만듭니다.

    // Create a "HighMessages" filtered subscription  
	SubscriptionInfo subInfo = new SubscriptionInfo("HighMessages");
    CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
	RuleInfo ruleInfo = new RuleInfo("myRuleGT3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber > 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "HighMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "HighMessages", "$Default");

마찬가지로, 아래 예제에서는
MessageNumber 속성이   
 3보다 작거나 같은 메시지만 선택 하는 SqlFilter를
사용하여   
 "LowMessages"라는 구독을 만듭니다.

    // Create a "LowMessages" filtered subscription
	SubscriptionInfo subInfo = new SubscriptionInfo("LowMessages");
	CreateSubscriptionResult result = 
		service.createSubscription("TestTopic", subInfo);
    RuleInfo ruleInfo = new RuleInfo("myRuleLE3");
	ruleInfo = ruleInfo.withSqlExpressionFilter("MessageNumber <= 3");
	CreateRuleResult ruleResult = 
		service.createRule("TestTopic", "LowMessages", ruleInfo);
    // Delete the default rule, otherwise the new rule won't be invoked.
    service.deleteRule("TestTopic", "LowMessages", "$Default");


이제 "TestTopic"으로 메시지가 전송되는 경우
"AllMessages" 토픽 구독을 구독하는 수신자에게는 항상 배달되고,
"HighMessages" 및 "LowMessages" 토픽을 구독하는 수신자에게는
메시지 내용에 따라 선택적으로
배달됩니다.

## <a name="bkmk_HowToSendMsgs"> </a>토픽에 메시지를 보내는 방법

서비스 버스 토픽에 메시지를 보내기 위해 응용 프로그램은
**ServiceBusContract** 개체를 사용하는 것입니다. 아래 코드에서는
"HowToSample" 서비스 네임스페이스 내에서 위에서 만든
"TestTopic" 토픽에 대한 메시지를 보내는 방법을 보여 줍니다.

    BrokeredMessage message = new BrokeredMessage("MyMessage");
    service.sendTopicMessage("TestTopic", message);

서비스 버스 토픽으로 전송된 메시지는
**BrokeredMessage** 클래스의 인스턴스입니다. **BrokeredMessage** 개체에는 표준 메서드
집합(예: **setLabel** 및 **TimeToLive**), 응용 프로그램별
사용자 지정 속성을 저장하는 데 사용되는 사전 및
임의 응용 프로그램 데이터 본문이 있습니다. 응용 프로그램은
직렬화 가능한 개체를
**BrokeredMessage**의 생성자에 전달하여 메시지 본문을 설정할 수 있으며 적절한 **DataContractSerializer**가
개체를 직렬화하는 데 사용됩니다. 또는
**java.io.InputStream**을 제공할 수 있습니다.

아래 예제에서는 위의 코드 조각에서 얻은
"TestTopic" **MessageSender**에 테스트 메시지 5개를 보내는 방법을 보여 줍니다.
루프가 반복될 때마다 각 메시지의 **MessageNumber** 속성 값이
달라집니다(이에 따라 메시지를 수신하는 구독
결정).

    for (int i=0; i<5; i++)  {
       	// Create message, passing a string message for the body
		BrokeredMessage message = new BrokeredMessage("Test message " + i);
		// Set some additional custom app-specific property
		message.setProperty("MessageNumber", i);
		// Send message to the topic
		service.sendTopicMessage("TestTopic", message);
	}

서비스 버스 토픽은 256MB의 최대 메시지 크기를 지원합니다(표준
및 사용자 지정 응용 프로그램 속성이 포함된 헤더의
최대 크기는 64MB임). 한 토픽에 저장되는 메시지 수에는
제한이 없지만 한 토픽에 저장되는 총 메시지 크기는
제한됩니다. 이 토픽 크기는 생성 시 정의되며
상한이 5GB입니다.

## <a name="bkmk_HowToReceiveMsgs"> </a>구독에서 메시지를 받는 방법

구독에서 메시지를 받는 기본 방법은
**ServiceBusContract** 개체를 사용하는 것입니다. 받은
메시지는 **ReceiveAndDelete** 및 **PeekLock**의 두 가지 모드로 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용할 경우 수신은 1단계
작업입니다. 즉, 서비스 버스가 메시지에 대한 읽기 요청을 받으면
메시지를 이용되는 것으로 표시하고 응용 프로그램에
반환합니다. **ReceiveAndDelete** 모드는 가장 단순한 모델이며
 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한
시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가
 수신 요청을 실행한 후 처리하기 전에
크래시되는 시나리오를 고려해 보세요. 서비스 버스가 메시지를 이용되는 것으로
표시했기 때문에 응용 프로그램이 다시 시작되고
메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는
누락됩니다.

**PeekLock** 모드에서는 수신이 2단계 작업이므로
메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수
있습니다. 서비스 버스가 요청을 받으면
이용할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후
응용 프로그램에 반환합니다. 응용 프로그램은
메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후
수신된 메시지에 대해 **Delete**를 호출하여
수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스는 **Delete** 호출을 확인한 후
메시지를 이용되는 것으로 표시하고 토픽에서 제거합니다.

아래 예제에서는
**PeekLock** 모드(기본 모드가 아님)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 아래 예제에서는
루프를 수행하고 ""HighMessages"" 구독 메시지를 처리한 후 추가 메시지가 없으면 종료됩니다(또는 새 메시지를 대기하도록 설정할 수 있음).

	try
	{
		ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
		opts.setReceiveMode(ReceiveMode.PEEK_LOCK);
	
		while(true)  { 
		    ReceiveSubscriptionMessageResult  resultSubMsg = 
		        service.receiveSubscriptionMessage("TestTopic", "HighMessages", opts);
		    BrokeredMessage message = resultSubMsg.getValue();
		    if (message != null && message.getMessageId() != null)
		    {
			    System.out.println("MessageID: " + message.getMessageId());    
			    // Display the topic message.
			    System.out.print("From topic: ");
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
			        message.getProperty("MessageNumber"));
			    // Delete message.
			    System.out.println("Deleting this message.");
			    service.deleteMessage(message);
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

## <a name="bkmk_HowToHandleAppCrash"> </a>응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로
복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든
수신 응용 프로그램이 메시지를 처리할 수 없는 경우
받은 메시지에 대해 **unlockMessage** 메서드를
**deleteMessage** 메서드 대신 호출할 수 있습니다. 그러면 서비스 버스에서
토픽 내 메시지의 잠금을 해제하여
동일한 이용 응용 프로그램이나 다른 이용 응용 프로그램이
다시 받을 수 있게 합니다.

토픽 내에서 잠긴 메시지와 연결된 제한 시간도 있으며,
응용 프로그램에서 잠금 시간 제한이 만료되기 전에
메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우)
서비스 버스가 메시지를 자동으로 잠금 해제하여
다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후
**deleteMessage** 요청이 실행되기 전에 크래시되는 경우
다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를
**최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가
최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가
다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는
응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를
응용 프로그램에 추가해야 합니다. 이 경우 대체로
대체로 배달 시도 간에 일정하게 유지되는 메시지의 **getMessageId** 메서드를
사용합니다.

## <a name="bkmk_HowToDeleteTopics"> </a>토픽과 구독을 삭제하는 방법

토픽 및 구독을 삭제하는 기본 방법은 
**ServiceBusContract** 개체를 사용하는 것입니다. 토픽을 삭제하면 토픽에 등록된 모든 구독도
삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다.

    // Delete subscriptions
    service.deleteSubscription("TestTopic", "AllMessages");
    service.deleteSubscription("TestTopic", "HighMessages");
    service.deleteSubscription("TestTopic", "LowMessages");

    // Delete a topic
	service.deleteTopic("TestTopic");

# <a name="bkmk_NextSteps"> </a>다음 단계

이제 서비스 버스 큐의 기본 사항을 익혔으므로 MSDN
항목 [서비스 버스 큐, 토픽 및 구독][]에 자세한 내용을 참조하세요.

  [Java용 Azure SDK]: http://www.windowsazure.com/ko-kr/develop/java/
  [서비스 버스 토픽 및 구독 정의]: #what-are-service-bus-topics
  [서비스 네임스페이스 만들기]: #create-a-service-namespace
  [네임스페이스에 대한 기본 관리 자격 증명 얻기]: #obtain-default-credentials
  [서비스 버스를 사용하도록 응용 프로그램 구성]: #bkmk_ConfigYourApp
  [방법: 토픽 만들기]: #bkmk_HowToCreateTopic
  [방법: 구독 만들기]: #bkmk_HowToCreateSubscrip
  [방법: 토픽에 메시지 보내기]: #bkmk_HowToSendMsgs
  [방법: 구독에서 메시지 받기]: #bkmk_HowToReceiveMsgs
  [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리]: #bkmk_HowToHandleAppCrash
  [방법: 토픽 및 구독 삭제]: #bkmk_HowToDeleteTopics
  [다음 단계]: #bkmk_NextSteps
  [서비스 버스 토픽 다이어그램]: ../../../DevCenter/Java/Media/SvcBusTopics_01_FlowDiagram.jpg
  [Azure 관리 포털]: http://manage.windowsazure.com/
  [서비스 버스 노드 스크린샷]: ../../../DevCenter/dotNet/Media/sb-queues-03.png
  [새 네임스페이스 만들기 ]: ../../../DevCenter/dotNet/Media/sb-queues-04.png
  [네임스페이스 목록 스크린샷]: ../../../DevCenter/dotNet/Media/sb-queues-05.png
  [속성 창 스크린샷]: ../../../DevCenter/dotNet/Media/sb-queues-06.png
  [기본 키 스크린샷]: ../../../DevCenter/dotNet/Media/sb-queues-07.png
  [서비스 버스 큐, 토픽 및 구독]: http://msdn.microsoft.com/library/windowsazure/hh367516.aspx
