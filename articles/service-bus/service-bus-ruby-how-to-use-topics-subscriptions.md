<properties 
	pageTitle="서비스 버스 토픽을 사용하는 방법(Ruby) - Azure" 
	description="Azure에서 서비스 버스 토픽 및 구독을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Ruby 응용 프로그램용으로 작성되었습니다." 
	services="service-bus" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="tomfitz"/>





# 서비스 버스 토픽/구독을 사용하는 방법

이 가이드에서는 Ruby 응용 프로그램에서 서비스 버스 토픽과 구독을 사용하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 **토픽 및 구독 만들기, 구독 필터 만들기, 토픽에 메시지 보내기**, **구독에서 메시지 받기**, **토픽 및 구독 삭제** 등이 포함됩니다. 토픽 및 구독에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하세요.

## 서비스 버스 토픽 및 구독 정의

서비스 버스 토픽 및 구독은 **게시/구독 메시징 통신** 모델을 지원합니다. 토픽 및 구독을 사용하는 경우 분산 응용 프로그램의 구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 토픽을 통해 메시지를 교환합니다.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

각 메시지가 단일 소비자에 의해 처리되는 서비스 버스 큐와 반대로, 토픽과 구독은 게시/구독 패턴을 사용하여 **일 대 다** 형태의 통신을 제공합니다. 하나의 토픽에 여러 구독을 등록할 수 있습니다. 토픽에 메시지를 전송하면 각 구독에서 독립적으로 처리할 수 있습니다.

토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 선택적으로 구독별 토픽에 대한 필터 규칙을 등록할 수 있으며, 이렇게 하면 각 토픽 구독에서 받는 토픽 메시지를 필터링/제한할 수 있습니다.

서비스 버스 토픽 및 구독을 사용하면 다수의 사용자와 응용 프로그램에 대해 다수의 메시지를 처리하도록 확장할 수 있습니다.

## 서비스 네임스페이스 만들기

Azure에서 서비스 버스 큐 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 서비스 네임스페이스는 응용 프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 지정 컨테이너를 
제공합니다. 포털에서는 ACS 연결을 사용하여 서비스 버스를 만들지 않으므로 명령줄 인터페이스를 통해 네임스페이스를 만들어야 합니다.

네임스페이스를 만들려면

1. Azure PowerShell 콘솔을 엽니다.

2. 아래와 같이 Azure 서비스 버스 네임스페이스를 만드는 명령을 입력합니다. 원하는 네임스페이스 값을 입력하고 응용 프로그램과 같은 지역을 지정합니다. 

      New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -CreateACSNamespace $true

      ![Create Namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## 네임스페이스에 대한 기본 관리 자격 증명 얻기

새 네임스페이스에 대해 큐 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다.

1. [Azure 관리 포털](http://manage.windowsazure.com/)에 로그온합니다.

2. 만든 서비스 버스 네임스페이스를 선택합니다.

     ![Select namespace](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectns.png)

3. 아래쪽에서 **연결 정보**를 선택합니다.

      ![Select connection](./media/service-bus-ruby-how-to-use-topics-subscriptions/selectconnection.png)

4. 기본 키를 복사합니다. 코드에서 이 값을 사용합니다.

       ![Copy key](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

## Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만듭니다. 자세한 내용은 [Azure에서 Ruby 응용 프로그램 만들기](/develop/ruby/tutorials/web-app-with-linux-vm/)를 참조하세요.

## 서비스 버스를 사용하도록 응용 프로그램 구성

Azure 서비스 버스를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### RubyGems를 사용하여 패키지 가져오기

1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2. 명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

주로 사용하는 텍스트 편집기에서 저장소를 사용할 Ruby 파일의 맨 위에 다음을 추가합니다.

    require "azure"

## Azure 서비스 버스 연결 설정

Azure 모듈은 **AZURE_SERVICEBUS_NAMESPACE** 및 **AZURE_SERVICEBUS_ACCESS_KEY** 
환경 변수를 읽고 Azure 서비스 버스 네임스페이스에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수를 설정하지 않은 경우 **Azure::ServiceBusService**를 사용하기 전에 다음 코드로 네임스페이스 정보를 지정해야 합니다.

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

서비스 버스 네임스페이스 값을 전체 URL이 아니라 만든 값으로 설정합니다. 예를 들어 "yourexamplenamespace.servicebus.windows.net"이 아니라 **"yourexamplenamespace"**를 사용합니다. 

## 토픽을 만드는 방법

**Azure::ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 다음 코드는 **Azure::ServiceBusService** 개체를 만듭니다. 토픽을 만들려면 **create_topic()** 메서드를 사용합니다. 다음 예제에서는 토픽을 만들거나 오류가 있는 경우 오류를 출력합니다.

	azure_service_bus_service = Azure::ServiceBusService.new
	begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

추가 옵션을 포함하여 **Azure::ServiceBus::Topic** 개체를 전달할 수도 있습니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 토픽 크기 등 기본 토픽 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB, TTL(Time to Live)을 1분으로 설정합니다.

	topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## 구독을 만드는 방법

토픽 구독도 **Azure::ServiceBusService** 개체를 사용하여 만듭니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

구독은 영구적이며, 구독 자체 또는 구독과 연결된 토픽이 삭제될 때까지 계속 유지됩니다. 응용 프로그램에 구독을 만들기 위한 논리가 포함된 경우, getSubscription 메서드를 사용하여 구독이 이미 존재하는지를 먼저 확인해야 합니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우 사용되는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 "all-messages"라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "all-messages")

### <a id="how-to-create-subscriptions"></a>필터를 사용하여 구독 만들기

토픽에 전송된 메시지 중 특정 토픽 구독 내에 나타나야 하는 메시지의 범위를 지정하는 필터를 설정할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **Azure::ServiceBus::SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 구문을 참조하세요.

**Azure::ServiceBusService** 개체의 **create_rule()** 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

기본 필터는 모든 새로운 구독에 자동으로 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll**이 지정되어 있을 수 있는 기타 필터를 재정의합니다. **Azure::ServiceBusService** 개체의 **delete_rule()** 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.

아래 예제에서는 사용자 지정 **message_number** 속성이 3보다 큰 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "high-messages"라는 구독을 만듭니다.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "high-messages")
	azure_service_bus_service.delete_rule("test-topic", "high-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("high-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "high-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number > 3" })
	rule = azure_service_bus_service.create_rule(rule)

마찬가지로, 다음 예제에서는 **message_number** 속성이 3 이하인 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "low-messages"라는 구독을 만듭니다.

	subscription = azure_service_bus_service.create_subscription("test-topic", 
	  "low-messages")
	azure_service_bus_service.delete_rule("test-topic", "low-messages", 
	  "$Default")
	
	rule = Azure::ServiceBus::Rule.new("low-messages-rule")
	rule.topic = "test-topic"
	rule.subscription = "low-messages"
	rule.filter = Azure::ServiceBus::SqlFilter.new({ 
	  :sql_expression => "message_number <= 3" })
	rule = azure_service_bus_service.create_rule(rule)

이제 "test-topic"으로 메시지가 전송되는 경우 "all-messages" 토픽 구독을 구독하는 수신자에게는 항상 배달되고, "high-messages" 및 "low-messages" 토픽 구독을 구독하는 수신자에게는 메시지 내용에 따라 선택적으로 배달됩니다.

## 토픽에 메시지를 보내는 방법

서비스 버스 토픽에 메시지를 보내려면 응용 프로그램에서 **Azure::ServiceBusService** 개체의 **send_topic_message()** 메서드를 사용해야 합니다. 서비스 버스 토픽으로 전송되는 메시지는 **Azure::ServiceBus::BrokeredMessage** 개체입니다. **Azure::ServiceBus::BrokeredMessage** 개체에는 **label**, **time_to_live** 등의 표준 속성 집합과 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전, 그리고 문자열 데이터의 본문이 포함되어 있습니다. 응용 프로그램은 문자열 값을 **send_topic_message()** 메서드에 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성에 기본값이 입력됩니다.

다음 예제에서는 "test-topic"에 5개의 테스트 메시지를 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 **message_number** 속성 값이 변경되며 이 값에 따라 해당 메시지를 받는 구독이 결정됩니다.

	5.times do |i|
	  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
	    { :message_number => i })
	  azure_service_bus_service.send_topic_message("test-topic", message)
	end

서비스 버스 토픽은 256MB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64MB임). 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## 구독에서 메시지를 받는 방법

**Azure::ServiceBusService** 개체의 **receive_subscription_message()** 메서드를 사용하여 구독에서 메시지를 받습니다. 기본적으로 메시지는 구독에서 삭제하지 않고 읽고(피크) 잠급니다. **peek_lock** 옵션을 **false**로 설정하면 구독에서 메시지를 읽고 삭제할 수 있습니다.

기본 동작은 읽기 및 삭제가 2단계 작업으로 수행되도록 하므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 **delete_subscription_message()** 메서드를 호출하고 삭제할 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. **delete_subscription_message()** 메서드는 메시지를 사용 중인 것으로 표시하고 구독에서 제거합니다.

**:peek_lock** 매개 변수를 **false**로 설정하면 메시지 읽기 및 삭제가 가장 간단한 모델로 설정됩니다. 이러한 모델은 오류 발생 시 응용 프로그램이 메시지를 처리하지 않아도 되는 시나리오에 가장 적합합니다 이 모드를 파악하려면 소비자가 수신 요청을 실행했는데 요청이 처리되기 전에 응용 프로그램 작동이 중지되는 시나리오를 고려할 수 있습니다. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

아래 예제에서는 **receive_subscription_message()**를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 이 예제에서는 먼저 **false**로 설정된 **:peek_lock**을 사용하여 "low-messages" 구독에서 메시지를 받고 삭제한 후 "high-messages"에서 다른 메시지를 받습니다. 그런 후에 **delete_subscription_message()**를 사용하여 해당 메시지를 삭제합니다.

    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
	  "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## 응용 프로그램 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신기 응용 프로그램은 메시지를 처리할 수 없는 경우 **Azure::ServiceBusService** 개체에 대해 **unlock_subscription_message()** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 구독 내 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **delete_subscription_message()** 메서드를 호출하기 전에 응용 프로그램 작동이 중단되면 응용 프로그램이 다시 시작될 때 메시지가 응용 프로그램으로 다시 배달됩니다. 이러한 과정을 **한 번 이상 처리**라고 합니다. 즉, 각 메시지가 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 작업은 배달을 여러 번 시도해도 일정하게 유지되는 **message_id** 속성을 사용하여 수행하는 경우가 많습니다.

## 토픽과 구독을 삭제하는 방법

토픽과 구독은 영구적이므로, [Azure 관리 포털](https://manage.windowsazure.com) 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다. 아래 예제에서는 "test-topic"이라는 토픽을 삭제하는 방법을 보여 줍니다.

	azure_service_bus_service.delete_topic("test-topic")

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드에서는 "test-topic" 토픽에서 "high-messages"라는 구독을 삭제하는 방법을 보여 줍니다.

	azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## 다음 단계

이제 서비스 버스 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

-   다음 MSDN 참조를 확인하세요. [큐, 토픽 및 구독](http://msdn.microsoft.com/library/windowsazure/hh367516.aspx)
-   [SqlFilter](http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx)에 대한 API 참조.
-	GitHub의 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 리포지토리 방문

<!--HONumber=47-->
 