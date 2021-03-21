---
title: Ruby에서 Queue Storage를 사용 하는 방법-Azure Storage
description: Azure Queue Storage을 사용 하 여 큐를 만들고 삭제 하 고 메시지를 삽입, 가져오기 및 삭제 하는 방법에 대해 알아봅니다. 샘플은 Ruby로 작성되었습니다.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587665"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Ruby에서 Queue Storage를 사용 하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요

이 가이드에서는 Microsoft Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 Ruby Azure API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 **큐 만들기 및 삭제** 뿐만 아니라 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제** 가 포함됩니다.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby 애플리케이션 만들기

Ruby 애플리케이션을 만듭니다. 지침은 [Linux의 App Service에서 Ruby 응용 프로그램 만들기](../../app-service/quickstart-ruby.md)를 참조 하세요.

## <a name="configure-your-application-to-access-storage"></a>스토리지에 액세스하도록 애플리케이션 구성

Azure Storage를 사용 하려면 저장소 REST 서비스와 통신 하는 편리한 라이브러리 집합이 포함 된 Ruby Azure 패키지를 다운로드 하 여 사용 해야 합니다.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>RubyGems를 사용하여 패키지 가져오기

1. PowerShell(Windows), Terminal(Mac) 또는 Bash(Unix)와 같은 명령줄 인터페이스를 사용합니다.
2. `gem install Azure`명령 창에를 입력 하 여 보석 및 종속성을 설치 합니다.

### <a name="import-the-package"></a>패키지 가져오기

원하는 텍스트 편집기를 사용하여 스토리지를 사용하려는 Ruby 파일의 맨 위에 다음을 추가합니다.

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure Storage 연결 설정

Azure 모듈은 환경 변수 `AZURE_STORAGE_ACCOUNT` 및 `AZURE_STORAGE_ACCESS_KEY` Azure Storage 계정에 연결 하는 데 필요한 정보를 읽습니다. 이러한 환경 변수를 설정 하지 않은 경우 `Azure::QueueService` 다음 코드와 함께를 사용 하기 전에 계정 정보를 지정 해야 합니다.

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Azure 포털의 클래식 또는 Resource Manager 스토리지 계정에서 이러한 값을 가져오려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 사용하려는 스토리지 계정으로 이동합니다.
3. 오른쪽의 **설정** 블레이드에서 **액세스 키** 를 클릭 합니다.
4. 표시 되는 **액세스** 키 블레이드에서 액세스 키 1 및 액세스 키 2가 표시 됩니다. 이 둘 중 하나를 사용할 수 있습니다.
5. 복사 아이콘을 클릭하여 키를 클립보드에 복사합니다.

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기

다음 코드는 `Azure::QueueService` 큐로 작업할 수 있는 개체를 만듭니다.

```ruby
azure_queue_service = Azure::QueueService.new
```

지정 된 `create_queue()` 이름의 큐를 만들려면 메서드를 사용 합니다.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>방법: 큐에 메시지 삽입

큐에 메시지를 삽입 하려면 메서드를 사용 `create_message()` 하 여 새 메시지를 만들고 큐에 추가 합니다.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 보기

큐에서 메시지를 제거 하지 않고 메서드를 호출 하 여 큐의 맨 앞에 있는 메시지를 피킹할 수 있습니다 `peek_messages()` . 기본적으로 `peek_messages()` 단일 메시지를 피킹합니다. 보려는 메시지의 수를 지정할 수도 있습니다.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>큐에서 다음 메시지를 제거하는 방법

2단계를 거쳐 큐에서 메시지를 제거할 수 있습니다.

1. 를 호출 하면 `list_messages()` 기본적으로 큐에서 다음 메시지를 가져옵니다. 가져오려는 메시지의 수를 지정할 수도 있습니다. 에서 반환 되는 메시지는 `list_messages()` 이 큐의 메시지를 읽는 다른 코드에는 표시 되지 않습니다. 표시 제한 시간(초 단위)을 매개 변수로 전달합니다.
2. 큐에서 메시지 제거를 완료 하려면도 호출 해야 `delete_message()` 합니다.

메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드 `delete_message()` 는 메시지가 처리 된 직후에 호출 됩니다.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 다음 코드에서는 메서드를 사용 하 여 `update_message()` 메시지를 업데이트 합니다. 메서드는 큐 메시지의 pop 수신 및 메시지가 큐에 표시 되는 시간을 나타내는 UTC 값을 포함 하는 튜플을 반환 합니다 `DateTime` .

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>방법: 메시지를 큐에서 제거할 수 있는 추가 옵션

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

1. 메시지의 배치를 가져올 수 있습니다.
2. 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

다음 코드 예제에서는 메서드를 사용 하 여 `list_messages()` 한 번의 호출로 15 개의 메시지를 가져옵니다. 그런 다음 각 메시지를 인쇄하고 삭제합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. `get_queue_metadata()`메서드는 대략적인 메시지 수와 기타 큐 메타 데이터를 반환 합니다.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제

큐 및 해당 큐에 포함 된 모든 메시지를 삭제 하려면 `delete_queue()` 큐 개체에서 메서드를 호출 합니다.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>다음 단계

Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡 한 저장소 작업에 대해 알아보세요.

- [Azure Storage 팀 블로그](/archive/blogs/windowsazurestorage/) 를 방문 하세요.
- GitHub에서 [AZURE SDK For Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) 리포지토리 방문

이 문서에서 설명 하는 Azure Queue Storage와 [Service Bus 큐를 사용 하는 방법](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)에 설명 된 Azure Service Bus를 비교 하려면 [azure Queue Storage 및 Service Bus 큐-비교 및 대조](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md) 를 참조 하세요.
