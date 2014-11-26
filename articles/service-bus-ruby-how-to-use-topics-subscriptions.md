<properties linkid="dev-ruby-how-to-service-bus-topics" urlDisplayName="Service Bus Topics" pageTitle="How to use Service Bus topics (Ruby) - Azure" metaKeywords="Get started Azure Service Bus topics, Get Started Service Bus topics, Azure publish subscribe messaging, Azure messaging topics and subscriptions, Service Bus topic ruby" description="Learn how to use Service Bus topics and subscriptions in Azure. Code samples are written for Ruby applications." metaCanonical="" services="service-bus" documentationCenter="Ruby" title="How to Use Service Bus Topics/Subscriptions" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# 서비스 버스 토픽/구독을 사용하는 방법

이 가이드에서는 Ruby 응용 프로그램에서 서비스 버스 토픽과 구독을 사용하는 방법을 보여 줍니다. 여기서 다루는 시나리오에는 **토픽 및 구독 만들기, 구독 필터 만들기, 토픽에 메시지 보내기,** **구독에서 메시지 받기,** **토픽 및 구독 삭제** 등이 포함됩니다. 토픽 및 구독에 대한 자세한 내용은 [다음 단계][다음 단계] 섹션을 참조하십시오.

## 목차

-   [서비스 버스 토픽 및 구독 정의][서비스 버스 토픽 및 구독 정의]
-   [서비스 네임스페이스 만들기][서비스 네임스페이스 만들기]
-   [네임스페이스에 대한 기본 관리 자격 증명 얻기][네임스페이스에 대한 기본 관리 자격 증명 얻기]
-   [Ruby 응용 프로그램 만들기][Ruby 응용 프로그램 만들기]
-   [서비스 버스를 사용하도록 응용 프로그램 구성][서비스 버스를 사용하도록 응용 프로그램 구성]
-   [Azure 서비스 버스 연결 설정][Azure 서비스 버스 연결 설정]
-   [토픽을 만드는 방법][토픽을 만드는 방법]
-   [구독을 만드는 방법][구독을 만드는 방법]
-   [토픽에 메시지를 보내는 방법][토픽에 메시지를 보내는 방법]
-   [구독에서 메시지를 받는 방법][구독에서 메시지를 받는 방법]
-   [응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법][응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법]
-   [토픽과 구독을 삭제하는 방법][토픽과 구독을 삭제하는 방법]
-   [다음 단계][다음 단계]

[WACOM.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## <span id="create-a-ruby-application"></span></a>Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만듭니다. 자세한 내용은 [Azure에서 Ruby 응용 프로그램 만들기][Azure에서 Ruby 응용 프로그램 만들기](영문)를 참조하십시오.

## <span id="configure-your-application-to-use-service-bus"></span></a> 서비스 버스를 사용하도록 응용 프로그램 구성

Azure 서비스 버스를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### RubyGems를 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2.  명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

주로 사용하는 텍스트 편집기에서 저장소를 사용할 Ruby 파일의 맨 위에 다음을 추가합니다.

    require "azure"

## <span id="setup-a-windows-azure-service-bus-connection"></span></a>Azure 서비스 버스 연결 설정

Azure 모듈은 **AZURE\_SERVICEBUS\_NAMESPACE** 및 **AZURE\_SERVICEBUS\_ACCESS\_KEY** 환경 변수를 읽고 Azure 서비스 버스 네임스페이스에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수를 설정하지 않은 경우 **Azure::ServiceBusService**를 사용하기 전에 다음 코드로 네임스페이스 정보를 지정해야 합니다.

    Azure.config.sb_namespace = "<your azure service bus namespace>"
    Azure.config.sb_access_key = "<your azure service bus access key>"

## <span id="how-to-create-a-topic"></span></a> 토픽을 만드는 방법

**Azure::ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 다음 코드는 **Azure::ServiceBusService** 개체를 만듭니다. 토픽을 만들려면 **create\_topic()** 메서드를 사용합니다. 다음 예제에서는 토픽을 만들거나 오류가 있는 경우 오류를 출력합니다.

    azure_service_bus_service = Azure::ServiceBusService.new
    begin
      topic = azure_service_bus_service.create_queue("test-topic")
    rescue
      puts $!
    end

추가 옵션을 사용하여 **Azure::ServiceBus::Topic** 개체를 전달할 수도 있습니다. 추가 옵션을 사용하면 메시지 TTL(Time to Live) 또는 최대 큐 크기와 같은 기본 토픽 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB, TTL(Time to Live)을 1분으로 설정합니다.

    topic = Azure::ServiceBus::Topic.new("test-topic")
    topic.max_size_in_megabytes = 5120
    topic.default_message_time_to_live = "PT1M"

    topic = azure_service_bus_service.create_topic(topic)

## <span id="how-to-create-subscriptions"></span></a> 구독을 만드는 방법

토픽 구독은 **Azure::ServiceBusService** 개체로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

**참고**
 구독은 영구적이며, 구독 자체 또는 구독과 연결된 토픽이 삭제될 때까지 계속 유지됩니다. 응용 프로그램에 구독을 만들기 위한 논리가 포함된 경우, getSubscription 메서드를 사용하여 구독이 이미 존재하는지를 먼저 확인해야 합니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우 사용되는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 "all-messages"라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

    subscription = azure_service_bus_service.create_subscription("test-topic", 
      "all-messages")

### <span id="how-to-create-subscriptions"></span></a>필터를 사용하여 구독 만들기

토픽에 전송된 메시지 중 특정 토픽 구독 내에 나타나야 하는 메시지의 범위를 지정하는 필터를 설정할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **Azure::ServiceBus::SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression][SqlFilter.SqlExpression] 구문을 참조하십시오.

**Azure::ServiceBusService** 개체의 **create\_rule()** 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

**참고**
 기본 필터는 모든 새 구독에 자동으로 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll** 이 사용자가 지정하는 기타 필터를 재정의합니다. **Azure::ServiceBusService** 개체의 **delete\_rule()** 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.

아래 예제에서는 사용자 지정 **message\_number** 속성이 3보다 큰 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "high-messages"라는 구독을 만듭니다.

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

마찬가지로, 다음 예제에서는 **message\_number** 속성이 3보다 작거나 같은 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "low-messages"라는 구독을 만듭니다.

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

## <span id="how-to-send-messages-to-a-topic"></span></a> 토픽에 메시지를 보내는 방법

서비스 버스 토픽에 메시지를 보내려면 응용 프로그램에서 **Azure::ServiceBusService** 개체의 **send\_topic\_message()** 메서드를 사용해야 합니다. 서비스 버스 토픽으로 전송되는 메시지는 **Azure::ServiceBus::BrokeredMessage** 개체입니다. **Azure::ServiceBus::BrokeredMessage** 개체에는 표준 속성 집합(예: **label** 및 **time\_to\_live**), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 문자열 데이터의 본문이 있습니다. 응용 프로그램은 문자열 값을 **send\_topic\_message()** 메서드에 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 "test-topic"에 5개의 테스트 메시지를 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 **message\_number** 사용자 지정 속성 값이 달라지는 것을 알 수 있습니다(메시지를 수신하는 구독 결정).

    5.times do |i|
      message = Azure::ServiceBus::BrokeredMessage.new("test message " + i, 
        { :message_number => i })
      azure_service_bus_service.send_topic_message("test-topic", message)
    end

서비스 버스 토픽은 256MB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64MB임). 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## <span id="how-to-receive-messages-from-a-subscription"></span></a> 구독에서 메시지를 받는 방법

**Azure::ServiceBusService** 개체의 **receive\_subscription\_message()** 메서드를 사용하여 구독에서 메시지를 받습니다. 기본적으로 메시지는 구독에서 삭제하지 않고 읽고(피크) 잠급니다. **peek\_lock** 옵션을 **false**로 설정하여 메시지를 구독에서 읽고 삭제할 수 있습니다.

기본 동작은 읽기 및 삭제가 2단계 작업으로 수행되도록 하므로 메시지 누락이 허용되지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, **delete\_subscription\_message()** 메서드를 호출하고 삭제될 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. **delete\_subscription\_message()** 메서드는 메시지를 이용되는 것으로 표시하고 구독에서 제거합니다.

**:peek\_lock** 매개 변수를 **false**로 설정하면 메시지 읽기 및 삭제가 가장 간단한 모델이 되며, 오류 발생 시 메시지를 처리하지 않는 것을 허용하는 응용 프로그램의 시나리오에 가장 적합합니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보십시오. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

아래 예제에서는 **receive\_subscription\_message()**를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 이 예제에서는 먼저 **false**로 설정된 **:peek\_lock**을 사용하여 "low-messages" 구독에서 메시지를 받고 삭제한 후 "high-messages"에서 다른 메시지를 받고 그런 다음 **delete\_subscription\_message()**를 사용하여 메시지를 삭제합니다.

    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "low-messages", { :peek_lock => false })
    message = azure_service_bus_service.receive_subscription_message(
      "test-topic", "high-messages")
    azure_service_bus_service.delete_subscription_message(message)

## <span id="how-to-handle-application-crashes-and-unreadable-messages"></span></a> 응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 **Azure::ServiceBusService** 개체의 **unlock\_subscription\_message()** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 구독 내 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **delete\_subscription\_message()** 메서드가 호출되기 전에 크래시되는 경우, 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **message\_id** 속성을 사용합니다.

## <span id="how-to-delete-topics-and-subscriptions"></span></a> 토픽과 구독을 삭제하는 방법

토픽과 구독은 영구적이므로, [Azure 관리 포털][Azure 관리 포털] 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다. 아래 예제에서는 "test-topic"이라는 토픽을 삭제하는 방법을 보여 줍니다.

    azure_service_bus_service.delete_topic("test-topic")

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드에서는 "test-topic" 토픽에서 "high-messages"라는 구독을 삭제하는 방법을 보여 줍니다.

    azure_service_bus_service.delete_subscription("test-topic", "high-messages")

## <span id="NextSteps"></span></a>다음 단계

이제 서비스 버스 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보십시오.

-   다음 MSDN 참조를 확인하세요. [큐, 토픽 및 구독][큐, 토픽 및 구독]
-   [SqlFilter][SqlFilter]에 대한 API 참조
-   GitHub에서 [Azure SDK for Ruby][Azure SDK for Ruby](영문) 리포지토리를 방문하세요.

  [다음 단계]: #NextSteps
  [서비스 버스 토픽 및 구독 정의]: #what-are-service-bus-topics
  [서비스 네임스페이스 만들기]: #create-a-service-namespace
  [네임스페이스에 대한 기본 관리 자격 증명 얻기]: #obtain-default-credentials
  [Ruby 응용 프로그램 만들기]: #create-a-ruby-application
  [서비스 버스를 사용하도록 응용 프로그램 구성]: #configure-your-application-to-use-service-bus
  [Azure 서비스 버스 연결 설정]: #setup-a-windows-azure-service-bus-connection
  [토픽을 만드는 방법]: #how-to-create-a-topic
  [구독을 만드는 방법]: #how-to-create-subscriptions
  [토픽에 메시지를 보내는 방법]: #how-to-send-messages-to-a-topic
  [구독에서 메시지를 받는 방법]: #how-to-receive-messages-from-a-subscription
  [응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법]: #how-to-handle-application-crashes-and-unreadable-messages
  [토픽과 구독을 삭제하는 방법]: #how-to-delete-topics-and-subscriptions
  [Azure에서 Ruby 응용 프로그램 만들기]: /ko-KR/develop/ruby/tutorials/web-app-with-linux-vm/
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/ko-KR/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Azure 관리 포털]: https://manage.windowsazure.com
  [큐, 토픽 및 구독]: http://msdn.microsoft.com/ko-KR/library/windowsazure/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/ko-KR/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
