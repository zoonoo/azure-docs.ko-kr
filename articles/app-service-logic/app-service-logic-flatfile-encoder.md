<properties 
   pageTitle="논리 앱에서 BizTalk 플랫 파일 인코더 사용 | Microsoft Azure" 
   description="BizTalk 플랫 파일 인코더 API 앱 또는 커넥터" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
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

# BizTalk 플랫 파일 인코더

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

BizTalk 플랫 파일 인코더 커넥터를 사용하여 플랫 파일 데이터(예: Excel 또는 CSV 파일)와 XML 데이터 간을 상호 운용합니다. 지정된 플랫 파일 인스턴스를 XML로 변환하거나 그 반대로 변환할 수 있습니다.

##BizTalk 플랫 파일 인코더 사용
BizTalk 플랫 파일 인코더를 사용하려면 먼저 BizTalk 플랫 파일 인코더 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 논리 앱을 만드는 동안 인라인으로 수행하거나 Azure Marketplace에서 BizTalk 플랫 파일 인코더 API 앱을 선택하여 수행할 수 있습니다. Azure 마켓플레이스에서 BizTalk 플랫 파일 인코더 API 앱을 만드는 단계는 다음과 같습니다.
1. Azure 포털(http://portal.azure.com)에 로그인  
2. 새로 만들기 > 마켓플레이스 > 모두 선택  
3. 검색 상자에서 "BizTalk 플랫 파일 인코더" 검색  
4. 결과 목록에서 BizTalk 플랫 파일 인코더 선택  
5. "Create"을 선택한 다음 이름 및 기타 필요한 세부 설정 제공  
6. "만들기" 선택 만들기 진행 상태를 볼 수 있는 시작 페이지로 리디렉션됩니다. 완료하려면 시간이 걸릴 수 있습니다. 작업이 완료될 때 알림을 받습니다.  

###BizTalk 플랫 파일 인코더 구성
앱 API를 만든 후에 논리 앱을 만들 때 Azure 포털 시작 페이지 또는 디자이너 화면에서 직접 시작할 수 있습니다.

Azure 시작 페이지에서 시작하려면 만들 때 BizTalk 플랫 파일 인코더에 지정한 이름을 입력하여 검색할 수 있습니다. 다음으로 이 작업을 수행합니다.
1. Auzre 포털에서 검색 상자에 BizTalk 플랫 파일 인코더의 이름을 입력 및 검색  
2. 다음으로 목록에서 BizTalk 플랫 파일 인코더 선택 그러면 BizTalk 플랫 파일 인코더 API 앱을 구성할 수 있는 API 앱 블레이드가 열립니다. 구성을 시작하려면 다음으로 스키마를 추가합니다.  
1. "스키마" 구성 요소 ![BizTalk 플랫 파일 인코더 스키마 파트][2]를 선택합니다.  
2. 그런 다음 열린 스키마 블레이드에서 "새로 추가" 선택 ![BizTalk 플랫 파일 인코더 작업 목록][7]  
3. 세 옵션 중 하나를 선택하여 스키마 제공 옵션은 새 스키마 업로드, JSON에서 생성 및 플랫 파일에서 생성 ![BizTalk 플랫 파일 인코더 작업 목록][8]  
4. 이전 단계에서 선택한 항목에 기반하여 스키마를 제공하는 단계 수행 그런 다음 스키마가 업로드된 것을 확인합니다.![BizTalk 플랫 파일 인코더 작업 목록][9]

###디자인 화면에서 BizTalk 플랫 파일 인코더 사용
이제 Biztalk 플랫 파일 인코더를 구성했으므로 논리 앱에서 사용해 봅니다. 시작하려면 새 논리 앱을 만들거나 미리 만든 기존 논리 앱을 시작한 다음 이 단계를 수행합니다.
1. '논리 시작' 카드에서 '이 논리를 수동으로 실행'을 클릭합니다.  
2. 갤러리에서 이전에 만든 BizTalk 플랫 파일 인코더 API 앱을 선택합니다.(화면 오른쪽의 API 앱 목록에서 만든 BizTalk 플랫 파일 인코더를 찾을 수 있습니다.)  
3. 검정색 오른쪽 화살표를 선택합니다. 두 가지 사용 가능한 작업(Flat File to Xml 및 Xml to Flat File)이 표시됩니다. ![BizTalk 플랫 파일 인코더 작업 목록][1] ![BizTalk 플랫 파일 인코더 작업 목록][4]

아래 단계에 따라 작업을 기반으로 선택합니다.

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
[7]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.addschema.PNG
[8]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.selectschemauploadoption.PNG
[9]: ./media/app-service-logic-flatfile-encoder/flatfileencoder.shemauploaded.PNG

 

<!---HONumber=AcomDC_0420_2016-->