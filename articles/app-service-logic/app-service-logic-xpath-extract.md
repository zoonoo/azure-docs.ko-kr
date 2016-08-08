<properties
   pageTitle="Azure 앱 서비스의 논리 앱에서 BizTalk XPath 추출기 사용 | Microsoft Azure"
   description="BizTalk XPath 추출기"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="rajram"/>

# BizTalk XPath 추출기

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


BizTalk XPath Extract 커넥터는 앱 조회를 지원하며, 지정된 XPath에 따라 XML 내용에서 데이터를 추출합니다.

## BIzTalk XPath 추출기 사용
1. BizTalk XPath 추출기를 사용하려면 먼저 BizTalk XPath 추출기 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk XPath 추출기 API 앱을 선택하여 수행할 수 있습니다.

	>[AZURE.NOTE] BizTalk Xpath 추출기와 연결된 구성 설정이 없습니다.
2. [새 논리 앱 만들기] 논리 앱 내에서 “트리거 및 동작”을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다.
3. 디자이너에서 오른쪽 창은 흐름을 작성하는데 사용할 수 있는 API 앱을 나열합니다. "BizTalk XPath 추출기"를 찾습니다. 이를 선택하면 Xpath 추출기가 흐름에 추가되고 해당 인스턴스가 프로비전됩니다.
4. 프로비전되면 디자이너에 BizTalk XPath 추출기 API 앱과 관련된 작업이 표시됩니다. ![BIzTalk XPath 추출기 작업 선택][1]

5. "XPath를 사용하여 추출"을 선택합니다. "XPath를 사용하여 추출"은 지정된 입력 XML에서 입력 xpath 식을 평가합니다. ![BIzTalk XPath 추출기 입력][2]

	매개 변수|형식|매개 변수에 대한 설명
---|---|---
XPath|string|Xml 내의 쿼리 경로
Input Xml|string|입력 Xml 내용

작업에서 출력을 문자열(Result)로 반환합니다. Result에는 XML 내의 쿼리 경로 값이 들어 있습니다.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!-- Links -->
[새 논리 앱 만들기]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0727_2016-->