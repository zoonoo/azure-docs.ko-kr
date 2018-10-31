---
title: Node.js를 사용하여 Azure Event Hubs로 이벤트 전송 | Microsoft Docs
description: Node.js를 사용하여 Event Hubs로 이벤트 전송 시작
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: 14ea98b9d31bee08b962e8b3801ed507472ba692
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49455796"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Node.js를 사용하여 Azure Event Hubs로 이벤트 전송

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 장치에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/저장소 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

이 자습서에서는 Node.js로 작성된 응용 프로그램에서 이벤트 허브로 이벤트를 전송하는 방법을 설명합니다.

> [!NOTE]
> [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client)에서 샘플로 이 빠른 시작을 다운로드하여 `EventHubConnectionString` 및 `EventHubName` 문자열을 이벤트 허브 값으로 대체하고, 실행합니다. 또는 이 자습서의 단계를 수행하여 직접 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

- Node.js 버전 8.x 이상. [https://nodejs.org](https://nodejs.org)에서 최신 LTS 버전을 다운로드하세요.
- Visual Studio Code(권장) 또는 다른 IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Event Hubs 네임스페이스 및 이벤트 허브 만들기
첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 응용 프로그램에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스와 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 수행한 다음, 이 자습서의 다음 단계를 진행하세요.

## <a name="clone-the-sample-git-repository"></a>샘플 Git 리포지토리 복제
컴퓨터의 [Github](https://github.com/Azure/azure-event-hubs-node)에서 샘플 Git 리포지토리를 복제합니다. 

## <a name="install-nodejs-package"></a>Node.js 패키지 설치
컴퓨터에 Azure Event Hubs용 Node.js 패키지를 설치합니다. 

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Git 리포지토리 복제
GitHub에서 [샘플](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples)을 다운로드 또는 복제합니다. 

## <a name="send-events"></a>이벤트 보내기
복제한 SDK에는 Node.js를 사용하여 이벤트 허브로 이벤트를 전송하는 방법을 보여 주는 여러 샘플이 포함되어 있습니다. 이 빠른 시작에서는 **simpleSender.js** 예제를 사용합니다. 수신되는 이벤트를 살펴보려면 다른 터미널을 열고 [receive sample](event-hubs-node-get-started-receive.md)을 사용하여 이벤트를 수신합니다.

1. Visual Studio Code에서 프로젝트를 엽니다. 
2. **client** 폴더에 **.env**라는 파일을 만듭니다. 루트 폴더의 **sample.env**에서 샘플 환경 변수를 복사하여 붙여넣습니다.
3. 이벤트 허브 연결 문자열, 이벤트 허브 이름 및 저장소 엔드포인트를 구성합니다. Azure Portal 이벤트 허브 페이지의 **RootManageSharedAccessKey** 아래 **연결 문자열-기본** 키에서 이벤트 허브의 연결 문자열을 복사할 수 있습니다. 자세한 단계는 [연결 문자열 가져오기](event-hubs-create.md#create-an-event-hubs-namespace)를 참조하세요.
4. Azure CLI에서 **client** 폴더 경로로 이동합니다. 다음 명령을 실행하여 node 패키지를 설치하고 프로젝트를 빌드합니다.

    ```nodejs
    npm i
    npm run build
    ```
5. 다음 명령을 실행하여 이벤트 전송을 시작합니다. 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>샘플 코드 검토 
node.js를 사용하여 이벤트 허브로 이벤트를 전송하는 샘플 코드는 다음과 같습니다. sampleSender.js 파일을 수동으로 만든 다음, 실행하면 이벤트 허브로 이벤트를 전송할 수 있습니다. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
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

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Node.js를 사용하여 이벤트 허브로 메시지를 전송했습니다. Node.js를 사용하여 이벤트 허브에서 이벤트를 수신하는 방법을 알아보려면 [이벤트 허브에서 이벤트 수신 - Node.js](event-hubs-node-get-started-receive.md)를 참조하세요.

[GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)에서 Event Hubs에 대한 다른 Node.js 샘플을 확인합니다.
