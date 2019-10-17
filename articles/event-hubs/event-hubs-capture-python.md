---
title: Python 앱에서 캡처한 데이터 읽기 - Azure Event Hubs | Microsoft Docs
description: Azure Python SDK를 사용 하 여 Event Hubs 캡처 기능을 보여 주는 스크립트
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 10/10/2019
ms.author: shvija
ms.openlocfilehash: 354964e1b66b55dcccd9b5674f011f8c5a38a1c5
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428939"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs 캡처 연습: Python

캡처는 Azure Event Hubs의 기능입니다. 캡처를 사용 하 여 이벤트 허브의 스트리밍 데이터를 선택한 Azure Blob 저장소 계정에 자동으로 전달할 수 있습니다. 이 기능을 사용 하면 실시간 스트리밍 데이터에 대 한 일괄 처리를 쉽게 수행할 수 있습니다. 이 문서에서는 Python과 함께 Event Hubs 캡처를 사용하는 방법을 설명합니다. Event Hubs 캡처에 대 한 자세한 내용은 [Azure Event Hubs를 통해 이벤트 캡처][Overview of Event Hubs Capture]를 참조 하세요.

이 연습에서는 [Azure PYTHON SDK](https://azure.microsoft.com/develop/python/) 를 사용 하 여 캡처 기능을 보여 줍니다. *Sender.py* 프로그램은 시뮬레이션 된 환경 원격 분석을 JSON 형식의 Event Hubs으로 보냅니다. 이벤트 허브는 캡처 기능을 사용 하 여이 데이터를 Blob storage에 일괄적으로 씁니다. *Capturereader.py* 앱은 이러한 blob을 읽고 각 장치에 대 한 추가 파일을 만들며 각 장치에서 *.csv* 파일에 데이터를 씁니다.

이 연습에서는 다음을 수행합니다. 

> [!div class="checklist"]
> * Azure Portal에서 Azure Blob storage 계정 및 컨테이너를 만듭니다.
> * Event Hubs 캡처를 사용 하도록 설정 하 고 저장소 계정에 직접 전달 합니다.
> * Python 스크립트를 사용 하 여 이벤트 허브로 데이터를 보냅니다.
> * 다른 Python 스크립트를 사용 하 여 Event Hubs 캡처에서 파일을 읽고 처리 합니다.

## <a name="prerequisites"></a>전제 조건

- @No__t-0이 설치 되 고 업데이트 된 Python 3.4 이상
  
- Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
  
- [빠른 시작: Azure Portal를 사용 하 여 이벤트 허브 만들기](event-hubs-create.md)의 지침에 따라 만든 활성 Event Hubs 네임 스페이스 및 이벤트 허브 이 연습의 뒷부분에서 사용할 네임 스페이스 및 이벤트 허브 이름을 적어 둡니다. 
  
  > [!NOTE]
  > 사용할 저장소 컨테이너가 이미 있는 경우 이벤트 허브를 만들 때 캡처를 사용 하도록 설정 하 고 저장소 컨테이너를 선택할 수 있습니다. 
  > 
  
- Event Hubs 공유 액세스 키 이름 및 기본 키 값입니다. Event Hubs 페이지의 **공유 액세스 정책** 에서 이러한 값을 찾거나 만듭니다. 기본 액세스 키 이름은 **RootManageSharedAccessKey**입니다. 이 연습의 뒷부분에서 사용할 액세스 키 이름 및 기본 키 값을 복사 합니다. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Azure Blob storage 계정 및 컨테이너 만들기

캡처에 사용할 저장소 계정 및 컨테이너를 만듭니다. 

1. [Azure portal][Azure portal]에 로그인합니다.
2. 왼쪽 탐색 영역에서 **저장소 계정**을 선택 하 고 **저장소 계정** 화면에서 **추가**를 선택 합니다.
3. 저장소 계정 만들기 화면에서 구독 및 리소스 그룹을 선택 하 고 저장소 계정에 이름을 지정 합니다. 다른 선택 항목은 기본적으로 그대로 둘 수 있습니다. **검토 + 만들기**를 선택 하 고 설정을 검토 한 후 **만들기**를 선택 합니다. 
   
   ![스토리지 계정 만들기][1]
   
4. 배포가 완료 되 면 **리소스로 이동**을 선택 하 고 저장소 계정 **개요** 화면에서 **컨테이너**를 선택 합니다.
5. **컨테이너** 화면에서 **+ 컨테이너**를 선택 합니다. 
6. **새 컨테이너** 화면에서 컨테이너에 이름을 지정 하 고 **확인**을 선택 합니다. 연습의 뒷부분에서 사용할 컨테이너 이름을 적어 둡니다. 
7. **컨테이너** 화면의 왼쪽 탐색 영역에서 **액세스 키**를 선택 합니다. **저장소 계정 이름**및 **key1**의 **키** 값을 복사 하 여 나중에 연습에서 사용 합니다.
 
## <a name="enable-event-hubs-capture"></a>Event Hubs 캡처 사용

1. Azure Portal에서 **모든 리소스**에서 Event Hubs 네임 스페이스를 선택 하 고 왼쪽 탐색에서 **event Hubs** 를 선택한 다음 이벤트 허브를 선택 하 여 이벤트 허브로 이동 합니다. 
2. 이벤트 허브 **개요** 화면에서 **캡처 이벤트**를 선택 합니다.
3. **캡처** 화면에서 **켜기**를 선택 합니다. 그런 다음 **Azure Storage 컨테이너**에서 **컨테이너 선택**을 선택 합니다. 
4. **컨테이너** 화면에서 사용 하려는 저장소 컨테이너를 선택 하 고 **선택**을 선택 합니다. 
5. **캡처** 화면에서 **변경 내용 저장**을 선택 합니다. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>이벤트 허브로 이벤트를 보내는 Python 스크립트 만들기
이 스크립트는 이벤트 허브에 200개의 이벤트를 전송합니다. 이벤트는 JSON으로 전송 되는 간단한 환경 판독값입니다.

1. 선호하는 Python 편집기(예: [Visual Studio Code][Visual Studio Code])를 엽니다.
2. *Sender.py*라는 새 파일을 만듭니다. 
3. *Sender.py*에 다음 코드를 붙여 넣습니다. Event Hubs \<namespace >, \<AccessKeyName >, \<primary key 값 >, \<eventhub >에 대 한 고유한 값을 대체 합니다.
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. 파일을 저장합니다.

## <a name="create-a-python-script-to-read-capture-files"></a>캡처 파일을 읽는 Python 스크립트 만들기

이 스크립트는 캡처된 파일을 읽고 각 장치에 대 한 파일을 만들어 해당 장치에 대해서만 데이터를 작성 합니다.

1. Python 편집기에서 *capturereader.py*라는 새 파일을 만듭니다. 
2. *Capturereader.py*에 다음 코드를 붙여 넣습니다. @No__t-0storageaccount >, \<storage 계정 액세스 키 > 및 @no__t 2storagecontainer >의 저장 된 값을 대체 합니다.
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Python 스크립트 실행

1. 해당 경로에 Python이 있는 명령 프롬프트를 열고 다음 명령을 실행 하 여 Python 필수 구성 요소 패키지를 설치 합니다.
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   @No__t-0 또는 `azure`의 이전 버전이 있는 경우 `--upgrade` 옵션을 사용 해야 할 수 있습니다.
   
   다음 명령을 실행 해야 할 수도 있습니다. 이 명령을 실행 하는 것은 대부분의 시스템에는 필요 하지 않습니다. 
   
   ```cmd
   pip install cryptography
   ```
   
2. *Sender.py* 및 *capturereader.py*를 저장 한 디렉터리에서 다음 명령을 실행 합니다.
   
   ```cmd
   start python sender.py
   ```
   
   명령은 발신자를 실행 하는 새 Python 프로세스를 시작 합니다.
   
3. 캡처 실행이 완료 되 면 다음 명령을 실행 합니다.
   
   ```cmd
   python capturereader.py
   ```

   캡처 프로세서는 저장소 계정 컨테이너에서 비어 있지 않은 모든 blob을 다운로드 하 고 로컬 디렉터리에 *.csv* 파일로 결과를 작성 합니다. 

## <a name="next-steps"></a>다음 단계

Event Hubs에 대 한 자세한 내용은 다음을 참조 하세요. 

* [Event Hubs 캡처 개요][Overview of Event Hubs Capture]
* [Event Hubs를 사용하는 샘플 애플리케이션](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Event Hubs 개요][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
