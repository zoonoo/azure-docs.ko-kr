<properties linkid="develop-php-how-to-guides-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (PHP) - Azure" metaKeywords="" description="Learn how to use Service Bus topics with PHP in Azure." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Topics/Subscriptions" authors="" solutions="" manager="" editor="" />

서비스 버스 토픽/구독을 사용하는 방법
=====================================

이 가이드에서는 서비스 버스 토픽과 구독을 사용하는 방법을 보여 줍니다. 샘플은 PHP로 작성되었으며 [PHP용 Azure SDK](http://go.microsoft.com/fwlink/?LinkId=252473)를 사용합니다. 여기서 다루는 시나리오에는 **토픽 및 구독 만들기**, **구독 필터 만들기**, **토픽에 메시지 보내기**, **구독에서 메시지 받기**, **토픽 및 구독 삭제** 등이 포함됩니다.

목차
----

-   [서비스 버스 토픽 및 구독 정의](#what-are-service-bus-topics)
-   [서비스 네임스페이스 만들기](#create-a-service-namespace)
-   [네임스페이스에 대한 기본 관리 자격 증명 얻기](#obtain-default-credentials)
-   [PHP 응용 프로그램 만들기](#CreateApplication)
-   [Azure 클라이언트 라이브러리 가져오기](#GetClientLibrary)
-   [서비스 버스를 사용하도록 응용 프로그램 구성](#ConfigureApp)
-   [방법: 토픽 만들기](#CreateTopic)
-   [방법: 구독 만들기](#CreateSubscription)
-   [방법: 토픽에 메시지 보내기](#SendMessage)
-   [방법: 구독에서 메시지 받기](#ReceiveMessages)
-   [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리](#HandleCrashes)
-   [방법: 토픽 및 구독 삭제](#DeleteTopicsAndSubscriptions)
-   [다음 단계](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

PHP 응용 프로그램 만들기
------------------------

Azure Blob 서비스에 액세스하는 PHP 응용 프로그램을 만드는 데 유일한 요구 사항은 코드 내에서 [PHP용 Azure SDK](http://go.microsoft.com/fwlink/?LinkId=252473)의 클래스를 참조하는 것입니다. 응용 프로그램을 만드는 데는 메모장을 포함한 어떠한 개발 도구도 사용할 수 있습니다.

> [WACOM.NOTE] PHP를 설치하려면 [OpenSSL 확장](http://php.net/openssl)(영문)도 설치되어 있고 사용하도록 설정되어 있어야 합니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 서비스 기능을 사용합니다.

Azure 클라이언트 라이브러리 가져오기
------------------------------------

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

서비스 버스를 사용하도록 응용 프로그램 구성
-------------------------------------------

Azure 서비스 버스 토픽 API를 사용하려면 다음을 수행해야 합니다.

1.  [require\_once](http://php.net/require_once) 문을 사용하여 자동 로더 파일 참조
2.  사용할 수 있는 모든 클래스 참조

다음 예제에서는 자동 로더 파일을 포함하고 **ServiceBusService** 클래스를 참조하는 방법을 보여 줍니다.

    > [WACOM.NOTE]
    > 이 예제(및 이 문서의 다른 예제)에서는 Azure용 PHP 클라이언트 라이브러리를 작성기를 통해 설치했다고 가정합니다. 라이브러리를 수동으로 설치했거나 PEAR 패키지로 설치한 경우 <code>WindowsAzure.php</code> 자동 로더 파일을 참조해야 합니다.

    require_once 'vendor\autoload.php';
    use WindowsAzure\Common\ServicesBuilder;

아래 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

Azure 서비스 버스 연결 설정
---------------------------

Azure 서비스 버스 클라이언트를 인스턴스화하려면 먼저 다음 형식에 따른 유효한 연결 문자열이 있어야 합니다.

    Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

여기서 끝점은 일반적으로 `https://[yourNamespace].servicebus.windows.net` 형식입니다.

Azure 서비스 클라이언트를 만들려면 **ServicesBuilder** 클래스를 사용해야 합니다. 다음을 수행할 수 있습니다.

-   연결 문자열을 직접 전달할 수 있습니다.
-   **CloudConfigurationManager(CCM)**를 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
    -   기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
    -   **ConnectionStringSource** 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;

    $connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

방법: 토픽 만들기
-----------------

**ServiceBusRestProxy** 클래스를 통해 서비스 버스 토픽에 대한 관리 작업을 수행할 수 있습니다. **ServiceBusRestProxy** 개체는 관리에 필요한 토큰 사용 권한을 캡슐화하는 적절한 연결 문자열을 사용한 **ServicesBuilder::createServiceBusService** 팩터리 메서드를 통해 구성됩니다.

아래 예제에서는 **ServiceBusRestProxy**를 인스턴스화하고 **ServiceBusRestProxy-\>createTopic**을 호출하여 `MySBNamespace` 서비스 네임스페이스 내에 `mytopic`이라는 토픽을 만드는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\TopicInfo;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Create topic.
        $topicInfo = new TopicInfo("mytopic");
        $serviceBusRestProxy->createTopic($topicInfo);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

    > [WACOM.NOTE]
    > <b>ServiceBusRestProxy</b> 개체에 대해 <b>listTopics</b> 메서드를 사용하여 지정된 이름의 토픽이 서비스 네임스페이스 내에 이미 있는지 여부를 확인할 수 있습니다.

방법: 구독 만들기
-----------------

토픽 구독은 **ServiceBusRestProxy-\>createSubscription** 메서드로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우 사용되는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 'mysubscription'이라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {
        // Create subscription.
        $subscriptionInfo = new SubscriptionInfo("mysubscription");
        $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

### 필터를 사용하여 구독 만들기

토픽에 전송된 메시지 중 특정 토픽 구독 내에 나타나야 하는 메시지의 범위를 지정하는 필터를 설정할 수도 있습니다. 구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SqlFilter에 대한 자세한 내용은 [SqlFilter.SqlExpression 속성](http://msdn.microsoft.com/ko-kr/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx)(영문)을 참조하십시오.

    > [WACOM.NOTE]
    > 구독에 대한 각 규칙은 들어오는 메시지를 독립적으로 처리하여 해당 결과 메시지를 구독에 추가합니다. 또한 각 새 구독에는 토픽의 모든 메시지를 구독에 추가하는 필터가 있는 기본 <b>규칙</b>이 있습니다. 필터와 일치하는 메시지만 받으려면 기본 규칙을 제거해야 합니다. <b>ServiceBusRestProxy->deleteRule</b> 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.

아래 예제에서는 사용자 지정 **MessageNumber** 속성이 3보다 큰 메시지만 선택하는 **SqlFilter**를 사용하여 "HighMessages"라는 구독을 만듭니다(사용자 지정 속성을 메시지에 추가하는 방법에 대한 자세한 내용은 [방법: 토픽에 메시지 보내기](#SendMessage) 참조).

	$subscriptionInfo = new SubscriptionInfo("HighMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

  	$ruleInfo = new RuleInfo("HighMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber > 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);

위 코드에서는 `WindowsAzure\ServiceBus\Models\SubscriptionInfo`라는 추가 네임스페이스를 사용해야 합니다.

마찬가지로, 다음 예제에서는 MessageNumber 속성이 3보다 작거나 같은 메시지만 선택하는 SqlFilter를 사용하여 "LowMessages"라는 구독을 만듭니다.

	$subscriptionInfo = new SubscriptionInfo("LowMessages");
   	$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

	$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

  	$ruleInfo = new RuleInfo("LowMessagesRule");
   	$ruleInfo->withSqlFilter("MessageNumber <= 3");
   	$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);

이제 `mytopic` 토픽으로 메시지가 전송되는 경우 `mysubscription` 구독을 구독하는 수신자에게는 항상 배달되고, "HighMessages" 및 "LowMessages" 구독을 구독하는 수신자에게는 메시지 내용에 따라 선택적으로 배달됩니다.

방법: 토픽에 메시지 보내기
--------------------------

서비스 버스 토픽에 메시지를 보내기 위해 응용 프로그램은 **ServiceBusRestProxy-\>sendTopicMessage** 메서드를 호출합니다. 아래 코드는 `MySBNamespace` 서비스 네임스페이스 내에서 위에서 만든 `mytopic` 토픽에 메시지를 보내는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\BrokeredMessage;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Create message.
        $message = new BrokeredMessage();
        $message->setBody("my message");

        // Send message.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/hh780775
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

서비스 버스 토픽으로 전송된 메시지는 **BrokeredMessage** 클래스 인스턴스입니다. **BrokeredMessage** 개체에는 표준 속성 및 메서드 집합(예: **getLabel**, **getTimeToLive**, **setLabel** 및 **setTimeToLive**)과 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용될 수 있는 속성이 있습니다. 다음 예제에서는 앞에서 만든 `mytopic` 토픽에 5개의 테스트 메시지를 보내는 방법을 보여 줍니다. **setProperty** 메서드는 각 메시지에 사용자 지정 속성(`MessageNumber`)을 추가하는 데 사용됩니다. 각 메시지에서 `MessageNumber` 속성 값이 어떻게 다른지 살펴봅니다. 이를 사용하여 위의 [방법: 구독 만들기](#CreateSubscription) 섹션에서와 같이 메시지를 받는 구독을 결정할 수 있습니다.

    for($i = 0; $i < 5; $i++){
        // Create message.
        $message = new BrokeredMessage();
        $message->setBody("my message ".$i);
            
        // Set custom property.
        $message->setProperty("MessageNumber", $i);
            
        // Send message.
        $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
    }

서비스 버스 큐는 256KB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB임). 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 큐 크기의 상한은 5GB입니다.

방법: 구독에서 메시지 받기
--------------------------

구독에서 메시지를 받는 기본 방법은 **ServiceBusRestProxy-\>receiveSubscriptionMessage** 메서드를 사용하는 것입니다. 받은 메시지는 **ReceiveAndDelete**(기본값) 및 **PeekLock**의 두 가지 모드로 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, 서비스 버스가 구독 메시지에 대한 읽기 요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete** 모드는 가장 단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한 시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보십시오. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

**PeekLock** 모드에서는 메시지 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지를 **ServiceBusRestProxy-\>deleteMessage**에 전달하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스는 **deleteMessage** 호출을 확인한 후 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

아래 예제에서는 **PeekLock** 모드(기본 모드 아님)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\Common\ServicesBuilder;
    use WindowsAzure\Common\ServiceException;
    use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
        
    try {
        // Set receive mode to PeekLock (default is ReceiveAndDelete)
        $options = new ReceiveMessageOptions();
        $options->setPeekLock();

        // Get message.
        $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", 
                                                                    "mysubscription", 
                                                                    $options);
        echo "Body: ".$message->getBody()."<br />";
        echo "MessageID: ".$message->getMessageId()."<br />";
        
        /*---------------------------
            Process message here.
        ----------------------------*/
        
        // Delete message. Not necessary if peek lock is not set.
        echo "Deleting message...<br />";
        $serviceBusRestProxy->deleteMessage($message);
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here:
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/hh780735
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리
------------------------------------------------------

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 **deleteMessage** 메서드 대신 **unlockMessage** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **deleteMessage** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **getMessageId** 메서드를 사용합니다.

토픽과 구독을 삭제하는 방법
---------------------------

토픽 또는 구독을 삭제하려면 각각 **ServiceBusRestProxy-\>deleteTopic** 또는 **ServiceBusRestProxy-\>deleteSubscripton** 메서드를 사용합니다. 토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다.

다음 예제에서는 토픽(`mytopic`) 및 등록된 해당 구독을 삭제하는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

    use WindowsAzure\ServiceBus\ServiceBusService;
    use WindowsAzure\ServiceBus\ServiceBusSettings;
    use WindowsAzure\Common\ServiceException;

    // Create Service Bus REST proxy.
    $serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

    try {       
        // Delete topic.
        $serviceBusRestProxy->deleteTopic("mytopic");
    }
    catch(ServiceException $e){
        // Handle exception based on error codes and messages.
        // Error codes and messages are here: 
        // http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179357
        $code = $e->getCode();
        $error_message = $e->getMessage();
        echo $code.": ".$error_message."<br />";
    }

**deleteSubscription** 메서드를 사용하여 구독을 독립적으로 삭제할 수 있습니다.

    $serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");

다음 단계
---------

지금까지 서비스 버스 큐의 기본 사항에 대해 알아보았습니다. 자세한 내용은 MSDN 항목 [큐, 토픽 및 구독](http://msdn.microsoft.com/ko-kr/library/windowsazure/hh367516.aspx)을 참조하십시오.

