<properties
   pageTitle="Azure 앱 서비스의 논리 앱에서 BizTalk XML 유효성 검사기 사용 | Microsoft Azure"
   description="논리 앱에서 BizTalk XML 유효성 검사기를 사용하여 스키마 유효성 검사"
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

# BizTalk XML 유효성 검사기

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

앱에서 BizTalk XML 유효성 검사기 커넥터를 사용하여 미리 정의된 XML 스키마에 대해 XML 데이터의 유효성을 검사합니다. 사용자는 기존 스키마를 사용하거나 플랫 파일 인스턴스, JSON 인스턴스 또는 기존 커넥터에 따라 스키마를 생성할 수 있습니다.

## BizTalk XML 유효성 검사기 사용
BizTalk XML 유효성 검사기를 사용하려면 먼저 BizTalk XML 유효성 검사기 API 앱의 인스턴스를 만듭니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk XML 유효성 검사기 API 앱을 선택하여 수행할 수 있습니다.

### BizTalk XML 유효성 검사기 구성
BizTalk XML 유효성 검사기는 스키마를 해당 구성의 일부로 사용합니다. 사용자는 Azure 포털에서 직접 API 앱을 시작하거나 디자이너 화면에 API 앱을 두 번 클릭하여 API 앱 구성 블레이드를 시작할 수 있습니다.

![BizTalk XML 유효성 검사기 구성][1]

사용자는 API 앱 블레이드에서 *스키마*를 선택하여 스키마를 구성할 수 있습니다.

![BizTalk XML 유효성 검사기 스키마 파트][2]

사용자는 디스크에서 스키마를 업로드하거나 플랫 파일 인스턴스 또는 JSON 인스턴스에서 스키마를 생성할 수 있습니다.

![BizTalk XML 유효성 검사기 스키마][3]


### 디자인 화면에서 BizTalk 플랫 파일 인코더 사용
구성이 완료되면 사용자는 *->*를 선택하고 작업 목록에서 작업을 선택할 수 있습니다.

![BizTalk XML 유효성 검사기 동작 목록][4]

#### Xml 유효성 검사

Xml 유효성 검사 작업은 미리 구성된 스키마에 대해 지정된 XML 입력의 유효성을 검사합니다.

![BizTalk XML 유효성 검사기 Xml 유효성 검사][5]

매개 변수|형식|매개 변수에 대한 설명
---|---|---
Input Xml|string|유효성을 검사할 입력 Xml

작업에서 출력을 개체로 반환합니다. 출력은 XML 유효성 검사기에서 응답을 나타내는 모델을 포함합니다. 이는 결과, 스키마 이름, 루트 노드 및 오류 설명으로 이루어져 있습니다.


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0727_2016-->