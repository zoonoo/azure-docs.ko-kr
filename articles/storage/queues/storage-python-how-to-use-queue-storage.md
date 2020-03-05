---
title: Python에서 Azure Queue storage v 2.1을 사용 하는 방법-Azure Storage
description: Python에서 Azure 큐 서비스 v 2.1을 사용 하 여 큐를 만들고 삭제 하 고 메시지를 삽입 하 고 가져오고 삭제 하는 방법에 대해 알아봅니다.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: 7812b62e2de3181ae1a901241a977e37f855704b
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268033"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Python에서 Azure Queue storage v 2.1을 사용 하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

이 문서에서는 Azure Queue storage 서비스를 사용 하는 일반적인 시나리오를 보여 줍니다. 큐 메시지 삽입, 보기, 가져오기 및 삭제, 큐 만들기 및 삭제 등의 시나리오를 다룹니다.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>개요

이 문서의 샘플은 Python으로 작성 되었으며 [Microsoft Azure Storage SDK for Python]를 사용 합니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python 다운로드 및 설치

[Python 용 AZURE STORAGE SDK](https://github.com/azure/azure-storage-python) 에는 python 버전 2.7, 3.3 이상이 필요 합니다.
 
### <a name="install-via-pypi"></a>PyPi를 통해 설치

PyPi(Python Package Index)를 통해 설치하려면 다음을 입력합니다.

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Azure Storage SDK for Python 버전 0.36 또는 이전 버전에서 업그레이드하는 경우 최신 패키지를 설치하기 전에 `pip uninstall azure-storage`를 사용하여 이전 SDK를 제거합니다.

대체 설치 방법은 [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/)을 참조하세요.

## <a name="view-the-sample-application"></a>샘플 애플리케이션 보기

Azure 큐에서 Python을 사용 하는 방법을 보여 주는 샘플 응용 프로그램을 보고 실행 하려면 [Azure Storage: python에서 Azure 큐 시작](https://github.com/Azure-Samples/storage-queue-python-getting-started)을 참조 하세요. 

샘플 애플리케이션을 실행하려면 `azure-storage-queue` 및 `azure-storage-common` 패키지를 둘 다 설치해야 합니다.

## <a name="create-a-queue"></a>큐 만들기

[QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) 개체를 사용하면 큐로 작업할 수 있습니다. 다음 코드는 `QueueService` 개체를 만듭니다. 프로그래밍 방식으로 Azure Storage에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

```python
from azure.storage.queue import QueueService
```

다음 코드는 스토리지 계정 이름 및 계정 키를 사용하는 `QueueService` 개체를 만듭니다. *Myaccount* 및 *mykey* 를 계정 이름 및 키로 바꿉니다.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>큐에 메시지 삽입

큐에 메시지를 삽입 하려면 [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) 메서드를 사용 하 여 새 메시지를 만들고 큐에 추가 합니다.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Azure 큐 메시지는 텍스트로 저장 됩니다. 이진 데이터를 저장 하려면 큐에 메시지를 넣기 전에 큐 서비스 개체에서 Base64 인코딩 및 디코딩 함수를 설정 합니다.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>다음 메시지 보기

큐에서 메시지를 제거 하지 않고 [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) 메서드를 호출 하 여 큐의 맨 앞에 있는 메시지를 피킹할 수 있습니다. 기본적으로 `peek_messages` 단일 메시지를 피킹합니다.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>큐에서 메시지 제거

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. [Get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)를 호출 하면 기본적으로 큐에서 다음 메시지를 가져옵니다. `get_messages`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-)도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리 된 직후에 `delete_message`를 호출 합니다.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제에서는 `get_messages` 메서드를 사용 하 여 한 번 호출에 16 개의 메시지를 가져옵니다. 그런 다음에 for 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 아래 코드는 [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) 메서드를 사용 하 여 메시지를 업데이트 합니다. 표시 제한 시간은 0으로 설정되어 있습니다.이는 메시지가 즉시 표시되고 콘텐츠가 업데이트됨을 의미합니다.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. [Get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) 메서드는 큐 서비스에 큐 및 `approximate_message_count`에 대 한 메타 데이터를 반환 하도록 요청 합니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 결과는 근사치일 뿐입니다.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>큐 삭제

큐 및 해당 큐에 포함 된 모든 메시지를 삭제 하려면 [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) 메서드를 호출 합니다.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>다음 단계

이제 queue storage의 기본 사항을 배웠으므로 다음 링크를 따라 자세히 알아보세요.

* [Azure 큐 Python API 참조](/python/api/azure-storage-queue)
* [Python 개발자 센터](https://azure.microsoft.com/develop/python/)
* [Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python]: https://github.com/Azure/azure-storage-python
