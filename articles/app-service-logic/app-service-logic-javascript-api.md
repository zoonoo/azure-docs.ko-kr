<properties
   pageTitle="논리 앱에서 JavaScript API 앱 사용 | Microsoft Azure"
   description="JavaScript API 앱 또는 커넥터"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="stepsic"/>

# JavaScript API 앱

>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

JavaScript API 앱은 *논리 앱을 실행하는 동안* 간단한 JavaScript 식을 실행하는 간편한 방법을 제공합니다.

## 이 API 앱을 언제 사용해야 할까요?
이 API 앱의 주요 시나리오는 작성하는 코드의 수명 주기가 논리 앱과 동일하고 다른 시나리오에서는 해당 코드를 호출하지 *않는* 경우입니다.

반면, 논리 앱에 독립적인 수명 주기를 갖는 코드의 재사용 가능한 코드 조각을 원한다면 WebJobs API 앱을 사용하여 간단한 코드 식을 만들어 논리 앱에서 호출해야 합니다.

마지막으로, 추가 패키지를 포함하려는 경우는 JavaScript API 앱을 사용하여 라이브러리를 추가할 수 없으므로 WebJobs API 앱을 사용해야 합니다.

C#에서 식을 작성하는 것을 선호한다면 [C# API 앱](app-service-logic-cs-api.md)을 사용합니다.

## JavaScript API 앱 만들기
JavaScript API 앱을 사용하려면 먼저 JavaScript API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure 마켓플레이스에서 JavaScript API 앱을 선택하여 수행할 수 있습니다.

## 논리 앱 디자이너 화면에서 JavaScript API 앱 사용
### 트리거
논리 앱 서비스가 폴링(사용자가 정의한 간격으로)하여 콘텐츠를 반환하는 경우 논리 앱이 실행되고 그렇지 않으면 다음 폴링 간격에서 다시 확인할 때까지 대기하는 트리거를 만들 수 있습니다.

트리거에 대한 입력은 다음과 같습니다.
- **JavaScript 식** - 평가할 식. 함수 내에서 호출되며 논리 앱이 실행되는 것을 원치 않을 때는 `false`를 반환해야 합니다. 논리 앱이 실행되도록 하려면 그 이외의 값을 반환할 수 있습니다. 그러면 논리 앱 작업에서 응답 내용을 사용할 수 있습니다.
- **컨텍스트 개체** - 트리거에 전달할 수 있는 선택적인 개체입니다. 원하는 만큼 속성을 정의할 수 있지만 최상위 엔터티는 `{ "bar" : 0}` 등의 개체여야 합니다.

예를 들어 해당 시간의 15분과 30분 사이에만 논리 앱을 실행하는 간단한 트리거가 있을 수 있습니다.

```
var d = new Date(); return (d.getMinutes() > 15) && (d.getMinutes() < 30);
```

### 작업

마찬가지로, 실행할 작업을 제공할 수도 있습니다.

작업에 대한 입력은 다음과 같습니다.
- **JavaScript 식** - 평가할 식. 모든 내용을 가져오려면 `return` 문을 포함해야 합니다.
- **컨텍스트 개체** - 트리거에 전달할 수 있는 선택적인 개체입니다. 원하는 만큼 속성을 정의할 수 있지만 최상위 엔터티는 `{ "bar" : 0}` 등의 개체여야 합니다.

예를 들어, Office 365 트리거 **새 전자 메일**을 사용한다고 생각해 보세요. 다음 출력이 반환됩니다.
```
{
	...
	"Attachments" : [
		{
			"name" : "Picture.png",
			"content" : {
				"ContentData" : "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFAQMAAAC3obSmAAAABGdBTUEAALGPC/xhBQAAAAFzUkdCAK7OHOkAAAAGUExURf///wAAAFXC034AAAASSURBVAjXY2BgCGBgYOhgKAAABEIBSWDJEbYAAAAASUVORK5CYII=",
				"ContentType" : "image/png",
				"ContentTransferEncoding" : "Base64"
			}
		},	
		{
			"name" : "File.txt",
			"content" : {
				"ContentData" : "Don't worry, be happy!",
				"ContentType" : "text/plain",
				"ContentTransferEncoding" : "None"
			}
		}	
	]
}
```

그러나 Yammer 게시물에 이러한 첨부 파일을 업로드하기를 원할 수 있습니다. 아쉽게도 Yammer 첨부 파일에 대한 스키마는 약간 다릅니다. 이제 논리 앱 내에서 이를 구문 분석할 수 있습니다. 컨텍스트 개체의 경우 `@triggerBody()`를 전달하고 식의 경우는 다음을 전달합니다.

```
return Attachments.map(function(obj){var a = obj.Content; a.FileName = obj.Name; return a;})
```

작업에서는 함수에서 반환한 JSON을 반환합니다. 따라서 Yammer API 앱에서 **첨부 파일** 속성에 대해 `@body('javascriptapi')`를 참조할 수 있습니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

 

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0803_2016-->