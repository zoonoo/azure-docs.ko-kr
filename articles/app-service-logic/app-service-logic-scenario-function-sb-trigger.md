<properties
   pageTitle="논리 앱 시나리오: Azure Function 서비스 버스 트리거 | Microsoft Azure"
   description="논리 앱에 대한 서비스 버스 트리거로 Azure Functions를 사용하는 방법을 참조하세요."
   services="app-service\logic,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>
   
# 논리 앱 시나리오: Azure Function 서비스 버스 트리거

장기 실행 수신기 또는 작업을 배포해야 할 때마다 Azure Functions를 논리 앱에 대한 트리거로 활용할 수 있습니다. 예를 들어 큐에서 수신 대기하고 즉시 푸시 트리거로 논리 앱을 실행하는 Azure Function을 만들 수 있습니다.

## 논리 앱 빌드

이 예에서 트리거할 필요가 있는 각 논리 앱에 대해 실행되는 함수를 가집니다. 따라서 먼저 HTTP 요청 트리거를 사용하여 논리 앱을 만들어야 합니다. Azure Function은 큐 메시지가 수신될 때마다 해당 끝점을 호출합니다.

1. 새 논리 앱을 열고 **수동 - HTTP 요청을 받은 경우** 트리거를 선택합니다.  
    * 큐 메시지가 [jsonschema.net](http://jsonschema.net)과 같은 도구를 통해 가지는 JSON 스키마를 필요에 따라 지정하고 트리거에 붙여 넣을 수 있습니다. 이를 통해 디자이너는 데이터의 형태를 이해하고 워크플로를 통해 속성을 쉽게 흐르게 할 수 있습니다.
1. 큐 메시지를 받은 후에 수행하려는 단계를 추가합니다. 예를 들어 Office 365를 통해 전자 메일 메시지를 보낼 수 있습니다.  
1. 논리 앱을 저장하여 이 논리 앱에 트리거에 대한 콜백 URL을 생성합니다. URL은 트리거 카드에 표시됩니다.

![][1]

## Azure Function 빌드

다음으로 트리거로 작동하고 큐에 수신 대기하는 Azure Function을 만들어야 합니다.

1. [Azure Functions 포털](https://functions.azure.com/signin)을 열고 **ServiceBusQueueTrigger - C#** 템플릿을 사용하여 **새 함수**를 선택합니다.

    ![][2]

2. 서비스 버스 큐에 대한 연결을 구성합니다(서비스 버스 SDK `OnMessageReceive()` 수신기 사용).
3. 큐 메시지로 논리 앱 끝점(위에서)을 호출하는 간단한 함수를 작성합니다. 아래는 `application/json`의 메시지 콘텐츠 형식이 있는 함수의 전체 예제이지만 필요에 따라 변경될 수 있습니다.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

[서비스 버스 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)와 같은 도구를 통해 큐 메시지를 추가하여 테스트하고 Function이 메시지를 수신한 후 즉시 논리 앱이 실행되는 것을 확인할 수 있습니다.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

<!---HONumber=AcomDC_0601_2016-->