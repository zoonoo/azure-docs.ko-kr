<properties
   pageTitle="QuickBooks 커넥터"
   description="QuickBooks 커넥터 사용 방법"
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
   ms.date="07/02/2015"
   ms.author="sameerch"/>


#논리 앱에서 QuickBooks 커넥터 사용#

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. QuickBooks 커넥터를 사용하여 다른 QuickBooks 엔터티를 만들고 수정할 수 있습니다. 다음은 QuickBooks 커넥터를 통해 지원되는 QuickBooks 엔터티의 목록입니다.

<Table>
<TR><TD><B>엔터티</TD><TD><B>설명</TR> <TR> <TD> 계정 </TD> <TD> 계정은 계정 차트의 구성 요소이며 회계 원장의 일부입니다. 특정 용도에 할당된 총 통화 금액을 기록하는 데 사용됩니다. </TD> </TR> <TR> <TD> 대변 메모 </TD> <TD> 대변 메모는 판매된 상품 또는 서비스에 대한 지불의 환불 또는 공제이거나 지불의 일부임을 나타내는 재무 거래입니다. </TD> </TR> <TR> <TD> 고객 </TD> <TD> 고객은 기업이 제공하는 서비스 또는 제품의 소비자입니다. </TD> </TR> <TR> <TD> 견적 </TD> <TD> 견적은 제안된 가격을 포함하여 기업에서 고객에게 판매될 상품이나 서비스를 판매하겠다는 금융 거래의 제안을 나타냅니다. </TD> </TR> <TR> <TD> 송장 </TD> <TD> 송장은 고객이 나중에 제품 또는 서비스에 대한 지불을 하는 판매 양식을 나타냅니다. 송장 데이터 모델 사용에 대한 자세한 내용은 여기를 참조하세요. </TD> </TR> <TR> <TD> 항목 </TD> <TD> 항목은 배송, 요금, 할인 및 판매세(적용 가능한 경우)를 처리하여 회사가 구매, 판매 또는 재판매하는 제품입니다. 항목은 송장이나 기타 판매 양식에서 한 줄로 표시됩니다. </TD> </TR> <TR> <TD> 영수증 </TD> <TD> 이 엔터티는 고객에게 제공되는 판매 영수증을 나타냅니다.</TD> </TR> </Table>


##QuickBooks 작업 ##
다음은 QuickBooks 커넥터에서 사용할 수 있는 다양한 작업입니다. <table> <tbody> <tr><td> <strong>작업</strong> </td> <td> <strong>설명</strong> </td> </tr> <tr> <td> 엔터티 읽기 </td> <td> 엔터티 개체를 읽습니다. </td> </tr> <tr> <td> 엔터티 만들기 또는 업데이트 </td> <td> 엔터티 개체를 만들거나 업데이트합니다. 개체는 이미 존재하는 경우에 업데이트되며 그렇지 않은 경우 새 개체가 만들어집니다. </td> </tr> <tr> <td> 삭제 </td> <td> 이 작업은 선택한 엔터티에서 특정 개체를 삭제합니다. </td> </tr> <tr> <td> 쿼리 </td> <td> 쿼리 작업은 엔터티에 대한 안내된 쿼리를 만드는 방법입니다.</td> </tr> </tbody> </table>

##QuickBooks 커넥터 API 앱 만들기##
1.	Azure 포털의 오른쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2.	"웹 및 모바일 > API 앱"으로 이동하여 "QuickBooks"를 검색합니다.
3.	호스팅 계획, 리소스 그룹에 대한 세부 정보를 제공하고 API 앱의 이름을 선택하여 QuickBooks 커넥터를 구성합니다.

	![][13]
4. ‘패키지 설정’에서 읽기/쓰기하려는 QuickBooks 엔터티를 구성할 수 있습니다.

이제 이를 통해 QuickBooks 커넥터 API 앱을 만들 수 있습니다.


##논리 앱 만들기##
QuickBooks에서 계정을 만들고 이 계정의 범주 유형을 업데이트하는 간단한 논리 앱을 만들어 보겠습니다.

1.	Azure 포털에 로그인하고 '새로 만들기 -> 웹 + 모바일 -> 논리 앱'을 클릭합니다.

	![][1]

2.	'논리 앱 만들기' 페이지에서 이름, 앱 서비스 계획 및 위치 등의 필요한 세부 정보를 입력합니다.

	![][2]

3.	'트리거 및 동작'을 클릭하면 논리 앱 편집기 화면이 표시됩니다.

	![][3]

4.	'이 논리를 수동으로 실행'을 선택합니다. 이렇게 하면 이 논리 앱을 수동으로만 호출할 수 있습니다.


5.	갤러리에서 ‘이 리소스 그룹의 API 앱’을 확장하여 사용할 수 있는 모든 API 앱을 확인합니다. 갤러리에서 ‘QuickBooks 커넥터’를 선택하면 ‘QuickBooks 커넥터’가 흐름에 추가됩니다.


6.	논리 앱을 인증하고 권한을 부여하여 QuickBooks가 온라인인 경우 사용자를 대신하여 작업을 수행하도록 해야 합니다. 권한 부여를 시작하려면 QuickBooks 커넥터에서 권한 부여를 클릭합니다.

	![][4]

7.	권한 부여를 클릭하면 QuickBooks의 인증 대화 상자가 열립니다. 작업을 수행하려는 QuickBooks 계정의 로그인 세부 정보를 제공합니다.

	![][5]

8. 동의 대화 상자에서 권한 부여를 클릭하여 사용자를 대신하여 작업을 수행할 수 있도록 논리 앱에 계정에 대한 액세스 권한을 부여합니다.

	![][6]

9.	인증이 완료되면 모든 작업이 표시됩니다.

	![][7]

10.	‘계정 만들기 또는 업데이트’ 작업을 선택하면 입력 매개 변수가 표시됩니다.

	![][8]

11.	‘이름’ 및 ‘계정 유형’을 입력하고 ✓을 클릭합니다.

	![][9]

12.	갤러리의 ‘최근에 사용한 항목’ 섹션에서 ‘QuickBooks 커넥터’를 선택하면 새 QuickBooks 작업이 추가됩니다.

13.	작업 목록에서 ‘계정 만들기 또는 업데이트’를 선택하면 작업의 입력 매개 변수가 표시됩니다.

	![][10]

14.	‘ID’ 옆에 있는 ‘+’를 클릭하여 계정 만들기 작업의 출력에서 ID 값을 선택합니다.

	![][11]

15.	계정 유형에 새 값을 제공하고 ✓을 클릭합니다.

	![][12]

16. 논리 앱 편집기 화면에서 확인을 클릭한 다음 '만들기'를 클릭합니다. 만들기를 완료하는 데 30초 정도 걸립니다.

17. 새로 만든 논리 앱을 찾아 ‘Run’을 클릭하여 실행을 시작합니다.

18. QuickBooks 계정에서 ‘Contoso’ 이름 옆에 새 계정이 만들어진 것을 확인할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-logic-connector-quickbooks/1_New_Logic_App.png
[2]: ./media/app-service-logic-connector-quickbooks/2_Logic_App_Settings.png
[3]: ./media/app-service-logic-connector-quickbooks/3_Logic_App_Editor.png
[4]: ./media/app-service-logic-connector-quickbooks/4_QuickBooks_Authorize.png
[5]: ./media/app-service-logic-connector-quickbooks/5_QuickBooks_Login.png
[6]: ./media/app-service-logic-connector-quickbooks/6_QuickBooks_User_Consent.png
[7]: ./media/app-service-logic-connector-quickbooks/7_QuickBooks_Actions.png
[8]: ./media/app-service-logic-connector-quickbooks/8_QuickBooks_Create_Account.png
[9]: ./media/app-service-logic-connector-quickbooks/9_Create_Account_OK.png
[10]: ./media/app-service-logic-connector-quickbooks/10_QuickBooks_Update_Account.png
[11]: ./media/app-service-logic-connector-quickbooks/11_Record_ID_from_Create.png
[12]: ./media/app-service-logic-connector-quickbooks/12_Update_Account_Address.png
[13]: ./media/app-service-logic-connector-quickbooks/13_Create_new_quickbooks_connector.png

<!---HONumber=July15_HO4-->