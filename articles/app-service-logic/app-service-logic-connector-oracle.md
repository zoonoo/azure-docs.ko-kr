<properties 
   pageTitle="Oracle 커넥터" 
   description="Oracle 커넥터를 사용하는 방법" 
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
   ms.date="03/20/2015"
   ms.author="sutalasi"/>


# Oracle 데이터베이스 커넥터 #

커넥터는 논리 앱에 사용하여 흐름의 일부로 데이터 가져오기, 처리 또는 푸시를 수행할 수 있습니다. 흐름에서 Oracle 커넥터를 활용하여 다양한 시나리오를 얻을 수 있습니다. 몇 가지 예:  

1.	웹 또는 모바일 사용자 프런트 엔드를 통해 Oracle 데이터베이스에 상주하는 데이터의 섹션을 표시합니다.
2.	저장소(예: 직원 레코드, 판매 주문 등)에 대한 Oracle 데이터베이스 테이블에 데이터를 삽입합니다.
3.	비즈니스 프로세스에서 사용할 데이터를 Oracle에서 추출합니다.

이러한 시나리오의 경우 다음과 같은 요구 사항을 수행해야 합니다. 

1. Oracle 커넥터 API 앱의 인스턴스를 만듭니다.
2. 온-프레미스 Oracle 서버와 통신하는 API 앱에 대한 하이브리드 연결을 설정합니다.
3. 논리 앱에서 생성된 API 앱을 사용하여 원하는 비즈니스 프로세스를 달성합니다.

	###기본 트리거 및 동작
		
    - 데이터 폴링(트리거) 
    - 테이블에 삽입
    - 테이블 업데이트
    - 테이블에서 선택
    - 테이블에서 삭제
    - 저장 프로시저 호출

## Oracle 데이터베이스 커넥터 API 앱의 인스턴스를 만듭니다. ##

Oracle 커넥터를 사용하려면 먼저 Oracle 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1. Azure 포털의 왼쪽 아래에 있는 '+ 새로 만들기' 옵션을 사용하여 Azure 마켓플레이스를 엽니다.
2. "웹 및 모바일 > API 앱"으로 이동하고 "Oracle 커넥터"를 검색합니다.
3. 첫 번째 블레이드에서 이름, 앱 서비스 계획 등과 같은 일반 세부 정보를 제공합니다.
4. 아래 테이블에서 언급한 패키지 설정을 제공합니다.

<style type="text/css">
	table.tableizer-table {
	border: 1px solid #CCC; font-family: Arial, Helvetica, sans-serif;
	font-size: 12px;
} 
.tableizer-table td {
	padding: 4px;
	margin: 3px;
	border: 1px solid #ccc;
}
.tableizer-table th {
	background-color: #525B64; 
	color: #FFF;
	font-weight: bold;
}
</style><table class="tableizer-table">
<tr class="tableizer-firstrow"><th>이름</th><th>필수</th><th>설명</th></tr>
 <tr><td>데이터 원본</td><td>예</td><td>Oracle 클라이언트가 설치되어 있는 컴퓨터의 tnsnames.ora 파일에 지정된 데이터 원본(Net 서비스) 이름입니다. 데이터 원본 이름 및 tnsnames.ora에 대한 자세한 내용은 [Oracle 클라이언트 구성]을 참조하세요.</td></tr>
 <tr><td>사용자 이름</td><td>예</td><td>Oracle 서버에 연결할 유효한 사용자 이름을 지정합니다.</td></tr>
 <tr><td>암호</td><td>예</td><td>Oracle 서버에 연결할 유효한 암호를 지정합니다.</td></tr>
 <tr><td>서비스 버스 연결 문자열</td><td>예</td><td>선택 사항입니다. Oracle 서버가 온-프레미스인 경우 이 매개 변수를 지정합니다. 이 문자열은 유효한 서비스 버스 네임스페이스 연결 문자열이어야 합니다. Oracle 서버에 액세스할 수 있는 서버에 수신기 에이전트를 설치해야 합니다. API 앱 통계 페이지로 이동하고 '하이브리드 연결'을 클릭하여 에이전트를 설치할 수 있습니다.</td></tr>
 <tr><td>테이블</td><td>아니요</td><td>선택 사항입니다. 커텍터를 통해 수정할 수 있는 데이터베이스의 테이블을 지정합니다. Ex:OrdersTable,EmployeeTable</td></tr>
 <tr><td>저장 프로시저</td><td>아니요</td><td>선택 사항입니다. 커넥터를 통해 호출할 수 있는 데이터베이스의 저장 프로시저를 지정합니다. 예제: IsEmployeeEligible,CalculateOrderDiscount</td></tr>
 <tr><td>함수</td><td>아니요</td><td>선택 사항입니다. 커넥터를 통해 호출할 수 있는 데이터베이스의 함수를 지정합니다. 예제: IsEmployeeEligible,CalculateOrderDiscount</td></tr>
 <tr><td>패키지 엔터티</td><td>아니요</td><td>선택 사항입니다. 커넥터를 통해 호출할 수 있는 데이터베이스의 패키지를 지정합니다. 예제: PackageOrderProcessing.CompleteOrder,PackageOrderProcessing.GenerateBill</td></tr>
 <tr><td>사용 가능한 데이터 문</td><td>아니요</td><td>선택 사항입니다. 폴링에 사용할 수 있는 데이터를 결정할 문을 지정합니다. 예제: SELECT * from table_name</td></tr>
 <tr><td>폴링 유형</td><td>아니요</td><td>선택 사항입니다. 폴링 유형을 지정합니다. 허용되는 값은 "선택", "프로시저", "함수" 및 "패키지"입니다.</td></tr>
 <tr><td>폴링 문</td><td>아니요</td><td>선택 사항입니다. Oracle 서버 데이터베이스 테이블을 폴링할 문을 지정합니다. 예제: SELECT * from table_name</td></tr>
 <tr><td>폴링 문 게시</td><td>아니요</td><td>선택 사항입니다. 폴링 후 실행할 문을 지정합니다. 예제: DELETE * from table_name.</td></tr>
</table>



 ![][1]  

## 하이브리드 구성 ##

찾아보기-> API 앱 -> <방금 만든 API 앱의 이름>을 통해 방금 만든 API 앱으로 이동하면 다음과 같은 동작이 표시됩니다. 하이브리드 연결이 아직 설정되지 않았으므로 설치가 완료되지 않습니다.

![][2] 

하이브리드 연결을 설정하려면 다음을 수행합니다.

1. 기본 연결 문자열을 복사합니다.
2.  '다운로드 및 구성' 링크를 클릭합니다.
3. 시작된 설치 프로세스를 따르고 메시지가 표시되면 기본 연결 문자열을 제공합니다.
4. 설치 프로세스가 완료되면 다음과 유사한 대화 상자가 표시됩니다.

![][3] 

이제 생성된 API 앱으로 다시 이동하면 하이브리드 연결 상태가 연결됨인 것을 볼 수 있습니다. 

![][4] 

참고: 보조 연결 문자열로 전환하려는 경우 하이브리드 설치 프로세스를 다시 수행하고 기본 연결 문자열 대신 보조 연결 문자열을 제공합니다.  

## 논리 앱에서 사용 ##

Oracle 커넥터는 논리 앱에서 트리거/동작으로만 사용할 수 있습니다. 트리거 및 모든 동작은 JSON 및 XML 데이터 형식을 모두 지원합니다. 패키지 설정의 일부로 제공되는 모든 테이블의 경우, 일련의 JSON 동작 및 일련의 XML 동작이 됩니다. XML 트리거/동작을 사용하는 경우, 변환 API 앱을 사용하여 데이터를 다른 XML 데이터 형식으로 변환할 수 있습니다. 

Oracle 테이블에서 데이터를 폴링하는 단순한 논리 앱을 사용하여 다른 테이블에 데이터를 추가하고 데이터를 업데이트해 봅니다.



-  논리 앱 만들기/편집을 수행할 때 트리거로 만든 Oracle 커넥터 API 앱을 선택합니다. 이는 사용할 수 있는 트리거 - 데이터 폴링(JSON) 및 데이터 폴링(XML)을 나열합니다.

 ![][5] 


- 트리거 - 데이터 폴링(JSON)을 선택하고 빈도를 지정한 다음 ✓을 클릭합니다.

![][6] 



- 이제 논리 앱에서 구성된 대로 트리거가 나타납니다. 트리거의 출력이 표시되며 후속 동작에서 입력으로 사용될 수 있습니다. 

![][7] 


- 갤러리에서 동일한 Oracle 커넥터를 동작으로 선택합니다. 삽입 동작 중 하나 - Insert Into TempEmployeeDetails(JSON)를 선택합니다.

![][8] 



- 삽입될 레코드의 입력을 제공하고 ✓을 클릭합니다. 

![][9] 



- 갤러리에서 동일한 Oracle 커넥터를 동작으로 선택합니다. 동일한 테이블에서 업데이트 동작을 선택합니다(예: Update EmployeeDetails)

![][11] 



- 업데이트 동작에 대해 입력하고 ✓을 클릭합니다. 

![][12] 

폴링 중인 테이블에서 새 레코드를 추가하여 논리 앱을 테스트할 수 있습니다.

<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.jpg
[2]: ./media/app-service-logic-connector-oracle/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-oracle/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-oracle/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.jpg
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.jpg
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.jpg
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.jpg
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.jpg
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.jpg

<!--Links-->
[Oracle 클라이언트 구성]: https://msdn.microsoft.com/ko-kr/library/dd787872.aspx



<!--HONumber=52--> 