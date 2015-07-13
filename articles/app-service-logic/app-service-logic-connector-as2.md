<properties 
   pageTitle="AS2 커넥터" 
   description="AS2 커넥터" 
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

#AS2 커넥터
Microsoft Azure AS2 커넥터는 B2B 통신에 AS2 전송 프로토콜을 통하여 메시지를 주고 받을 수 있도록 합니다. AS2는 Applicability Statement 2의 약어입니다. 데이터는 인터넷을 통해 안전하고 안정적으로 전송됩니다. 보안은 디지털 인증서 및 암호화를 사용하여 수행됩니다.

##필수 구성 요소
- TPM API 앱: AS2 커넥터를 만들기 전에 [BizTalk 거래 업체 관리 커넥터][1]를 만들어야 합니다.
- SQL Azure 데이터베이스: 각 B2B API 앱에는 자체 Azure SQL 데이터베이스가 필요합니다.
- Azure Blob 저장소 컨테이너: AS2 보관을 사용하도록 설정한 경우 메시지 속성을 저장합니다. AS2 메시지 보관이 불필요한 경우에는 저장소 컨테이너가 필요하지 않습니다. 

##AS2 커넥터 사용
AS2 커넥터를 사용하려면 먼저 AS2 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure 마켓플레이스에서 AS2 커넥터 API 앱을 선택하여 수행할 수 있습니다.

##AS2 커넥터 구성
거래 업체는 B2B(Business to Business) 통신과 관련된 엔터티입니다. 두 파트너가 관계를 설정하는 경우 이를 규약이라고 합니다. 정의된 규약은 두 파트너가 수행하려고 하는 통신을 기반으로 하며 특정 프로토콜 또는 전송입니다.

거래 업체 규약을 만드는 단계에 대한 문서는 [여기][2]에 있습니다.

##논리 앱 디자이너 화면에서 AS2 커넥터 사용
AS2 커넥터는 트리거 또는 작업으로 사용할 수 있습니다.

###트리거
- Azure 논리 앱 흐름 디자이너를 시작합니다.
- 오른쪽 창에서 AS2 커넥터를 클릭합니다.

	![트리거 설정][3]
- ->를 클릭합니다.

	![트리거 옵션][4]
- AS2 커넥터에 하나의 트리거가 표시됩니다. *수신 및 디코딩*을 선택합니다.

	![수신 및 디코딩 입력][5]
- 이 트리거는 입력이 없습니다. ->를 클릭합니다.

	![수신 및 디코딩 구성][6]
- 출력의 일부로 커넥터는 AS2 페이로드 뿐 아니라 AS2 특정 메타데이터를 반환합니다.

###작업
- 오른쪽 창에서 AS2 커넥터를 클릭합니다.

	![작업 설정][7]
- ->를 클릭합니다.

	![작업 목록][8]
- AS2 커넥터는 하나의 작업만 지원합니다. *인코딩 및 송신*을 선택합니다.

	![인코딩 및 송신 입력][9]
- 작업에 대한 입력 제공 및 구성

	![인코딩 및 송신 구성][10]

<table>
	<tr>
		<th>매개 변수</th>
		<th>형식</th>
		<th>매개 변수에 대한 설명</th>
	</tr>
	<tr>
		<td>Payload</td>
		<td>object</td>
		<td>페이로드</td>
	</tr>
	<tr>
		<td>AS2 From</td>
		<td>string</td>
		<td>AS2 보낸 사람</td>
	</tr>
	<tr>
		<td>AS2 To</td>
		<td>string</td>
		<td>AS2 받는 사람</td>
	</tr>
	<tr>
		<td>Partner URL</td>
		<td>string</td>
		<td>파트너 URL</td>
	</tr>
	<tr>
		<td>Enable Archiving</td>
		<td>부울</td>
		<td>보관 사용</td>
	</tr>
</table>

작업이 성공적으로 완료되면 HTTP 200 응답 코드를 반환합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

커넥터의 성능 통계 및 제어 보안을 검토할 수 있습니다. [API 앱 및 커넥터를 관리 및 모니터링](../app-service-api/app-service-api-manage-in-portal.md)을 참조하세요.

<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-as2/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-as2/TriggerOptions.PNG
[5]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeInput.PNG
[6]: ./media/app-service-logic-connector-as2/ReceiveAndDecodeConfigured.PNG
[7]: ./media/app-service-logic-connector-as2/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-as2/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-as2/EncodeAndSendInput.PNG
[10]: ./media/app-service-logic-connector-as2/EncodeAndSendConfigured.PNG

<!---HONumber=62-->