---
title: Node.js에서 Azure Queue storage를 사용 하는 방법-Azure Storage
description: Azure 큐 서비스을 사용 하 여 큐를 만들고 삭제 하는 방법을 알아봅니다. Node.js를 사용 하 여 메시지를 삽입 하 고 가져오고 삭제 하는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/31/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 77c35ae4b9e845cd3c0f638407c0d71c36fcf9f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289687"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Node.js에서 Azure Queue storage를 사용 하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>개요

이 가이드에서는 Microsoft Azure 큐 서비스를 사용 하 여 일반적인 시나리오를 수행 하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 큐 메시지 삽입, 보기, 가져오기 및 삭제가 포함 됩니다. 큐를 만들고 삭제 하는 방법에 대해서도 알아봅니다.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

빈 Node.js 응용 프로그램을 만들려면 [Azure App Service에서 Node.js 웹 앱 만들기][Create a Node.js web app in Azure App Service]및 Windows PowerShell을 사용 하 여 [Azure 클라우드 서비스에 Node.js 응용 프로그램 빌드 및 배포][Build and deploy a Node.js application to an Azure Cloud Service] 또는 [Visual Studio Code][Visual Studio Code]을 참조 하세요.

## <a name="configure-your-application-to-access-storage"></a>스토리지에 액세스하도록 애플리케이션 구성

[JavaScript 용 Azure Storage 클라이언트 라이브러리][Azure Storage client library for JavaScript] 에는 저장소 REST 서비스와 통신 하는 편리한 라이브러리 집합이 포함 되어 있습니다.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 가져오기

1. PowerShell (Windows), 터미널 (Mac) 또는 Bash (Unix)와 같은 명령줄 인터페이스를 사용 하 여 샘플 응용 프로그램을 만든 폴더로 이동 합니다.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

1. 명령 창에 **npm install \@ azure/storage-queue** 를 입력 합니다.

1. **Node \_ modules** 폴더가 만들어졌는지 확인 합니다. 이 폴더 내에서 저장소에 액세스 하는 데 필요한 클라이언트 라이브러리가 포함 된 ** \@ azure/storage 큐** 패키지를 찾을 수 있습니다.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. 명령 창에 **npm install azure-storage** 를 입력합니다.

1. **Node \_ modules** 폴더가 만들어졌는지 확인 합니다. 이 폴더 내에서 저장소에 액세스 하는 데 필요한 라이브러리가 포함 된 **azure 저장소** 패키지를 찾을 수 있습니다.

---

### <a name="import-the-package"></a>패키지 가져오기

코드 편집기를 사용 하 여 큐를 사용 하려는 JavaScript 파일의 맨 위에 다음을 추가 합니다.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>큐를 만드는 방법

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

다음 코드는 호출 된 환경 변수의 값을 가져오고 `AZURE_STORAGE_CONNECTION_STRING` 이를 사용 하 여 [QueueServiceClient](/javascript/api/@azure/storage-queue/queueserviceclient) 개체를 만듭니다. 그런 다음 **QueueServiceClient** 개체를 사용 하 여 [QueueClient](/javascript/api/@azure/storage-queue/queueclient) 개체를 만듭니다. **QueueClient** 개체를 사용 하면 특정 큐로 작업할 수 있습니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

큐가 이미 있는 경우 예외가 throw 됩니다.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Azure 모듈은 환경 변수 `AZURE_STORAGE_ACCOUNT` 및 `AZURE_STORAGE_ACCESS_KEY` 를 읽고 `AZURE_STORAGE_CONNECTION_STRING` azure storage 계정에 연결 하는 데 필요한 정보를 확인 합니다. 이러한 환경 변수가 설정 되지 않은 경우 **createQueueService**를 호출할 때 계정 정보를 지정 해야 합니다.

다음 코드는 **QueueService** 개체를 만들어 큐 작업을 수행할 수 있게 해 줍니다.

```javascript
var queueSvc = azure.createQueueService();
```

**CreateQueueIfNotExists** 메서드를 호출 하 여 지정 된 이름의 새 큐를 만들거나 이미 있는 경우 큐를 반환 합니다.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

큐가 만들어지면 `result.created` 는 true가 됩니다. 큐가 있을 경우 `result.created` 는 false가 됩니다.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>큐에 메시지를 삽입하는 방법

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

큐에 메시지를 추가 하려면 [sendMessage](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) 메서드를 호출 합니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

큐에 메시지를 삽입 하려면 **createMessage** 메서드를 호출 하 여 새 메시지를 만들고 큐에 추가 합니다.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>다음 메시지를 피킹 (peeking) 하는 방법

**PeekMessages** 메서드를 호출 하 여 큐에서 메시지를 제거 하지 않고 큐에서 메시지를 피킹할 수 있습니다.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

기본적으로 [peekMessages](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) 는 단일 메시지를 볼 수 있게 해 줍니다. 다음 예에서는 큐의 처음 5 개 메시지를 피킹합니다. 5 개 미만의 메시지가 표시 되 면 표시 되는 메시지만 반환 됩니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

기본적으로 **peekMessages** 는 단일 메시지를 볼 수 있게 해 줍니다.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result` 에 메시지가 포함됩니다.

---

큐에 메시지가 없을 때 **peekMessages** 를 호출 하면 오류가 반환 되지 않습니다. 그러나 메시지는 반환 되지 않습니다.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>대기 중인 메시지의 콘텐츠를 변경 하는 방법

다음 예에서는 메시지의 텍스트를 업데이트 합니다.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

[UpdateMessage](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-)를 호출 하 여 큐에 있는 메시지의 내용을 변경 합니다. 

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

**UpdateMessage**를 호출 하 여 큐에 있는 메시지의 내용을 변경 합니다. 

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>메시지를 큐에서 제거 하는 방법

큐에서 메시지 큐를 제거 하는 과정은 다음 두 단계로 진행 됩니다.

1. 메시지를 가져옵니다.

1. 메시지를 삭제합니다.

다음 예에서는 메시지를 가져온 다음 삭제 합니다.

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

메시지를 가져오려면 [receiveMessages](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) 메서드를 호출 합니다. 이 호출을 수행 하면 메시지가 큐에 표시 되지 않으므로 다른 클라이언트에서 메시지를 처리할 수 없습니다. 애플리케이션에서 메시지를 처리하고 나면 [deleteMessage](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-)를 호출하여 큐에서 삭제합니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

기본적으로 메시지는 30 초 동안만 숨겨집니다. 30 초 후에는 다른 클라이언트에 표시 됩니다. **ReceiveMessages**를 호출할 때 [visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) 를 설정 하 여 다른 값을 지정할 수 있습니다.

큐에 메시지가 없을 때 **receiveMessages** 를 호출 하면 오류가 반환 되지 않습니다. 그러나 메시지는 반환 되지 않습니다.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

메시지를 가져오려면 **Getmessages** 메서드를 호출 합니다. 이 호출을 수행 하면 메시지가 큐에 표시 되지 않으므로 다른 클라이언트에서 메시지를 처리할 수 없습니다. 애플리케이션에서 메시지를 처리하고 나면 **deleteMessage**를 호출하여 큐에서 삭제합니다.

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

기본적으로 메시지는 30 초 동안만 숨겨집니다. 30 초 후에는 다른 클라이언트에 표시 됩니다. `options.visibilityTimeout` 와 **getMessages**를 사용하여 다른 값을 지정할 수 있습니다.

큐에 메시지가 없을 때 **Getmessages** 를 사용 하면 오류가 반환 되지 않습니다. 그러나 메시지는 반환 되지 않습니다.

---

## <a name="additional-options-for-dequeuing-messages"></a>큐에서 메시지를 제거하는 추가 옵션

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

* [옵션. numberofmessages](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages) -메시지 일괄 처리를 검색 합니다 (최대 32).
* [visibilityTimeout](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) -더 길거나 짧은 표시 안 함 제한 시간을 설정 합니다.

다음 예제에서는 **receiveMessages** 메서드를 사용 하 여 한 번의 호출로 5 개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 이 메서드에서 반환되는 모든 메시지의 표시하지 않는 시간 제한을 5분으로 설정합니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

* `options.numOfMessages` - 메시지 배치를 검색합니다(최대 32개).
* `options.visibilityTimeout` - 표시하지 않는 시간 제한을 더 길거나 짧게 설정합니다.

다음 예에서는 **getMessages** 메서드를 사용하여 한 번 호출에 15개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 이 메서드에서 반환되는 모든 메시지의 표시하지 않는 시간 제한을 5분으로 설정합니다.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>큐 길이를 가져오는 방법

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

[GetProperties](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) 메서드는 큐에서 대기 중인 메시지의 대략적인 수를 포함 하 여 큐에 대 한 메타 데이터를 반환 합니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

**GetQueueMetadata** 메서드는 큐에서 대기 중인 메시지의 대략적인 수를 포함 하 여 큐에 대 한 메타 데이터를 반환 합니다.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>큐를 나열 하는 방법

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

큐 목록을 검색 하려면 [QueueServiceClient 큐]()를 호출 합니다. 특정 접두사로 필터링 된 목록을 검색 하려면 **Listqueues**에 대 한 호출에서 [options. prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) 를 설정 합니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

큐 목록을 검색하려면 **listQueuesSegmented**를 사용합니다. 특정 접두사를 기준으로 필터링된 목록을 검색하려면 **listQueuesSegmentedWithPrefix**를 사용합니다.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

모든 큐를 반환할 수 없는 경우 `result.continuationToken` **listQueuesSegmented** 의 첫 번째 매개 변수로 전달 하거나 **listQueuesSegmentedWithPrefix** 의 두 번째 매개 변수를 전달 하 여 더 많은 결과를 검색 합니다.

---

## <a name="how-to-delete-a-queue"></a>큐를 삭제 하는 방법

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

큐 및 해당 큐에 포함 된 모든 메시지를 삭제 하려면 **QueueClient** 개체에서 [deleteQueue](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) 메서드를 호출 합니다.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

큐에서 메시지를 삭제 하지 않고 모든 메시지를 지우려면 [Clearmessages](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-)를 호출 합니다.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

큐 및 해당 큐에 포함 된 모든 메시지를 삭제 하려면 큐 개체의 **deleteQueue** 메서드를 호출 합니다.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

큐에서 메시지를 삭제 하지 않고 모든 메시지를 지우려면 **Clearmessages**를 호출 합니다.

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>다음 단계

이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 스토리지 작업에 대해 알아보세요.

* [Azure Storage 팀 블로그][Azure Storage Team Blog] 를 방문 하 여 새로운 기능에 대해 알아보세요.
* GitHub에서 [JavaScript 리포지토리의 Azure Storage 클라이언트 라이브러리][Azure Storage client library for JavaScript] 방문

[Azure Storage client library for JavaScript]: https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript
[Azure Storage Team Blog]: https://techcommunity.microsoft.com/t5/azure-storage/bg-p/AzureStorageBlog
[Build and deploy a Node.js application to an Azure Cloud Service]: ../../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Create a Node.js web app in Azure App Service]: ../../app-service/quickstart-nodejs.md
[Visual Studio Code]: https://code.visualstudio.com/docs/nodejs/nodejs-tutorial
