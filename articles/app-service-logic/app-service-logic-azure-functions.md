---
title: "논리 앱과 함께 Azure Functions 사용 | Microsoft Docs"
description: "논리 앱과 함께 Azure Functions를 사용하는 방법 확인"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: dwrede
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 37e246ddb362c3b19db988224a5e6df4e41bd1e7


---
# <a name="using-azure-functions-with-logic-apps"></a>논리 앱과 함께 Azure Functions 사용
논리 앱에서 Azure Functions를 사용하여 C# 또는 node.js의 사용자 지정 코드 조각을 실행할 수 있습니다.  [Azure Functions](../azure-functions/functions-overview.md) 는 Microsoft Azure에서 서버 없는 계산을 제공합니다. 다음 작업을 수행하는 데 유용합니다.

* 고급 서식 지정 또는 논리 앱 내에서 필드의 계산
* 워크플로 내에서 계산 수행
* C# 또는 node.js에서 지원되는 함수로 논리 앱 기능 확장

## <a name="create-a-function-for-logic-apps"></a>논리 앱용 함수 만들기
**일반 웹후크 - 노드** 또는 **일반 웹후크 - C#** 템플릿을 사용하여 Azure Functions 포털에서 새 함수를 만드는 것이 좋습니다. 이렇게 하면 논리 앱에서 `application/json` 을 수락하는 템플릿이 자동으로 채워집니다.  Azure Functions에서 **통합** 탭을 선택하는 경우 **모드**를 **웹후크** 및 **일반 JSON**의 **Webhook 유형**으로 설정해야 합니다.  이러한 템플릿을 사용하는 함수는 논리 앱 디자이너의 **내 영역에서 Azure Functions**

WebHook 함수는 요청을 수락하고 `data` 변수를 통해 메서드를 제공합니다. `data.foo`와 같은 점 표기법을 사용하여 페이로드 속성에 액세스할 수 있습니다.  예를 들어 DateTime 값을 날짜 문자열로 변환하는 간단한 JavaScript 함수는 다음 예와 같습니다.

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>논리 앱에서 Azure Functions 호출
디자이너에서 **작업** 메뉴를 클릭하면 **내 영역의 Azure Functions**를 선택할 수 있습니다.  이렇게 하면 구독의 컨테이너가 나열되고 호출할 함수를 선택할 수 있습니다.  

함수를 선택하면 입력 페이로드 개체를 지정하라는 메시지가 표시됩니다. 이 메시지는 논리 앱이 함수로 보내는 메시지로, JSON 개체여야 합니다. 예를 들어 salesforce 트리거에서 **마지막으로 수정한 날짜** 를 전달하려는 경우 함수 페이로드는 다음과 같을 수 있습니다.

![마지막으로 수정된 날짜][1]

## <a name="trigger-logic-apps-from-a-function"></a>함수에서 논리 앱 트리거
함수 내에서 논리 앱을 트리거할 수도 있습니다.  이렇게 하려면 수동 트리거를 사용하여 논리 앱을 만들면 됩니다. 자세한 내용은 [호출 가능 끝점인 논리 앱](app-service-logic-http-endpoint.md)을 참조하세요.  그러면 함수 내에서 논리 앱으로 보낼 페이로드를 사용하여 수동 트리거 URL로의 HTTP POST를 생성합니다.

### <a name="create-a-function-from-the-designer"></a>디자이너에서 함수 만들기
디자이너 내에서 node.js WebHook 함수를 만들 수도 있습니다. 먼저 **내 영역의 Azure Functions** 를 선택하고 함수에 대한 컨테이너를 선택합니다.  컨테이너가 없으면 [Azure Functions 포털](https://functions.azure.com/signin)에서 만들어야 합니다. 그런 다음 **새로 만들기**를 선택합니다.  

계산하려는 데이터를 기준으로 템플릿을 생성하려면 함수에 전달할 컨텍스트 개체를 지정합니다. 이것은 JSON 개체여야 합니다. 예를 들어 FTP 작업의 파일 콘텐츠를 전달하는 경우 상황에 맞는 페이로드는 다음과 같습니다.

![상황에 맞는 페이로드][2]

> [!NOTE]
> 이 개체는 문자열로 캐스팅되지 않았으므로 콘텐츠가 JSON 페이로드에 직접 추가됩니다. 하지만 JSON 토큰이 아니면(즉, 문자열 또는 JSON 개체/배열) 오류입니다. 문자열로 캐스팅하려면 이 문서의 첫 번째 그림에 나와 있는 것처럼 따옴표를 추가하면 됩니다.
> 
> 

그러면 디자이너는 인라인으로 만들 수 있는 함수 템플릿을 생성합니다. 변수는 함수에 전달하려는 컨텍스트를 기준으로 미리 생성됩니다.

<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png



<!--HONumber=Nov16_HO3-->


