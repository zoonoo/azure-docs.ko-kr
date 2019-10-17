---
title: Python을 사용 하 여 이벤트 전송 및 수신-Azure Event Hubs
description: 이 연습에서는 Azure Event Hubs에 이벤트를 보내거나 이벤트를 수신 하는 Python 스크립트를 만들고 실행 하는 방법을 보여 줍니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428890"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Python을 사용 하 여 Event Hubs와 함께 이벤트 전송 및 수신

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산 소프트웨어 및 장치에서 이벤트, 데이터 또는 원격 분석을 처리 하 고 저장할 수 있습니다. 이벤트 허브로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대 한 자세한 내용은 azure Event Hubs에서 [azure Event Hubs](event-hubs-about.md) 및 [기능 및 용어](event-hubs-features.md)를 참조 하세요.

이 빠른 시작에서는 이벤트 허브에서 이벤트를 보내고 받는 Python 응용 프로그램을 만드는 방법을 보여 줍니다. 

> [!NOTE]
> 퀵 스타트를 사용 하는 대신 GitHub에서 [샘플 앱](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) 을 다운로드 하 고 실행할 수 있습니다. @No__t-0 및 `EventHubName` 문자열을 이벤트 허브 값으로 바꿉니다. 

## <a name="prerequisites"></a>전제 조건

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- [빠른 시작: Azure Portal를 사용 하 여 이벤트 허브 만들기](event-hubs-create.md)의 지침에 따라 만든 활성 Event Hubs 네임 스페이스 및 이벤트 허브 이 연습의 뒷부분에서 사용할 네임 스페이스 및 이벤트 허브 이름을 적어 둡니다. 
- Event Hubs 네임 스페이스에 대 한 공유 액세스 키 이름 및 기본 키 값입니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)의 지침에 따라 액세스 키 이름과 값을 가져옵니다. 기본 액세스 키 이름은 **RootManageSharedAccessKey**입니다. 이 연습의 뒷부분에서 사용할 키 이름 및 기본 키 값을 복사 합니다. 
- @No__t-0이 설치 되 고 업데이트 된 Python 3.4 이상
- Event Hubs에 대 한 Python 패키지입니다. 패키지를 설치 하려면 해당 경로에 Python이 있는 명령 프롬프트에서 다음 명령을 실행 합니다. 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > 이 빠른 시작의 코드는 Event Hubs SDK의 현재 안정적인 버전 1.3.1을 사용 합니다. SDK의 미리 보기 버전을 사용 하는 샘플 코드는 [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples)을 참조 하세요.

## <a name="send-events"></a>이벤트 보내기

이벤트 허브로 이벤트를 보내는 Python 응용 프로그램을 만들려면 다음을 수행 합니다.

1. 즐겨 사용 하는 Python 편집기 (예: [Visual Studio Code](https://code.visualstudio.com/) )를 엽니다.
2. *Send.py*라는 새 파일을 만듭니다. 이 스크립트는 이벤트 허브에 100개의 이벤트를 전송합니다.
3. *Send.py*에 다음 코드를 붙여 넣어 Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > 및 \< 기본 키 값 >을 값으로 바꿉니다. 
   
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

스크립트를 실행 하려면 *send.py*를 저장 한 디렉터리에서 다음 명령을 실행 합니다.

```cmd
start python send.py
```

축하합니다. 이제 Event Hub에 메시지를 보냈습니다.

## <a name="receive-events"></a>이벤트 수신

이벤트 허브에서 이벤트를 수신 하는 Python 응용 프로그램을 만들려면 다음을 수행 합니다.

1. Python 편집기에서 *recv.py*라는 파일을 만듭니다.
2. *Recv.py*에 다음 코드를 붙여 넣어 Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > 및 \< 기본 키 값 >을 값으로 바꿉니다. 
   
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

스크립트를 실행 하려면 *recv.py*를 저장 한 디렉터리에서 다음 명령을 실행 합니다.

```cmd
start python recv.py
```

## <a name="next-steps"></a>다음 단계
Event Hubs에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)

