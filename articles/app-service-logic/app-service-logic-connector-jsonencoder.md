<properties
   pageTitle="논리 앱에서 BizTalk JSON 인코더 사용 | Microsoft Azure 앱 서비스"
   description="BizTalk JSON 인코더 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="rajram"/>

# BizTalk JSON 인코더 시작 및 논리 앱에 추가 

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


BizTalk JSON Encode Decode 커넥터를 사용하면 JSON 및 XML 데이터 간에 사용자 앱을 상호 운용할 수 있습니다. 이것은 주어진 JSON 인스턴스를 XML로 변환하며 그 반대로 변환할 수도 있습니다.

BizTalk JSON 인코더를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## BizTalk JSON 인코더 사용
BizTalk JSON 인코더를 사용하려면 먼저 BizTalk JSON 인코더 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk JSON 인코더 API 앱을 선택하여 수행할 수 있습니다.

## 논리 앱 디자이너 화면에서 BizTalk JSON 인코더 사용
[논리 앱 만들기] 단계를 따르세요. BizTalk JSON 인코더는 작업으로 사용할 수 있습니다. 트리거가 필요하지 않습니다.

### 작업
- 오른쪽 창에서 BizTalk JSON 인코더 클릭

	![작업 설정][3]
- ->를 클릭합니다.

	![작업 목록][4]
- BizTalk JSON 인코더는 두 가지 동작을 지원합니다. *Xml을 JSON으로* 선택

	![Xml에서 JSON으로 입력][5]
- 작업에 대한 입력 제공 및 구성

	![인코딩 및 송신 구성][6]

매개 변수|형식|매개 변수에 대한 설명
---|---|---
Input Xml|object|입력 Xml 내용
외부 봉투 제거|string|Xml 콘텐츠에서 루트 노드를 제거하도록 플래그 설정

이 작업은 입력 콘텐츠의 JSON 표현을 반환합니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 흐름에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE] Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

 

<!--References -->
[1]: app-service-logic-connector-tpm
[2]: app-service-logic-create-a-trading-partner-agreement
[3]: ./media/app-service-logic-json-encoder/ActionSettings.PNG
[4]: ./media/app-service-logic-json-encoder/ListOfActions.PNG
[5]: ./media/app-service-logic-json-encoder/EncodeInput.PNG
[6]: ./media/app-service-logic-json-encoder/EncodeConfigured.PNG

<!--Links -->
[논리 앱 만들기]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0420_2016-->