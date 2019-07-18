---
title: Node.js에서 Azure Service Bus 큐를 사용 하는 방법 | Microsoft Docs
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
ms.openlocfilehash: 1426b3d31159280ad9aac2dd240a5f083c40752d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988300"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azure-sb-package"></a>Node.js 및 azure sb 패키지와 함께 Service Bus 큐를 사용 하는 방법
> [!div class="op_multi_selector" title1="프로그래밍 언어" title2="Node.js 패키지"]
> - [(Node.js | azure sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

이 자습서에서는 메시지를 전송 하 여 Service Bus 큐에서 메시지를 수신 하는 Node.js 응용 프로그램을 만드는 방법을 배웁니다 합니다 [azure-sb](https://www.npmjs.com/package/azure-sb) 패키지 있습니다. 샘플은 JavaScript로 작성 되었으며 Node.js를 사용 하 여 [Azure 모듈](https://www.npmjs.com/package/azure) 내부적으로 사용 하는 `azure-sb` 패키지 있습니다.

합니다 [azure-sb](https://www.npmjs.com/package/azure-sb) 사용 하 여 패키지 [Service Bus REST 런타임 Api](/rest/api/servicebus/service-bus-runtime-rest)합니다. New를 사용 하는 빠른 경험을 얻을 수 있습니다 [ @azure/service-bus ](https://www.npmjs.com/package/@azure/service-bus) 패키지는 더 빨리 [AMQP 1.0 프로토콜](service-bus-amqp-overview.md)합니다. 새 패키지에 대 한 자세한 내용은를 참조 하세요. [Node.js를 사용 하 여 Service Bus 큐를 사용 하는 방법 및 @azure/service-bus 패키지](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), 그렇지 않은 읽기를 사용 하는 방법을 알아보려면 계속 진행 합니다 [azure](https://www.npmjs.com/package/azure) 패키지 합니다.

## <a name="prerequisites"></a>필수 조건
- Azure 구독. 이 자습서를 완료하려면 Azure 계정이 필요합니다. 활성화할 수 있습니다 하 [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) 에 등록 또는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)합니다.
- 다음 단계를 사용 하려면 큐가 없는 경우는 [Service Bus 큐를 사용 하 여 Azure portal](service-bus-quickstart-portal.md) 큐를 만드는 문서입니다.
    1. 빠른 읽을 **개요** Service bus **큐**합니다. 
    2. Service Bus를 만듭니다 **네임 스페이스**합니다. 
    3. 가져오기의 **연결 문자열**합니다. 

        > [!NOTE]
        > 만들려는 **큐** 이 자습서에서는 Node.js를 사용 하 여 Service Bus 네임 스페이스에서입니다. 
 

## <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기
빈 Node.js 애플리케이션을 만듭니다. Node.js 애플리케이션을 만드는 방법에 대한 지침은 [Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포][Create and deploy a Node.js application to an Azure Website] 또는 Windows PowerShell을 사용하는 [Node.js 클라우드 서비스][Node.js Cloud Service]를 참조하세요.

## <a name="configure-your-application-to-use-service-bus"></a>Service Bus를 사용하도록 애플리케이션 구성
Azure Service Bus를 사용하려면 Node.js Azure 패키지를 다운로드하여 사용하세요. 이 패키지에는 Service Bus REST 서비스와 통신하는 라이브러리 집합이 포함되어 있습니다.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 가져오기
1. **Windows PowerShell for Node.js** 명령 창을 사용하여 애플리케이션 예제를 만든 **c:\\node\\sbqueues\\WebRole1** 폴더로 이동합니다.
2. 형식 **npm 설치 azure** 명령 창에서 다음 예와 비슷한 출력이 표시 될 해야 있습니다.

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. **ls** 명령을 수동으로 실행하여 **node_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 해당 폴더 내에서 찾을 합니다 **azure** Service Bus 큐에 액세스 하는 데 필요한 라이브러리가 포함 된 패키지입니다.

### <a name="import-the-module"></a>모듈 가져오기
메모장 또는 다른 텍스트 편집기를 사용하여 다음을 애플리케이션의 **server.js** 파일 맨 위에 추가합니다.

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Azure Service Bus 연결 설정
Azure 모듈은 `AZURE_SERVICEBUS_CONNECTION_STRING` 환경 변수를 읽어 Service Bus에 연결하는 데 필요한 정보를 가져옵니다. 호출할 때 계정 정보를 지정 해야이 환경 변수 설정 하지 않으면 `createServiceBusService`합니다.

Azure 웹 사이트의 [Azure Portal][Azure portal]에서 환경 변수를 설정하는 방법에 대한 예제는 [스토리지를 포함한 Node.js 웹 애플리케이션][Node.js Web Application with Storage]을 참조하세요.

## <a name="create-a-queue"></a>큐 만들기
**ServiceBusService** 개체를 사용하면 Service Bus 큐로 작업할 수 있습니다. 다음 코드는 **ServiceBusService** 개체를 만듭니다. 이 코드를 **server.js** 파일의 위쪽, Azure 모듈을 가져오기 위한 문 뒤에 추가합니다.

```javascript
var serviceBusService = azure.createServiceBusService();
```

**ServiceBusService** 개체의 `createQueueIfNotExists`를 호출하면 지정한 큐가 반환되거나(있는 경우) 지정한 이름의 새 큐가 생성됩니다. 다음 코드는 `createQueueIfNotExists`를 사용하여 `myqueue`이라는 큐를 만들거나 이 큐에 연결합니다.

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

`createServiceBusService`는 추가 옵션도 지원합니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 큐 크기 등 기본 큐 설정을 재정의할 수 있습니다. 다음은 최대 큐 크기를 5GB,TTL(Time to Live)을 1분으로 설정하는 예제입니다.

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>필터
**ServiceBusService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동 재시도 등을 포함할 수 있습니다. 필터는 시그니쳐가 있는 메서드를 구현하는 개체입니다.

```javascript
function handle (requestOptions, next)
```

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 `next`를 호출해야 합니다.

```javascript
function (returnObject, finalCallback, next)
```

이 처리 한 후 합니다 `returnObject` (응답 요청에서 서버로) 콜백 중 하나를 호출 해야 합니다 `next` 다른 필터를 계속 처리 하거나 호출 하는 경우 `finalCallback`, 서비스 호출을 종료 하는 .

재시도 논리를 구현하는 두 개의 필터는 Node.js용 Azure SDK, `ExponentialRetryPolicyFilter` 및 `LinearRetryPolicyFilter`를 포함합니다. 다음 코드는 `ExponentialRetryPolicyFilter`를 사용하는 `ServiceBusService` 개체를 만듭니다.

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
Service Bus 큐에 메시지를 보내기 위해 애플리케이션은 **ServiceBusService** 개체에 대해 `sendQueueMessage` 메서드를 호출합니다. Service Bus 큐로 보내고 받는 메시지는 **BrokeredMessage** 개체이며 표준 속성 집합(예: **Label** 및 **TimeToLive**), 애플리케이션별 사용자 지정 속성을 저장하는 데 사용되는 사전 및 임의 애플리케이션 데이터 본문이 있습니다. 애플리케이션은 문자열을 메시지로 전달하여 메시지 본문을 설정할 수 있습니다. 필수 표준 속성이 기본값으로 채워집니다.

다음 예제에서는 `sendQueueMessage`를 사용하여 `myqueue`라는 큐에 테스트 메시지를 보내는 방법을 보여줍니다.

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 표준 및 사용자 지정 애플리케이션 속성이 포함된 헤더의 최대 크기는 64KB입니다. 큐에 보관 된 메시지의 수에 제한이 있지만 큐에서 대기 하는 메시지의 총 크기는 합니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량][Service Bus quotas]을 참조하세요.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
**ServiceBusService** 개체의 `receiveQueueMessage` 메서드를 사용하여 큐에서 메시지를 받습니다. 기본적으로 읽은 메시지는 큐에서 삭제됩니다. 그러나 선택적 매개 변수 `isPeekLock`을 **true**로 설정하여 큐에서 삭제되지 않도록 메시지를 읽은(최대) 후 잠글 수 있습니다.

읽고 메시지를 받기 작업의 일부로 삭제의 기본 동작은 가장 단순한 모델 이며는 응용 프로그램 오류가 발생할 경우 메시지를 처리 하지 허용할 수 없는 시나리오에 가장 적합. 이 동작에 대한 이해를 돕기 위해 소비자가 수신 요청을 실행한 후 처리하기 전에 크래시되는 시나리오를 고려해 보세요. Service Bus는 메시지를 이용 되는 것으로 표시 한 다음 응용 프로그램 다시 시작 되 고 메시지 소비를 다시 시작할 때 했기 때문은 누락 크래시 전에 이용 된 메시지입니다.

경우는 `isPeekLock` 매개 변수는 설정 **true**, 수신 되는 2 단계 작업 이므로 메시지 누락을 허용 하지 않는 응용 프로그램을 지원할 수 있습니다. Service Bus는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 애플리케이션에 반환합니다. 애플리케이션은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후, `deleteMessage` 메서드를 호출하고 삭제될 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. `deleteMessage` 메서드는 메시지를 사용 중인 것으로 표시하고 큐에서 제거합니다.

다음 예제에서는 `receiveQueueMessage`를 사용하여 메시지를 받고 처리하는 방법을 보여줍니다. 먼저 메시지를 받고 삭제한 다음 **true**로 설정된 `isPeekLock`을 사용하여 메시지를 받고 `deleteMessage`를 사용하여 메시지를 삭제합니다.

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>애플리케이션 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법
Service Bus는 애플리케이션 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 어떤 이유로든 수신 애플리케이션이 메시지를 처리할 수 없는 경우 **ServiceBusService** 개체의 `unlockMessage` 메서드를 호출할 수 있습니다. Service Bus 큐 내에서 메시지를 잠금 해제 하 고 동일한 소비 응용 프로그램이 나 다른 소비 응용 프로그램을 다시 받을 수 있도록에 발생 합니다.

큐 내에서 잠긴 메시지와 연결 된 제한 역시 고 실패 하면 응용 프로그램에서 잠금 시간 제한이 만료 되기 전에 메시지를 처리 하는 데 (예를 들어 응용 프로그램이 크래시 되는 경우) 만료 되 면 Service Bus는 메시지를 자동으로 잠금 해제 하 고 다시 받을 수는 사용할 수 있습니다.

애플리케이션이 메시지를 처리한 후 `deleteMessage` 메서드가 호출되기 전에 충돌하는 경우, 다시 시작될 때 메시지가 애플리케이션에 다시 배달됩니다. 이 접근 방식 이라고 *한 번 이상 처리 이상*, 즉, 각 메시지가 최소 한 번 이상 처리 되지만 특정 상황에서는 동일한 메시지를 다시 배달 될 수 있습니다. 시나리오가 중복 처리를 허용할 수 없는, 응용 프로그램 개발자가 중복 메시지 배달을 처리 하는 응용 프로그램에 추가 논리를 추가 해야 합니다. 대체로 사용 하 여 **MessageId** 배달 시도 간에 일정 남아 있는 메시지의 속성입니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
큐에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [큐, 토픽 및 구독][Queues, topics, and subscriptions]
* GitHub의 [Node용 Azure SDK][Azure SDK for Node] 리포지토리
* [Node.js 개발자 센터](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
