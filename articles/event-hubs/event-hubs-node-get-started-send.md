---
title: Node.js를 사용 하 여 이벤트 전송 및 수신-Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs에서 이벤트를 보내는 Node.js 애플리케이션을 만드는 연습을 제공합니다.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: 3bb222d3197ef37d56767300d71cc350d25a37bd
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984475"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js를 사용 하 여 Azure Event Hubs에서 이벤트를 보내거나 받습니다.

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 node.js 응용 프로그램을 만들어 이벤트 허브에서 이벤트를 보내거나 이벤트를 수신 하는 방법을 설명 합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- Node.js 버전 8.x 이상. [https://nodejs.org](https://nodejs.org)에서 최신 LTS 버전을 다운로드하세요.
- Visual Studio Code(권장) 또는 다른 IDE
- **Event Hubs 네임 스페이스 및 이벤트 허브를 만듭니다**. 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 계속 진행하세요. 그런 다음 문서의 지침에 따라 이벤트 허브 네임 스페이스에 대 한 연결 문자열을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 해당 연결 문자열은 이 자습서의 뒷부분에서 사용합니다.


### <a name="install-npm-package"></a>Npm 패키지 설치
[Event Hubs에 대 한 npm 패키지](https://www.npmjs.com/package/@azure/event-hubs)를 설치 하려면 해당 경로에가 `npm` 있는 명령 프롬프트를 열고 디렉터리를 예제가 포함 될 폴더로 변경한 후이 명령을 실행 합니다.

```shell
npm install @azure/event-hubs
```

[이벤트 프로세서 호스트에 대 한 npm 패키지](https://www.npmjs.com/package/@azure/event-processor-host)를 설치 하려면 아래 명령을 대신 실행 합니다.

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>이벤트 보내기

이 섹션에서는 이벤트 허브로 이벤트를 전송 하는 node.js 응용 프로그램을 만드는 방법을 보여 줍니다. 

1. [Visual Studio Code](https://code.visualstudio.com)와 같은 원하는 편집기를 엽니다. 
2. 이라는 `send.js` 파일을 만들고 아래 코드를 붙여넣습니다. 문서의 지침에 따라 이벤트 허브 네임스페이스에 대한 연결 문자열을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에서 연결 문자열 및 이벤트 허브의 이름을 입력 합니다.
4. 그런 다음 명령 프롬프트 `node send.js` 에서 명령을 실행 하 여이 파일을 실행 합니다. 이렇게 하면 100 이벤트가 이벤트 허브로 전송 됩니다.

축하합니다. 이제 이벤트 허브로 이벤트를 보냈습니다.


## <a name="receive-events"></a>이벤트 수신

이 섹션에서는 이벤트 허브에 있는 기본 소비자 그룹의 단일 파티션에서 이벤트를 수신 하는 node.js 응용 프로그램을 만드는 방법을 보여 줍니다. 

1. [Visual Studio Code](https://code.visualstudio.com)와 같은 원하는 편집기를 엽니다. 
2. 이라는 `receive.js` 파일을 만들고 아래 코드를 붙여넣습니다.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에서 연결 문자열 및 이벤트 허브의 이름을 입력 합니다.
4. 그런 다음 명령 프롬프트 `node receive.js` 에서 명령을 실행 하 여이 파일을 실행 합니다. 그러면 이벤트 허브에 있는 기본 소비자 그룹의 파티션 중 하나에서 이벤트가 수신 됩니다.

축하합니다. 이제 이벤트 허브에서 이벤트를 받았습니다.

## <a name="receive-events-using-event-processor-host"></a>이벤트 프로세서 호스트를 사용하여 이벤트 수신

이 섹션에서는 node.js 응용 프로그램에서 Azure [EventProcessorHost](event-hubs-event-processor-host.md) 를 사용 하 여 이벤트 허브에서 이벤트를 수신 하는 방법을 보여 줍니다. EPH(EventProcessorHost)를 사용하면 이벤트 허브의 소비자 그룹 내 모든 파티션에서 수신기를 만들어 이벤트 허브에서 이벤트를 효율적으로 수신할 수 있습니다. EPH는 Azure Storage Blob에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

1. [Visual Studio Code](https://code.visualstudio.com)와 같은 원하는 편집기를 엽니다. 
2. 이라는 `receiveAll.js` 파일을 만들고 아래 코드를 붙여넣습니다.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Azure Blob Storage에 대 한 연결 문자열과 함께 위의 코드에 있는 이벤트 허브의 연결 문자열 및 이름을 입력 합니다.
4. 그런 다음 명령 프롬프트 `node receiveAll.js` 에서 명령을 실행 하 여이 파일을 실행 합니다.

축하합니다. 이제 이벤트 프로세서 호스트를 사용 하 여 이벤트 허브에서 이벤트를 받았습니다. 그러면 이벤트 허브에 있는 기본 소비자 그룹의 모든 파티션에서 이벤트가 수신 됩니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs의 기능 및 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)
- GitHub의 [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) 및 [이벤트 프로세서 호스트](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) 에 대 한 다른 node.js 샘플을 확인 하세요.
