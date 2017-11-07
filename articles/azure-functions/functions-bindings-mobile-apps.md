---
title: "Azure Functions Mobile Apps 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure 모바일 앱 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-mobile-apps-bindings"></a>Azure Functions 모바일 앱 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 Mobile Apps에 대한 입력 및 출력 바인딩을 지원합니다.

Mobile Apps 입력 및 출력 바인딩을 사용하면 모바일 앱에서 [데이터 테이블에서 읽고 쓸](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) 수 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Mobile Apps 입력 바인딩
Mobile Apps 입력 바인딩은 모바일 테이블 끝점에서 레코드를 로드하여 함수에 전달합니다. C# 및 F# 함수에서 함수가 성공적으로 종료되면 레코드에 변경한 내용을 자동으로 다시 테이블에 전송합니다.

함수에 대한 Mobile Apps 입력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

다음 사항에 유의하세요.

* `id`는 정적이거나 함수를 호출하는 트리거를 기반으로 할 수 있습니다. 예를 들어, 함수에 대해 [큐 트리거]()를 사용하는 경우 `"id": "{queueTrigger}"`는 검색할 레코드 ID로 큐 메시지의 문자열 값을 사용합니다.
* `connection`에는 모바일 앱의 URL이 포함되어 있는 함수 앱의 앱 설정 이름이 포함되어야 합니다. 함수는 이 URL을 사용하여 모바일 앱에 대해 필요한 나머지 작업을 구성합니다. 모바일 앱의 URL(`http://<appname>.azurewebsites.net`과 유사)을 포함하는 [함수 앱의 앱 설정을 만들고](), 다음으로 입력 바인딩의 `connection` 속성에서 앱 설정의 이름을 지정합니다. 
* [Node.js 모바일 앱 백 엔드에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)하거나 [.NET 모바일 앱 백 엔드에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)한 경우 `apiKey`를 지정해야 합니다. 이렇게 하려면 API 키가 포함된 [함수 앱의 앱 설정을 만들고](), 다음으로 `apiKey` 속성을 앱 설정의 이름과 함께 입력 바인딩에 추가합니다. 
  
  > [!IMPORTANT]
  > 이 API 키는 모바일 앱 클라이언트와 공유해서는 안 됩니다. Azure Functions처럼 서비스 측 클라이언트에게만 안전하게 배포되어야 합니다. 
  > 
  > [!NOTE]
  > Azure Functions는 사용자의 소스 제어 리포지토리를 확인하지 않도록 사용자 연결 정보 및 API 키를 앱 설정으로 저장합니다. 이는 사용자의 중요한 정보를 보호합니다.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>입력 사용
이 섹션에서는 함수 코드에서 Mobile Apps 입력 바인딩을 사용하는 방법을 보여 줍니다. 

지정된 테이블 및 레코드 ID를 사용하는 레코드가 발견되면 명명된 [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) 매개 변수로 전달됩니다(또는 Node.js에서는 `context.bindings.<name>` 개체로 전달됨). 레코드가 없는 경우 매개 변수는 `null`입니다. 

C# 및 F# 함수에서 함수가 성공적으로 종료되면 입력 레코드에 변경한 내용(입력 매개 변수)을 자동으로 다시 Mobile Apps 테이블에 전송합니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 레코드에 액세스합니다. Node.js에서 레코드를 수정할 수 없습니다.

<a name="inputsample"></a>

## <a name="input-sample"></a>입력 샘플
큐 트리거 메시지의 Id로 Mobile App 테이블 레코드를 검색하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
],
"disabled": false
}
```

바인딩에서 입력 레코드를 사용하는 언어별 샘플을 참조하세요. C# 및 F # 샘플은 레코드의 `text` 속성도 수정합니다.

* [C#](#inputcsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C#의 입력 샘플 #

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js에서 입력 샘플

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Mobile Apps 출력 바인딩
Mobile Apps 출력 바인딩을 사용하여 Mobile Apps 테이블 끝점에 새 레코드를 작성할 수 있습니다.  

함수에 대한 Mobile Apps 출력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

다음 사항에 유의하세요.

* `connection`에는 모바일 앱의 URL이 포함되어 있는 함수 앱의 앱 설정 이름이 포함되어야 합니다. 함수는 이 URL을 사용하여 모바일 앱에 대해 필요한 나머지 작업을 구성합니다. 모바일 앱의 URL(`http://<appname>.azurewebsites.net`과 유사)을 포함하는 [함수 앱의 앱 설정을 만들고](), 다음으로 입력 바인딩의 `connection` 속성에서 앱 설정의 이름을 지정합니다. 
* [Node.js 모바일 앱 백 엔드에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)하거나 [.NET 모바일 앱 백 엔드에 API 키를 구현](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)한 경우 `apiKey`를 지정해야 합니다. 이렇게 하려면 API 키가 포함된 [함수 앱의 앱 설정을 만들고](), 다음으로 `apiKey` 속성을 앱 설정의 이름과 함께 입력 바인딩에 추가합니다. 
  
  > [!IMPORTANT]
  > 이 API 키는 모바일 앱 클라이언트와 공유해서는 안 됩니다. Azure Functions처럼 서비스 측 클라이언트에게만 안전하게 배포되어야 합니다. 
  > 
  > [!NOTE]
  > Azure Functions는 사용자의 소스 제어 리포지토리를 확인하지 않도록 사용자 연결 정보 및 API 키를 앱 설정으로 저장합니다. 이는 사용자의 중요한 정보를 보호합니다.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>출력 사용
이 섹션에서는 함수 코드에서 Mobile Apps 출력 바인딩을 사용하는 방법을 보여 줍니다. 

C# 함수에서 `out object` 형식의 명명된 출력 매개 변수를 사용하여 출력 레코드에 액세스합니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력 레코드에 액세스합니다.

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
큐 트리거 및 Mobile Apps 출력을 정의하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

큐 메시지의 내용으로 Mobile Apps 테이블 끝점에 레코드를 작성하는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C#에서 출력 샘플 #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js에서 출력 샘플

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

