<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# Node.js에서 큐 서비스를 사용하는 방법

이 가이드에서는 Microsoft Azure 큐 서비스를 사용하여 일반 시나리오를
수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여
작성되었습니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입**, **보기**,
**가져오기** 및 **삭제**와 **큐 만들기 및 삭제**가
포함됩니다. 큐에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

## 목차

-   [큐 서비스 정의][]
-   [개념][]
-   [Azure 저장소 계정 만들기][]
-   [Node.js 응용 프로그램 만들기][]
-   [저장소에 액세스하도록 응용 프로그램 구성][]
-   [Azure 저장소 연결 문자열 설정][]
-   [방법: 큐 만들기][]
-   [방법: 큐에 메시지 삽입][]
-   [방법: 다음 메시지 보기][]
-   [방법: 큐에서 다음 메시지 제거][]
-   [방법: 대기 중인 메시지의 콘텐츠 변경][]
-   [방법: 큐에서 메시지를 제거하는 추가 옵션][]
-   [방법: 큐 길이 가져오기][]
-   [방법: 큐 삭제][]
-   [방법: 공유 액세스 서명 작업][]
-   [다음 단계][]

[WACOM.INCLUDE [howto-queue-storage][]]

## <a name="create-account"></a>Azure 저장소 계정 만들기

[WACOM.INCLUDE [create-storage-account][]]

## <a name="create-app"> </a>Node.js 응용 프로그램 만들기

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기][], [Node.js 클라우드 서비스][](Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트][]를 참조하세요.

## <a name="configure-access"> </a>저장소에 액세스하도록 응용 프로그램 구성

Azure 저장소를 사용하려면 저장소 REST 서비스와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js용
Azure Storage SDK가 필요합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure-storage**를 입력합니다. 그러면
    다음과 같이 출력됩니다.

        azure-storage@0.1.0 node_modules\azure-storage
        ├── extend@1.2.1
        ├── xmlbuilder@0.4.3
        ├── mime@1.2.11
        ├── underscore@1.4.4
        ├── validator@3.1.0
        ├── node-uuid@1.4.1
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)

3.  **ls** 명령을 수동으로 실행하여
    **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 저장소에
    액세스하는 데 필요한 라이브러리가 들어 있는 **azure-storage** 패키지를
    찾습니다.

### 패키지 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의
**server.js** 파일 맨 위에 다음을 추가합니다.

    var azure = require('azure-storage');

## <a name="setup-connection-string"> </a>Azure 저장소 연결 설정

Azure 모듈은 AZURE\_STORAGE\_ACCOUNT 및 AZURE\_STORAGE\_ACCESS\_KEY, 또는 AZURE\_STORAGE\_CONNECTION\_STRING 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않은 경우 **createQueueService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예는 [Node.js 웹 응용 프로그램 및 저장소][]를 참조하세요.

## <a name="create-queue"> </a>방법: 큐 만들기

다음 코드는 **QueueService** 개체를 만들어 큐 작업을 수행할 수 있게
해 줍니다.

    var queueSvc = azure.createQueueService();

**createQueueIfNotExists** 메서드를 사용합니다. 이 메서드는 지정된
큐가 이미 있으면 해당 큐를 반환하고 지정된 큐가 아직 없으면
지정한 이름으로 새 큐를 만듭니다.

    queueSvc.createQueueIfNotExists('myqueue', function(error, result, response){
      if(!error){
        // Queue created or exists
      }
    });

큐가 만들어질 경우 `result`값은 true가 됩니다. 큐가 있는 경우 `result` 값은 false가 됩니다.

### 필터

**QueueService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

        function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

        function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음은 **ExponentialRetryPolicyFilter**를 사용하는 **QueueService** 개체를 만듭니다.

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueSvc = azure.createQueueService().withFilter(retryOperations);

## <a name="insert-message"> </a>방법: 큐에 메시지 삽입

큐에 메시지를 삽입하려면 **createMessage** 메서드를 사용하여 새
메시지를 만들고 이 메시지를 큐에 추가하면 됩니다.

    queueSvc.createMessage('myqueue', "Hello world!", function(error, result, response){
      if(!error){
        // Message inserted
      }
    });

## <a name="peek-message"> </a>방법: 다음 메시지 보기

큐에서 메시지를 제거하지 않고도 **peekMessages** 메서드를
호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다. 기본적으로
**peekMessages**는 단일 메시지를 볼 수 있게 해 줍니다.

    queueSvc.peekMessages('myqueue', function(error, result, response){
      if(!error){
        // Messages peeked
      }
    });

`result` 값에 메시지가 포함됩니다.

> [WACOM.NOTE] 큐에 메시지가 없을 때 **peekMessages**를 사용하면 오류가 반환되지 않지만 메시지도 반환되지 않습니다.

## <a name="get-message"> </a>방법: 큐에서 다음 메시지 제거

메시지 처리는 2단계 프로세스입니다.

1.  메시지를 큐에서 제거합니다.

2.  메시지를 삭제합니다.

메시지를 큐에서 제거하려면 **getMessage**를 사용합니다. 그러면 큐에서 메시지가 보이지 않으므로 다른 클라이언트에서 처리할 수 없습니다. 응용 프로그램에서 메시지를 처리하고 나면 **deleteMessage**를 호출하여 큐에서 삭제합니다. 다음 예에서는 메시지를 가져온 후 삭제합니다.

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // message dequed
        var message = result[0];
        queueSvc.deleteMessage('myqueue', message.messageid, message.popreceipt, function(error, response){
          if(!error){
            //message deleted
          }
        });
      }
    });

> [WACOM.NOTE] 기본적으로 메시지는 30초 동안만 숨겨져 있다가 다른 클라이언트에 표시됩니다. 다른 값을 지정하려면 `options.visibilityTimeout`을 **getMessages**와 함께 사용합니다.

> [WACOM.NOTE]
> 큐에 메시지가 없을 때 **getMessages**를 사용하면 오류가 반환되지 않지만 메시지도 반환되지 않습니다.

## <a name="change-contents"> </a>방법: 대기 중인 메시지의 콘텐츠 변경

**updateMessage**를 사용하면 큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 다음 예에서는 메시지의 텍스트를 업데이트합니다.

    queueSvc.getMessages('myqueue', function(error, result, response){
      if(!error){
        // Got the message
        var message = result[0];
        queueSvc.updateMessage('myqueue', message.messageid, message.popreceipt, 10, {messageText: 'new text'}, function(error, result, response){
          if(!error){
            // Message updated successfully
          }
        });
      }
    });

## <a name="advanced-get"> </a>방법: 큐에서 메시지를 제거하는 추가 옵션

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다.

-   `options.numOfMessages` - 메시지 배치 검색(최대 32개)
-   `options.visibilityTimeout` - 표시하지 않는 시간을 더 길거나 짧게 설정합니다.

다음 예에서는 **getMessages** 메서드를 사용하여 한 번 호출에 15개의 메시지를 가져옵니다. 그런 다음에 for 루프를
사용하여 각 메시지를 처리합니다. 또한 이 메서드에서 반환되는 모든 메시지의 표시하지 않는 시간 제한을 5분으로 설정합니다.

    queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, result, response){
      if(!error){
        // Messages retreived
        for(var index in result){
          // text is available in result[index].messageText
          var message = result[index];
          queueSvc.deleteMessage(queueName, message.messageid, message.popreceipt, function(error, response){
            if(!error){
              // Message deleted
            }
          });
        }
      }
    });

## <a name="get-queue-length"> </a>방법: 큐 길이 가져오기

**getQueueMetadata**에서는 큐에서 대기 중인 메시지의 대략적인 수와 같이 큐에 관련된 메타데이터를 반환합니다.

    queueSvc.getQueueMetadata('myqueue', function(error, result, response){
      if(!error){
        // Queue length is available in result.approximatemessagecount
      }
    });

## <a name="list-queue"> </a>방법: 큐 나열

큐 목록을 검색하려면 **listQueuesSegmented**를 사용합니다. 특정 접두사를 기준으로 필터링된 목록을 검색하려면 **listQueuesSegmentedWithPrefix**를 사용합니다.

    queueSvc.listQueuesSegmented(null, function(error, result, response){
      if(!error){
        // result.entries contains the list of queues
      }
    });

큐를 모두 반환할 수 없는 경우에는 `result.continuationToken`을 **listQueuesSegmented**의 첫 번째 매개 변수나 **listQueuesSegmentedWithPrefix**의 두 번째 매개 변수로 사용하여 더 많은 결과를 검색할 수 있습니다.

## <a name="delete-queue"> </a>방법: 큐 삭제

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의
**deleteQueue** 메서드를 호출합니다.

    queueSvc.deleteQueue(queueName, function(error, response){
        if(!error){
            // Queue has been deleted
        }
    });

큐를 삭제하지 않고 모든 메시지를 지우려면 **clearMessages**를 사용합니다.

## <a name="sas"></a>방법: 공유 액세스 서명 작업

SAS(공유 액세스 서명)는 저장소 계정 이름이나 키를 제공하지 않으면서 큐에 세분화된 액세스 권한을 안전하게 제공하는 방법입니다. SAS는 모바일 앱에서 메시지를 제출하도록 허용하는 경우와 같이 큐에 대해 제한된 액세스를 제공하는 경우에 자주 사용합니다.

클라우드 기반 서비스와 같이 신뢰할 수 있는 응용 프로그램에서는 **QueueService**의 **generateSharedAccessSignature**를 사용하여 SAS를 생성하고 신뢰할 수 없거나 신뢰가 약한 응용 프로그램에 제공합니다. 예를 들면 모바일 앱이 여기에 해당됩니다. SAS는 SAS가 유효한 시작 및 종료 날짜와 SAS 소유자에게 부여되는 액세스 수준을 설명하는 정책을 사용하여 생성됩니다.

다음 예에서는 SAS 소유자가 큐에 메시지를 추가할 수 있도록 허용하며 만든 후 100분이 지나면 만료되는 새 공유 액세스 정책을 생성합니다.

    var startDate = new Date();
    var expiryDate = new Date(startDate);
    expiryDate.setMinutes(startDate.getMinutes() + 100);
    startDate.setMinutes(startDate.getMinutes() - 100);

    var sharedAccessPolicy = {
      AccessPolicy: {
        Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
        Start: startDate,
        Expiry: expiryDate
      }
    };

    var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
    var host = queueSvc.host;

SAS 소유자가 큐에 액세스할 때 필요하므로 호스트 정보도 제공해야 합니다.

그러고 나면 클라이언트 응용 프로그램에서 **QueueServiceWithSAS**에 SAS를 사용하여 큐에 대한 작업을 수행합니다. 다음 예에서는 큐를 연결하고 메시지를 만듭니다.

    var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
    sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
      if(!error){
        //message added
      }
    });

SAS가 추가 액세스만으로 생성되었기 때문에 메시지를 읽거나 업데이트, 삭제하려고 하면 오류가 반환됩니다.

### 액세스 제어 목록

ACL(액세스 제어 목록)을 사용하여 SAS에 액세스 정책을 설정할 수도 있습니다. 이 방법은 여러 클라이언트에서 큐에 액세스하게 하면서 각 클라이언트에 서로 다른 액세스 정책을 제공하려는 경우에 유용합니다.

ACL은 각 정책에 ID가 연결된 액세스 정책 배열을 사용하여 구현됩니다. 다음 예에서는 'user1'와 'user2'에 대해 하나씩, 두 개의 정책을 정의합니다.

    var sharedAccessPolicy = [
      {
        AccessPolicy: {
          Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user1'
      },
      {
        AccessPolicy: {
          Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
          Start: startDate,
          Expiry: expiryDate
        },
        Id: 'user2'
      }
    ];

다음 예에서는 **myqueue**에 대한 현재 ACL을 가져온 다음 **setQueueAcl**을 사용하여 새 정책을 추가합니다. 이 접근 방식을 통해 다음을 수행할 수 있습니다.

    queueSvc.getQueueAcl('myqueue', function(error, result, response) {
      if(!error){
        //push the new policy into signedIdentifiers
        result.signedIdentifiers.push(sharedAccessPolicy);
        queueSvc.setQueueAcl('myqueue', result, function(error, result, response){
          if(!error){
            // ACL set
          }
        });
      }
    });

ACL이 설정되고 나면 정책의 ID를 기반으로 SAS를 만들 수 있습니다. 다음 예에서는 'user2'에 대해 새 SAS를 만듭니다.

    queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });

## <a name="next-steps"> </a>다음 단계

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소
작업을 수행하는 방법을 알아보세요.

-   다음 MSDN 참조를 확인하세요. [Azure에 데이터 저장 및 액세스][]
-   [Azure 저장소 팀 블로그][](영문)를 방문하세요.
-   GitHub에서 [Azure Storage SDK for Node][] 리포지토리를 방문하세요.

  [다음 단계]: #next-steps
  [큐 서비스 정의]: #what-is
  [개념]: #concepts
  [Azure 저장소 계정 만들기]: #create-account
  [Node.js 응용 프로그램 만들기]: #create-app
  [저장소에 액세스하도록 응용 프로그램 구성]: #configure-access
  [Azure 저장소 연결 문자열 설정]: #setup-connection-string
  [방법: 큐 만들기]: #create-queue
  [방법: 큐에 메시지 삽입]: #insert-message
  [방법: 다음 메시지 보기]: #peek-message
  [방법: 큐에서 다음 메시지 제거]: #get-message
  [방법: 대기 중인 메시지의 콘텐츠 변경]: #change-contents
  [방법: 큐에서 메시지를 제거하는 추가 옵션]: #advanced-get
  [방법: 큐 길이 가져오기]: #get-queue-length
  [방법: 큐 삭제]: #delete-queue
  [방법: 공유 액세스 서명 작업]: #sas
  [howto-queue-storage]: ../includes/howto-queue-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기]: /ko-kr/documentation/articles/web-sites-nodejs-develop-deploy-mac/
  [Node.js 클라우드 서비스]: /ko-kr/documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [WebMatrix를 사용하는 웹 사이트]: /ko-kr/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Node.js 웹 응용 프로그램 및 저장소]: /ko-kr/documentation/articles/storage-nodejs-use-table-storage-web-site/
  [Azure에 데이터 저장 및 액세스]: http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx
  [Azure 저장소 팀 블로그]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node
