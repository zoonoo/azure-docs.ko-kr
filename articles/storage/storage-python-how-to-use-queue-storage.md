<properties 
	pageTitle="Python에서 큐 저장소를 사용하는 방법 | Microsoft Azure" 
	description="Azure 큐 서비스를 사용하여 Python에서 큐를 작성 및 삭제하고 메시지를 삽입하고 가져오고 삭제하는 방법을 알아봅니다.&quot;" 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Python에서 큐 저장소를 사용하는 방법

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 개요

이 가이드에서는 Azure 큐 저장소 서비스를 사용하여 일반 시나리오를 수행하는 방법을 설명합니다. 샘플은 Python으로 작성되었으며 [Python Azure 패키지][]를 사용합니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**와 **큐 만들기 및 삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계 ]섹션을 참조하세요.

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]


> [AZURE.NOTE]Python 또는 [Python Azure 패키지][]를 설치해야 하는 경우 [Python 설치 가이드](../python-how-to-install.md)를 참조하세요.

## 큐를 만드는 방법

**QueueService** 개체를 사용하면 큐로 작업할 수 있습니다. 다음 코드는 **QueueService** 개체를 만듭니다. 프로그래밍 방식으로 Azure 저장소에 액세스하려는 Python 파일의 맨 위쪽에 다음을 추가합니다.

	from azure.storage import QueueService

다음 코드는 저장소 계정 이름 및 계정 키를 사용하는 **QueueService** 개체를 만듭니다. 'myaccount' 및 'mykey'를 실제 계정 및 키로 바꾸세요.

	queue_service = QueueService(account_name='myaccount', account_key='mykey')

	queue_service.create_queue('taskqueue')


## 큐에 메시지를 삽입하는 방법

큐에 메시지를 삽입하려면 **put_message** 메서드를 사용하여 새 메시지를 만들고 큐에 추가합니다.

	queue_service.put_message('taskqueue', 'Hello World')


## 다음 메시지를 보는 방법

큐에서 메시지를 제거하지 않고도 **peek_messages** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다. 기본적으로 **peek_messages**는 단일 메시지를 봅니다.

	messages = queue_service.peek_messages('taskqueue')
	for message in messages:
		print(message.message_text)


## 큐에서 다음 메시지를 제거하는 방법

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **get_messages**를 호출하면 기본적으로 큐에서 다음 메시지를 가져올 수 있습니다. **get_messages**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **delete_message**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **delete_message**를 호출합니다.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)


## 대기 중인 메시지의 콘텐츠 변경 방법

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 아래 코드는 **update_message** 메서드를 사용하여 메시지를 업데이트합니다.

	messages = queue_service.get_messages('taskqueue')
	for message in messages:
		queue_service.update_message('taskqueue', message.message_id, 'Hello World Again', message.pop_receipt, 0)

## dequeuing 메시지의 추가옵션을 설정하는 방법

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **get_messages** 메서드를 사용하여 한 번 호출에 16개의 메시지를 가져옵니다. 그런 다음에 for 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

	messages = queue_service.get_messages('taskqueue', numofmessages=16, visibilitytimeout=5*60)
	for message in messages:
		print(message.message_text)
		queue_service.delete_message('taskqueue', message.message_id, message.pop_receipt)

## 방법: 큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **get_queue_metadata** 메서드는 큐에 대한 메타데이터를 반환하도록 큐 서비스에 요청하고 **x-ms-approximate-messages-count**는 개수를 찾기 위해 반환된 사전에 대한 인덱스로 사용되어야 합니다. 큐 서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 결과는 근사치일 뿐입니다.

	queue_metadata = queue_service.get_queue_metadata('taskqueue')
	count = queue_metadata['x-ms-approximate-messages-count']

## 방법: 큐 삭제

큐 및 해당 큐의 모든 메시지를 삭제하려면 **delete_queue** 메서드를 호출합니다.

	queue_service.delete_queue('taskqueue')

## 다음 단계

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업에 대해 알아보세요.

-   다음 MSDN 참조를 확인하세요. [Azure에 데이터 저장 및 액세스][]
-   [Azure 저장소 팀 블로그][](영문)를 방문하세요.

[Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
[Python Azure 패키지]: https://pypi.python.org/pypi/azure
 

<!---HONumber=July15_HO2-->