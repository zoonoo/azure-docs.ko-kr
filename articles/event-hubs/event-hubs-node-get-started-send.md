---
title: 송신 및 Node.js-Azure Event Hubs를 사용 하 여 이벤트 수신 | Microsoft Docs
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
ms.openlocfilehash: e67be59e0ed78b2080986acb73a33fc87599c9d3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539340"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>이벤트를 보내거나 Node.js를 사용 하 여 Azure Event Hubs에서 이벤트 수신

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에는 이벤트를 보내거나 이벤트 허브에서 이벤트 수신에 Node.js 응용 프로그램을 만드는 방법을 설명 합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- Node.js 버전 8.x 이상. [https://nodejs.org](https://nodejs.org)에서 최신 LTS 버전을 다운로드하세요.
- Visual Studio Code(권장) 또는 다른 IDE
- **Event Hubs 네임 스페이스를 만들고 event hub**합니다. 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 계속 진행하세요. 그런 다음 문서에서 지침에 따라 이벤트 허브 네임 스페이스에 대 한 연결 문자열을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 해당 연결 문자열은 이 자습서의 뒷부분에서 사용합니다.


### <a name="install-npm-package"></a>Npm 패키지 설치
설치 하는 [Event Hubs에 대 한 npm 패키지](https://www.npmjs.com/package/@azure/event-hubs)에 있는 명령 프롬프트를 열고 `npm` 해당 경로에서 샘플 있고 후이 명령을 실행 하려는 폴더로 디렉터리를 변경

```shell
npm install @azure/event-hubs
```

설치 합니다 [이벤트 프로세서 호스트에 대 한 npm 패키지](https://www.npmjs.com/package/@azure/event-processor-host)실행는 아래 명령을 대신

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>이벤트 보내기

이 섹션에서는 이벤트 허브로 이벤트를 보내는 Node.js 응용 프로그램을 만드는 방법을 보여 줍니다. 

1. 와 같은 원하는 편집기를 엽니다 [Visual Studio Code](https://code.visualstudio.com)합니다. 
2. 라는 파일을 만듭니다 `send.js` 붙여넣습니다는 아래 코드를 넣습니다.
    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Define connection string and the name of the Event Hub
    const connectionString = "";
    const eventHubsName = "";

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
3. 위의 코드에서 연결 문자열 및 이벤트 허브의 이름 입력
4. 다음 명령을 실행 `node send.js` 이 파일을 실행 하려면 명령 프롬프트에서. 100 개의 이벤트를 이벤트 허브로 전송

축하합니다! 이제 이벤트 허브로 이벤트에 보냈습니다.


## <a name="receive-events"></a>이벤트 수신

이 섹션에서는 이벤트 허브에서 기본 소비자 그룹의 단일 파티션에서 이벤트를 수신 하는 Node.js 응용 프로그램을 만드는 방법을 보여 줍니다. 

1. 와 같은 원하는 편집기를 엽니다 [Visual Studio Code](https://code.visualstudio.com)합니다. 
2. 라는 파일을 만듭니다 `receive.js` 붙여넣습니다는 아래 코드를 넣습니다.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Define connection string and related Event Hubs entity name here
    const connectionString = "";
    const eventHubsName = "";

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
4. 다음 명령을 실행 `node receive.js` 이 파일을 실행 하려면 명령 프롬프트에서. 이 Event Hub에서 기본 소비자 그룹의 파티션 중 하나에서 이벤트를 수신 합니다.

축하합니다! 이제 이벤트 허브에서 이벤트를 받았습니다.

## <a name="receive-events-using-event-processor-host"></a>이벤트 프로세서 호스트를 사용 하 여 이벤트 수신

이 섹션에서는 Azure를 사용 하 여 이벤트 허브에서 이벤트를 수신 하는 방법을 보여 줍니다 [EventProcessorHost](event-hubs-event-processor-host.md) Node.js 응용 프로그램에서입니다. EPH(EventProcessorHost)를 사용하면 이벤트 허브의 소비자 그룹 내 모든 파티션에서 수신기를 만들어 이벤트 허브에서 이벤트를 효율적으로 수신할 수 있습니다. EPH는 Azure Storage Blob에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

1. 와 같은 원하는 편집기를 엽니다 [Visual Studio Code](https://code.visualstudio.com)합니다. 
2. 라는 파일을 만듭니다 `receiveAll.js` 붙여넣습니다는 아래 코드를 넣습니다.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Define connection string and related Event Hubs entity name here
    const eventHubConnectionString = "";
    const eventHubName = "";
    const storageConnectionString = "";

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
3. Azure Blob Storage에 대 한 연결 문자열과 함께 위 코드에서 연결 문자열 및 이벤트 허브의 이름 입력
4. 다음 명령을 실행 `node receiveAll.js` 이 파일을 실행 하려면 명령 프롬프트에서.

축하합니다! 이제 이벤트 프로세서 호스트를 사용 하 여 이벤트 허브에서 이벤트를 받았습니다. 이 Event Hub에서 기본 소비자 그룹의 모든 파티션에서 이벤트를 수신 합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [기능 및 Azure Event Hubs의 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)
- 에 대 한 다른 Node.js 샘플을 확인해 [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) 하 고 [이벤트 프로세서 호스트](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) GitHub에서
