---
title: Python을 사용하여 메시지 보내기 및 받기 - Azure Event Hubs | Microsoft Docs
description: Python을 사용하여 Event Hubs로 이벤트를 보내고, Event Hubs에서 이벤트를 받고, Event Hubs를 통해 이벤트 스트리밍을 캡처하는 방법을 알아봅니다.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 4a0e2cd7e0c768512e1aafc042fe55338fdc206e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084977"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Python 애플리케이션에서 Azure Event Hubs를 사용하는 방법
Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 장치에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. 자세한 내용은 [Event Hubs 소개](event-hubs-what-is-event-hubs.md)를 참조하세요. 

이 문서에서는 **Python**으로 작성된 애플리케이션에서 다음 작업을 수행하는 방법을 보여주는 문서의 링크를 제공합니다.

- [이벤트 허브로 이벤트 보내기](#send-events-to-event-hubs)
- [이벤트 허브에서 이벤트 받기](#receive-events-from-event-hubs)
- [Azure Storage에서 캡처한 이벤트 데이터 읽기](#capture-event-hubs-data) 

## <a name="prerequisites"></a>필수 조건
- 다음 빠른 시작 중 하나를 수행하여 이벤트 허브를 만듭니다. [Azure Portal](event-hubs-create.md), [Azure CLI](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [Azure Resource Manager 템플릿](event-hubs-resource-manager-namespace-event-hub.md). 
- 머신에 Python 3.4 이상이 설치되어 있어야 합니다.

## <a name="install-python-package"></a>Python 패키지 설치

Event Hubs용 Python 패키지를 설치하려면 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행합니다. 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Event Hubs에 이벤트 보내기
다음 코드는 Python 애플리케이션에서 이벤트 허브로 이벤트를 보내는 방법을 보여줍니다. 

1. 이벤트 허브, 키 이름 및 키 값의 URL을 보관할 변수를 만듭니다. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Event Hubs 클라이언트를 만들고, 보낸 사람을 추가하고, 클라이언트를 실행하고, 보낸 사람을 사용하여 이벤트를 보내고, 작업을 마쳤으면 클라이언트를 중지합니다. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Python으로 작성된 애플리케이션에서 이벤트 허브로 이벤트를 보내는 방법에 대한 완전한 자습서는 [이 문서](event-hubs-python-get-started-send.md)를 참조하세요.

## <a name="receive-events-from-event-hubs"></a>Event Hubs에서 이벤트 받기
다음 코드는 Python 애플리케이션에서 이벤트 허브로부터 이벤트를 받는 방법을 보여줍니다. 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Python으로 작성된 애플리케이션에서 이벤트 허브로부터 이벤트를 받는 방법에 대한 완전한 자습서는 [이 문서](event-hubs-python-get-started-receive.md)를 참조하세요.

## <a name="read-capture-event-data-from-azure-storage"></a>Azure Storage에서 캡처한 이벤트 데이터 읽기
다음 코드는 Python 애플리케이션의 **Azure Blob 스토리지**에 저장되는 캡처된 이벤트 데이터를 읽는 방법을 보여줍니다. 다음 지침을 따라 이벤트 허브에 **캡처** 기능을 사용하도록 설정합니다. [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](event-hubs-capture-enable-through-portal.md) 그런 다음, 코드를 테스트하기 전에 일부 이벤트를 이벤트 허브로 보냅니다. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

Python으로 작성된 애플리케이션에서 Azure Blob 스토리지에 캡처된 Event Hubs 데이터를 읽는 방법에 대한 완전한 자습서는 [이 문서](event-hubs-capture-python.md)를 참조하세요.

## <a name="github-samples"></a>GitHub 샘플
[azure-event-hubs-python Git 리포지토리](https://github.com/Azure/azure-event-hubs-python/)에서 더 많은 Python 샘플을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계
[개념] 섹션에서 [Event Hubs 기능 개요](event-hubs-features.md)부터 시작하여 문서를 끝까지 읽어보세요.