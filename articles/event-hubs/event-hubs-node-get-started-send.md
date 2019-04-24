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
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203230"
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
- 복제는 [샘플 GitHub 리포지토리](https://github.com/Azure/azure-event-hubs-node) 컴퓨터에 있습니다. 


## <a name="send-events"></a>이벤트 보내기
이 섹션에서는 이벤트 허브로 이벤트를 보내는 Node.js 응용 프로그램을 만드는 방법을 보여 줍니다. 

### <a name="install-nodejs-package"></a>Node.js 패키지 설치
컴퓨터에 Azure Event Hubs용 Node.js 패키지를 설치합니다. 

```shell
npm install @azure/event-hubs
```

필수 구성 요소에서 설명한 대로 Git 리포지토리를 복제 하지 않은 경우 다운로드 합니다 [샘플](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) GitHub에서. 

복제한 SDK에는 Node.js를 사용하여 이벤트 허브로 이벤트를 전송하는 방법을 보여 주는 여러 샘플이 포함되어 있습니다. 이 빠른 시작에서는 **simpleSender.js** 예제를 사용합니다. 수신되는 이벤트를 살펴보려면 다른 터미널을 열고 [receive sample](event-hubs-node-get-started-receive.md)을 사용하여 이벤트를 수신합니다.

1. Visual Studio Code에서 프로젝트를 엽니다. 
2. **client** 폴더에 **.env**라는 파일을 만듭니다. 루트 폴더의 **sample.env**에서 샘플 환경 변수를 복사하여 붙여넣습니다.
3. 이벤트 허브 연결 문자열, 이벤트 허브 이름 및 저장소 엔드포인트를 구성합니다. 이벤트 허브에 대한 연결 문자열을 가져오는 방법에 대한 자세한 내용은 [연결 문자열 가져오기](event-hubs-create.md#create-an-event-hubs-namespace)를 참조하세요.
4. Azure CLI에서 **client** 폴더 경로로 이동합니다. 다음 명령을 실행하여 node 패키지를 설치하고 프로젝트를 빌드합니다.

    ```shell
    npm i
    npm run build
    ```
5. 다음 명령을 실행하여 이벤트 전송을 시작합니다. 

    ```shell
    node dist/examples/simpleSender.js
    ```


### <a name="review-the-sample-code"></a>샘플 코드 검토 
이벤트 허브로 이벤트를 보내려면 simpleSender.js 파일의 샘플 코드를 검토합니다.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

스크립트를 실행하기 전에 환경 변수를 설정하세요. 다음 예제와 같이 명령줄에서 환경 변수를 구성할 수도 있고, [dotenv package](https://www.npmjs.com/package/dotenv#dotenv)를 사용할 수도 있습니다. 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="receive-events"></a>이벤트 수신
이 자습서에서는 Node.js 애플리케이션에서 Azure [EventProcessorHost](event-hubs-event-processor-host.md)를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 설명합니다. EPH(EventProcessorHost)를 사용하면 이벤트 허브의 소비자 그룹 내 모든 파티션에서 수신기를 만들어 이벤트 허브에서 이벤트를 효율적으로 수신할 수 있습니다. EPH는 Azure Storage Blob에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

이 빠른 시작용 코드는 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor)에서 제공됩니다.

### <a name="clone-the-git-repository"></a>Git 리포지토리 복제
GitHub에서 [샘플](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)을 다운로드 또는 복제합니다. 

### <a name="install-the-eventprocessorhost"></a>EventProcessorHost 설치
Event Hubs 모듈용 EventProcessorHost를 설치합니다. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>EventProcessorHost를 사용하여 이벤트 수신
복제한 SDK에는 Node.js를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 보여 주는 여러 샘플이 포함되어 있습니다. 이 빠른 시작에서는 **singleEPH.js** 예제를 사용합니다. 수신되는 이벤트를 살펴보려면 다른 터미널을 열고 [send sample](event-hubs-node-get-started-send.md)을 사용하여 이벤트를 전송합니다.

1. Visual Studio Code에서 프로젝트를 엽니다. 
2. **processor** 폴더에 **.env** 파일을 만듭니다. 루트 폴더의 **sample.env**에서 샘플 환경 변수를 복사하여 붙여넣습니다.
3. 이벤트 허브 연결 문자열, 이벤트 허브 이름 및 저장소 엔드포인트를 구성합니다. Azure Portal 이벤트 허브 페이지의 **RootManageSharedAccessKey** 아래 **연결 문자열-기본** 키에서 이벤트 허브의 연결 문자열을 복사할 수 있습니다. 자세한 단계는 [연결 문자열 가져오기](event-hubs-create.md#create-an-event-hubs-namespace)를 참조하세요.
4. Azure CLI에서 **processor** 폴더 경로로 이동합니다. 다음 명령을 실행하여 node 패키지를 설치하고 프로젝트를 빌드합니다.

    ```shell
    npm i
    npm run build
    ```
5. 다음 명령을 실행하여 이벤트 프로세서 호스트를 사용해 이벤트를 수신합니다.

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>샘플 코드 검토 
node.js를 사용하여 이벤트 허브에서 이벤트를 수신하는 샘플 코드는 다음과 같습니다. sampleEph.js 파일을 수동으로 만든 다음 실행하면 이벤트 허브로 이벤트를 수신할 수 있습니다. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

스크립트를 실행하기 전에 환경 변수를 설정하세요. 다음 예제에 나와 있는 것처럼 명령줄에서 환경 변수를 구성할 수도 있고 [dotenv package](https://www.npmjs.com/package/dotenv#dotenv)를 사용할 수도 있습니다. 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

[여기](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)서 더 많은 샘플을 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계
다음 문서를 읽어보세요.

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [기능 및 Azure Event Hubs의 용어](event-hubs-features.md)
- [Event Hubs FAQ](event-hubs-faq.md)
- [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)에서 Event Hubs에 대한 다른 Node.js 샘플을 확인합니다.
