---
title: Python(이전)을 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 연습에서는 이전 azure-eventhub 버전 1 패키지를 사용하여 이벤트를 Azure Event Hubs로 보내거나 받는 Python 스크립트를 만들고 실행하는 방법을 보여줍니다.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162602"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>빠른 시작: Python(azure-eventhub 버전 1)을 사용하여 Event Hubs와 이벤트 주고 받기
이 빠른 시작에서는 **azure-eventhubs 버전 1** Python 패키지를 사용하여 이벤트 허브와 이벤트를 주고 받는 방법을 보여줍니다. 

> [!WARNING]
> 이 빠른 시작에서는 이전 azure-eventhub 버전 1 패키지를 사용합니다. 패키지의 최신 **버전 5**를 사용하는 빠른 시작은 [azure-eventhub 버전 5를 사용하여 이벤트 보내기 및 받기](get-started-python-send-v2.md)를 참조하세요. 이전 패키지를 사용하던 애플리케이션을 새 패키지로 이동하려면 [azure-eventhub 버전 1에서 버전 5로 마이그레이션하기 위한 가이드](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md)를 참조하세요.
 

## <a name="prerequisites"></a>사전 요구 사항
Azure Event Hubs를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Event Hubs 개요](event-hubs-about.md)를 참조하세요. 

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- **Microsoft Azure 구독**. Azure Event Hubs를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다.  기존 Azure 계정이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입하거나 [계정을 만들 때](https://azure.microsoft.com) MSDN 구독자 혜택을 사용할 수 있습니다.
- `pip`가 설치 및 업데이트된 Python 3.4 이상
- Event Hubs용 Python 패키지. 이 패키지를 설치하려면 경로에 Python이 있는 명령 프롬프트에서 다음 명령을 실행합니다. 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그런 다음, 다음 문서의 지침에 따라 이벤트 허브에 대한 액세스 키의 값을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 액세스 키는 이 빠른 시작의 뒷부분에서 작성하는 코드에 사용합니다. 기본 키 이름은 **RootManageSharedAccessKey**입니다. 


## <a name="send-events"></a>이벤트 보내기

이벤트 허브로 이벤트를 보내는 Python 애플리케이션을 만드는 방법은 다음과 같습니다.

> [!NOTE]
> 이 빠른 시작을 진행하는 대신, GitHub에서 [샘플 앱](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)을 다운로드하여 실행해도 됩니다. 이 경우 `EventHubConnectionString` 및 `EventHubName` 문자열을 해당 이벤트 허브 값으로 바꿔야 합니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. *send.py*라는 새 파일을 만듭니다. 이 스크립트는 이벤트 허브에 100개의 이벤트를 전송합니다.
3. 다음 코드를 *send.py* 파일에 붙여넣고, Event Hubs \<네임스페이스>, \<eventhub>, \<AccessKeyName> 및 \<기본 키 값>을 사용자 고유의 값으로 바꿉니다. 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. 파일을 저장합니다. 

스크립트를 실행하려면 *send.py* 파일을 저장한 디렉터리에서 다음 명령을 실행합니다.

```cmd
start python send.py
```

축하합니다! 이제 Event Hub에 메시지를 보냈습니다.

## <a name="receive-events"></a>이벤트 수신

이벤트 허브에서 이벤트를 수신하는 Python 애플리케이션을 만드는 방법은 다음과 같습니다.

1. Python 편집기에서 *recv.py*라는 파일을 만듭니다.
2. 다음 코드를 *recv.py* 파일에 붙여넣고, Event Hubs \<네임스페이스>, \<eventhub>, \<AccessKeyName> 및 \<기본 키 값>을 사용자 고유의 값으로 바꿉니다. 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. 파일을 저장합니다.

스크립트를 실행하려면 *recv.py* 파일을 저장한 디렉터리에서 다음 명령을 실행합니다.

```cmd
start python recv.py
```

## <a name="next-steps"></a>다음 단계
Event Hubs에 대한 자세한 내용은 다음 문서를 참조하세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)

