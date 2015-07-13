<properties 
   pageTitle="BizTalk JSON 인코더" 
   description="BizTalk JSON 인코더" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="06/14/2015"
   ms.author="rajram"/>

#BizTalk JSON 인코더
BizTalk JSON Encode Decode 커넥터를 사용하면 JSON 및 XML 데이터 간에 사용자 앱을 상호 운용할 수 있습니다. 이것은 주어진 JSON 인스턴스를 XML로 변환하며 그 반대로 변환할 수도 있습니다.

##BizTalk JSON 인코더 사용
BizTalk JSON 인코더를 사용하려면 먼저 BizTalk JSON 인코더 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk JSON 인코더 API 앱을 선택하여 수행할 수 있습니다.

##논리 앱 디자이너 화면에서 BizTalk JSON 인코더 사용
BizTalk JSON 인코더는 작업으로 사용할 수 있습니다. 트리거가 필요하지 않습니다.

###작업
- 오른쪽 창에서 BizTalk JSON 인코더 클릭

	![작업 설정][3]
- 클릭 ->

	![작업 목록][4]
- BizTalk JSON 인코더는 하나의 작업만 지원합니다. *Xml을 JSON으로* 선택

	![Xml에서 JSON으로 입력][5]
- 작업에 대한 입력 제공 및 구성

	![인코딩 및 송신 구성][6]

<table>
	<tr>
		<th>매개 변수</th>
		<th>형식</th>
		<th>매개 변수에 대한 설명</th>
	</tr>
	<tr>
		<td>Input Xml</td>
		<td>object</td>
		<td>입력 Xml 내용</td>
	</tr>
	<tr>
		<td>외부 봉투 제거</td>
		<td>string</td>
		<td>Xml 콘텐츠에서 루트 노드를 제거하도록 플래그 설정</td>
	</tr>
</table>

이 작업은 입력 콘텐츠의 JSON 표현을 반환합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참고합니다.

커넥터의 성능 통계 및 제어 보안을 검토할 수 있습니다. [API 앱 및 커넥터를 관리 및 모니터링](../app-service-api/app-service-api-manage-in-portal.md)을 참고합니다.

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!---HONumber=62-->