---
title: Python을 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: Python을 사용하여 Event Hubs로 이벤트 전송 시작
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703523"
---
# <a name="send-events-to-event-hubs-using-python"></a>Python을 사용하여 Event Hubs에 이벤트 보내기

Azure Event Hubs는 초당 수백만 개의 이벤트를 처리할 수 있는 확장성이 뛰어난 이벤트 관리 시스템으로, 연결된 장치와 기타 시스템에서 생성되는 대량의 데이터를 처리 및 분석할 수 있습니다. 이벤트 허브에 이벤트가 수집되면 In Process 처리기를 사용하여 또는 다른 분석 시스템으로 전달하여 이벤트를 받아서 처리할 수 있습니다.

Event Hubs에 대한 자세한 내용은 [Event Hubs 개요][Event Hubs overview]를 참조하세요.

이 자습서에서는 Python으로 작성된 응용 프로그램에서 이벤트 허브로 이벤트를 전송하는 방법을 설명합니다. 이벤트를 수신하려면 [해당 수신 문서](event-hubs-python-get-started-receive.md)를 참조하세요.

이 자습서의 코드는 [이 GitHub 샘플](https://github.com/Azure/azure-event-hubs-python/tree/master/examples)에서 가져온 것으로, 이 코드를 검사하면 가져온 명령문 및 변수 선언을 포함하여 온전하게 작동하는 응용 프로그램을 볼 수 있습니다. 다른 예제는 동일한 GitHub 폴더에 제공됩니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- Python 3.4 이상
- 기존 Event Hubs 네임스페이스 및 이벤트 허브 [이 문서의 지침](event-hubs-create.md)에 따라 이러한 엔터티를 만들 수 있습니다. 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Python 패키지 설치

Event Hubs용 Python 패키지를 설치하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다. 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>이벤트를 보내는 Python 스크립트 만들기

다음으로, 이벤트 허브로 이벤트를 보내는 Python 응용 프로그램을 만듭니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code][Visual Studio Code])를 엽니다.
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
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

## <a name="send-events"></a>이벤트 보내기

이 스크립트를 실행하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다.

```bash
start python send.py
```
 
## <a name="next-steps"></a>다음 단계

이제 Python을 사용하여 이벤트 허브로 이벤트를 보냈으므로 이벤트를 수신하려면 [해당 수신 문서](event-hubs-python-get-started-receive.md)를 참조하세요.

다음 페이지를 방문하여 Event Hubs에 대해 자세히 알아보세요.

* [Event Hubs 개요][Event Hubs overview]
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
