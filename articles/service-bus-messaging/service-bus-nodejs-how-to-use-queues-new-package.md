---
title: Node.js에서 azure/service bus 큐를 사용 하는 방법
description: Azure/Service Bus 패키지를 사용 하 여 node.js 앱에서 Azure의 Service Bus 큐를 사용 하는 방법을 알아봅니다.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790304"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Node.js 및 azure/Service Bus 패키지를 사용 하 여 Service Bus 큐를 사용 하는 방법
> [!div class="op_multi_selector" title1="프로그래밍 언어" title2="Node.js 패키지"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

이 자습서에서는 새 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 패키지를 사용 하 여 Service Bus 큐에서 메시지를 보내고 수신 하는 nodejs 프로그램을 작성 하는 방법에 대해 알아봅니다. 이 패키지는 더 빠른 [Amqp 1.0 프로토콜](service-bus-amqp-overview.md) 을 사용 하는 반면, 이전 [azure-SB](https://www.npmjs.com/package/azure-sb) 패키지는 [REST 런타임 api Service Bus](/rest/api/servicebus/service-bus-runtime-rest)사용 했습니다. 샘플은 JavaScript로 작성 되었습니다.

## <a name="prerequisites"></a>전제 조건
- Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) 을 활성화 하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 등록할 수 있습니다.
- 사용할 큐가 없는 경우 [Azure Portal를 사용 하 여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다. Service Bus 인스턴스에 대 한 연결 문자열 및 사용자가 만든 큐의 이름을 적어둡니다. 샘플에서 이러한 값을 사용 합니다.

> [!NOTE]
> - 이 자습서에서는 [Nodejs](https://nodejs.org/)를 사용 하 여 복사 하 고 실행할 수 있는 샘플에서 작업 합니다. Node.js 응용 프로그램을 만드는 방법에 대 한 지침은 node.js [응용 프로그램을 만들어 Azure 웹 사이트에 배포](../app-service/app-service-web-get-started-nodejs.md)또는 [Windows PowerShell을 사용 하 여 node.js 클라우드 서비스](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)를 참조 하세요.
> - 새 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 패키지는 아직 큐 만들기를 지원 하지 않습니다. 프로그래밍 방식으로 [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) 패키지를 만들려면 패키지를 사용 하십시오.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 설치
Service Bus에 대 한 npm 패키지를 설치 하려면 해당 경로에 `npm` 있는 명령 프롬프트를 열고 디렉터리를 샘플을 사용할 폴더로 변경한 후이 명령을 실행 합니다.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
Service Bus 큐와의 상호 작용은 [Servicebusclient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 클래스를 인스턴스화하고 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 클래스를 인스턴스화하는 데 사용 됩니다. 큐 클라이언트를 만든 후 발신자를 만들어 [송신](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) 또는 [sendbatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) 메서드를 사용 하 여 메시지를 보낼 수 있습니다.

1. 원하는 편집기를 엽니다 (예: [Visual Studio Code](https://code.visualstudio.com/)
2. `send.js` 라는 파일을 만들고 아래 코드를 붙여넣습니다. 이 코드는 큐에 10 개의 메시지를 보냅니다.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에 있는 큐의 연결 문자열 및 이름을 입력 합니다.
4. 그런 다음 명령 프롬프트에서 명령 `node send.js`를 실행 하 여이 파일을 실행 합니다.

축하합니다. Service Bus 큐에 메시지를 보냈습니다.

메시지에는를 보낼 때 설정할 수 있는 `label` 및 `messageId`와 같은 몇 가지 표준 속성이 있습니다. 사용자 지정 속성을 설정 하려면 사용자 지정 데이터의 키-값 쌍을 보유할 수 있는 json 개체인 `userProperties`를 사용 합니다.

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 큐에 저장 되는 메시지 수에는 제한이 없지만 큐에 저장 된 메시지의 총 크기에는 한도가 있습니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량](service-bus-quotas.md)을 참조하세요.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
Service Bus 큐와의 상호 작용은 [Servicebusclient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 클래스를 인스턴스화하고 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 클래스를 인스턴스화하는 데 사용 됩니다. 큐 클라이언트를 만든 후에는 수신기를 만들어 메시지를 수신 하는 [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) 또는 [registermessagehandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) 메서드를 사용할 수 있습니다.

1. 원하는 편집기를 엽니다 (예: [Visual Studio Code](https://code.visualstudio.com/)
2. `recieve.js` 라는 파일을 만들고 아래 코드를 붙여넣습니다. 이 코드는 큐에서 10 개의 메시지를 수신 하려고 합니다. 실제로 받는 수는 큐에 있는 메시지 수와 네트워크 대기 시간에 따라 달라 집니다.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에 있는 큐의 연결 문자열 및 이름을 입력 합니다.
4. 그런 다음 명령 프롬프트에서 명령 `node receiveMessages.js`를 실행 하 여이 파일을 실행 합니다.

축하합니다. Service Bus 큐에서 메시지를 받았습니다.

[CreateReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) 메서드는 값이 [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) 및 [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)인 열거형 인 `ReceiveMode`를 사용 합니다. 메시지에 대해 `complete()`, `abandon()`, `defer()`또는 `deadletter()` 메서드 중 하나를 사용 하 여 `PeekLock` 모드를 사용 하는 경우 [메시지](message-transfers-locks-settlement.md#settling-receive-operations) 를 사용 해야 합니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 다음 리소스를 참조 하세요.
- [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- [GitHub에서 Service Bus에 대 한 다른 Nodejs 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript) 체크 아웃
- [Node.js 개발자 센터](https://azure.microsoft.com/develop/nodejs/)

