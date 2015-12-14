<properties
   pageTitle="논리 앱에서 Oracle 데이터베이스 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Oracle 데이터베이스 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
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


# Oracle 데이터베이스 커넥터 시작 및 논리 앱에 추가
온-프레미스 Oracle 데이터베이스 서버에 연결하여 사용자의 정보 또는 데이터를 만들고 변경할 수 있습니다. 커넥터는 논리 앱에서 사용하여 "워크플로"의 일부로 데이터를 검색, 처리 또는 푸시할 수 있습니다. 워크플로에서 Oracle 커넥터를 활용하면 다양한 시나리오를 수행할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- 웹 또는 모바일 응용 프로그램을 통해 Oracle 데이터베이스에 있는 데이터의 섹션을 표시합니다.
- Oracle 데이터베이스 테이블에 데이터를 삽입하여 저장합니다. 예를 들어 직원 레코드를 입력하고 판매 주문을 업데이트할 수 있습니다.
- Oracle에서 데이터를 가져와 비즈니스 프로세스에 사용합니다. 예를 들어 고객 레코드를 가져와 SalesForce에 이러한 고객 레코드를 입력할 수 있습니다.


## 트리거 및 작업
*트리거*는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. *작업*은 트리거의 결과입니다. 예를 들어 주문이 업데이트되면 영업 직원에게 경고를 보내는 것이 작업입니다. 또는 새 고객이 추가되면 새 고객에게 환영 전자 메일을 보냅니다.

Oracle 데이터베이스 커넥터는 논리 앱에서 트리거나 작업으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. 패키지 설정에 포함된 모든 테이블(항목의 뒷부분에 자세히 설명)에는 JSON 작업 및 XML 작업 집합이 있습니다. XML 트리거나 작업을 사용하는 경우 [변환 API 앱](app-service-logic-transform-xml-documents.md)을 사용하여 데이터를 다른 XML 데이터 형식으로 변환할 수 있습니다.

Oracle 데이터베이스 커넥터에서 사용할 수 있는 트리거와 작업은 다음과 같습니다.

트리거 | 작업
--- | ---
데이터 폴링 | <ul><li>테이블에 삽입</li><li>테이블 업데이트</li><li>테이블에서 선택</li><li>테이블에서 삭제</li><li>저장 프로시저 호출</li>


## Oracle 데이터베이스 커넥터 만들기

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. **API 앱**을 선택하고 "Oracle 데이터베이스 커넥터"를 검색합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
4. 다음 패키지 설정을 입력합니다.

	이름 | 필수 | 설명
--- | --- | ---
데이터 원본 | 예 | Oracle 클라이언트가 설치되어 있는 컴퓨터의 tnsnames.ora 파일에 지정된 데이터 원본(Net 서비스) 이름입니다. 데이터 원본 이름 및 tnsnames.ora에 대한 자세한 내용은 [Oracle 클라이언트 구성](http://msdn.microsoft.com/library/dd787872.aspx)을 참조하세요.
사용자 이름 | 예 | Oracle 서버에 연결할 사용자 이름을 입력합니다.
암호 | 예 | 사용자 이름 암호를 입력합니다.
서비스 버스 연결 문자열 | 예 | 온-프레미스에 연결할 경우 서비스 버스 릴레이 연결 문자열을 입력합니다.<br/><br/>[하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)<br/>[서비스 버스 가격](http://azure.microsoft.com/pricing/details/service-bus/)
테이블 | 아니요 | 커넥터를 통해 수정할 수 있는 데이터베이스의 테이블을 입력합니다. 예를 들어 *OrdersTable, EmployeeTable*을 입력합니다.
저장 프로시저 | 아니요 | 커넥터를 통해 호출할 수 있는 데이터베이스의 저장 프로시저를 입력합니다. 예를 들어 *IsEmployeeEligible, CalculateOrderDiscount*를 입력합니다.
함수 | 아니요 | 커넥터를 통해 호출할 수 있는 데이터베이스의 함수를 입력합니다. 예를 들어 *IsEmployeeEligible, CalculateOrderDiscount*를 입력합니다.
패키지 엔터티 | 아니요 | 커넥터를 통해 호출할 수 있는 데이터베이스의 패키지를 입력합니다. 예를 들어 *PackageOrderProcessing.CompleteOrder, PackageOrderProcessing.GenerateBill*을 입력합니다.
사용 가능한 데이터 문 | 아니요 | 폴링에 사용할 수 있는 데이터를 결정할 문을 입력합니다. 예를 들어 *SELECT * from table\_name*을 입력합니다.
폴링 유형 | 아니요 | 폴링 유형을 입력합니다. 허용되는 값은 "Select", "Procedure", "Function" 및 "Package"입니다.
폴링 문 | 아니요 | Oracle 서버 데이터베이스를 폴링할 문을 입력합니다. 예를 들어 *SELECT * from table\_name*을 입력합니다.
폴링 후 문 | 아니요 | 폴링 후 실행할 문을 입력합니다. 예를 들어 *DELETE * from table\_name*을 입력합니다.

5. 완료되면 패키지 설정은 다음과 유사하게 됩니다. <br/> ![][1]


## 커넥터를 트리거로 사용
Oracle 테이블에서 데이터를 폴링하는 간단한 논리 앱을 살펴보고 다른 테이블에 데이터를 추가하고 데이터를 업데이트해 봅니다.

### 트리거 추가
1. 논리 앱을 만들거나 편집할 때 트리거로 만든 Oracle 커넥터를 선택합니다. 이렇게 하면 사용 가능한 트리거인 **Poll Data (JSON)** 및 **Poll Data (XML)**가 나열됩니다.<br/> ![][5]

2. **데이터 폴링(JSON)** 트리거를 선택하고 빈도를 지정한 다음 ✓을 클릭합니다.<br/> ![][6]

3. 이제 논리 앱에서 구성된 대로 트리거가 나타납니다. 트리거의 출력이 표시되며 이는 후속 작업의 입력으로 사용될 수 있습니다.<br/> ![][7]

## 커넥터를 작업으로 사용
Oracle 테이블에서 데이터를 폴링하는 간단한 논리 앱을 사용하여 다른 테이블에 데이터를 추가하고 데이터를 업데이트합니다.

Oracle 커넥터를 작업으로 사용하려면 Oracle 커넥터를 만들 때 입력한 테이블 및/또는 저장 프로시저의 이름을 입력합니다.

1. 갤러리에서 동일한 Oracle 커넥터를 작업으로 선택합니다. *Insert Into TempEmployeeDetails (JSON)*와 같은 삽입 작업 중 하나를 선택합니다.<br/> ![][8]

2. 삽입될 레코드의 입력 값을 입력하고 ✓을 클릭합니다.<br/> ![][9]

3. 갤러리에서, 전에 만든 것과 동일한 Oracle 커넥터를 선택합니다. 작업으로 *Update TempEmployeeDetails*와 같이 동일한 테이블의 업데이트 작업을 선택합니다.<br/> ![][11]

4. 업데이트 작업을 위한 입력 값을 입력하고 ✓을 클릭합니다.<br/> ![][12]

폴링 중인 테이블에 새 레코드를 추가하여 논리 앱을 테스트할 수 있습니다.

## 하이브리드 구성

> [AZURE.NOTE]이 단계는 방화벽 뒤의 Oracle 온-프레미스를 사용하는 경우에만 필요합니다.

앱 서비스는 하이브리드 구성 관리자를 사용하여 온-프레미스 시스템에 안전하게 연결합니다. 커넥터가 온-프레미스 Oracle을 사용하는 경우 하이브리드 연결 관리자가 필요합니다.

[하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)을 참조하세요.

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-oracle/Create.png
[5]: ./media/app-service-logic-connector-oracle/LogicApp1.png
[6]: ./media/app-service-logic-connector-oracle/LogicApp2.png
[7]: ./media/app-service-logic-connector-oracle/LogicApp3.png
[8]: ./media/app-service-logic-connector-oracle/LogicApp4.png
[9]: ./media/app-service-logic-connector-oracle/LogicApp5.png
[10]: ./media/app-service-logic-connector-oracle/LogicApp6.png
[11]: ./media/app-service-logic-connector-oracle/LogicApp7.png
[12]: ./media/app-service-logic-connector-oracle/LogicApp8.png

<!---HONumber=AcomDC_1203_2015-->