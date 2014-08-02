<properties linkid="dev-nodejs-how-to-service-bus-queues" urlDisplayName="Queue Service" pageTitle="How to use the queue service (Node.js) | Microsoft Azure" metaKeywords="Azure Queue Service get messages Node.js" description="Learn how to use the Azure Queue service to create and delete queues, and insert, get, and delete messages. Samples written in Node.js." metaCanonical="" services="storage" documentationCenter="Node.js" title="How to Use the Queue Service from Node.js" authors="" solutions="" manager="" editor="" />

Node.js에서 큐 서비스를 사용하는 방법
=====================================

이 가이드에서는 Azure 큐 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Node.js API를 사용하여 작성되었습니다. 여기서 다루는 시나리오에는 큐 메시지 **삽입**, **보기**, **가져오기** 및 **삭제**와 **큐 만들기 및 삭제**가 포함됩니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

목차
----

-   [큐 서비스 정의](#what-is)
-   [개념](#concepts)
-   [Azure 저장소 계정 만들기](#create-account)
-   [Node.js 응용 프로그램 만들기](#create-app)
-   [저장소에 액세스하도록 응용 프로그램 구성](#configure-access)
-   [Azure 저장소 연결 문자열 설정](#setup-connection-string)
-   [방법: 큐 만들기](#create-queue)
-   [방법: 큐에 메시지 삽입](#insert-message)
-   [방법: 다음 메시지 보기](#peek-message)
-   [방법: 큐에서 다음 메시지 제거](#get-message)
-   [방법: 대기 중인 메시지의 콘텐츠 변경](#change-contents)
-   [방법: 큐에서 메시지를 제거하기 위한 추가적인 옵션](#advanced-get)
-   [방법: 큐 길이 가져오기](#get-queue-length)
-   [방법: 큐 삭제](#delete-queue)
-   [다음 단계](#next-steps)

큐 서비스 정의
--------------

Azure 큐 서비스는 HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 단일 큐 메시지의 크기는 최대 64KB일 수 있으며, 하나의 큐에 저장소 계정의 100TB 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다. 큐 서비스의 일반적인 사용은 다음과 같습니다.

-   비동기적으로 처리할 작업 백로그 만들기
-   Azure 웹 역할에서 작업자 역할로 메시지 전달

개념
----

큐 서비스에는 다음 구성 요소가 포함됩니다.

![Queue1](./media/storage-nodejs-how-to-use-queues/queue1.png)

-   **URL 형식:** 다음 URL 형식을 사용하여 큐에 주소를 지정할 수 있습니다.

        http://storageaccount.queue.core.windows.net/queue  

    다음 URL은 다이어그램에 있는 큐 중 하나의 주소를 지정합니다.

        http://myaccount.queue.core.windows.net/imagesToDownload

-   **저장소 계정:** Azure 저장소에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 저장소 계정은 큐 액세스를 위한 가장 높은 수준의 네임스페이스입니다. 저장소 계정에 있는 Blob, 테이블 및 큐 콘텐츠의 총 크기는 100TB를 초과할 수 없습니다.

-   **큐:** 큐에는 메시지 집합이 포함됩니다. 모든 메시지는 큐에 있어야 합니다.

-   **메시지:** 최대 64KB의 임의 형식의 메시지입니다.

Azure 저장소 계정 만들기
------------------------

저장소 작업을 사용하려면 Azure 저장소 계정이 필요합니다. 다음 단계에 따라 저장소 계정을 만들 수 있습니다. [REST API를 사용](http://msdn.microsoft.com/en-us/library/windowsazure/hh264518.aspx)하여 저장소 계정을 만들 수도 있습니다.

1.  [Azure 관리 포털](http://manage.windowsazure.com)에 로그인합니다.

2.  탐색 창 맨 아래쪽에서 **+새로 만들기**를 클릭합니다.

    ![+새로 만들기](./media/storage-nodejs-how-to-use-queues/plus-new.png)

3.  **저장소 계정**을 클릭한 후 **빠른 생성**을 클릭합니다.

    ![빠른 생성 대화 상자](./media/storage-nodejs-how-to-use-queues/quick-storage.png)

4.  URL에서 저장소 계정의 URI에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다.

5.  저장소를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 저장소를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

6.  **저장소 계정 만들기**를 클릭합니다.

Node.js 응용 프로그램 만들기
----------------------------

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포하기](/en-us/develop/nodejs/tutorials/create-a-website-(mac)/), [Node.js 클라우드 서비스](Windows PowerShell 사용) 또는 [WebMatrix를 사용하는 웹 사이트](/en-us/develop/nodejs/tutorials/web-site-with-webmatrix/)를 참조하십시오.

저장소에 액세스하도록 응용 프로그램 구성
----------------------------------------

Azure 저장소를 사용하려면 저장소 REST 라이브러리와 통신하는 편리한 라이브러리 집합이 포함되어 있는 Node.js Azure 패키지를 다운로드하여 사용해야 합니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 샘플 응용 프로그램을 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure**를 입력합니다. 그러면 다음과 같이 출력됩니다.

        azure@0.7.5 node_modules\azure
        |-- dateformat@1.0.2-1.2.3
        |-- xmlbuilder@0.4.2
        |-- node-uuid@1.2.0
        |-- mime@1.2.9
        |-- underscore@1.4.4
        |-- validator@1.1.1
        |-- tunnel@0.0.2
        |-- wns@0.5.3
        |-- xml2js@0.2.7 (sax@0.5.2)
        |-- request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)

3.  **ls** 명령을 수동으로 실행하여 **node\_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 저장소에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure** 패키지를 찾습니다.

### 패키지 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 저장소를 사용할 응용 프로그램의 **server.js** 파일 맨 위에 다음을 추가합니다.

    var azure = require('azure');

Azure 저장소 연결 설정
----------------------

Azure 모듈은 AZURE\_STORAGE\_ACCOUNT 및 AZURE\_STORAGE\_ACCESS\_KEY 환경 변수를 읽고 Azure 저장소 계정에 연결하는 데 필요한 정보를 확인합니다. 이러한 환경 변수가 설정되지 않은 경우 **createQueueService**를 호출할 때 계정 정보를 지정해야 합니다.

Azure 클라우드 서비스의 구성 파일에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 클라우드 서비스 및 저장소](/en-us/develop/nodejs/tutorials/web-app-with-storage/)를 참조하십시오.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예제는 [Node.js 웹 응용 프로그램 및 저장소](/en-us/develop/nodejs/tutorials/web-site-with-storage/)를 참조하십시오.

방법: 큐 만들기
---------------

다음 코드는 **QueueService** 개체를 만들어 큐 작업을 수행할 수 있게 해 줍니다.

    var queueService = azure.createQueueService();

**createQueueIfNotExists** 메서드를 사용합니다. 이 메서드는 지정된 큐가 이미 있으면 해당 큐를 반환하고 지정된 큐가 아직 없으면 지정한 이름으로 새 큐를 만듭니다.

    queueService.createQueueIfNotExists(queueName, function(error){
        if(!error){
            // Queue exists
        }
    });

### 필터

**QueueService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

     function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

     function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음은 **ExponentialRetryPolicyFilter**를 사용하는 **QueueService** 개체를 만듭니다.

    var retryOperations = new azure.ExponentialRetryPolicyFilter();
    var queueService = azure.createQueueService().withFilter(retryOperations);

방법: 큐에 메시지 삽입
----------------------

큐에 메시지를 삽입하려면 **createMessage** 메서드를 사용하여 새 메시지를 만들고 이 메시지를 큐에 추가하면 됩니다.

    queueService.createMessage(queueName, "Hello world!", function(error){
        if(!error){
            // Message inserted
        }
    });

방법: 다음 메시지 보기
----------------------

큐에서 메시지를 제거하지 않고도 **peekMessages** 메서드를 호출하여 큐의 맨 앞에서 원하는 메시지를 볼 수 있습니다. 기본적으로 **peekMessages**는 단일 메시지를 볼 수 있게 해 줍니다.

    queueService.peekMessages(queueName, function(error, messages){
        if(!error){
            // Messages peeked
            // Text is available in messages[0].messagetext
        }
    });

> [WACOM.NOTE] 큐에 메시지가 없을 때 **peekMessage**를 사용하면 오류가 반환되지 않지만 메시지도 반환되지 않습니다.

방법: 큐에서 다음 메시지 제거
-----------------------------

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. **getMessages**를 호출하면 기본적으로 큐에서 다음 메시지를 가져옵니다. **getMessages**에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 **deleteMessage**도 호출해야 합니다. 메시지를 제거하는 이 2단계 프로세스는 코드가 하드웨어 또는 소프트웨어 오류로 인해 메시지를 처리하지 못하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와서 다시 시도할 수 있도록 보장합니다. 코드는 메시지가 처리된 직후에 **deleteMessage**를 호출합니다.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Process the message in less than 30 seconds, the message
            // text is available in messages[0].messagetext 
            var message = messages[0]
            queueService.deleteMessage(queueName
                , message.messageid
                , message.popreceipt
                , function(error){
                    if(!error){
                        // Message deleted
                    }
                });
        }
    });

> [WACOM.NOTE] 큐에 메시지가 없을 때 **getMessages**를 사용하면 오류가 반환되지 않지만 메시지도 반환되지 않습니다.

방법: 대기 중인 메시지의 콘텐츠 변경
------------------------------------

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다. 아래 코드에서는 **updateMessage** 메서드를 사용하여 메시지를 업데이트합니다.

    queueService.getMessages(queueName, function(error, messages){
        if(!error){
            // Got the message
            var message = messages[0];
            queueService.updateMessage(queueName
                , message.messageid
                , message.popreceipt
                , 10
                , { messagetext: 'in your message, doing stuff.' }
                , function(error){
                    if(!error){
                        // Message updated successfully
                    }
                });
        }
    });

방법: 큐에서 메시지를 제거하기 위한 추가적인 옵션
-------------------------------------------------

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다. 다음 코드 예제는 **getMessages** 메서드를 사용하여 한 번 호출에 15개의 메시지를 가져옵니다. 그런 다음 for 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

    queueService.getMessages(queueName
        , {numofmessages: 15, visibilitytimeout: 5 * 60}
        , function(error, messages){
        if(!error){
            // Messages retreived
            for(var index in messages){
                // text is available in messages[index].messagetext
                var message = messages[index];
                queueService.deleteMessage(queueName
                    , message.messageid
                    , message.popreceipt
                    , function(error){
                        if(!error){
                            // Message deleted
                        }
                    });
            }
        }
    });

방법: 큐 길이 가져오기
----------------------

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다. **getQueueMetadata** 메서드는 큐에 대한 메타데이터를 반환하도록 큐 서비스에 요청하고, 큐 서비스로부터 받은 응답의 **approximatemessagecount** 속성에는 얼마나 많은 메시지가 큐에 있는지 나타내는 개수가 포함되어 있습니다. 큐 서비스가 요청에 응답한 이후에 메시지가 추가되거나 제거될 수 있기 때문에 이 개수는 근사값일 뿐입니다.

    queueService.getQueueMetadata(queueName, function(error, queueInfo){
        if(!error){
            // Queue length is available in queueInfo.approximatemessagecount
        }
    });

방법: 큐 삭제
-------------

큐 및 해당 큐의 모든 메시지를 삭제하려면 큐 개체의 **deleteQueue** 메서드를 호출합니다.

    queueService.deleteQueue(queueName, function(error){
        if(!error){
            // Queue has been deleted
        }
    });

다음 단계
---------

이제 큐 저장소의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   MSDN 참조: [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/en-us/library/windowsazure/gg433040.aspx)
-   [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 방문하십시오.
-   GitHub에서 [Azure SDK for Node](https://github.com/WindowsAzure/azure-sdk-for-node) 리포지토리를 방문하십시오.

  [Node.js 클라우드 서비스]: {localLink:2221} "Web App with Express"