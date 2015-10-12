<properties
   pageTitle="논리 앱에서 SugarCRM 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="SugarCRM 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/23/2015"
   ms.author="sameerch"/>


# SugarCRM 커넥터 시작 및 논리 앱에 추가
SugarCRM 커넥터를 사용하여 계정, 잠재 고객, 연락처 등과 같은 다양한 엔터티를 만들고 수정할 수 있습니다. 다음은 SugarCRM과 관련된 일반적인 통합 시나리오입니다.

- SugarCRM과 ERP 시스템(예: SAP) 간의 계정 동기화
- Marketo 및 SugarCRM 간의 계정, 연락처 및 잠재 고객 동기화
- SugarCRM에서 ERP 시스템으로의 주문 결제(Order to Cash) 흐름

커넥터 패키지 설정의 일부로, 커넥터에서 관리할 수 있는 엔터티를 선택할 수 있으며, 작업, 입력 및 출력 매개 변수가 동적으로 채워집니다.

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. SugarCRM 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.



## SugarCRM 커넥터 작업
다음은 SugarCRM 커넥터에서 사용할 수 있는 다양한 작업입니다.

- 모듈 만들기- 계정, 잠재 고객, 연락처와 같은 SugarCRM 모듈용 새 레코드를 만들려면 이 작업을 사용합니다.

- 모듈 업데이트 - SugarCRM 모듈용 기존 레코드를 업데이트하려면 이 작업을 사용합니다.

- 모듈 삭제 - SugarCRM 모듈용 기존 레코드를 삭제하려면 이 작업을 사용합니다.

- 모듈 나열 - 필터링된 레코드를 나열하려면 이 작업을 사용합니다. 지정된 쿼리가 없는 경우 모든 레코드가 반환됩니다.

- 모듈 가져오기 - 지정된 모듈에서 단일 레코드를 검색하려면 이 작업을 사용합니다.

- 레코드 계정 가져오기 - 쿼리와 일치하는 모듈에서 레코드 수를 가져오려면 이 작업을 사용합니다. 지정된 쿼리가 없는 경우 해당 모듈에서 레코드의 총 수가 반환됩니다.

- 모듈 중복 검사 - 한 모듈 내에서 중복된 레코드를 검사하려면 이 작업을 사용합니다.

*참고*: 쿼리에서 지원되는 인수에 대한 자세한 내용은 [SugarCRM REST API](https://msdn.microsoft.com/library/dn705870) 설명서를 참조하세요.

## SugarCRM 커넥터 API 앱 만들기
1.	portal.azure.com으로 이동합니다. Azure 포털의 왼쪽 모서리에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"마켓플레이스 > 모든 항목"으로 이동하여 "SugarCRM"을 검색합니다.
3.	앱 서비스 계획, 리소스 그룹에 대한 세부 정보를 제공하고 API 앱의 이름을 입력하여 SugarCRM 커넥터를 구성합니다.
4. SugarCRM 커넥터 패키지 설정을 구성합니다. 다음은 커넥터를 만드는 데 제공해야 하는 패키지 설정입니다.

	이름 | 필수 | 설명
--- | --- | ---
사이트 URL | 예 | SugarCRM 인스턴스의 URL을 입력합니다. 예를 들어 https://abcde1234.sugarcrm.com를 입력합니다.
클라이언트 ID | 예 | SugarCRM에서 OAUTH 2.0 키의 소비자 키를 입력합니다. 
클라이언트 암호 | 예 | OAUTH의 소비자 암호를 입력합니다.
사용자 이름 | 예 | SugarCRM 사용자의 사용자 이름을 입력합니다.
암호 | 예 | SugarCRM 사용자의 암호를 입력합니다.
모듈 이름 | 예 | 작업을 수행하려는 SugarCRM 모듈(예: 계정, 연락처, 제품)을 입력합니다.<br><br>예: 계정, 잠재 고객, 연락처  
  
![][9]



## 논리 앱 만들기
SugarCRM에서 계정을 만들고 동일한 계정의 청구 주소 정보를 업데이트하는 간단한 논리 앱을 만들겠습니다.

1.	Azure 포털에 로그인하고 '새로 만들기 -> 웹 + 모바일 -> 논리 앱'을 클릭합니다. ![][1]

2.	'논리 앱 만들기' 페이지에서 이름, 앱 서비스 계획 및 위치 등의 필요한 세부 정보를 입력합니다. ![][2]

3.	'트리거 및 동작'을 클릭하면 논리 앱 편집기 화면이 표시됩니다. '이 논리를 수동으로 실행'을 선택합니다. 이렇게 하면 이 논리 앱을 수동으로만 호출할 수 있습니다.

4.	갤러리에서 ‘이 리소스 그룹의 API 앱’을 확장하여 사용할 수 있는 모든 API 앱을 확인합니다. 갤러리에서 'SugarCRM'을 선택하면 'SugarCRM 커넥터'가 흐름에 추가됩니다. ![][3]

5.	'계정 만들기' 작업을 선택하면 입력 매개 변수가 표시됩니다. ![][4]

6.	이름으로 'Microsoft 계정'을 입력하고 ✓를 클릭합니다. ![][5]

7.	갤러리의 ‘최근에 사용한 항목’ 섹션에서 ‘SugarCRM 커넥터’를 선택하면 새 SugarCRM 작업이 추가됩니다.

8.	작업 목록에서 '계정 업데이트'(고급 작업 '...' 아래에 있음)를 선택하면 '계정 업데이트' 작업의 입력 매개 변수가 표시됩니다. ![][6]

9.	'레코드 ID' 옆에 있는 '+'를 클릭하여 '계정 만들기' 동작의 출력에서 "ID" 값을 선택합니다. ![][7]

10.	청구 주소 정보의 값을 입력하고 ✓를 클릭합니다. ![][8]

11. 논리 앱 편집기 화면에서 확인을 클릭한 다음 '만들기'를 클릭합니다. 만들기를 완료하는 데 30초 정도 걸립니다.

12. 새로 만든 논리 앱을 찾아 ‘지금 실행’을 클릭하여 실행을 시작합니다.

13. ‘Microsoft 계정’이라는 이름으로 새 계정이 SugarCRM 계정에서 만들어지고 동일한 계정이 청구 주소 정보로 업데이트됨도 확인할 수 있습니다.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-sugarcrm/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-sugarcrm/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-sugarcrm/3_Select_SugarCRM_Gallery.png
[4]: ./media/app-service-logic-connector-sugarcrm/4_SugarCRM_Create_Account.png
[5]: ./media/app-service-logic-connector-sugarcrm/5_Create_Account_OK.png
[6]: ./media/app-service-logic-connector-sugarcrm/6_SugarCRM_Update_Account.png
[7]: ./media/app-service-logic-connector-sugarcrm/7_Record_ID_from_Create.png
[8]: ./media/app-service-logic-connector-sugarcrm/8_Update_Account_Address.png
[9]: ./media/app-service-logic-connector-sugarcrm/9_Create_new_SugarCRM_connector.png

<!---HONumber=Oct15_HO1-->