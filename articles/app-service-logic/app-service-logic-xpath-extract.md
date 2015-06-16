<properties 
   pageTitle="BizTalk XPath 추출기" 
   description="BizTalk XPath 추출기" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="rajram"/>

#BizTalk XPath 추출기

BizTalk XPath Extract 커넥터는 앱 조회를 지원하며, 지정된 XPath에 따라 XML 내용에서 데이터를 추출합니다.

##BIzTalk XPath 추출기 사용
1. BizTalk XPath 추출기를 사용하려면 먼저 BizTalk XPath 추출기 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk XPath 추출기 API 앱을 선택하여 수행할 수 있습니다.

		NOTE: There are no configuration settings associated with BizTalk Xpath Extractor.
2. 디자이너에 BizTalk XPath 추출기 API 앱과 관련된 작업이 표시됩니다.
	
![BIzTalk XPath 추출기 작업 선택][1]

3. "Extract Using XPath" 선택

"Extract Using XPath"는 지정된 입력 Xml에서 입력 xpath 식을 평가합니다.

![BIzTalk XPath 추출기 입력][2]

<table>
	<tr>
		<th>매개 변수</th>
		<th>형식</th>
		<th>매개 변수에 대한 설명</th>
	</tr>
	<tr>
		<td>XPath</td>
		<td>string</td>
		<td>Xml 내의 쿼리 경로</td>
	</tr>
	<tr>
		<td>Input Xml</td>
		<td>string</td>
		<td>입력 Xml 내용</td>
	</tr>
</table>

작업에서 출력을 문자열(Result)로 반환합니다. Result에는 Xml 내의 쿼리 경로 값이 들어 있습니다.

<!-- References -->
[1]: ./media/app-service-logic-xpath-extract/ChooseAction.PNG
[2]: ./media/app-service-logic-xpath-extract/ConfigureInput.PNG

<!---HONumber=58--> 