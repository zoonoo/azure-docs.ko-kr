<properties 
   pageTitle="논리 앱에서 AS2 커넥터 사용 | Microsoft Azure 앱 서비스" 
   description="AS2 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법" 
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
   ms.date="11/30/2015"
   ms.author="rajram"/>

# AS2 커넥터 시작 및 논리 앱에 추가
AS2 커넥터를 사용하여 B2B 통신에서 AS2(Applicability Statement 2) 전송 프로토콜을 통해 메시지를 주고받을 수 있습니다. 데이터는 인터넷을 통해 안전하고 안정적으로 전송됩니다. 보안은 디지털 인증서 및 암호화를 사용하여 수행됩니다.

AS2 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 B2B 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 트리거 및 작업
트리거는 파트너의 AS2 메시지 도착과 같은 특정 이벤트를 기반으로 새 인스턴스를 시작합니다. 동작은 AS2 메시지를 받은 후 AS2를 사용하여 메시지를 보내는 것과 같은 결과입니다.

AS2 커넥터는 논리 앱에서 트리거 또는 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. AS2 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | 동작
--- | ---
수신 및 디코딩 | 인코딩 및 보내기

## 시작하기 위한 요구 사항
다음 항목을 AS2 커넥터에서 사용하려면 먼저 사용자가 해당 항목을 만들어야 합니다.

요구 사항 | 설명
--- | ---
TPM API 앱 | AS2 커넥터를 만들기 전에 [BizTalk 거래 업체 관리 커넥터][1]를 만들어야 합니다. <br/><br/>**참고** TPM API 앱의 이름을 확인하세요. 
Azure SQL 데이터베이스 | 파트너, 스키마, 인증서 및 규약을 비롯한 B2B 항목을 저장합니다. 각 B2B API 앱에는 자체 Azure SQL 데이터베이스가 필요합니다.<br/><br/>**참고** 이 데이터베이스에 연결 문자열을 복사합니다.<br/><br/>[Azure SQL 데이터베이스 만들기](../sql-database-get-started.md)
Azure Blob 저장소 컨테이너 | AS2 보관을 사용하도록 설정한 경우 메시지 속성을 저장합니다. AS2 메시지 보관이 불필요한 경우에는 저장소 컨테이너가 필요하지 않습니다. <br/><br/>**참고** 보관을 사용하도록 설정하는 경우 이 Blob 저장소에 연결 문자열을 복사합니다.<br/><br/>[Azure 저장소 계정 정보](../storage-create-storage-account.md).

## AS2 커넥터 만들기

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "AS2 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
4. 다음 패키지 설정을 입력합니다.

	속성 | 설명
--- | --- 
데이터베이스 연결 문자열 | 만든 Azure SQL 데이터베이스에 대한 ADO.NET 데이터베이스 연결 문자열을 입력합니다. 연결 문자열을 복사할 때 암호는 연결 문자열에 추가되지 않습니다. 붙여넣기 전에 연결 문자열에 암호를 입력해야 합니다.
들어오는 메시지에 대해 보관 사용 | 선택 사항입니다. 파트너로부터 받은 들어오는 AS2 메시지의 메시지 속성을 저장하려면 이 속성을 사용하도록 설정합니다. 
Azure Blob 저장소 연결 문자열 | 만든 Azure Blob 저장소 컨테이너에 대한 연결 문자열을 입력합니다. 보관을 사용하도록 설정하는 경우 인코딩 및 디코딩된 메시지는 이 저장소 컨테이너에 저장됩니다.
TPM 인스턴스 이름 | 이전에 만든 **BizTalk 거래 업체 관리** API 앱의 이름을 입력합니다. AS2 커넥터를 만들 때 이 커넥터는 이 특정 TPM 인스턴스 내에서만 AS2 규약을 실행합니다.

5. **만들기**를 선택합니다.

거래 업체는 B2B(Business to Business) 통신과 관련된 엔터티입니다. 두 파트너가 관계를 설정하는 경우 이를 규약이라고 합니다. 정의된 규약은 두 파트너가 수행하려고 하는 통신을 기반으로 하며 특정 프로토콜 또는 전송입니다.

거래 업체 규약을 만드는 단계에 대한 문서는 [여기][2]에 있습니다.

## 커넥터를 트리거로 사용

1. 논리 앱을 만들거나 편집할 때 오른쪽 창에서 만든 AS2 커넥터를 선택합니다. ![트리거 설정][3]

2. 오른쪽 화살표 →를 클릭합니다. ![트리거 옵션][4]

3. AS2 커넥터에 하나의 트리거가 표시됩니다. *수신 및 디코딩*을 선택합니다. ![수신 및 디코딩 입력][5]

4. 이 트리거는 입력이 없습니다. 오른쪽 화살표 →를 클릭합니다. ![수신 및 디코딩 구성][6]

출력의 일부로 커넥터는 AS2 페이로드 뿐 아니라 AS2 특정 메타데이터를 반환합니다.

AS2 페이로드가 https://{Host URL}/decode에 대한 POST로 사용되는 경우 트리거가 수행됩니다. API 앱 설정에서 호스트 URL을 찾을 수 있습니다. 또한 응용 프로그램 설정에서 API 앱의 액세스 수준을 공용(인증 또는 익명)으로 변경해야 합니다.

## 커넥터를 작업으로 사용
1. 트리거(또는 '이 논리를 수동으로 실행'을 선택) 후 오른쪽 창에서 만든 AS2 커넥터를 추가합니다. ![작업 설정][7]

2. 오른쪽 화살표 →를 클릭합니다. ![작업 목록][8]

3. AS2 커넥터는 하나의 동작만 지원합니다. *인코딩 및 송신*을 선택합니다. ![인코딩 및 송신 입력][9]

4. 동작에 대한 입력을 제공하고 다음을 구성합니다. ![인코딩 및 송신 구성][10]

	매개 변수는 다음과 같습니다.

	매개 변수 | 형식 | 설명
--- | --- | ---
Payload | object| 인코딩한 후 구성된 끝점에 게시하기 위한 페이로드 콘텐츠입니다. 페이로드는 JSON 개체로 제공해야 합니다.
AS2 From | string | AS2 메시지를 보낸 사람의 AS2 ID입니다. 이 매개 변수는 메시지를 보내기 위한 적절한 계약을 조회하는 데 사용됩니다.
AS2 To | string | AS2 메시지를 받는 사람의 AS2 ID입니다. 이 매개 변수는 메시지를 보내기 위한 적절한 계약을 조회하는 데 사용됩니다.
Partner URL | string | 메시지를 보내야 하는 파트너의 끝점입니다.
Enable Archiving | boolean | 아웃바운드 메시지를 보관해야 하는지를 결정합니다.

작업이 성공적으로 완료되면 HTTP 200 응답 코드를 반환합니다.

## 커넥터의 추가 기능
논리 앱에 대한 자세한 내용은 [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

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

<!---HONumber=AcomDC_1203_2015-->