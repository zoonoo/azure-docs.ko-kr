<properties 
   pageTitle="Salesforce 커넥터" 
   description="Salesforce 커넥터를 사용하는 방법" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="sutalasi" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


#논리 앱에서 Saleforce 커넥터 사용#

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. Salesforce 커넥터를 사용하여 다양한 엔터티(예: 계정, 잠재 고객 등)를 만들고 수정할 수 있습니다. 다음은 Salesforce와 관련된 일반적인 통합 시나리오입니다.

- Salesforce와 ERP 시스템(예: SAP, QuickBooks) 간의 계정 동기화	

- Salesforce에서 ERP 시스템으로의 주문 결제(Order to Cash) 흐름


커넥터 패키지 설정의 일부로, 사용자가 커넥터에서 관리할 수 있는 엔터티를 지정할 수 있으며, 작업, 입력 및 출력 매개 변수가 동적으로 채워집니다. 다음은 Salesforce 커넥터에서 사용할 수 있는 다양한 작업입니다.
 
- 엔터티 만들기- 계정 또는 사용자 지정 개체와 같은 새 Salesforce 엔터티를 만들려면 이 작업을 사용합니다.

- 엔터티 업데이트 - 기존 Salesforce 엔터티를 업데이트하려면 이 작업을 사용합니다.

- Upsert 엔터티 - 기존 Salesforce 엔터티를 업데이트하거나 존재하지 않는 경우 새로 만들려면 이 작업을 사용합니다.

- 엔터티 삭제 - 기존 Salesforce 엔터티를 삭제하려면 이 작업을 사용합니다.

- 쿼리 실행 - SOQL(Salesforce 개체 쿼리 언어)로 작성된 SELECT 쿼리를 실행하려면 이 작업을 사용합니다.


##SalesForce 커넥터 API 앱 만들기##
1.	Azure 포털의 오른쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > API 앱"으로 이동하여 "SalesForce"를 검색합니다.
3.	호스팅 계획, 리소스 그룹에 대한 세부 정보를 제공하고 API 앱의 이름을 선택하여 SalesForce 커넥터를 구성합니다.

	![][15]
4. ‘패키지 설정’에서 읽기/쓰기에 관련된 SalesForce 엔터티를 구성합니다.

이제 이를 통해 SalesForce 커넥터 API 앱을 만들 수 있습니다.


##논리 앱 만들기##
Salesforce에서 계정을 만들고 동일한 계정의 청구 주소 정보를 업데이트하는 간단한 논리 앱을 만들겠습니다.

1.	Azure 포털에 로그인하고 '새로 만들기 -> 웹 + 모바일 -> 논리 앱'을 클릭합니다.

	![][1]

2.	'논리 앱 만들기' 페이지에서 이름, 앱 서비스 계획 및 위치 등의 필요한 세부 정보를 입력합니다.

	![][2]

3.	'트리거 및 동작'을 클릭하면 논리 앱 편집기 화면이 표시됩니다.

	![][3]

4.	'이 논리를 수동으로 실행'을 선택합니다. 이렇게 하면 이 논리 앱을 수동으로만 호출할 수 있습니다.

    ![][4]

5.	갤러리에서 ‘이 리소스 그룹의 API 앱’을 확장하여 사용할 수 있는 모든 API 앱을 확인합니다. 갤러리에서 'Salesforce'를 선택하면 'Salesforce 커넥터'가 흐름에 추가됩니다.


	![][5]

8.	SalesForce 계정에 액세스할 수 있는 권한을 논리 앱에 부여하려면 '권한 부여’를 클릭하여 Salesforce 로그인 자격 증명을 제공합니다.

	![][6]

9.	Salesforce 로그인 페이지로 리디렉션되면 Salesforce 자격 증명으로 인증할 수 있습니다.

	![][7]

	![][8]

10.	인증이 완료되면 모든 작업이 표시됩니다.

	![][9]

11.	'계정 만들기' 작업을 선택하면 입력 매개 변수가 표시됩니다.

	![][10]

12.	'계정 이름'을 제공하고 ✓를 클릭합니다.

	![][11]

13.	갤러리의 '최근에 사용한 항목' 섹션에서 'Salesforce 커넥터'를 선택하면 새 Salesforce 작업이 추가됩니다.

14.	작업의 목록에서 '계정 업데이트'를 선택하면 '계정 업데이트' 작업의 입력 매개 변수가 표시됩니다.

	![][12]

15.	'레코드 ID' 옆에 있는 '+'를 클릭하여 '계정 만들기' 작업의 출력에서 ID 값을 선택합니다.

	![][13]

16.	청구 주소, 청구 시, 청구 상태 및 청구 우편 번호에 대한 값을 제공하고 ✓를 클릭합니다.

	![][14]

17. 논리 앱 편집기 화면에서 확인을 클릭한 다음 '만들기'를 클릭합니다. 만들기를 완료하는 데 30초 정도 걸립니다.

18. 새로 만든 논리 앱을 찾아 ‘Run’을 클릭하여 실행을 시작합니다.

19. Salesforce 계정에 새 계정이 'Contoso'란 이름으로 만들어진 것을 확인할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-logic-connector-salesforce/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-salesforce/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-salesforce/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-salesforce/4_Manual_Logic_App.png
[5]: ./media/app-service-logic-connector-salesforce/5_Select_Salesforce_Gallery.png
[6]: ./media/app-service-logic-connector-salesforce/6_Salesforce_Authorize.png
[7]: ./media/app-service-logic-connector-salesforce/7_Salesforce_Login.png
[8]: ./media/app-service-logic-connector-salesforce/8_Salesforce_User_Consent.png
[9]: ./media/app-service-logic-connector-salesforce/9_Salesforce_Actions.png
[10]: ./media/app-service-logic-connector-salesforce/10_Salesforce_Create_Account.png
[11]: ./media/app-service-logic-connector-salesforce/11_Create_Account_OK.png
[12]: ./media/app-service-logic-connector-salesforce/12_Salesforce_Update_Account.png
[13]: ./media/app-service-logic-connector-salesforce/13_Record_ID_from_Create.png
[14]: ./media/app-service-logic-connector-salesforce/14_Update_Account_Address.png
[15]: ./media/app-service-logic-connector-salesforce/15_Create_new_salesforce_connector.png


 

<!---HONumber=62-->