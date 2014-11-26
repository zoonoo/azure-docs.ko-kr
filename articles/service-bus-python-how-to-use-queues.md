<properties linkid="develop-python-service-bus-queues" urlDisplayName="Service Bus Queues" pageTitle="How to use Service Bus queues (Python) - Azure" metaKeywords="Azure Service Bus queues, Azure queues, Azure messaging, Azure queues Python" description="Learn how to use Service Bus queues in Azure. Code samples written in Python." metaCanonical="" services="service-bus" documentationCenter="Python" title="How to Use Service Bus Queues" authors="huvalo" solutions="" manager="" editor="" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# 서비스 버스 큐를 사용하는 방법

이 가이드에서는 서비스 버스 큐를 사용하는 방법을 보여 줍니다. 샘플은 Python으로 작성되었으며 Python Azure 모듈을 사용합니다. 여기서 다루는 시나리오에는 **큐 만들기, 메시지 보내기 및 받기**, **큐 삭제** 등이 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계][다음 단계] 섹션을 참조하세요.

## 목차

-   [서비스 버스 큐 정의][서비스 버스 큐 정의]
-   [서비스 네임스페이스 만들기][서비스 네임스페이스 만들기]
-   [네임스페이스에 대한 기본 관리 자격 증명 얻기][네임스페이스에 대한 기본 관리 자격 증명 얻기]
-   [방법: 큐 만들기][방법: 큐 만들기]
-   [방법: 큐에 메시지 보내기][방법: 큐에 메시지 보내기]
-   [방법: 큐에서 메시지 받기][방법: 큐에서 메시지 받기]
-   [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리][방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리]
-   [다음 단계][다음 단계]

[WACOM.INCLUDE [howto-service-bus-queues](../includes/howto-service-bus-queues.md)]

**참고:** Python 또는 클라이언트 라이브러리를 설치해야 하는 경우 [Python 설치 가이드][Python 설치 가이드](영문)를 참조하세요.

## <a name="create-queue"> </a>큐를 만드는 방법

**ServiceBusService** 개체를 사용하면 큐로 작업할 수 있습니다. 프로그래밍 방식으로 Azure 서비스 버스에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

    from azure.servicebus import *

다음 코드는 **ServiceBusService** 개체를 만듭니다. 'mynamespace', 'sharedaccesskeyname' 및 'sharedaccesskey'를 실제 네임스페이스, SAS(공유 액세스 서명) 키 이름 및 값으로 바꿉니다.

    bus_service = ServiceBusService(
        service_namespace='mynamespace',
        shared_access_key_name='sharedaccesskeyname',
        shared_access_key_value='sharedaccesskey')

SAS 키 이름 및 값에 대한 값은 Azure 포털 연결 정보 또는 Visual Studio 속성 창(이전 섹션에 표시된 대로 서버 탐색기에서 서비스 버스 네임스페이스 선택)에서 확인할 수 있습니다.

    bus_service.create_queue('taskqueue')

**create\_queue**는 추가 옵션도 지원합니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 큐 크기 등 기본 큐 설정을 재정의할 수 있습니다. 다음 예제에서는 최대 큐 크기를 5GB, TTL(Time to Live)을 1분으로 설정합니다.

    queue_options = Queue()
    queue_options.max_size_in_megabytes = '5120'
    queue_options.default_message_time_to_live = 'PT1M'

    bus_service.create_queue('taskqueue', queue_options)

## <a name="send-messages"> </a>큐에 메시지를 보내는 방법

서비스 버스 큐에 메시지를 보내기 위해 응용 프로그램은 **ServiceBusService** 개체의 **send\_queue\_message** 메서드를 호출합니다.

다음 예에서는 **send\_queue\_message**를 사용하여 *taskqueue*라는 큐에 테스트 메시지를 보내는 방법을 보여 줍니다.

    msg = Message('Test Message')
    bus_service.send_queue_message('taskqueue', msg)

서비스 버스 큐는 256KB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64KB임). 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다.

## <a name="receive-messages"> </a>큐에서 메시지를 받는 방법

**ServiceBusService** 개체의 **receive\_queue\_message** 메서드를 사용하여 큐에서 메시지를 받습니다.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
    print(msg.body)

**peek\_lock** 매개 변수가 **False**로 설정된 경우 메시지를 읽으면 큐에서 해당 메시지가 삭제됩니다. **peek\_lock** 매개 변수를 **True**로 설정하여 큐에서 삭제되지 않도록 메시지를 읽은(peek) 후 잠글 수 있습니다.

받기 작업의 일부로 메시지를 읽고 삭제하는 동작은 가장 단순한 모델이며, 실패할 경우 응용 프로그램이 메시지를 처리하지 않아도 되는 시나리오에서 가장 효과적입니다. 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

**peek\_lock** 매개 변수를 **True**로 설정하면 수신은 2단계 작업이 되므로, 메시지 누락을 허용하지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다.응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, **Message** 개체의 **delete** 메서드를 호출하여 수신 프로세스의 두 번째 단계를 완료합니다. **delete** 메서드는 메시지를 이용되는 것으로 표시하고 큐에서 제거합니다.

    msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
    print(msg.body)

    msg.delete()

## <a name="handle-crashes"> </a> 응용 프로그램 크래시 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 응용 프로그램이 메시지를 처리할 수 없는 경우 **Message** 개체의 **unlock** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 큐 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

큐 내에서 잠긴 메시지와 연결된 제한 시간도 있으며, 응용 프로그램에서 잠금 제한 시간이 만료되기 전에 메시지를 처리하지 못하는 경우(예: 응용 프로그램이 크래시되는 경우) 서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있게 합니다.

응용 프로그램이 메시지를 처리한 후 **delete** 메서드가 호출되기 전에 크래시되는 경우, 다시 시작될 때 메시지가 응용 프로그램에 다시 배달됩니다. 이를 **최소 한 번 이상 처리**라고 합니다. 즉, 각 메시지가 최소 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 경우 대체로 배달 시도 간에 일정하게 유지되는 메시지 **MessageId** 속성을 사용합니다.

## <a name="next-steps"> </a>다음 단계

이제 서비스 버스 큐의 기본 사항을 익혔으므로 다음 링크를 따라 자세히 알아보세요.

-   다음 MSDN 참조를 확인하세요. [큐, 토픽 및 구독][큐, 토픽 및 구독]

  [다음 단계]: #next-steps
  [서비스 버스 큐 정의]: #what-are-service-bus-queues
  [서비스 네임스페이스 만들기]: #create-a-service-namespace
  [네임스페이스에 대한 기본 관리 자격 증명 얻기]: #obtain-default-credentials
  [방법: 큐 만들기]: #create-queue
  [방법: 큐에 메시지 보내기]: #send-messages
  [방법: 큐에서 메시지 받기]: #receive-messages
  [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리]: #handle-crashes
  [Python 설치 가이드]: ../python-how-to-install/
  [큐, 토픽 및 구독]: http://msdn.microsoft.com/ko-KR/library/windowsazure/hh367516.aspx
