---
title: Node.js-azure/서비스 버스에서에서 Azure Service Bus 큐를 사용 하는 방법 | Microsoft Docs
description: Node.js app에서 Azure의 Service Bus 큐를 사용하는 방법에 대해 알아봅니다.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 7aacefde9c037fcce64d9256e35082eb04e0a2f3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988362"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Node.js 및 azure/service bus 패키지를 사용 하 여 Service Bus 큐를 사용 하는 방법
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

이 자습서에서는 메시지를 전송 하 여 새 Service Bus 큐에서 메시지를 수신 하는 Nodejs 프로그램을 작성 하는 방법을 알아봅니다 [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) 패키지 있습니다. 이 패키지를 사용 하 여 더 빨리 [AMQP 1.0 프로토콜](service-bus-amqp-overview.md) 반면 이전 [azure sb](https://www.npmjs.com/package/azure-sb) 사용 되는 패키지 [Service Bus REST 런타임 Api](/rest/api/servicebus/service-bus-runtime-rest)합니다. 샘플은 JavaScript로 작성 됩니다.

## <a name="prerequisites"></a>필수 조건
- Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
- 다음 단계를 사용 하려면 큐가 없는 경우는 [Service Bus 큐를 사용 하 여 Azure portal](service-bus-quickstart-portal.md) 큐를 만드는 문서입니다. Service Bus 인스턴스 이름과 만든 큐에 대 한 연결 문자열 아래로 note 합니다. 샘플에서 이러한 값을 사용 하겠습니다.

> [!NOTE]
> - 이 자습서에서는 복사 하 고 사용 하 여 실행할 수 있는 샘플이 [Nodejs](https://nodejs.org/)합니다. Node.js 응용 프로그램을 만드는 방법에 대 한 지침을 참조 하세요 [만들기 및 Azure 웹 사이트에 Node.js 응용 프로그램을 배포](../app-service/app-service-web-get-started-nodejs.md), 또는 [Windows PowerShell을 사용 하 여 Node.js 클라우드 서비스](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)합니다.
> - 새 [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) 패키지가 아직 큐 만들기를 지원 하지 않습니다. 사용 하십시오 합니다 [ @azure/arm-servicebus ](https://www.npmjs.com/package/@azure/arm-servicebus) 만들고 프로그래밍 방식으로 하려는 경우 패키지 있습니다.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 설치
Service Bus에 대 한 npm 패키지를 설치 하려면 있는 명령 프롬프트를 열고 `npm` 해당 경로에서 샘플 있고 후이 명령을 실행 하려는 폴더로 디렉터리를 변경 합니다.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
Service Bus와 상호 작용 큐 시작 인스턴스화를 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 클래스를 인스턴스화할 하는 데 사용 하는 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 클래스입니다. 큐 클라이언트를 만든 후 보낸 사람 만들기를 사용 하 여 [보낼](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) 또는 [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) 메서드를 메시지를 보낼 수 있습니다.

1. 같은 선호 하는 편집기를 열고 [Visual Studio Code](https://code.visualstudio.com/)
2. 라는 파일을 만듭니다 `send.js` 붙여넣습니다는 아래 코드를 넣습니다. 이 코드는 큐에 10 개의 메시지를 보냅니다.

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
3. 위의 코드에서 큐 이름을 확인 하 고 연결 문자열을 입력 합니다.
4. 다음 명령을 실행 `node send.js` 이 파일을 실행 하려면 명령 프롬프트에서.

축하합니다! 방금 Service Bus 큐에 메시지를 보냈습니다.

메시지와 같은 일부 표준 속성을 가질 `label` 고 `messageId` 보낼 때 설정할 수 있는 합니다. 사용 하 여 사용자 지정 속성을 설정 하려는 경우는 `userProperties`는 사용자 지정 데이터의 키-값 쌍을 보유할 수 있는 json 개체입니다.

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 큐에 보관 된 메시지의 수에 제한이 있지만 큐에서 대기 하는 메시지의 총 크기는 합니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량](service-bus-quotas.md)을 참조하세요.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
Service Bus와 상호 작용 큐 시작 인스턴스화를 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 클래스를 인스턴스화할 하는 데 사용 하는 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 클래스입니다. 큐 클라이언트를 만든 후 수신기 만들기를 사용 하 여 [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) 하거나 [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) 메서드 메시지를 수신 하는 것입니다.

1. 같은 선호 하는 편집기를 열고 [Visual Studio Code](https://code.visualstudio.com/)
2. 라는 파일을 만듭니다 `recieve.js` 붙여넣습니다는 아래 코드를 넣습니다. 이 코드는 큐에서 10 개의 메시지를 수신 하려고 합니다. 받게 되는 실제 큐 및 네트워크 대기 시간에 메시지의 수에 따라 달라 집니다.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.ReceiveAndDelete);
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
3. 위의 코드에서 큐 이름을 확인 하 고 연결 문자열을 입력 합니다.
4. 다음 명령을 실행 `node receiveMessages.js` 이 파일을 실행 하려면 명령 프롬프트에서.

축하합니다! Service Bus 큐에서 메시지를 받았습니다.

[createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) 메서드를 `ReceiveMode` 값을 사용 하 여 열거형은 [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) 하 고 [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)합니다. 해야 [메시지 납부](message-transfers-locks-settlement.md#settling-receive-operations) 사용 하는 경우를 `PeekLock` 모드 중 하나를 사용 하 여 `complete()`, `abandon()`, `defer()`, 또는 `deadletter()` 메시지 메서드.

> [!NOTE]
> 사용 하 여 Service Bus 리소스를 관리할 수 있습니다 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer/)합니다. Service Bus 탐색기를 사용 하면 Service Bus 네임 스페이스에 연결 하 고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스에서 notification hubs 및 이벤트 허브를 테스트할 수와 같은 고급 기능을 제공 합니다. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조 합니다.
- [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- 체크 아웃 다른 [GitHub에서 Service Bus에 대 한 Nodejs 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Node.js 개발자 센터](https://azure.microsoft.com/develop/nodejs/)

