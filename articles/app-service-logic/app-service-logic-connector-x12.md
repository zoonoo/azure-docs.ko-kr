<properties 
   pageTitle="논리 앱에서 BizTalk X12 커넥터 사용 | Microsoft Azure 앱 서비스" 
   description="BizTalk X12 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법" 
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
   ms.date="12/17/2015"
   ms.author="rajram"/>

# BizTalk X12 API 앱 시작 및 논리 앱에 추가
BizTalk X12 서비스를 사용하여 B2B 커뮤니케이션에서 X12 프로토콜에 따라 메시지를 주고받을 수 있습니다. X12는 일반적으로 ASC(공인 표준 위원회) X12라고도 하며 여러 산업에서 널리 사용됩니다.

BizTalk X12 API 앱을 비즈니스 워크플로에 추가하고 논리 앱 내에서 B2B 워크플로의 일부로 데이터를 처리할 수 있습니다.


## 필수 조건
- TPM API 앱: X12 커넥터를 만들기 전에 [BizTalk 거래 업체 관리 커넥터][1]를 만들어야 합니다.
- SQL Azure 데이터베이스: 각 B2B API 앱에는 자체 Azure SQL 데이터베이스가 필요합니다.
- Azure 서비스 버스: 선택 사항이며, 일괄 처리에만 사용됩니다.

## BizTalk X12 커넥터 사용
BizTalk X12 커넥터를 사용하려면 먼저 BizTalk X12 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure 마켓플레이스에서 BizTalk X12 커넥터 API 앱을 선택하여 수행할 수 있습니다.

## BizTalk X12 커넥터 구성
거래 업체는 B2B(Business to Business) 통신과 관련된 엔터티입니다. 두 파트너가 관계를 설정하는 경우 이를 규약이라고 합니다. 정의된 규약은 두 파트너가 수행하려고 하는 통신을 기반으로 하며 특정 프로토콜 또는 전송입니다.

[거래 업체 규약 만들기][2] 단계를 참조하세요.

## 논리 앱 디자이너 화면에서 X12 커넥터 사용
X12 커넥터는 트리거 또는 작업으로 사용할 수 있습니다.

### 트리거
- Azure 논리 앱 흐름 디자이너를 시작합니다.
- 오른쪽 창에서 X12 커넥터를 클릭합니다. ![트리거 설정][3]
- ->를 클릭합니다. ![트리거 옵션][4]
- BizTalk X12 커넥터에 하나의 트리거가 표시됩니다. *일괄 처리 릴리스*를 선택합니다. ![일괄 처리 릴리스 입력][5]
- 이 트리거는 입력이 없습니다. ->를 클릭합니다. ![구성된 일괄 처리 릴리스][6]
- 출력의 일부로 커넥터는 X12 페이로드, 규약 ID뿐 아니라 메시지가 일괄 처리되었는지 아닌지에 대한 정보도 반환합니다.

### 작업
- 오른쪽 창에서 X12 커넥터를 클릭합니다. ![작업 설정][7]
- ->를 클릭합니다. ![작업 목록][8]
- X12 커넥터는 여러 작업을 지원합니다. *인코딩*을 선택합니다. ![입력 인코딩][9]
- 동작에 대한 입력을 제공하고 구성합니다. ![구성된 인코딩][10]

매개 변수|형식|매개 변수에 대한 설명
---|---|---
Content|string|XML 메시지
규약 ID|문자열|규약 ID
메시지 일괄 처리 여부|문자열|메시지 일괄 처리 여부

작업은 X12 페이로드를 포함하는 개체를 반환합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [API 앱 및 커넥터를 관리 및 모니터링](../app-service-api/app-service-api-manage-in-portal.md)을 참조하세요.


<!--References -->
[1]: app-service-logic-connector-tpm.md
[2]: app-service-logic-create-a-trading-partner-agreement.md
[3]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG
[4]: ./media/app-service-logic-connector-x12/ListOfTriggers.PNG
[5]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerInput.PNG
[6]: ./media/app-service-logic-connector-x12/ReleaseBatchTriggerConfigured.PNG
[7]: ./media/app-service-logic-connector-x12/ActionSettings.PNG
[8]: ./media/app-service-logic-connector-x12/ListOfActions.PNG
[9]: ./media/app-service-logic-connector-x12/EncodeInput.PNG
[10]: ./media/app-service-logic-connector-x12/EncodeConfigured.PNG
[11]: ./media/app-service-logic-connector-x12/TriggerSettings.PNG

<!---HONumber=AcomDC_1223_2015-->