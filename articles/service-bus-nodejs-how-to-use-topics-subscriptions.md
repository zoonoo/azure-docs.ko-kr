<properties 
	pageTitle="서비스 버스 토픽을 사용하는 방법(Node.js) - Azure" 
	description="Azure에서 서비스 버스 토픽 및 구독을 사용하는 방법에 대해 알아봅니다. 코드 샘플은 Node.js 응용 프로그램용으로 작성되었습니다." 
	services="service-bus" 
	documentationCenter="nodejs" 
	authors="sethmanheim" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="02/10/2015" 
	ms.author="sethm"/>






# 서비스 버스 토픽 및 구독을 사용하는 방법

이 가이드에서는 Node.js 응용 프로그램에서 서비스 버스 토픽과 구독을 사용하는 방법을 설명합니다. 여기서 다루는 시나리오에는 **토픽 및 구독 만들기, 구독 필터 만들기, 토픽에 메시지 보내기**, **구독에서 메시지 받기**, **토픽 및 구독 삭제** 등이 포함됩니다. 토픽 및 구독에 대한 자세한 내용은 [다음 단계][] 섹션을 참조하세요.

[AZURE.INCLUDE [howto-service-bus-topics](../includes/howto-service-bus-topics.md)]

## Node.js 응용 프로그램 만들기

빈 Node.js 응용 프로그램을 만듭니다. Node.js 응용 프로그램을 만드는 방법에 대한 지침은 [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포], [Node.js 클라우드 서비스][Node.js 클라우드 서비스](Windows PowerShell 사용) 또는 [WebMatrix를 사용한 웹 사이트]를 참조하세요.

## 서비스 버스를 사용하도록 응용 프로그램 구성

서비스 버스를 사용하려면 Node.js azure 패키지를 다운로드합니다. 이 패키지에는 서비스 버스 REST 서비스와 통신하는 라이브러리 집합이 포함되어 있습니다.

### NPM(Node Package Manager)을 사용하여 패키지 가져오기

1.  **PowerShell**(Windows), **Terminal**(Mac), **Bash**(Unix) 등과 같은 명령줄 인터페이스를 사용하여 응용 프로그램 예제를 만든 폴더로 이동합니다.

2.  명령 창에 **npm install azure**를 입력합니다. 그러면
    다음과 같이 출력됩니다.

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

3.  **ls** 명령을 수동으로 실행하여 **node_modules** 폴더가 만들어졌는지 확인할 수 있습니다. 이 폴더에서 서비스 버스 토픽에 액세스하는 데 필요한 라이브러리가 들어 있는 **azure** 패키지를 찾습니다.

### 모듈 가져오기

메모장 또는 다른 텍스트 편집기를 사용하여 다음 코드를 응용 프로그램의 **server.js** 파일 맨 위에 추가합니다.

    var azure = require('azure');

### 서비스 버스 연결 설정

Azure 모듈은 Azure 서비스 버스에 연결하는 데 필요한 정보를 얻기 위해 환경 변수 AZURE_SERVICEBUS_NAMESPACE 및 AZURE_SERVICEBUS_ACCESS_KEY를 읽습니다. 이러한 환경 변수가 설정되지 않은 경우 **createServiceBusService** 호출 시 계정 정보를 지정해야 합니다.

Azure 클라우드 서비스의 구성 파일에서 환경 변수를 설정하는 방법에 대한 예제는 [저장소를 사용하는 Node.js 클라우드 서비스]를 참조하세요.

Azure 웹 사이트의 관리 포털에서 환경 변수를 설정하는 방법에 대한 예제는 [저장소를 사용하는 Node.js 웹 응용 프로그램]를 참조하세요.

## 토픽을 만드는 방법

**ServiceBusService** 개체를 사용하면 토픽으로 작업할 수 있습니다. 다음 코드는 **ServiceBusService** 개체를 만듭니다. 이 코드를 **server.js** 파일 위쪽의 Azure 모듈을 가져오는 문 뒤에 추가합니다.

    var serviceBusService = azure.createServiceBusService();

**ServiceBusService** 개체에 대해 **createTopicIfNotExists**를 호출하면 지정한 토픽이 있는 경우 반환되거나 지정한 이름의 새 토픽이 만들어집니다. 다음 코드는
**createTopicIfNotExists**를 사용하여
'MyTopic'이라는 토픽을 만들거나 해당 토픽에 연결합니다.

    serviceBusService.createTopicIfNotExists('MyTopic',function(error){
        if(!error){
            // Topic was created or exists
            console.log('topic created or exists.');
        }
    });

**createServiceBusService**는 추가 옵션도 지원합니다. 이러한 옵션을 통해 메시지 TTL(Time to Live)이나 최대 토픽 크기 등 기본 토픽 설정을 재정의할 수 있습니다. 다음은 최대 토픽 크기를 5GB, TTL(Time to Live)을 1분으로 설정하는 예제입니다.

    var topicOptions = {
            MaxSizeInMegabytes: '5120',
            DefaultMessageTimeToLive: 'PT1M'
        };

    serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
        if(!error){
            // topic was created or exists
        }
    });

### 필터

**ServiceBusService**를 사용하여 수행되는 작업에 선택적 필터링 작업을 적용할 수 있습니다. 필터링 작업은 로깅, 자동으로 다시 시도 등을 포함할 수 있습니다. 필터는 서명을 사용하여 메서드를 구현하는 개체입니다.

		function handle (requestOptions, next)

요청 옵션에 대한 전처리를 수행한 후 메서드는 다음 서명을 사용하여 콜백을 전달하는 "next"를 호출해야 합니다.

		function (returnObject, finalCallback, next)

이 콜백에서 returnObject(서버에 요청 응답 반환)를 처리한 후 콜백은 next(있는 경우)를 호출하여 다른 필터를 계속 처리하거나 finalCallback을 호출하여 서비스 호출을 종료해야 합니다.

Azure SDK for Node.js에는 재시도 논리를 구현하는 두 필터 **ExponentialRetryPolicyFilter** 및 **LinearRetryPolicyFilter**가 포함되어 있습니다. 다음 코드는 **ExponentialRetryPolicyFilter**를 사용하는 **ServiceBusService** 개체를 만듭니다.

	var retryOperations = new azure.ExponentialRetryPolicyFilter();
	var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);

## 구독을 만드는 방법

토픽 구독도 **ServiceBusService** 개체를 사용하여 만듭니다. 구독에는 이름이 지정되며, 구독의 가상 큐에 전달되는 메시지 집합을 제한하는 선택적 필터가 있을 수 있습니다.

> [AZURE.NOTE] 구독은 영구적이며, 구독 자체 또는 구독과 연결된 토픽이 삭제될 때까지 계속 유지됩니다. 응용 프로그램에 구독을 만들기 위한 논리가 포함된 경우,
**getSubscription** 메서드를 사용하여 구독이 이미 있는지를 먼저 확인해야 합니다.

### 기본(MatchAll) 필터를 사용하여 구독 만들기

**MatchAll** 필터는 새 구독을 만들 때 필터를 지정하지 않은 경우 사용되는 기본 필터입니다. **MatchAll** 필터를 사용하면 토픽에 게시된 모든 메시지가 구독의 가상 큐에 배치됩니다. 다음 예제에서는 'AllMessages' 구독을 만들고 기본 **MatchAll** 필터를 사용합니다.

    serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
        if(!error){
            // subscription created
        }
    });

### 필터를 사용하여 구독 만들기

토픽으로 전송된 메시지 중 특정 토픽 구독 내에 표시되어야 하는 메시지의 범위를 지정하는 필터를 만들 수도 있습니다.

구독에서 지원하는 가장 유연한 유형의 필터는 SQL92 하위 집합을 구현하는
**SqlFilter**입니다. SQL 필터는 토픽에 게시된 메시지의 속성에 적용됩니다. SQL 필터와 함께 사용할 수 있는 식에 대한 자세한 내용은 [SqlFilter.SqlExpression][SqlFilter.SqlExpression]구문을 검토하세요.

**ServiceBusService** 개체의 **createRule** 메서드를 사용하여 구독에 필터를 추가할 수 있습니다. 이 메서드를 사용하면 기존 구독에 새 필터를 추가할 수 있습니다.

> [AZURE.NOTE]

> 기본 필터는 모든 새로운 구독에 자동으로 적용되므로 먼저 기본 필터를 제거해야 합니다. 그렇지 않으면
<strong>MatchAll</strong> 이 사용자가 지정하는 기타 필터를 재정의합니다. 기본 규칙을 제거하려면 <strong>deleteRule</strong> 메서드(
<strong>ServiceBusService</strong> 개체)를 사용합니다.

아래 예제에서는 'HighMessages' 구독을 만듭니다. 이 구독에 포함된
**SqlFilter**는 사용자 지정
**messagenumber** 속성의 값이 3보다 큰 메시지만 선택합니다.

    serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'HighMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber > 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'HighMessages', 
                'HighMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

마찬가지로, 다음 예제에서는 **MessageNumber** 속성이 3보다 작거나 같은 메시지만 선택하는 **SqlFilter**를 사용하여 'LowMessages'라는 구독을 만듭니다.

    serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
        if(!error){
            // subscription created
            rule.create();
        }
    });
    var rule={
        deleteDefault: function(){
            serviceBusService.deleteRule('MyTopic',
                'LowMessages', 
                azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME, 
                rule.handleError);
        },
        create: function(){
            var ruleOptions = {
                sqlExpressionFilter: 'messagenumber <= 3'
            };
            rule.deleteDefault();
            serviceBusService.createRule('MyTopic', 
                'LowMessages', 
                'LowMessageFilter', 
                ruleOptions, 
                rule.handleError);
        },
        handleError: function(error){
            if(error){
                console.log(error)
            }
        }
    }

이제  'MyTopic'으로 전송하는 메시지는 항상  'AllMessages' 토픽 구독에 가입된 수신기로만 배달되며 메시지 내용에 따라 선택적으로  'HighMessages' 및
'LowMessages' 토픽 구독에 가입된 수신기로 배달됩니다.

## 토픽에 메시지를 보내는 방법

서비스 버스 토픽에 메시지를 보내려면 응용 프로그램이 **ServiceBusService** 개체의 **sendTopicMessage** 메서드를 사용해야 합니다. 서비스 버스 토픽으로 전송되는 메시지는 **BrokeredMessage** 개체입니다.
**BrokeredMessage** 개체에는 **Label**, **TimeToLive** 등의 표준 속성 집합과 응용 프로그램별 사용자 지정 속성을 저장하는 데 사용되는 사전, 그리고 문자열 데이터의 본문이 포함되어 있습니다. 응용 프로그램은 문자열 값을 **sendTopicMessage**에 전달하여 메시지의 본문을 설정할 수 있습니다. 그러면 필수 표준 속성에 기본값이 입력됩니다.

아래 예제에서는 5개의 테스트 메시지를 'MyTopic'에 보내는 방법을 보여 줍니다. 루프가 반복될 때마다 각 메시지의 **messagenumber** 속성 값이 변경되며 이 값에 따라 해당 메시지를 받는 구독이 결정됩니다.

    var message = {
        body: '',
        customProperties: {
            messagenumber: 0
        }
    }

    for (i = 0;i < 5;i++) {
        message.customProperties.messagenumber=i;
        message.body='This is Message #'+i;
        serviceBusService.sendTopicMessage(topic, message, function(error) {
          if (error) {
            console.log(error);
          }
        });
    }

서비스 버스 토픽은 256MB의 최대 메시지 크기를 지원합니다(표준 및 사용자 지정 응용 프로그램 속성이 포함된 헤더의 최대 크기는 64MB임). 한 토픽에 저장되는 메시지 수에는 제한이 없지만 한 토픽에 저장되는 총 메시지 크기는 제한됩니다. 이 토픽 크기는 생성 시 정의되며 상한이 5GB입니다.

## 구독에서 메시지를 받는 방법

구독에서 메시지를 받을 때는
**ServiceBusService** 개체의 **receiveSubscriptionMessage** 메서드를 사용합니다. 기본적으로 읽은 메시지는 구독에서 삭제됩니다. 그러나 선택적 매개 변수 **isPeekLock**을 **true**로 설정하여, 구독에서 삭제되지 않도록 메시지를 읽은(peek) 후 잠글 수 있습니다.

받기 작업의 일부로 메시지를 읽고 삭제하는 기본 동작은 가장 단순한 모델이며, 실패할 경우 응용 프로그램이 메시지를 처리하지 않아도 되는 시나리오에서 가장 효과적입니다. 이 모드를 파악하려면 소비자가 수신 요청을 실행했는데 요청이 처리되기 전에 응용 프로그램 작동이 중지되는 시나리오를 고려할 수 있습니다. 서비스 버스는 메시지를 이용되는 것으로 표시하기 때문에 응용 프로그램이 다시 시작되고 메시지 소비를 다시 시작할 경우 크래시 전에 소비된 메시지가 누락됩니다.

**isPeekLock** 매개 변수를 **true**로 설정하면 수신은 2단계 작업이 되므로, 메시지 누락을 허용하지 않는 응용 프로그램을 지원할 수 있습니다. 서비스 버스는 요청을 받으면 소비할 다음 메시지를 찾아서 다른 소비자가 수신할 수 없도록 잠근 후 응용 프로그램에 반환합니다.
응용 프로그램은 메시지 처리를 완료하거나 추가 처리를 위해 안전하게 저장한 후 **deleteMessage** 메서드를 호출하고 삭제할 메시지를 매개 변수로 제공하여 수신 프로세스의 두 번째 단계를 완료합니다. **deleteMessage** 메서드는 메시지를 사용 중인 것으로 표시하고 구독에서 제거합니다.

아래 예제에서는 **receiveSubscriptionMessage**를 사용하여 메시지를 받고 처리하는 방법을 보여 줍니다. 이 예제에서는 먼저  'LowMessages' 구독에서 메시지를 받아서 삭제한 다음  'HighMessages' 구독에서 메시지를
받습니다. 이때 **isPeekLock**을 true로 설정합니다. 그런 다음
**deleteMessage**를 사용하여 메시지를 삭제합니다.

    serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        }
    });
    serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
        if(!error){
            // Message received and locked
            console.log(lockedMessage);
            serviceBusService.deleteMessage(lockedMessage, function (deleteError){
                if(!deleteError){
                    // Message deleted
                    console.log('message has been deleted.');
                }
            }
        }
    });

## 응용 프로그램 작동 중단 및 읽을 수 없는 메시지를 처리하는 방법

서비스 버스는 응용 프로그램 오류나 메시지 처리 문제를 정상적으로 복구하는 데 유용한 기능을 제공합니다. 수신기 응용 프로그램은 메시지를 처리할 수 없는 경우 **ServiceBusService** 개체에 대해 **unlockMessage** 메서드를 호출할 수 있습니다. 그러면 서비스 버스에서 구독 내 메시지의 잠금을 해제하므로 동일한 소비 응용 프로그램이나 다른 소비 응용 프로그램에서 메시지를 다시 받을 수 있습니다.

구독 내에서 잠긴 메시지에는 제한 시간도 연결됩니다. 응용 프로그램 작동이 중단되는 등의 원인으로 인해 응용 프로그램이 잠금 시간 제한이 만료되기 전에 메시지를 처리하지 못하면
서비스 버스가 메시지를 자동으로 잠금 해제하여 다시 받을 수 있도록 설정합니다.

응용 프로그램이 메시지를 처리한 후 **deleteMessage** 메서드를 호출하기 전에 응용 프로그램 작동이 중단되면 응용 프로그램이 다시 시작될 때 메시지가 응용 프로그램으로 다시 배달됩니다. 이러한 과정을 **한 번 이상 처리**라고 합니다. 즉, 각 메시지가 한 번 이상 처리되지만 특정 상황에서는 동일한 메시지가 다시 배달될 수 있습니다. 중복 처리가 허용되지 않는 시나리오에서는 응용 프로그램 개발자가 중복 메시지 배달을 처리하는 논리를 응용 프로그램에 추가해야 합니다. 이 작업은 배달을 여러 번 시도해도 일정하게 유지되는 메시지의
**MessageId** 속성을 사용하여 수행하는 경우가 많습니다.

## 토픽 및 구독을 삭제하는 방법

토픽과 구독은 영구적이므로, Azure 관리 포털 또는 프로그래밍 방식을 통해 명시적으로 삭제해야 합니다.
아래 예제에서는  'MyTopic' 토픽을 삭제하는 방법을 보여 줍니다.

    serviceBusService.deleteTopic('MyTopic', function (error) {
        if (error) {
            console.log(error);
        }
    });

토픽을 삭제하면 토픽에 등록된 모든 구독도 삭제됩니다. 구독을 개별적으로 삭제할 수도 있습니다. 아래 코드는 'HighMessages'
구독을  'MyTopic' 토픽에서 삭제하는 방법을 보여 줍니다.

    serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
        if(error) {
            console.log(error);
        }
    });

## 다음 단계

이제 서비스 버스 토픽의 기본 사항을 익혔으므로 다음 링크를 따라 이동하여 자세한 내용을 확인할 수 있습니다.

-   다음 MSDN 참조를 확인하세요. [큐, 토픽 및 구독][].
-   [SqlFilter][]에 대한 API 참조.
-   GitHub에서 [Azure SDK for Node] 리포지토리를 방문하세요.

  [Azure SDK for Node]: https://github.com/WindowsAzure/azure-sdk-for-node
  [다음 단계]: #nextsteps
  [서비스 버스 토픽 및 구독 정의]: #what-are-service-bus-topics
  [서비스 네임스페이스 만들기]: #create-a-service-namespace
  [네임스페이스에 대한 기본 관리 자격 증명 얻기]: #obtain-default-credentials
  [Node.js 응용 프로그램 만들기]: #Create_a_Nodejs_Application
  [서비스 버스를 사용하도록 응용 프로그램 구성]: #Configure_Your_Application_to_Use_Service_Bus
  [방법: 토픽 만들기]: #How_to_Create_a_Topic
  [방법: 구독 만들기]: #How_to_Create_Subscriptions
  [방법: 토픽에 메시지 보내기]: #How_to_Send_Messages_to_a_Topic
  [방법: 구독에서 메시지 받기]: #How_to_Receive_Messages_from_a_Subscription
  [방법: 응용 프로그램 크래시 및 읽을 수 없는 메시지 처리]: #How_to_Handle_Application_Crashes_and_Unreadable_Messages
  [방법: 토픽 및 구독 삭제]: #How_to_Delete_Topics_and_Subscriptions
  [1]: #Next_Steps
  [Azure 관리 포털]: http://manage.windowsazure.com
  [SqlFilter.SqlExpression]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [큐, 토픽 및 구독]: http://msdn.microsoft.com/library/hh367516.aspx
  [SqlFilter]: http://msdn.microsoft.com/library/windowsazure/microsoft.servicebus.messaging.sqlfilter.aspx
  [WebMatrix를 사용한 웹 사이트]: /develop/nodejs/tutorials/web-site-with-webmatrix/
  [Node.js 클라우드 서비스]: /documentation/articles/cloud-services-nodejs-develop-deploy-app/
  [Node.js 응용 프로그램을 만들어 Azure 웹 사이트에 배포]: /develop/nodejs/tutorials/create-a-website-(mac)/
  [저장소를 사용하는 Node.js 클라우드 서비스]: /develop/nodejs/tutorials/web-app-with-storage/
  [저장소를 사용하는 Node.js 웹 응용 프로그램]: /develop/nodejs/tutorials/web-site-with-storage/

<!--HONumber=47-->
