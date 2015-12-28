<properties
	pageTitle="Ruby를 통해 서비스 버스 큐를 사용하는 방법 | Microsoft Azure"
	description="Azure에서 서비스 버스 큐를 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Ruby로 작성되었습니다."
	services="service-bus"
	documentationCenter="ruby"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="sethm"/>

# 서비스 버스 큐를 사용하는 방법

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

이 가이드에서는 서비스 버스 큐를 사용하는 방법을 설명합니다. 샘플은 Ruby로 작성되었으며 Azure gem을 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기, 메시지 보내기 및 받기**, **큐 삭제** 등이 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

## 서비스 버스 큐 정의

서비스 버스 큐는 *조정된 메시징* 통신 모델을 지원합니다. 큐를 사용하는 경우 분산 응용 프로그램의 구성 요소가 서로 직접 통신하지 않고 중간자 역할을 하는 큐를 통해 메시지를 교환합니다. 메시지 생산자(보낸 사람)는 메시지를 큐로 전달한 후 계속해서 처리합니다. 메시지 소비자(받는 사람)는 비동기적으로 큐에서 메시지를 끌어와서 처리합니다. 생산자는 계속해서 추가 메시지를 처리하고 보내기 위해 소비자의 회신을 기다릴 필요가 없습니다. 큐는 하나 이상의 경쟁 소비자에게 **FIFO(선입선출)** 메시지 배달을 제공합니다. 즉, 일반적으로 메시지가 큐에 추가된 순서대로 받는 사람이 메시지를 받고 처리하며, 각 메시지가 하나의 메시지 소비자에 의해서만 수신 및 처리됩니다.

![QueueConcepts](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

서비스 버스 큐는 다양한 시나리오에 사용할 수 있는 범용 기술입니다.

-   [다중 계층 Azure 응용 프로그램](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)에서 웹 역할과 작업자 역할 간의 통신
-   [하이브리드 솔루션](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)에서 온-프레미스 앱과 Azure 호스티드 앱 간의 통신
-   서로 다른 조직이나 조직의 부서에서 온-프레미스로 실행되는 분산 응용 프로그램 구성 요소 간의 통신

큐를 사용하면 응용 프로그램 규모를 더 효율적으로 확장할 수 있으며, 아키텍처의 복원력을 증가시킬 수 있습니다.

## 서비스 네임스페이스 만들기

Azure에서 서비스 버스 큐 사용을 시작하려면 먼저 서비스 네임스페이스를 만들어야 합니다. 서비스 네임스페이스는 응용 프로그램 내에서 서비스 버스 리소스의 주소를 지정하기 위한 범위 컨테이너를 제공합니다. Azure 클래식 포털에서는 ACS 연결을 사용하여 네임스페이스를 만들지 않으므로 명령줄 인터페이스를 통해 네임스페이스를 만들어야 합니다.

서비스 네임스페이스를 만들려면

1. Azure PowerShell 콘솔을 엽니다.

2. 다음 명령을 입력하여 서비스 버스 네임스페이스를 만듭니다. 원하는 네임스페이스 값을 입력하고 응용 프로그램과 같은 지역을 지정합니다.

    ```
	New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true

    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
	```

## 네임스페이스에 대한 관리 자격 증명 얻기

새 네임스페이스에 대해 큐 만들기 등의 관리 작업을 수행하려면 네임스페이스에 대한 관리 자격 증명을 받아야 합니다.

Azure 서비스 버스 네임스페이스를 만들 때 실행한 PowerShell cmdlet이 네임스페이스 관리에 사용할 수 있는 키를 표시합니다. **DefaultKey** 값을 복사합니다. 이 자습서의 뒷부분에 나오는 코드에 이 값을 사용할 것입니다.

![키 복사](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [AZURE.NOTE][Azure 클래식 포털](http://manage.windowsazure.com/)에 로그인하여 서비스 버스 네임스페이스에 대한 연결 정보로 이동하여 이 키를 찾을 수도 있습니다.

## Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만듭니다. 자세한 내용은 [Azure에서 Ruby 응용 프로그램 만들기](/develop/ruby/tutorials/web-app-with-linux-vm/)를 참조하십시오.

## 서비스 버스를 사용하도록 응용 프로그램 구성

Azure 서비스 버스를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용합니다.

### RubyGems를 사용하여 패키지 가져오기

1. **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2. 명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

원하는 텍스트 편집기를 사용하여 저장소를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

```
require "azure"
```

## Azure 서비스 버스 연결 설정

Azure 모듈은 **AZURE\_SERVICEBUS\_NAMESPACE** 및 **AZURE\_SERVICEBUS\_ACCESS\_KEY** 환경 변수를 읽고 서비스 버스 네임스페이스에 연결하는 데 필요한 정보를 가져옵니다. 이러한 환경 변수를 설정하지 않은 경우 **Azure::ServiceBusService**를 사용하기 전에 다음 코드로 네임스페이스 정보를 지정해야 합니다.

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

네임스페이스 값을 전체 URL이 아니라 만든 값으로 설정합니다. 예를 들어 "yourexamplenamespace.servicebus.windows.net"이 아니라 **"yourexamplenamespace"**를 사용합니다.

## 큐를 만드는 방법

**Azure::ServiceBusService** 개체를 사용하면 큐로 작업할 수 있습니다. 큐를 만들려면 **create\_queue()** 메서드를 사용합니다. 다음 예제에서는 큐를 만들거나 오류를 출력합니다.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

추가 옵션을 사용하여 **Azure::ServiceBus::Queue** 개체를 전달할 수도 있습니다. 추가 옵션을 사용하면 메시지 TTL(Time to Live) 또는 최대 큐 크기와 같은 기본 큐 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB, TTL(Time to Live)을 1분으로 설정하는 방법을 보여 줍니다.

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## 큐에 메시지를 보내는 방법

서비스 버스 큐에 메시지를 보내기 위해 응용 프로그램은 **Azure::ServiceBusService** 개체에 대해 **send\\_queue\\_message()** 메서드를 호출합니다. 서비스 버스 큐로 보내고 받는 메시지는 **Azure::ServiceBus::BrokeredMessage** 개체이며 표준 속성 집합(예: **label** 및 **time\_to\_live**), 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 응용 프로그램 데이터 본문을 포함합니다. 응용 프로그램은 문자열 값을 메시지로 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 **send\_queue\_message()**를 사용하여 "test-queue"라는 큐에 테스트 메시지를 보내는 방법을 보여 줍니다.

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

서비스 버스 큐는 256KB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB임). 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다.

## 큐에서 메시지를 받는 방법

**Azure::ServiceBusService** 개체의 **receive\_queue\_message()** 메서드를 사용하여 큐에서 메시지를 받습니다. 기본적으로 메시지는 큐에서 삭제하지 않고 읽고 잠급니다. 그러나 **:peek\_lock** 옵션을 **false**로 설정하여 메시지를 읽고 큐에서 삭제할 수 있습니다.

기본 동작은 읽기 및 삭제가 2단계 작업으로 수행되도록 하므로 메시지 누락이 허용되지 않는 응용 프로그램도 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다. 응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, **delete\_queue\_message()** 메서드를 호출하고 삭제될 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. **delete\_queue\_message()** 메서드는 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

**:peek\_lock** 매개 변수를 **false**로 설정하면 메시지 읽기 및 삭제가 가장 간단한 모델이 되며, 오류 발생 시 메시지를 처리하지 않는 것을 허용하는 응용 프로그램의 시나리오에 가장 적합합니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스가 메시지를 이용되는 것으로 표시했기 때문에 응용 프로그램이 다시 시작되고 메시지 이용을 다시 시작할 때 크래시 전에 이용된 메시지는 누락됩니다.

다음 예제에서는 **receive\_queue\_message()**를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 이 예제에서는 먼저 **false**로 설정된 **:peek\_lock**을 사용하여 메시지를 받고 삭제한 후 다른 메시지를 받고 그런 다음 **delete\_queue\_message()**를 사용하여 메시지를 삭제합니다.

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## 응용 프로그램 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 **Azure::ServiceBusService** 개체의 **unlock\_queue\_message()** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 시간 제한도 있으며, 응용 프로그램에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **delete\_queue\_message()** 메서드가 호출되기 전에 크래시되는 경우, 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지하는 메시지의 **message\_id** 속성을 사용합니다.

## 다음 단계

이제 서비스 버스 큐의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보세요.

-   [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md) 개요
-   GitHub에서 [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) 리포지토리를 방문하십시오.

이 문서에서 설명한 Azure 서비스 버스 큐와 [Azure 큐 서비스를 사용하는 방법](/develop/ruby/how-to-guides/queue-service/) 문서에서 설명한 Azure 큐를 비교하려면 [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조](service-bus-azure-and-service-bus-queues-compared-contrasted.md)를 참조하세요.
 

<!---HONumber=AcomDC_1217_2015-->