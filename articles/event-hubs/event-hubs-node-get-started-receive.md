---
title: Node.js를 사용하여 Azure Event Hubs에서 이벤트 수신 | Microsoft Docs
description: Node.js를 사용하여 Event Hubs에서 이벤트를 수신하는 방법에 대해 알아봅니다.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 6d5b52c8a5dd0306a349cac5e67eecc809005c6f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49429187"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Node.js를 사용하여 Azure Event Hubs에서 이벤트 수신

Azure Event Hubs는 초당 수백만 개의 이벤트를 처리할 수 있는 확장성이 뛰어난 이벤트 관리 시스템으로, 연결된 장치와 기타 시스템에서 생성되는 대량의 데이터를 처리 및 분석할 수 있습니다. 이벤트 허브에 이벤트가 수집되면 In Process 처리기를 사용하여 또는 다른 분석 시스템으로 전달하여 이벤트를 받아서 처리할 수 있습니다. 이벤트 데이터가 처리되기 전에 Azure Storage 또는 Azure Data Lake Store에서 캡처할 수도 있습니다.  

Event Hubs에 대해 자세히 알아보려면 [Event Hubs 개요](event-hubs-about.md)를 참조하세요.

이 자습서에서는 Node.js 응용 프로그램에서 Azure [EventProcessorHost](event-hubs-event-processor-host.md)를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 설명합니다. EPH(EventProcessorHost)를 사용하면 이벤트 허브의 소비자 그룹 내 모든 파티션에서 수신기를 만들어 이벤트 허브에서 이벤트를 효율적으로 수신할 수 있습니다. EPH는 Azure Storage Blob에서 일정한 간격으로 수신된 메시지의 메타데이터에 검사점을 적용합니다. 이러한 방식이 사용되므로 메시지 수신이 중지된 이후 중지된 시점부터 계속해서 쉽게 메시지를 수신할 수 있습니다.

이 빠른 시작용 코드는 [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor)에서 제공됩니다.

> [!NOTE]
>  Node.js를 사용하여 Event Hubs로 이벤트를 보내려면 [Node.js를 사용하여 Azure Event Hubs로 이벤트 전송](event-hubs-node-get-started-send.md) 문서를 참조하세요. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- Node.js 버전 8.x 이상. [https://nodejs.org](https://nodejs.org)에서 최신 LTS 버전을 다운로드하세요. node.js의 이전 LTS 버전을 사용하면 안 됩니다. 
- 활성 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][]을 만듭니다.

## <a name="create-a-namespace-and-event-hub"></a>네임스페이스 및 이벤트 허브 만들기
첫 단계에서는 Azure Portal을 사용하여 이벤트 허브가 포함된 Event Hubs 네임스페이스를 만듭니다. 기존 엔터티가 없으면 [Azure Portal을 사용하여 Events Hubs 네임스페이스 및 이벤트 허브 만들기](event-hubs-create.md)의 지침에 따라 해당 엔터티를 만들 수 있습니다.

## <a name="create-a-storage-account-and-container"></a>저장소 계정 및 컨테이너 만들기
EventProcessorHost를 사용하려면 Azure Storage 계정이 있어야 합니다. 이벤트 스트림의 검사점 및 파티션 임대와 같은 상태 정보는 Azure Storage 컨테이너를 사용하여 수신기 간에 공유됩니다. [이 문서](../storage/common/storage-quickstart-create-account.md)의 지침에 따라 Azure Storage 계정을 만들 수 있습니다.

## <a name="clone-the-git-repository"></a>Git 리포지토리 복제
GitHub에서 [샘플](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)을 다운로드 또는 복제합니다. 

## <a name="install-the-eventprocessorhost"></a>EventProcessorHost 설치
Event Hubs 모듈용 EventProcessorHost를 설치합니다. 

```nodejs
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>EventProcessorHost를 사용하여 이벤트 수신
복제한 SDK에는 Node.js를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 보여 주는 여러 샘플이 포함되어 있습니다. 이 빠른 시작에서는 **singleEPH.js** 예제를 사용합니다. 수신되는 이벤트를 살펴보려면 다른 터미널을 열고 [send sample](event-hubs-node-get-started-send.md)을 사용하여 이벤트를 전송합니다.

1. Visual Studio Code에서 프로젝트를 엽니다. 
2. **processor** 폴더에 **.env** 파일을 만듭니다. 루트 폴더의 **sample.env**에서 샘플 환경 변수를 복사하여 붙여넣습니다.
3. 이벤트 허브 연결 문자열, 이벤트 허브 이름 및 저장소 엔드포인트를 구성합니다. Azure Portal 이벤트 허브 페이지의 **RootManageSharedAccessKey** 아래 **연결 문자열-기본** 키에서 이벤트 허브의 연결 문자열을 복사할 수 있습니다. 자세한 단계는 [연결 문자열 가져오기](event-hubs-create.md#create-an-event-hubs-namespace)를 참조하세요.
4. Azure CLI에서 **processor** 폴더 경로로 이동합니다. 다음 명령을 실행하여 node 패키지를 설치하고 프로젝트를 빌드합니다.

    ```nodejs
    npm i
    npm run build
    ```
5. 다음 명령을 실행하여 이벤트 프로세서 호스트를 사용해 이벤트를 수신합니다.

    ```nodejs
    node dist/examples/singleEph.js
    ```

## <a name="review-the-sample-code"></a>샘플 코드 검토 
node.js를 사용하여 이벤트 허브에서 이벤트를 수신하는 샘플 코드는 다음과 같습니다. sampleEph.js 파일을 수동으로 만든 다음 실행하면 이벤트 허브로 이벤트를 수신할 수 있습니다. 

  ```nodejs
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processo Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path
      },
      onEphError: (error) => {
        console.log("This handler will notify you of any internal errors that happen " +
        "during partition and lease management: %O", error);
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

```
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

[여기](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples)서 더 많은 샘플을 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계

다음 페이지를 방문하여 Event Hubs에 대해 자세히 알아보세요.

* [Node.js를 사용하여 이벤트 보내기](event-hubs-go-get-started-send.md)
* [Event Hubs 샘플](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/)
* [Azure Storage 또는 Data Lake Store로 이벤트 캡처](event-hubs-capture-overview.md)
* [Event Hubs FAQ](event-hubs-faq.md)

<!-- Links -->
[체험 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
