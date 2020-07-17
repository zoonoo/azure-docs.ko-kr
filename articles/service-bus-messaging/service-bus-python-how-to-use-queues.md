---
title: '빠른 시작: Python에서 Azure Service Bus 큐 사용'
description: 이 문서에서는 Python을 사용하여 Azure Service Bus 큐에서 메시지를 보내고 받는 방법을 설명합니다.
author: spelluru
documentationcenter: python
ms.devlang: python
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: seo-python-october2019, tracking-python
ms.openlocfilehash: f4fa90025e9aaa12c065514e0dd4e5d76e4f8d24
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85337294"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>빠른 시작: Python에서 Azure Service Bus 큐 사용

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

이 문서에서는 Python을 사용하여 Azure Service Bus 큐에서 메시지를 보내고 받는 방법을 설명합니다. 

Python Azure Service Bus 라이브러리에 대한 자세한 내용은 [Python용 Service Bus 라이브러리](/python/api/overview/azure/servicebus?view=azure-python)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독 [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- [빠른 시작: Azure Portal을 사용하여 Service Bus 토픽 및 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md)의 단계에 따라 만든 Service Bus 네임스페이스입니다. **공유 액세스 정책** 화면에서 기본 연결 문자열을 복사하여 이 문서의 뒷부분에서 사용합니다. 
- [Python Azure Service Bus][Python Azure Service Bus package] 패키지가 설치되어 있는 Python 3.4x 이상. 자세한 내용은 [Python 설치 가이드](/azure/developer/python/azure-sdk-install)를 참조하세요. 

## <a name="create-a-queue"></a>큐 만들기

**ServiceBusClient** 개체를 사용하면 큐로 작업할 수 있습니다. 프로그래밍 방식으로 Service Bus에 액세스하려면 Python 파일의 맨 위쪽에 다음 줄을 추가합니다.

```python
from azure.servicebus import ServiceBusClient
```

다음 코드를 추가하여 **ServiceBusClient** 개체를 만듭니다. `<connectionstring>`을 Service Bus 기본 연결 문자열 값으로 바꿉니다. [Azure Portal][Azure portal]의 Service Bus 네임스페이스에 있는 **공유 액세스 정책**에서 이 값을 찾을 수 있습니다.

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

다음 코드는 **ServiceBusClient**의 `create_queue` 메서드를 사용하여 기본 설정이 포함된 `taskqueue`라는 큐를 만듭니다.

```python
sb_client.create_queue("taskqueue")
```

옵션을 사용하여 메시지 TTL(Time to Live) 또는 최대 토픽 크기와 같은 기본 큐 설정을 재정의할 수 있습니다. 다음 코드에서는 최대 큐 크기는 5GB이고 TTL 값은 1분인 `taskqueue`라는 큐를 만듭니다.

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기

Service Bus 큐에 메시지를 보내기 위해 애플리케이션은 **ServiceBusClient** 개체에 대해 `send` 메서드를 호출합니다. 다음 코드 예제에서는 큐 클라이언트를 만들고 `taskqueue` 큐에 테스트 메시지를 보냅니다. `<connectionstring>`을 Service Bus 기본 연결 문자열 값으로 바꿉니다. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>메시지 크기 제한 및 할당량

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 생성 시 큐 크기를 정의할 수 있으며, 상한이 5GB입니다. 

할당량에 대한 자세한 내용은 [Service Bus 할당량][Service Bus quotas]을 참조하세요.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기

큐 클라이언트는 **ServiceBusClient** 개체의 `get_receiver` 메서드를 사용하여 큐에서 메시지를 받습니다. 다음 코드 예제에서는 큐 클라이언트를 만들고 `taskqueue` 큐에서 메시지를 수신합니다. `<connectionstring>`을 Service Bus 기본 연결 문자열 값으로 바꿉니다. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>peek_lock 매개 변수 사용

`get_receiver`의 선택적 `peek_lock` 매개 변수는 메시지를 읽을 때 Service Bus가 큐에서 메시지를 삭제할지 여부를 결정합니다. 메시지 수신에 대한 기본 모드는 *PeekLock*이거나, `peek_lock`이 큐에서 메시지를 삭제하지 않고 메시지를 읽고(피킹) 잠그는 **True**로 설정됩니다. 그런 다음, 각 메시지를 명시적으로 완료하여 큐에서 제거해야 합니다.

큐에서 메시지를 읽을 때 메시지를 삭제하려면 `get_receiver`의 `peek_lock` 매개 변수를 **False**로 설정하면 됩니다. 수신 작업의 일부로 메시지를 삭제하는 것은 가장 간단한 모델이지만, 오류가 발생하는 경우 애플리케이션에서 누락된 메시지를 허용할 수 있는 경우에만 작동합니다. 이 동작에 대한 이해를 돕기 위해 소비자가 수신 요청을 실행한 다음, 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 메시지가 수신되는 동안 삭제된 경우 애플리케이션이 다시 시작되고 메시지를 다시 사용하기 시작할 때 크래시 전에 수신된 메시지는 누락됩니다.

애플리케이션에서 누락된 메시지를 허용할 수 없는 경우 수신은 2단계 작업입니다. PeekLock은 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후, 애플리케이션에 반환합니다. 애플리케이션은 메시지를 처리하거나 저장한 후 **Message** 개체에 대해 `complete` 메서드를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다.  `complete` 메서드는 메시지를 사용 중인 것으로 표시하고 큐에서 제거합니다.

## <a name="handle-application-crashes-and-unreadable-messages"></a>애플리케이션 크래시 및 읽을 수 없는 메시지 처리

Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **Message** 개체의 `unlock` 메서드를 호출할 수 있습니다. Service Bus에서 큐 내 메시지의 잠금을 해제하므로 동일하거나 다른 소비 애플리케이션에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지에 대한 시간 제한도 있습니다. 애플리케이션에서 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 애플리케이션이 크래시되는 경우) Service Bus가 메시지의 잠금을 자동으로 해제하여 다시 받을 수 있게 합니다.

애플리케이션이 메시지를 처리한 후 `complete` 메서드가 호출되기 전에 크래시되는 경우 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 동작은 일반적으로 *최소 1회 처리*라고 합니다. 즉, 각 메시지가 최소 1회 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 시나리오에서 중복 처리를 허용할 수 없는 경우 배달 시도 간에 일정하게 유지되는 메시지의 **MessageId** 속성을 사용하여 중복 메시지 배달을 처리할 수 있습니다. 

> [!TIP]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하여 손쉽게 메시징 엔터티를 관리할 수 있습니다. 이 도구를 통해 가져오기/내보내기 기능 또는 토픽, 큐, 구독, 릴레이 서비스, Notification Hubs 및 Event Hubs를 테스트하는 기능과 같은 고급 기능을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

지금까지 Service Bus 큐의 기본 사항에 대해 알아보았습니다. 더 자세히 알아보려면 [큐, 토픽 및 구독][Queues, topics, and subscriptions]을 참조하세요.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
