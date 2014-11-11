<properties linkid="dev-ruby-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Ruby) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Ruby" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Queue Storage Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Ruby에서 큐 저장소 서비스를 사용하는 방법

이 가이드에서는 Microsoft Azure 큐 저장소 서비스를 사용하여 일반 시나리오를
수행하는 방법을 보여 줍니다. 샘플은 Ruby Azure API를 사용하여 작성되었습니다.
여기서 다루는 시나리오에는 큐 메시지 **삽입**, **보기**, **가져오기**,
및 **삭제**와 **큐 만들기 및
삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계][다음 단계] 섹션을
참조하세요.

## 목차

-   [큐 저장소 정의][큐 저장소 정의]
-   [개념][개념]
-   [Azure 저장소 계정 만들기][Azure 저장소 계정 만들기]
-   [Ruby 응용 프로그램 만들기][Ruby 응용 프로그램 만들기]
-   [저장소에 액세스하도록 응용 프로그램 구성][저장소에 액세스하도록 응용 프로그램 구성]
-   [Azure 저장소 연결 설정][Azure 저장소 연결 설정]
-   [방법: 큐 만들기][방법: 큐 만들기]
-   [방법: 큐에 메시지 삽입][방법: 큐에 메시지 삽입]
-   [방법: 다음 메시지 보기][방법: 다음 메시지 보기]
-   [방법: 큐에서 다음 메시지 제거][방법: 큐에서 다음 메시지 제거]
-   [방법: 대기 중인 메시지의 콘텐츠 변경][방법: 대기 중인 메시지의 콘텐츠 변경]
-   [방법: 큐에서 메시지를 제거하는 추가 옵션][방법: 큐에서 메시지를 제거하는 추가 옵션]
-   [방법: 큐 길이 가져오기][방법: 큐 길이 가져오기]
-   [방법: 큐 삭제][방법: 큐 삭제]
-   [다음 단계][다음 단계]

[WACOM.INCLUDE [howto-queue-storage](../includes/howto-queue-storage.md)]

## <span id="CreateAccount"></span></a>Azure 저장소 계정 만들기

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="create-a-ruby-application"></span></a>Ruby 응용 프로그램 만들기

Ruby 응용 프로그램을 만듭니다. 자세한 내용은
[Azure에서 Ruby 응용 프로그램 만들기][Azure에서 Ruby 응용 프로그램 만들기](영문)를 참조하세요.

## <span id="configure-your-application-to-access-storage"></span></a>저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함된 Ruby Azure 패키지를 다운로드하여 사용해야 합니다.

### RubyGems를 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac) 또는 **Bash**(Unix)와 같은 명령줄 인터페이스를 사용합니다.

2.  명령 창에 "gem install azure"를 입력하여 gem 및 종속성을 설치합니다.

### 패키지 가져오기

주로 사용하는 텍스트 편집기에서 저장소를 사용할 Ruby 파일의 맨 위에 다음을 추가합니다.

    require "azure"

## <span id="setup-a-windows-azure-storage-connection"></span></a>Azure 저장소 연결 설정

Azure 모듈은 **AZURE\_STORAGE\_ACCOUNT** 및 **AZURE\_STORAGE\_ACCESS\_KEY**
 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않은 경우에는
**Azure::QueueService**를 사용하기 전에 다음 코드로 계정 정보를 지정해야 합니다.

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

이러한 값을 얻으려면

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인합니다.
2.  사용하려는 저장소 계정으로 이동합니다.
3.  탐색 창 아래쪽에서 **키 관리**를 클릭합니다.
4.  팝업 대화 상자에 저장소 계정 이름, 기본 액세스 키 및 보조 액세스 키가 표시됩니다. 액세스 키의 경우 기본 액세스 키 또는 보조 액세스 키를 선택할 수 있습니다.

## <span id="how-to-create-a-queue"></span></a>방법: 큐 만들기

다음 코드는 **Azure::QueueService** 개체를 만들어 큐 작업을 수행할 수 있게 해 줍니다.

    azure_queue_service = Azure::QueueService.new

**create\_queue()** 메서드를 사용하여 지정된 이름이 있는 큐를 만듭니다.

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## <span id="how-to-insert-a-message-into-a-queue"></span></a>방법: 큐에 메시지 삽입

큐에 메시지를 삽입하려면 **create\_message()** 메서드를 사용하여 새 메시지를 만들고 이 메시지를 큐에 추가합니다.

    azure_queue_service.create_message("test-queue", "test message")

## <span id="how-to-peek-at-the-next-message"></span></a>방법: 다음 메시지 보기

큐에서 메시지를 제거하지 않고도 **peek\_messages()** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다. 기본적으로 **peek\_messages()**는 단일 메시지를 볼 수 있게 해 줍니다. 보려는 메시지의 수를 지정할 수도 있습니다.

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## <span id="how-to-dequeue-the-next-message"></span></a>방법: 큐에서 다음 메시지 제거

2단계를 거쳐 큐에서 메시지를 제거할 수 있습니다.

1.  **list\_messages()**를 호출하면 기본적으로 큐에서 다음 메시지를 가져옵니다. 가져오려는 메시지의 수를 지정할 수도 있습니다. **list\_messages()**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 표시 제한 시간(초 단위)을 매개 변수로 전달합니다.

2.  큐에서 메시지 제거를 완료하려면 **delete\_message()**도 호출해야 합니다.

메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **delete\_message()**를 호출합니다.

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## <span id="how-to-change-the-contents-of-a-queued-message"></span></a>방법: 대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 아래 코드는 **update\_message()** 메서드를 사용하여 메시지를 업데이트합니다. 이 메서드는 큐 메시지의 pop 확인 및 메시지가 큐에 표시되는 시간을 나타내는 UTC 날짜 시간 값이 포함된 튜플을 반환합니다.

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## <span id="how-to-additional-options-for-dequeuing-messages"></span></a>방법: 큐에서 메시지를 제거하는 추가 옵션

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

1.  메시지의 배치를 가져올 수 있습니다.

2.  표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

다음 코드 예제는 **list\_messages()** 메서드를 사용하여 한 번 호출에 15개의 메시지를 가져옵니다. 그런 다음 각 메시지를 인쇄하고 삭제합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## <span id="how-to-get-the-queue-length"></span></a>방법: 큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **get\_queue\_metadata()** 메서드는 큐 서비스에 대략적인 메시지 개수 및 큐에 대한 메타데이터를 반환하도록 요청합니다.

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## <span id="how-to-delete-a-queue"></span></a>방법: 큐 삭제

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 **delete\_queue()** 메서드를 호출합니다.

    azure_queue_service.delete_queue("test-queue")

## <span id="next-steps"></span></a>다음 단계

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보세요.

-   다음 MSDN 참조를 확인하세요. [Azure에서 데이터 저장 및 액세스][Azure에서 데이터 저장 및 액세스]
-   [Azure 저장소 팀 블로그][Azure 저장소 팀 블로그](영문)를 방문하세요.
-   GitHub에서 [Azure SDK for Ruby][Azure SDK for Ruby](영문) 리포지토리를 방문하세요.

이 항목에서 다룬 Azure 큐 서비스와 [서비스 버스 큐를 사용하는 방법][서비스 버스 큐를 사용하는 방법] 항목에서 다루는 Azure 서비스 버스 큐를 비교하려면 [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조][Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조]를 참조하세요.

  [다음 단계]: #next-steps
  [큐 저장소 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #CreateAccount
  [Ruby 응용 프로그램 만들기]: #create-a-ruby-application
  [저장소에 액세스하도록 응용 프로그램 구성]: #configure-your-application-to-access-storage
  [Azure 저장소 연결 설정]: #setup-a-windows-azure-storage-connection
  [방법: 큐 만들기]: #how-to-create-a-queue
  [방법: 큐에 메시지 삽입]: #how-to-insert-a-message-into-a-queue
  [방법: 다음 메시지 보기]: #how-to-peek-at-the-next-message
  [방법: 큐에서 다음 메시지 제거]: #how-to-dequeue-the-next-message
  [방법: 대기 중인 메시지의 콘텐츠 변경]: #how-to-change-the-contents-of-a-queued-message
  [방법: 큐에서 메시지를 제거하는 추가 옵션]: #how-to-additional-options-for-dequeuing-messages
  [방법: 큐 길이 가져오기]: #how-to-get-the-queue-length
  [방법: 큐 삭제]: #how-to-delete-a-queue
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Azure에서 Ruby 응용 프로그램 만들기]: /ko-kr/develop/ruby/tutorials/web-app-with-linux-vm/
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [Azure에서 데이터 저장 및 액세스]: http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
  [서비스 버스 큐를 사용하는 방법]: /ko-kr/develop/ruby/how-to-guides/service-bus-queues/
  [Azure 큐 및 Azure 서비스 버스 큐 - 비교 및 대조]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh767287.aspx
