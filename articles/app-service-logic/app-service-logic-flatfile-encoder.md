<properties 
   pageTitle="BizTalk 플랫 파일 인코더" 
   description="BizTalk 플랫 파일 인코더" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/01/2015"
   ms.author="rajram"/>

# BizTalk 플랫 파일 인코더

BizTalk 플랫 파일 인코더 커넥터를 사용하여 플랫 파일 데이터(예: excel, csv)와 XML 데이터 간을 상호 운용합니다. 지정된 플랫 파일 인스턴스를 XML로 변환하거나 그 반대로 변환할 수 있습니다.

##BizTalk 플랫 파일 인코더 사용
BizTalk 플랫 파일 인코더를 사용하려면 먼저 BizTalk 플랫 파일 인코더 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk 플랫 파일 인코더 API 앱을 선택하여 수행할 수 있습니다.

###BizTalk 플랫 파일 인코더 구성
BizTalk 플랫 파일 인코더는 스키마를 해당 구성의 일부로 사용합니다. 사용자는 Azure 포털에서 직접 API 앱을 시작하거나 디자이너 화면에 API 앱을 두 번 클릭하여 API 앱 구성 블레이드를 시작할 수 있습니다.

![BizTalk 플랫 파일 인코더 구성][1]

사용자는 API 앱 블레이드에서 *스키마* 파트를 클릭하여 스키마를 구성할 수 있습니다.

![BizTalk 플랫 파일 인코더 스키마 파트][2]

사용자는 디스크에서 스키마를 업로드하거나 플랫 파일 인스턴스 또는 JSON 인스턴스에서 스키마를 생성할 수 있습니다.

![BizTalk 플랫 파일 인코더 스키마 파트][3]


###디자인 화면에서 BizTalk 플랫 파일 인코더 사용
구성이 완료되면 사용자는 *->*를 클릭하고 작업 목록에서 작업을 선택할 수 있습니다.

![BizTalk 플랫 파일 인코더 작업 목록][4]

####Flat File to Xml

![BizTalk 플랫 파일 인코더 작업 목록][5]

매개 변수|형식|매개 변수에 대한 설명
---|---|---
플랫 파일|string|입력 플랫 파일의 내용
Schema Name|string|입력 플랫 파일을 나타내는 스키마의 이름
Root Name|string|플랫 파일 스키마의 루트 노드 이름


작업에서 출력을 문자열(Output Xml)로 반환합니다. Output Xml에는 입력 플랫 파일 내용의 xml 표현이 들어 있습니다.

####Xml to Flat File

![BizTalk 플랫 파일 인코더 작업 목록][6]

매개 변수|형식|매개 변수에 대한 설명
---|---|---
Input Xml|string|입력 Xml 내용

작업에서 출력을 문자열(Flat File)로 반환합니다. 출력에는 입력 xml 내용의 플랫 파일 표현이 들어 있습니다.

<!-- References -->
[1]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ClickToConfigure.PNG
[2]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemasPart.PNG
[3]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.SchemaUpload.PNG
[4]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.ListOfActions.PNG
[5]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.FlatFileToXml.PNG
[6]: ./media/app-service-logic-flatfile-encoder/FlatFileEncoder.XmlToFlatFile.PNG
 

<!---HONumber=Oct15_HO2-->