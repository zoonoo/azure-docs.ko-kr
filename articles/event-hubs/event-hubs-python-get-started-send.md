---
title: Python을 사용 하 여 이벤트 보내기 또는 받기-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에 이벤트를 전송 하는 Python 응용 프로그램을 만들기 위한 연습을 제공 합니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/16/2019
ms.author: shvija
ms.openlocfilehash: 5162c6359c4b6e6bdd53d2778ca247704e2f16be
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059137"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Python을 사용 하 여 Event Hubs에서 이벤트 보내기 또는 받기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 이벤트 허브에서 이벤트를 보내거나 받는 Python 응용 프로그램을 만드는 방법을 설명 합니다. 

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
- Python 3.4 이상
- [Azure Portal](https://portal.azure.com) 를 사용 하 여 Event Hubs 형식의 네임 스페이스를 만들고 응용 프로그램이 이벤트 허브와 통신 하는 데 필요한 관리 자격 증명을 가져옵니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그런 다음 문서의 지침에 따라 이벤트 허브에 대 한 액세스 키의 값을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 액세스 키는 이 자습서의 뒷부분에서 작성하는 코드에 사용합니다. 기본 키 이름은 **RootManageSharedAccessKey**입니다.

## <a name="install-python-package"></a>Python 패키지 설치

Event Hubs용 Python 패키지를 설치하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다. 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>이벤트 보내기

> [!NOTE]
> 이 섹션의 코드는 Event Hubs SDK의 현재 안정적인 버전 (1.3.1)에 대 한 것입니다. SDK의 미리 보기 버전을 사용 하는 샘플 코드를 찾고 있는 경우 [이 페이지](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples)를 참조 하세요.

### <a name="create-a-python-script-to-send-events"></a>이벤트를 보내는 Python 스크립트 만들기

다음으로, 이벤트 허브로 이벤트를 보내는 Python 애플리케이션을 만듭니다.

1. 즐겨 사용 하는 Python 편집기 (예: [Visual Studio Code](https://code.visualstudio.com/) )를 엽니다.
2. **send.py**라는 스크립트를 만듭니다. 이 스크립트는 이벤트 허브에 100개의 이벤트를 전송합니다.
3. send.py에 다음 코드를 붙여넣습니다. 여기서 ADDRESS, USER 및 KEY 값을 이전 섹션의 Azure Portal에서 얻은 값으로 바꿉니다. 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://<EVENTHUBS NAMESPACE NAME>.servicebus.windows.net/<EVENTHUB NAME>"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "<SHARED ACCESS KEY FOR THE EVENT HUBS NAMESPACE>"

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

### <a name="run-application-to-send-events"></a>애플리케이션을 실행하여 이벤트 전송

이 스크립트를 실행하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다.

```bash
start python send.py
```

지금까지 이제 Event Hub에 메시지를 보냈습니다.

## <a name="receive-events"></a>이벤트 수신

### <a name="create-a-python-script-to-receive-events"></a>이벤트를 수신하는 Python 스크립트 만들기

다음으로, 이벤트 허브에서 이벤트를 수신하는 Python 애플리케이션을 만듭니다.

1. 즐겨 사용 하는 Python 편집기 (예: [Visual Studio Code](https://code.visualstudio.com/) )를 엽니다.
2. **recv.py**라는 스크립트를 만듭니다.
3. recv.py에 다음 코드를 붙여넣습니다. 여기서 ADDRESS, USER 및 KEY 값을 이전 섹션의 Azure Portal에서 얻은 값으로 바꿉니다. 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://<EVENTHUBS NAMESPACE NAME>.servicebus.windows.net/<EVENTHUB NAME>"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "<SHARED ACCESS KEY FOR THE EVENT HUBS NAMESPACE>"

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

### <a name="receive-events"></a>이벤트 수신

이 스크립트를 실행하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다.

```bash
start python recv.py
```
 
## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)

