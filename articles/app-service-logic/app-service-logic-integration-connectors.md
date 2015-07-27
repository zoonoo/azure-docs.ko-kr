<properties 
	pageTitle="Microsoft Azure 앱 서비스의 BizTalk 통합 API 앱 | Azure" 
	description="BizTalk 통합 API 앱을 만들고 구성하는 방법을 알아봅니다. 마이크로 서비스 아키텍처" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="mandia"/>


# Microsoft Azure 앱 서비스의 BizTalk 통합 API 앱

> [AZURE.NOTE]이 항목은 사용 중지됩니다. 모든 사용가능한 기본 제공 커넥터 및 API 앱을 보려면 [커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)을 참조하세요.


Microsoft Azure 앱 서비스(또는 줄여서 앱 서비스)에는 통합 환경에 중요한 여러 BizTalk API 앱이 포함되어 있습니다. 이러한 API 앱은 BizTalk Server 내에서 사용되는 개념과 도구를 기반으로 하지만 이제 Azure 앱 서비스의 일부로 사용할 수 있습니다.

이러한 API 앱 범주 중 하나가 BizTalk 통합 API 앱입니다. BizTalk API 앱을 사용하면 BizTalk Server로 온-프레미스에서 작업하는 것처럼 쉽게 비즈니스 규칙 추가, XML 메시지 변환 및 유효성 검사, 플랫 파일 및 JSON 데이터 인코딩 등의 작업을 수행할 수 있습니다.

이러한 통합 API 앱은 "작업" 기능을 제공합니다. XML 메시지를 받은 후 XML 스키마와 비교하여 XML 메시지의 유효성을 검사하는 것처럼 작업은 결과입니다.


## 통합 API 앱 정의
BizTalk 통합 API 앱은 데이터 처리를 수행하고 출력을 생성할 수 있는 미리 빌드된 기존 API 앱입니다. 예를 들어 이러한 API 앱 중 일부는 서로 다른 파일 형식이 함께 작동할 수 있게 하고 일부는 비즈니스 논리나 응용 프로그램 논리를 적용합니다. 통합 API 앱에는 다음이 포함됩니다.

API 앱 | 설명
--- | ---
<ul><li>X12 API 앱</li><li>AS2 커넥터</li><li>EDIFACT API 앱</li><li>거래 업체 관리 API 앱</li> | 이 API 앱은 B2B 기능을 제공합니다. [B2B 커넥터](app-service-logic-b2b-connectors.md)에서 이 API 앱에 대해 자세히 설명합니다.
BizTalk 플랫 파일 인코더 | **동작** API 앱입니다. 플랫 파일 데이터(예: Excel, csv)와 XML 데이터가 함께 작동하게 합니다(상호 운용성). 플랫 파일 인스턴스를 XML로 변환하며 그 반대로 변환할 수도 있습니다.
BizTalk JSON 인코더 | **동작** API 앱입니다. JSON과 XML 데이터가 함께 작동하게 합니다(상호 운용성). JSON 인스턴스를 XML로 변환하며 그 반대로 변환할 수도 있습니다.
BizTalk 규칙 | **동작** API 앱입니다. 비즈니스 논리 또는 "규칙"을 구현 및 적용하여 비즈니스 작업을 제어할 수 있습니다. 규칙은 동적이며 언제든지 변경될 수 있습니다. BizTalk 규칙은 사용자가 코드를 작성하지 않고 비즈니스 논리를 사용하도록 설정할 수 있게 하며, 응용 프로그램에 영향을 주지 않고 쉽게 업데이트할 수 있습니다.
BizTalk 변환 | **동작** API 앱입니다. 데이터를 한 형식에서 다른 형식으로 변환합니다. 다양한 기본 제공 함수를 사용하여 문자열 조작, 산술 식 완성, 날짜 및 시간 형식 지정 등을 수행할 수 있습니다. 
BizTalk XML 유효성 검사기 | **동작** API 앱입니다. 미리 정의된 XML 스키마와 비교하여 XML 데이터의 유효성을 검사합니다. JSON 인스턴스 또는 기존 커넥터를 기준으로 스키마를 사용할 수 있습니다. 
BizTalk XPath 추출기 | **동작** API 앱입니다. 특정 XPath를 기준으로 XML 콘텐츠에서 데이터를 조회 및 추출합니다.
Wait | 입력한 기간 동안 또는 특정 시간까지 실행을 지연합니다. 논리 앱에 추가할 경우 전체 앱의 실행을 지연하는 데 사용할 수 있습니다.


	> [AZURE.NOTE] If the input xml has a simple node with an attribute (like "<authorid= ”1”>abc</author>"), then the JSON output of the library is { “author”: { “@id” : “1”, “#text”: “abc”}}. To handle the “Id” attribute, a new “#text” key is added for the text content of the node. To handle this kind of node, add a constant key. This is by design in the Newtonsoft.Json library. When you insert this into SQL, use “JSONOutput.Author.#text”; do not use “JsonOutput.Author”.

이 API 앱을 통해 다양한 메시징 또는 데이터 작업을 완료할 수 있습니다. 예를 들어 BizTalk 규칙 API 앱을 통해 주문을 받고 특정 수량이 주문된 경우 할인을 적용할 수 있습니다. 또는 우편 번호에 따라 특정 세율을 변경할 수 있습니다.

API 앱은 원하는 개수만큼 쉽게 만들 수 있습니다. 여러 시나리오나 워크플로에서 API 앱을 다시 사용할 수도 있습니다.

예를 들어 고객이 제품 항목을 100개 주문할 경우 10% 할인을 적용하는 비즈니스 정책이 있다고 가정해 보세요. 앱에서 주문 수량을 확인하고 100개를 초과할 경우 10% 할인을 적용하는 BizTalk 규칙 API 앱을 추가할 수 있습니다.

해당 비즈니스 정책을 확장할 수도 있습니다. 북캐롤라이나의 매출 증가 목표가 있다고 가정해 보세요. 100개 이상 주문에 대한 10% 할인 외에도 북캐롤라이나에서 주문된 경우 무료 배송을 제공합니다. 이 북캐롤라이나 조건을 기존 BizTalk 규칙에 쉽게 추가할 수 있습니다.

코드를 작성하지 않고 이 API 앱을 사용하면 됩니다. 이제 시작하겠습니다.


## API 앱 만들기
Azure 포털이나 REST API를 사용하여 통합 API 앱을 만들 수 있습니다.


### REST API를 사용하여 API 앱 만들기
[REST API](http://go.microsoft.com/fwlink/p/?LinkId=529766)를 참조하세요.


### Azure 포털에서 통합 API 앱 만들기
Azure 포털에서 논리 앱, 웹앱 또는 모바일 앱을 만들 때 BizTalk 통합 API 앱을 만들 수 있습니다. 또는 자체 블레이드를 사용하여 만들 수 있습니다. 두 가지 방법 모두 쉬우므로 요구 사항 또는 선호도에 따라 선택하면 됩니다. 일부 사용자는 모든 통합 API 앱과 해당 특정 속성을 먼저 만들려고 합니다. 그런 다음 논리 앱, 웹앱 또는 모바일 앱을 만들고 생성된 통합 API 앱을 추가합니다.

다음 단계에서는 API 앱 블레이드를 사용하여 BizTalk 통합 API 앱을 만듭니다.

1. Azure 포털 시작 보드(홈페이지)에서 **마켓플레이스**를 선택합니다. **API 앱**에 기존 API 앱과 커넥터가 모두 나열됩니다. 특정 BizTalk API 앱을 **검색**할 수도 있습니다.
2. API 앱을 선택합니다. 새 블레이드에서 **만들기**를 선택합니다. 
3. 다음과 같은 속성을 입력합니다. 

	속성 | 설명
--- | ---
이름 | API 앱의 이름을 임의로 입력합니다. 예를 들어 이름을 *RulesDiscountTaxCode* 또는 *APIAppValidateXML*로 지정할 수 있습니다.
앱 서비스 계획 | 요금제를 나열합니다. 필요한 리소스가 더 많거나 적은 경우 변경할 수 있습니다.
가격 책정 계층 | Azure 구독 내에서 가격 책정 범주를 나열하는 읽기 전용 속성입니다. 
리소스 그룹 | 새 그룹을 만들거나 기존 그룹을 사용합니다. 논리 앱, 웹앱 및 모바일 앱에 대한 모든 API 앱과 커넥터가 동일한 리소스 그룹에 속해야 합니다. <br/><br/>이 속성은 [리소스 그룹 사용](../resource-group-overview.md)에서 설명합니다. 
구독 | 현재 구독을 나열하는 읽기 전용 속성입니다.
위치 | Azure 서비스를 호스트하는 지리적 위치입니다. 
시작 보드에 추가 | 통합 시작 보드(홈페이지)에 API 앱을 추가하려면 이 속성을 선택합니다.


## BizTalk API 앱 구성
Azure 관리 포털에서 BizTalk API 앱을 엽니다. **구성 요소** 섹션에서 API 앱을 완성하는 데 필요한 기타 구성 요소를 추가할 수 있습니다.

	API App | Tasks
--- | ---
BizTalk 플랫 파일 인코더 | XML로 변환할 플랫 파일(예: Excel 또는 csv 파일)을 입력합니다. 또는 플랫 파일로 변환할 XML 파일을 입력합니다.
BizTalk JSON 인코더 | XML로 변환할 JSON 파일을 입력합니다. 또는 JSON으로 변환할 XML 파일을 입력합니다. 
BizTalk 규칙 | 어휘를 추가하고 IF THEN 규칙을 만듭니다. [BizTalk 규칙 사용](app-service-logic-use-biztalk-rules.md)을 참조하세요. 
BizTalk 변환 | 맵을 추가하고 입력 XML 스키마와 출력 XML 스키마를 입력합니다. 기본 제공 함수를 사용하여 들어오는 메시지 또는 데이터를 출력 XML 스키마와 일치하도록 조작합니다. [XML 문서 변환](app-service-logic-transform-xml-documents.md)을 참조하세요. 
BizTalk XML 유효성 검사기 | 미리 정의된 XML 스키마와 비교하여 유효성을 검사할 XML을 입력합니다. JSON 인스턴스 또는 기존 커넥터를 기준으로 스키마를 사용할 수 있습니다. 
BizTalk XPath 추출기 | 특정 XPath를 기준으로 XML 콘텐츠에서 데이터를 조회 및 추출합니다.
Wait | 웹앱, 모바일 앱 또는 논리 앱을 실행할 기간이나 특정 시간을 입력합니다.


## API 앱 모니터링
Azure 관리 포털에서 BizTalk API 앱을 엽니다. **작업** 섹션에서 다양한 관리 작업을 볼 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 정보 및 오류 이벤트 보기
- 작업자 프로세스(w3wp)의 메모리 사용 및 스레드 수 보기
- 응용 프로그램 및 웹 서버 로그 보기\\

자세한 내용은 [논리 앱 모니터링](app-service-logic-monitor-your-logic-apps.md)을 참조하세요.


## 응용 프로그램에 BizTalk API 앱 추가 
Microsoft Azure 앱 서비스는 이러한 통합 API 앱을 사용할 수 있는 다양한 응용 프로그램 종류를 표시합니다. BizTalk API 앱을 새로 만들거나 기존 BizTalk API 앱을 논리 앱, 모바일 앱 또는 웹앱에 추가할 수 있습니다.

앱 내의 갤러리에서 BizTalk API 앱을 선택하기만 하면 앱에 자동으로 추가됩니다.

다음 단계에서는 BizTalk API 앱을 논리 앱, 모바일 앱 또는 웹앱에 추가합니다.

1. Azure 포털 시작 보드(홈페이지)에서 **마켓플레이스**로 이동하여 논리 앱, 모바일 앱 또는 웹앱을 검색합니다. 

	새 앱을 만드는 경우 논리 앱, 모바일 앱 또는 웹앱을 검색합니다. 앱을 선택하고 새 블레이드에서 **만들기**를 선택합니다. [논리 앱 만들기](app-service-logic-create-a-logic-app.md)에 단계가 나열되어 있습니다.

2. 앱을 열고 **트리거 및 동작**을 선택합니다.

3. **갤러리**에서 BizTalk API 앱을 선택하면 자동으로 앱에 추가됩니다.

4. **확인**을 선택하여 변경 내용을 저장합니다.


## 추가 통합 API 앱 리소스
[VETR을 사용하여 EAI 논리 앱 만들기](app-service-logic-create-EAI-logic-app-using-VETR.md)<br/> [XML 문서 변환](app-service-logic-transform-xml-documents.md)<br/> [BizTalk 규칙 사용](app-service-logic-use-biztalk-rules.md)<br/> [커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)


## 논리 앱 및 웹앱 알아보기
[논리 앱 정의](app-service-logic-what-are-logic-apps.md)<br/> [Azure 앱 서비스의 웹 사이트 및 웹앱](../app-service-web/app-service-web-overview.md)


## 추가 커넥터

[커넥터 및 API 앱 목록](app-service-logic-connectors-list.md)<br/><br/> [커넥터 및 BizTalk API 앱 정의](app-service-logic-what-are-biztalk-api-apps.md)

<!---HONumber=July15_HO3-->