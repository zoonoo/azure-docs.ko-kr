---
title: "Service Bus 토픽을 사용하는 방법(Ruby) | Microsoft Docs"
description: "Azure에서 서비스 버스 토픽 및 구독을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Ruby 응용 프로그램용으로 작성되었습니다."
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a033aee05db1a837e0891256db26d63fe80e05a2


---
# <a name="how-to-use-service-bus-topicssubscriptions"></a>Service Bus 토픽/구독을 사용하는 방법
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

이 문서에서는 Ruby 응용 프로그램에서 Service Bus 토픽과 구독을 사용하는 방법을 설명합니다. 여기서 다루는 시나리오에는 **토픽 및 구독 만들기, 구독 필터 만들기, 토픽에 **메시지 보내기, **구독에서 메시지 받기**, **토픽 및 구독 삭제** 등이 포함됩니다. 토픽 및 구독에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="service-bus-topics-and-subscriptions"></a>Service Bus 토픽 및 구독
서비스 버스 토픽 및 구독은 *게시/구독* 메시징 통신 모델을 지원합니다. 토픽 및 구독을 사용하는 경우 분산 응용 프로그램의 구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 토픽을 통해 메시지를 교환합니다.

![TopicConcepts](./media/service-bus-ruby-how-to-use-topics-subscriptions/sb-topics-01.png)

각 메시지가 단일 소비자에 의해 처리되는 Service Bus 큐와 반대로, 토픽과 구독은 게시/구독 패턴을 사용하여 **일 대 다** 형태의 통신을 제공합니다. 하나의 토픽에 여러 구독을 등록할 수 있습니다. 토픽에 메시지를 전송하면 각 구독에서 독립적으로 처리할 수 있습니다.

토픽 구독은 토픽에 전송된 메시지의 복사본을 받는 가상 큐와 유사합니다. 선택적으로 구독별 토픽에 대한 필터 규칙을 등록할 수 있으며, 이렇게 하면 각 토픽 구독에서 받는 토픽 메시지를 필터링/제한할 수 있습니다.

Service Bus 토픽 및 구독을 사용하면 다수의 사용자와 응용 프로그램에 대해 다수의 메시지를 처리하도록 확장할 수 있습니다.

## <a name="create-a-namespace"></a>네임스페이스 만들기
Azure에서 서비스 버스 큐 사용을 시작하려면 먼저 네임스페이스를 만들어야 합니다. 네임스페이스는 응용 프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다. [Azure 포털][Azure 포털]에서는 ACS 연결을 사용하여 네임스페이스를 만들지 않으므로 명령줄 인터페이스를 통해 네임스페이스를 만들어야 합니다.

네임스페이스를 만들려면

1. Azure PowerShell 콘솔 창을 엽니다.
2. 다음 명령을 입력하여 네임스페이스를 만듭니다. 원하는 네임스페이스 값을 입력하고 응용 프로그램과 같은 지역을 지정합니다.
   
    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
    ```
   
    ![네임스페이스 만들기](./media/service-bus-ruby-how-to-use-topics-subscriptions/showcmdcreate.png)

## <a name="obtain-default-management-credentials-for-the-namespace"></a>네임스페이스에 대한 기본 관리 자격 증명 얻기
새 네임스페이스에 대해 큐 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다.

서비스 버스 네임스페이스를 만들 때 실행한 PowerShell cmdlet이 네임스페이스 관리에 사용할 수 있는 키를 표시합니다. **DefaultKey** 값을 복사합니다. 이 자습서의 뒷부분에 나오는 코드에 이 값을 사용할 것입니다.

![키 복사](./media/service-bus-ruby-how-to-use-topics-subscriptions/defaultkey.png)

> [!NOTE]
> [Azure 포털][Azure 포털]에 로그인하여 네임스페이스에 대한 연결 정보로 이동하여 이 키를 찾을 수도 있습니다.
> 
> 

## <a name="create-a-ruby-application"></a>Ruby 응용 프로그램 만들기
자세한 내용은 [Azure에서 Ruby 응용 프로그램 만들기](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md)를 참조하세요.

## <a name="configure-your-application-to-use-service-bus"></a>서비스 버스를 사용하도록 응용 프로그램 구성
서비스 버스를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용합니다.

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems를 사용하여 패키지 가져오기
1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.
2. 명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### <a name="import-the-package"></a>패키지 가져오기
원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

```
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>서비스 버스 연결 설정
Azure 모듈은 **AZURE\_SERVICEBUS\_NAMESPACE** 및 **AZURE\_SERVICEBUS\_ACCESS\_KEY** 환경 변수를 읽고 Service Bus 네임스페이스에 연결하는 데 필요한 정보를 가져옵니다. 이러한 환경 변수를 설정하지 않은 경우 **Azure::ServiceBusService**를 사용하기 전에 다음 코드로 네임스페이스 정보를 지정해야 합니다.

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

네임스페이스 값을 전체 URL이 아니라 만든 값으로 설정합니다. 예를 들어 "yourexamplenamespace.servicebus.windows.net"이 아니라 **"yourexamplenamespace"**를 사용합니다.

## <a name="create-a-topic"></a>토픽 만들기
**Azure::ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 다음 코드는 **Azure::ServiceBusService** 개체를 만듭니다. 토픽을 만들려면 **create\_topic()** 메서드를 사용합니다. 다음 예제에서는 토픽을 만들거나 오류가 있는 경우 오류를 출력합니다.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

추가 옵션을 사용하여 **Azure::ServiceBus::Topic** 개체를 전달할 수도 있습니다. 추가 옵션을 사용하면 메시지 TTL(Time to Live) 또는 최대 큐 크기와 같은 기본 토픽 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB, TTL(Time to Live)을 1분으로 설정합니다.

```
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>구독 만들기
토픽 구독은 **Azure::ServiceBusService** 개체로도 만들 수 있습니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

구독은 영구적이며, 구독 자체 또는 구독과 연결된 토픽이 삭제될 때까지 계속 유지됩니다. 응용 프로그램에 구독을 만들기 위한 논리가 포함된 경우, getSubscription 메서드를 사용하여 구독이 이미 존재하는지를 먼저 확인해야 합니다.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>기본(MatchAll) 필터를 사용하여 구독 만들기
**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우 사용되는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 "all-messages"라는 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>필터를 사용하여 구독 만들기
토픽으로 전송된 메시지 중 특정 구독 내에 표시되어야 하는 메시지의 범위를 지정하는 필터를 정의할 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는 **Azure::ServiceBus::SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx) 구문을 참조하세요.

**Azure::ServiceBusService** 개체의 **create\_rule()** 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

기본 필터는 모든 새로운 구독에 자동으로 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면 **MatchAll**이 사용자가 지정하는 기타 필터를 재정의합니다. **Azure::ServiceBusService** 개체의 **delete\_rule()** 메서드를 사용하여 기본 규칙을 제거할 수 있습니다.

다음 예제에서는 사용자 지정 **message\_number** 속성이 3보다 큰 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "high-messages"라는 구독을 만듭니다.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

마찬가지로, 다음 예제에서는 **message_number** 속성이 3보다 작거나 같은 메시지만 선택하는 **Azure::ServiceBus::SqlFilter**를 사용하여 "low-messages"라는 구독을 만듭니다.

```
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

이제 "test-topic"으로 메시지가 전송되는 경우 "all-messages" 토픽 구독을 구독하는 수신자에게는 항상 배달되고, "high-messages" 및 "low-messages" 토픽 구독을 구독하는 수신자에게는 메시지 내용에 따라 선택적으로 배달됩니다.

## <a name="send-messages-to-a-topic"></a>토픽에 메시지 보내기
Service Bus 토픽에 메시지를 보내려면 응용 프로그램에서 **Azure::ServiceBusService** 개체의 **send\_topic\_message()** 메서드를 사용해야 합니다. Service Bus 토픽으로 전송되는 메시지는 **Azure::ServiceBus::BrokeredMessage** 개체의 인스턴스입니다. **Azure::ServiceBus::BrokeredMessage** 개체에는 표준 속성 집합(예: **label** 및 **time\_to\_live**), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 문자열 데이터의 본문이 있습니다. 응용 프로그램은 문자열 값을 **send\_topic\_message()**에 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 "test-topic"에 5개의 테스트 메시지를 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 **message_number** 사용자 지정 속성 값이 달라지는 것을 알 수 있습니다(메시지를 수신하는 구독 결정).

```
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus 토픽은 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
**Azure::ServiceBusService** 개체의 **receive\_subscription\_message()** 메서드를 사용하여 구독에서 메시지를 받습니다. 기본적으로 메시지는 구독에서 삭제하지 않고 읽고(피크) 잠급니다. **peek\_lock** 옵션을 **false**로 설정하여 메시지를 구독에서 읽고 삭제할 수 있습니다.

기본 동작은 읽기 및 삭제가 2단계 작업으로 수행되도록 하므로 메시지 누락이 허용되지 않는 응용 프로그램도 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, **delete\_subscription\_message()** 메서드를 호출하고 삭제될 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. **delete\_subscription\_message()** 메서드는 메시지를 이용되는 것으로 표시하고 구독에서 제거합니다.

**:peek\_lock** 매개 변수를 **false**로 설정하면 메시지 읽기 및 삭제가 가장 간단한 모델이 되며, 오류 발생 시 메시지를 처리하지 않는 것을 허용하는 응용 프로그램의 시나리오에 가장 적합합니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

다음 예제에서는 **receive\_subscription\_message()**를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 이 예제에서는 먼저 **false**로 설정된 **:peek\_lock**을 사용하여 "low-messages" 구독에서 메시지를 받고 삭제한 후 "high-messages"에서 다른 메시지를 받고 그런 다음 **delete\_subscription\_message()**를 사용하여 메시지를 삭제합니다.

```
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>응용 프로그램 크래시 및 읽을 수 없는 메시지 처리
서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 **Azure::ServiceBusService** 개체의 **unlock\_subscription\_message()** 메서드를 호출할 수 있습니다. 그러면 Service Bus에서 구독 내 메시지의 잠금을 해제하고 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) Service Bus가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **delete\_subscription\_message()** 메서드가 호출되기 전에 크래시되는 경우, 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지의 **message\_id** 속성을 사용합니다.

## <a name="delete-topics-and-subscriptions"></a>토픽 및 구독 삭제
토픽과 구독은 영구적이므로, [Azure 포털][Azure 포털] 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다. 아래 예제에서는 "test-topic"이라는 토픽을 삭제하는 방법을 보여 줍니다.

```
azure_service_bus_service.delete_topic("test-topic")
```

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 다음 코드에서는 "test-topic" 토픽에서 "high-messages"라는 구독을 삭제하는 방법을 보여 줍니다.

```
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>다음 단계
이제 서비스 버스 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

* [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)을 참조하세요.
* [SqlFilter](http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx)에 대한 API 참조.
* GitHub에서 [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) 리포지토리를 방문하세요.

[Azure 포털]: https://portal.azure.com



<!--HONumber=Nov16_HO3-->


