<properties urlDisplayName="Service Bus Queues" pageTitle="서비스 버스 큐를 사용하는 방법(PHP) - Azure" metaKeywords="Azure 서비스 버스 큐, Azure 큐, Azure 메시징, Azure 큐 PHP" description="Learn how to use Service Bus queues in Azure. Code samples written in PHP." metaCanonical="" services="service-bus" documentationCenter="PHP" title="How to Use Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/13/2014" ms.author="sethm" />

# 서비스 버스 큐를 사용하는 방법

이 가이드에서는 PHP로 서비스 버스 큐를 사용하는 방법을 보여 줍니다. 샘플은 PHP로 작성되었으며 [PHP용 Azure SDK][download-sdk]를 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기**, **메시지 보내기 및 받기**, **큐 삭제** 등이 포함됩니다.

## 목차

-   [서비스 버스 큐 정의](#what-are-service-bus-queues)
-   [서비스 네임스페이스 만들기](#create-a-service-namespace)
-   [네임스페이스에 대한 기본 관리 자격 증명 얻기](#obtain-default-credentials)
- 	[PHP 응용 프로그램 만들기](#CreateApplication)
-	[Azure 클라이언트 라이브러리 가져오기](#GetClientLibrary)
-   [서비스 버스를 사용하도록 응용 프로그램 구성](#ConfigureApp)
-   [방법: 큐 만들기](#CreateQueue)
-   [방법: 큐에 메시지 보내기](#SendMessages)
-   [방법: 큐에서 메시지 받기](#ReceiveMessages)
-   [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리](#HandleCrashes)
-   [다음 단계](#NextSteps)

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

##<a id="CreateApplication"></a>PHP 응용 프로그램 만들기

Azure Blob 서비스에 액세스하는 PHP 응용 프로그램을 만드는 데 유일한 요구 사항은 코드 내에서 [PHP용 Azure SDK][download-sdk]의 클래스를 참조하는 것입니다. 응용 프로그램을 만드는 데는 메모장을 포함한 개발 도구를 무엇이든 사용할 수 있습니다.

> [WACOM.NOTE]
> PHP를 설치하려면 <a href="http://php.net/openssl">OpenSSL 확장</a>(영문)도 설치되어 있고 사용하도록 설정되어 있어야 합니다.

이 가이드에서는 PHP 응용 프로그램 내에서 로컬로 또는 Azure 웹 역할, 작업자 역할 또는 웹 사이트 내에서 실행되는 코드에서 호출할 수 있는 서비스 기능을 사용합니다.

##<a id="GetClientLibrary"></a>Azure 클라이언트 라이브러리 가져오기

[WACOM.INCLUDE [get-client-libraries](../includes/get-client-libraries.md)]

##<a id="ConfigureApp"></a>서비스 버스를 사용하도록 응용 프로그램 구성

Azure 서비스 버스 API를 사용하려면 다음을 수행해야 합니다.

1. [require_once][require_once] 문을 사용하여 자동 로더 파일 참조
2. 사용할 수 있는 모든 클래스 참조

아래 예제에서는 자동 로더 파일을 포함하고 **ServicesBuilder** 클래스를 참조하는 방법을 보여 줍니다.

> [WACOM.NOTE] 
> 이 예제(및 이 문서의 다른 예제)에서는 Azure용 PHP 클라이언트 라이브러리를 작성기를 통해 설치했다고 가정합니다. 라이브러리를 수동으로 또는 PEAR 패키지로 설치한 경우 <code>WindowsAzure.php</code> 자동 로더 파일을 참조해야 합니다.

	require_once 'vendor\autoload.php';
	use WindowsAzure\Common\ServicesBuilder;


아래 예제에서 `require_once` 문은 항상 표시되지만 예제를 실행하는 데 필요한 클래스만 참조됩니다.

##<a id="ConnectionString"></a>Azure 서비스 버스 연결 설정

Azure 서비스 버스 클라이언트를 인스턴스화하려면 먼저 다음 형식에 따른 유효한 연결 문자열이 있어야 합니다.

	Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]

여기서 끝점은 일반적으로 `https://[yourNamespace].servicebus.windows.net` 형식입니다.

Azure 서비스 클라이언트를 만들려면 **ServicesBuilder** 클래스를 사용해야 합니다. 다음을 수행할 수 있습니다.

* 연결 문자열을 직접 전달할 수 있습니다.
* **CloudConfigurationManager(CCM)**를 사용하여 여러 외부 소스에서 연결 문자열을 확인할 수 있습니다.
	* 기본적으로 하나의 외부 소스, 환경 변수에 대한 지원이 제공됩니다.
	* **ConnectionStringSource** 클래스를 확장하여 새 소스를 추가할 수 있습니다.

여기에 설명된 예제의 경우 연결 문자열이 직접 전달됩니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;

	$connectionString = "Endpoint=[yourEndpoint];SharedSecretIssuer=[Default Issuer];SharedSecretValue=[Default Key]";

	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);


##<a id="CreateQueue"></a>방법: 큐 만들기

**ServiceBusRestProxy** 클래스를 통해 서비스 버스 큐에 대한 관리 작업을 수행할 수 있습니다. A **ServiceBusRestProxy** 개체는 관리에 필요한 토큰 사용 권한을 캡슐화하는 적절한 연결 문자열을 사용한 **ServicesBuilder::createServiceBusService** 팩터리 메서드를 통해 구성됩니다.

아래 예제에서는 **ServiceBusRestProxy**를 인스턴스화하고 **ServiceBusRestProxy->createQueue**를 호출하여 'MySBNamespace' 서비스 네임스페이스 내에서 'myqueue'라는 큐를 만드는 방법을 보여 줍니다.

    require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\Models\QueueInfo;

	// Create Service Bus REST proxy.
		$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
	
	try	{
		$queueInfo = new QueueInfo("myqueue");
		
		// Create queue.
		$serviceBusRestProxy->createQueue($queueInfo);
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
> <b>ServiceBusRestProxy</b> 개체의 <b>listQueues</b> 메서드를 사용하여 서비스 네임스페이스 내에 지정된 이름의 큐가 이미 있는지 확인할 수 있습니다.

##<a id="SendMessages"></a>방법: 큐에 메시지 보내기

서비스 버스 큐에 메시지를 보내기 위해 응용 프로그램은 **ServiceBusRestProxy->sendQueueMessage** 메서드를 호출합니다. 아래 코드는 위에서 'MySBNamespace' 서비스 네임스페이스 내에서 만든 'myqueue' 큐에 메시지를 보내는
방법을 보여 줍니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\BrokeredMessage;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Create message.
		$message = new BrokeredMessage();
		$message->setBody("my message");
	
		// Send message.
		$serviceBusRestProxy->sendQueueMessage("myqueue", $message);
	}
	catch(ServiceException $e){
		// Handle exception based on error codes and messages.
		// Error codes and messages are here: 
		// http://msdn.microsoft.com/ko-kr/library/windowsazure/hh780775
		$code = $e->getCode();
		$error_message = $e->getMessage();
		echo $code.": ".$error_message."<br />";
	}

서비스 버스 큐로 보내고 받은 메시지는 **BrokeredMessage** 클래스의 인스턴스입니다. **BrokeredMessage** 개체에는 표준 메서드 집합(예: **getLabel**, **getTimeToLive**, **setLabel** 및 **setTimeToLive**), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 속성 및 임의 응용 프로그램 데이터 본문이 있습니다.

서비스 버스 큐는 256KB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB임). 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 큐 크기의 상한은 5GB입니다.

##<a id="ReceiveMessages"></a>방법: 큐에서 메시지 받기

큐에서 메시지를 받는 기본 방법은 **ServiceBusRestProxy->receiveQueueMessage** 메서드를 사용하는 것입니다. 메시지는 **ReceiveAndDelete**(기본값) 및 **PeekLock**의 두 가지 모드로 작동할 수 있습니다.

**ReceiveAndDelete** 모드를 사용하는 경우 수신은 1단계 작업입니다. 즉, 서비스 버스가 큐 메시지에 대한 읽기 요청을 받으면 메시지를 이용되는 것으로 표시하고 응용 프로그램에 반환합니다. **ReceiveAndDelete** 가장 단순한 모델이며, 응용 프로그램이 실패 이벤트 시 메시지를 처리하지 않아도 안전한
시나리오에서 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

**PeekLock** 모드에서는 메시지 수신이 2단계 작업이므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 수신된 메시지를 **ServiceBusRestProxy->deleteMessage**에 전달하여 수신 프로세스의 두 번째 단계를 완료합니다. 서비스 버스는 **deleteMessage** 호출을 확인한 후 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

아래 예제에서는 **PeekLock** 모드(기본 모드 아님)를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다.

	require_once 'vendor\autoload.php';

	use WindowsAzure\Common\ServicesBuilder;
	use WindowsAzure\Common\ServiceException;
	use WindowsAzure\ServiceBus\models\ReceiveMessageOptions;

	// Create Service Bus REST proxy.
	$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
		
	try	{
		// Set the receive mode to PeekLock (default is ReceiveAndDelete).
		$options = new ReceiveMessageOptions();
		$options->setPeekLock();
		
		// Receive message.
		$message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
		echo "Body: ".$message->getBody()."<br />";
		echo "MessageID: ".$message->getMessageId()."<br />";
		
		/*---------------------------
			Process message here.
		----------------------------*/
		
		// Delete message. Not necessary if peek lock is not set.
		echo "Message deleted.<br />";
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

##<a id="HandleCrashes"></a>방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 받은 메시지에 대해 **deleteMessage** 메서드 대신 **unlockMessage** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **deleteMessage** 요청이 실행되기 전에 크래시되는 경우 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가하는 것이 좋습니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **getMessageId** 메서드를 사용합니다.

##<a id="NextSteps"></a>다음 단계

지금까지 서비스 버스 큐의 기본 사항에 대해 알아보았습니다. 자세한 내용은 MSDN 항목 [큐, 토픽 및 구독][]을 참조하세요.

[download-sdk]: http://go.microsoft.com/fwlink/?LinkId=252473
[서비스 버스 큐 다이어그램]: ../../../DevCenter/Java/Media/SvcBusQueues_01_FlowDiagram.jpg
[Azure 관리 포털]: http://manage.windowsazure.com/
[서비스 버스 노드 스크린샷]: ../../../DevCenter/Java/Media/SvcBusQueues_02_SvcBusNode.jpg
[새 네임스페이스 만들기 스크린샷]: ../../../DevCenter/Java/Media/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[사용 가능한 네임스페이스 스크린샷]: ../../../DevCenter/Java/Media/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[네임스페이스 목록 스크린샷]: ../../../DevCenter/Java/Media/SvcBusQueues_05_NamespaceList.jpg
[속성 창 스크린샷]: ../../../DevCenter/Java/Media/SvcBusQueues_06_PropertiesPane.jpg
[기본 키 스크린샷]: ../../../DevCenter/Java/Media/SvcBusQueues_07_DefaultKey.jpg
[큐, 토픽 및 구독]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh367516.aspx
[require_once]: http://php.net/require_once

