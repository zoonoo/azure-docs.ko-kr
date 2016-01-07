<properties
   pageTitle="논리 앱에서 SQL 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="SQL 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
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
   ms.date="11/30/2015"
   ms.author="sameerch"/>


# Microsoft SQL 커넥터 시작 및 논리 앱에 추가
온-프레미스 SQL Server 또는 Azure SQL 데이터베이스에 연결하여 정보나 데이터를 만들고 변경할 수 있습니다. 커넥터는 논리 앱에 사용하여 "워크플로"의 일부로 데이터 검색하기, 처리 또는 푸시를 수행할 수 있습니다. 워크플로에서 SQL 커넥터를 활용하면 다양한 시나리오를 수행할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 웹 또는 모바일 응용 프로그램을 통해 SQL 데이터베이스에 있는 데이터의 섹션을 표시합니다.
- SQL 데이터베이스 테이블에 데이터를 삽입하여 저장합니다. 예를 들어 직원 레코드를 입력하고 판매 주문을 업데이트할 수 있습니다.
- SQL에서 데이터를 가져와 비즈니스 프로세스에 사용합니다. 예를 들어 고객 레코드를 가져와 SalesForce에 이러한 고객 레코드를 입력할 수 있습니다.

SQL 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 트리거 및 작업
*트리거*는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. *작업*은 트리거의 결과입니다. 예를 들어 주문이 업데이트되면 영업 직원에게 경고를 보내는 것이 작업입니다. 또는 새 고객이 추가되면 새 고객에게 환영 전자 메일을 보냅니다.

SQL 데이터베이스 커넥터는 논리 앱에서 트리거나 작업으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. 패키지 설정에 포함된 모든 테이블(항목의 뒷부분에 자세히 설명)에는 JSON 작업 및 XML 작업 집합이 있습니다.

SQL 커넥터에서 사용할 수 있는 트리거와 작업은 다음과 같습니다.

트리거 | 작업
--- | ---
데이터 폴링 | <ul><li>테이블에 삽입</li><li>테이블 업데이트</li><li>테이블에서 선택</li><li>테이블에서 삭제</li><li>저장 프로시저 호출</li></ul>

## SQL 커넥터 만들기

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "SQL 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
4. 다음 패키지 설정을 입력합니다.

	이름 | 필수 | 설명
--- | --- | ---
서버 이름 | 예 | SQL Server 이름을 입력합니다. 예를 들어 *SQLserver/sqlexpress* 또는 *SQLserver.mydomain.com*을 입력합니다.
포트 | 아니요 | 기본값은 1433입니다.
사용자 이름 | 예 | SQL Server에 로그인할 수 있는 사용자 이름을 입력합니다. 온-프레미스 SQL Server에 연결하는 경우 SQL 인증 자격 증명을 입력합니다.
암호 | 예 | 사용자 이름 암호를 입력합니다.
데이터베이스 이름 | 예 | 연결 중인 데이터베이스를 입력합니다. 예를 들어 *Customers* 또는 *dbo/orders*를 입력할 수 있습니다.
온-프레미스 | 예 | 기본값은 False입니다. Azure SQL 데이터베이스에 연결하는 경우 False를 입력합니다. 온-프레미스 SQL Server에 연결하는 경우 True를 입력합니다.
서비스 버스 연결 문자열 | 아니요 | 온-프레미스에 연결할 경우 서비스 버스 릴레이 연결 문자열을 입력합니다.<br/><br/>[하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)<br/>[서비스 버스 가격](http://azure.microsoft.com/pricing/details/service-bus/)
파트너 서버 이름 | 아니요 | 기본 서버를 사용할 수 없는 경우 대체 또는 백업 서버로 파트너 서버를 입력할 수 있습니다.
테이블 | 아니요 | 커넥터를 통해 업데이트할 수 있는 데이터베이스 테이블을 나열합니다. 예를 들어 *OrdersTable* 또는 *EmployeeTable*을 입력합니다. 테이블이 입력되지 않은 경우 모든 테이블을 사용할 수 있습니다. 유효한 테이블 및/또는 저장 프로시저는 해당 커넥터를 작업으로서 사용하도록 요구됩니다.
저장 프로시저 | 아니요 | 커넥터를 통해 호출할 수 있는 기존 저장 프로시저를 입력합니다. 예를 들어 *sp\_IsEmployeeEligible* 또는 *sp\_CalculateOrderDiscount*를 입력합니다. 유효한 테이블 및/또는 저장 프로시저는 해당 커넥터를 작업으로서 사용하도록 요구됩니다.
사용 가능한 데이터 쿼리 | 트리거 지원용 | SQL Server 데이터베이스 테이블 폴링에 사용할 수 있는 데이터를 결정할 SQL 문입니다. 이는 사용할 수 있는 데이터 행의 수를 나타내는 숫자 값을 반환해야 합니다. 예: SELECT COUNT(*) from table\_name 
데이터 폴링 쿼리 | 트리거 지원용 |SQL Server 데이터베이스 테이블을 폴링할 SQL 문입니다. 세미콜론으로 구분하여 여러 SQL 문을 입력할 수 있습니다. 이 문은 트랜잭션 방식으로 실행되며 데이터가 논리 앱에 안전하게 저장된 경우에만 제출됩니다. 예: SELECT * FROM table\_name; DELETE FROM table\_name. <br/><br/>**참고**<br/>동일한 데이터가 다시 폴링되지 않도록 선택한 데이터를 삭제, 이동 또는 업데이트하여 무한 루프를 방지하는 폴링 문을 제공해야 합니다.

5. 완료되면 패키지 설정은 다음과 유사하게 됩니다. ![][1]

6. **만들기**를 선택합니다.


## 커넥터를 트리거로 사용
SQL 테이블에서 데이터를 폴링하는 단순한 논리 앱을 보고 다른 테이블에 데이터를 추가하며 데이터를 업데이트해 봅니다.

SQL 커넥터를 트리거로 사용하려면 **데이터 사용 가능한 쿼리** 및 **데이터 폴링 쿼리** 값을 입력합니다. **데이터 사용 가능한 쿼리**는 입력된 일정에 따라 실행되고 사용 가능한 데이터가 있는지 확인합니다. 이 쿼리는 스칼라 숫자만 반환하므로 빈번한 실행에 맞게 조정되고 최적화될 수 있습니다.

**데이터 폴링 쿼리**는 데이터 사용 가능 쿼리가 데이터를 사용 가능한 것으로 나타내는 경우에 실행됩니다. 이 문은 트랜잭션 내에서 실행되고 추출한 데이터가 워크플로에 영구적으로 저장될 때만 제출됩니다. 동일한 데이터를 무한으로 다시 추출하는 것을 방지하는 것이 중요합니다. 다음에 데이터를 쿼리할 때 수집하지 않도록 하기 위해 데이터를 삭제하거나 업데이트하려면 실행의 트랜잭션 특성을 사용할 수 있습니다.

> [AZURE.NOTE]이 문에 의해 반환되는 스키마는 커넥터에 사용 가능한 속성을 식별합니다. 모든 열은 이름을 지정해야 합니다.

#### 데이터 사용 가능한 쿼리 예

	SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### 데이터 폴링 쿼리 예

	SELECT *, GetData() as 'PollTime' FROM [Order]
		WHERE OrderStatus = 'ProcessedForCollection'
		ORDER BY Id DESC;
	UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
		WHERE Id =
		(SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### 트리거 추가
1. 논리 앱 만들기 또는 편집을 수행할 때 트리거로 만든 SQL 커넥터를 선택합니다. 이렇게 하면 사용 가능한 트리거인 **데이터 폴링(JSON)** 및 **데이터 폴링(XML)**이 나열됩니다. ![][5]

2. **데이터 폴링(JSON)** 트리거를 선택하고 빈도를 지정한 다음 ✓를 클릭합니다. ![][6]

3. 이제 논리 앱에서 구성된 대로 트리거가 표시됩니다. 트리거의 출력이 표시되며 후속 작업에서 입력으로 사용할 수 있습니다. ![][7]

## 커넥터를 작업으로 사용
SQL 테이블에서 데이터를 폴링하는 단순한 논리 앱 시나리오를 사용하여 다른 테이블에 데이터를 추가하고 데이터를 업데이트합니다.

SQL 커넥터를 동작으로 사용하려면 SQL 커넥터를 만들 때 입력한 테이블 및/또는 저장 프로시저의 이름을 입력합니다.

1. 트리거(또는 '이 논리를 수동으로 실행'을 선택) 뒤에 갤러리에서 만든 SQL 커넥터를 추가합니다. *Insert Into TempEmployeeDetails(JSON)*와 같은 삽입 작업 중 하나를 선택합니다. ![][8]

2. 삽입할 레코드의 입력 값을 입력하고 ✓를 클릭합니다. ![][9]

3. 갤러리에서, 전에 만든 것과 동일한 SQL 커넥터를 선택합니다. 작업으로 동일한 테이블의 업데이트 작업을 선택합니다(예: *Update EmployeeDetails*). ![][11]

4. 업데이트 작업의 입력 값을 입력하고 ✓를 클릭합니다. ![][12]

폴링 중인 테이블에서 새 레코드를 추가하여 논리 앱을 테스트할 수 있습니다.

## 할 수 있는 작업과 할 수 없는 작업

SQL 쿼리 | 지원됨 | 지원되지 않음
--- | --- | ---
Where 절 | <ul><li>연산자: AND, OR, =, <>, <, <=, >, >= 및 LIKE</li><li>여러 하위 조건은 '('및')'로 결합할 수 있습니다.</li><li>문자열 리터럴, Datetime(작은 따옴표로 묶음), 숫자(숫자만 포함해야 함)</li><li>엄격히 이진 식 형식이어야 합니다. 예를 들면 다음과 같습니다. ((피연사자 연산자 피연산자) AND/OR (피연산자 연산자 피연산자))**</li></ul>|<ul><li>연산자: Between, IN</li><li>ADD(), MAX() NOW(), POWER() 등과 같이 모든 기본 제공 함수</li><li>*, -, + 등과 같은 수학 연산자</li><li>+을 사용한 문자열 연결.</li><li>모든 조인</li><li>IS NULL 및 IS NOT Null</li><li>숫자가 아닌 문자를 포함하는 모든 숫자(예: 16진수)</li></ul>
필드(Select 쿼리) |<ul><li>쉼표로 구분된 유효한 열 이름 테이블 이름 접두사는 허용되지 않습니다(커넥터는 한 번에 한 테이블에만 작동함).</li><li>이름은 '[' 및 ']'를 사용하여 이스케이프할 수 있습니다.</li></ul> | <ul><li>키워드(예: TOP, DISTINCT 등)</li><li>별칭 지정(예: Street + City + Zip AS 주소)</li><li>ADD(), MAX() NOW(), POWER() 등과 같은 모든 기본 제공 함수</li><li>-, + 등과 같은 수학 연산자</li><li>+를 사용하는 문자열 연결</li></ul>

#### 팁

- 고급 쿼리의 경우 저장 프로시저를 만들고 저장 프로시저 실행 API를 사용하여 실행하는 것이 좋습니다.
- 내부 쿼리를 사용하는 경우 저장 프로시저 내에서 사용합니다.
- 여러 조건을 조인하려면 'AND' 및 'OR' 연산자를 사용할 수 있습니다.

## 하이브리드 구성(선택 사항)

> [AZURE.NOTE]이 단계는 방화벽 뒤의 SQL Server 온-프레미스를 사용하는 경우에만 필요합니다.

앱 서비스는 하이브리드 구성 관리자를 사용하여 온-프레미스 시스템에 안전하게 연결합니다. 커넥터가 온-프레미스 SQL Server을 사용하는 경우 하이브리드 연결 관리자가 필요합니다.

> [AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)을 참조하세요.


## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png

<!----HONumber=AcomDC_1203_2015-->