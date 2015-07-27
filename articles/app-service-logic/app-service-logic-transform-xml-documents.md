<properties 
	pageTitle="BizTalk 변환" 
	description="XML 문서를 한 스키마에서 다른 스키마로 변환하는 방법을 알아봅니다." 
	authors="anuragdalmia" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/30/2015"
	ms.author="anuragdalmia"/>

#BizTalk 변환


## 개요
간단히 설명하면 BizTalk 변환 API 앱은 데이터를 한 형식에서 다른 형식으로 변환합니다. 예를 들어 구매 주문에서 배송 및 청구 주소를 가져와 송장 문서에 삽입할 수 있습니다. 또는 *YearMonthDay* 형식의 현재 날짜가 포함된 들어오는 메시지가 있을 수 있습니다. 날짜를 *MonthDayYear* 형식으로 변경하려고 합니다.

Microsoft Azure 앱 서비스의 변환 API 앱을 사용하여 이 작업을 할 수 있습니다. 변환 또는 맵은 원본 XML 스키마(입력)와 대상 XML 스키마(출력)로 구성됩니다. 다양한 기본 제공 함수를 사용하여 문자열 조작, 조건부 할당, 산술 식, 날짜 시간 포맷터, 루핑 구문 등을 비롯한 데이터를 조작하거나 제어할 수 있습니다.

맵은 Visual Studio에서 [Microsoft Azure BizTalk 서비스 SDK](http://www.microsoft.com/download/details.aspx?id=39087)를 사용하여 만들어집니다. 맵 생성과 테스트가 완료되면 맵(.trfm)을 BizTalk 변환 API 앱으로 업로드합니다.

추가 기능은 다음과 같습니다.

- 맵에서 만든 변환은 이름과 주소를 한 문서에서 다른 문서로 복사하는 것과 같이 단순할 수 있습니다. 또는 기본 맵 작업을 사용하여 더 복잡한 변환을 만들 수 있습니다.
- 문자열, 날짜 시간 함수 등 여러 맵 작업이나 함수를 바로 사용할 수 있습니다.
- 스키마 간에 직접 데이터 복사를 수행할 수 있습니다. BizTalk 매퍼에서 원본 스키마의 요소를 대상 스키마의 대응 항목과 연결하는 선을 그리기만 하면 됩니다.
- 맵을 만들 때 만들어진 모든 관계와 링크 표시를 포함하여 맵의 그래픽 표현이 표시됩니다.
- **맵 테스트** 기능을 사용하여 샘플 XML 메시지를 추가합니다. 한 번만 클릭하면 만든 맵을 테스트하고 생성된 출력을 확인할 수 있습니다.
- 기존 Azure BizTalk 서비스 맵(.trfm)을 업로드하고 변환 API 앱의 모든 이점을 활용합니다.
- 지도를 만들 때, 스키마를 추가할 필요가 없습니다. 지도가 준비되면, 변환 API 앱에 추가합니다. 이제 모든 준비가 되었습니다. 
- XML 형식 지원을 포함합니다.


## 커넥터 API 앱에서 스키마 다운로드
API 앱 통계 페이지에서 SQL, SAP 및 SharePoint와 같은 커넥터에 대한 XML 스키마를 다운로드할 수 있습니다. 예를 들어 특정 XAP 커넥터 API 앱에 대한 XML 스키마를 다운로드하려면 API 앱을 찾아서 요약 페이지를 엽니다. **스키마 다운로드**를 선택하면 SAP 작업에 해당하는 모든 스키마가 포함된 zip 파일이 컴퓨터로 다운로드됩니다. 스키마를 사용하여 Visual Studio에서 맵(.trfm)을 작성할 수 있습니다.


## 맵 만들기 및 추가
변한 또는 맵은 Visual Studio에서 무료 다운로드인 [Microsoft Azure BizTalk 서비스 SDK](http://www.microsoft.com/download/details.aspx?id=39087)를 사용하여 만들어집니다.

맵을 만드는 방법에 대한 자세한 내용은 [Visual Studio에서 맵 만들기](http://aka.ms/createamapinvs)를 참조하세요. 맵이 만들어지고 프로덕션에 사용할 준비가 되면 Azure 관리 포털에서 만든 BizTalk 변환 API 앱에 맵(.trfm 파일)을 추가할 수 있습니다.

맵이 업로드된 후 변경되거나 수정되면 업데이트된 맵을 업로드할 수 있으며, 업데이트된 맵이 변환 API 앱의 기존 맵을 대체합니다.

1.	Azure 관리 포털(화면 왼쪽)에서 **찾아보기**를 선택한 다음 **API 앱**을 선택합니다. **API 앱**이 표시되지 않는 경우 **모두**를 선택한 다음 사용 가능 목록에서 **API 앱**을 선택합니다.

	![][7]

2.	Azure 구독에서 만든 모든 **API 앱** 목록이 표시됩니다.

	![][8]

3.	이전 섹션에서 만든 BizTalk 변환 API 앱을 선택합니다.

4.	API 앱에 대한 구성 블레이드가 열립니다. 구성 요소 섹션에서 **맵**을 확인할 수 있습니다.

	![][9]

5.	**맵**을 선택하여 맵 목록이 포함된 새 블레이드를 엽니다.

6.	맨 위에 있는 **맵 추가** 아이콘을 선택하여 **맵 추가** 블레이드를 엽니다.

	![][10]

7.	파일 아이콘을 선택하고 로컬 컴퓨터에서 맵 파일(.trfm)을 찾습니다.

8.  **확인**을 선택하면 새 맵이 만들어집니다. 맵 목록에 맵이 표시됩니다.


## 논리 앱에서 BizTalk 변환 API 앱 사용
맵이 작성 및 테스트되고 나면 사용할 준비가 완료되었습니다.

1. 논리 앱 내의 오른쪽 갤러리에서 BizTalk 변환을 사용할 수 있습니다. 갤러리에서 **BizTalk 변환 서비스**를 선택합니다. 변환이 흐름에 추가됩니다.

	![][11]

2. **변환** 동작을 선택합니다. 입력 매개 변수가 표시됩니다.

	![][12]

3. 다음 매개 변수를 입력하여 **변환** 동작 구성을 완료합니다.
		 
	- 입력 XML
		- 변환 API 앱에서 맵의 원본 스키마를 준수하는 유효한 XML 콘텐츠를 입력합니다. 이 콘텐츠는 'RFC 호출 – SAP' 또는 '테이블에 삽입 – SQL'과 같은 논리 앱에서 이전 작업의 출력일 수 있습니다.
		
	- 맵 이름(옵션)
		- 변환 API 앱에서 이미 업로드된 유효한 맵 이름을 입력합니다. 맵을 입력하지 않으면 입력 XML이 준수하는 원본 스키마에 따라 맵이 자동으로 선택됩니다.

	![][13]

4. '출력 XML' 작업의 출력을 논리 앱의 후속 작업에 사용할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-logic-transform-xml-documents/Create_Everything.png
[2]: ./media/app-service-logic-transform-xml-documents/Create_Marketplace.png
[4]: ./media/app-service-logic-transform-xml-documents/Search_TransformAPIApp.png
[5]: ./media/app-service-logic-transform-xml-documents/Transform_APIApp_Landing_Page.png
[6]: ./media/app-service-logic-transform-xml-documents/New_TransformAPIApp_Blade.png
[7]: ./media/app-service-logic-transform-xml-documents/Browse_APIApps.png
[8]: ./media/app-service-logic-transform-xml-documents/Select_APIApp_List.png
[9]: ./media/app-service-logic-transform-xml-documents/Configure_Transform_APIApp.png
[10]: ./media/app-service-logic-transform-xml-documents/Add_Map.png
[11]: ./media/app-service-logic-transform-xml-documents/Transform_action_flow.png
[12]: ./media/app-service-logic-transform-xml-documents/Transform_Inputs.png
[13]: ./media/app-service-logic-transform-xml-documents/Transform_configured.png
[14]: ./media/app-service-logic-transform-xml-documents/Download_Schemas.png



 

<!---HONumber=July15_HO3-->