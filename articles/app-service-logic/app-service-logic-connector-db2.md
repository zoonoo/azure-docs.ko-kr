<properties
   pageTitle="Microsoft Azure 앱 서비스에서 DB2 커넥터 사용 | Microsoft Azure"
   description="논리 앱 트리거 및 동작을 통해 DB2 커넥터를 사용하는 방법"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# DB2 커넥터
>[AZURE.NOTE] 이 문서 버전은 논리 앱 2014-12-01-미리 보기 스키마 버전에 적용됩니다.

DB2용 Microsoft 커넥터는 Azure 앱 서비스를 통해 IBM DB2 데이터베이스에 저장된 리소스에 응용 프로그램을 연결하기 위한 API 앱입니다. 커넥터는 Azure 서비스 버스 릴레이를 사용하여 온-프레미스 DB2 서버에 Azure 하이브리드 연결을 비롯한 TCP/IP 네트워크 연결을 통해 원격 DB2 서버 컴퓨터에 연결하는 Microsoft 클라이언트를 포함합니다. 커넥터는 다음 데이터베이스 작업을 지원합니다.

- SELECT를 사용하여 행 읽기
- SELECT 뒤에 SELECT COUNT를 사용하여 행을 읽도록 폴링
- INSERT를 사용하여 한 행 또는 여러(대량으로) 행 추가
- UPDATE를 사용하여 한 행 또는 여러(대량으로) 행 변경
- DELETE를 사용하여 한 행 또는 여러(대량으로) 행 삭제
- 현재의 커서 위치를 업데이트 뒤에 커서 선택을 사용하여 행을 변경하도록 읽기
- 현재의 커서 위치를 업데이트 뒤에 커서 선택을 사용하여 행을 삭제하도록 읽기
- 호출을 사용하여 입력 및 출력 매개 변수, 반환 값, resultset이 있는 프로시저 실행
- SELECT, INSERT, UPDATE, DELETE를 사용하는 사용자 지정 명령 및 복합 작업

## 트리거 및 작업
커넥터는 다음 논리 앱 트리거 및 동작을 지원합니다.

트리거 | 동작
--- | ---
<ul><li>데이터 폴링</li></ul> | <ul><li>대량 삽입</li><li>삽입</li><li>대량 업데이트</li><li>업데이트</li><li>호출</li><li>대량 삭제</li><li>삭제</li><li>선택</li><li>조건부 업데이트</li><li>EntitySet에 게시</li><li>조건부 삭제</li><li>단일 엔터티 선택</li><li>삭제</li><li>Upsert to EntitySet</li><li>사용자 지정 명령</li><li>복합 작업</li></ul>


## DB2 커넥터 만들기
다음 예와 같이 논리 앱 내에서 또는 Azure 마켓플레이스에서 커넥터를 정의할 수 있습니다.

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. **모든** 블레이드에서 **모두 검색** 상자에 **db2**를 입력한 다음 입력 키를 클릭합니다.
3. 모든 결과 검색 창에서 **DB2 커넥터**를 선택합니다.
4. DB2 커넥터 설명 블레이드에서 **만들기**를 선택합니다.
5. DB2 커넥터 패키지 블레이드에서 이름(예: "Db2ConnectorNewOrders"), 앱 서비스 계획 및 기타 속성을 입력합니다.
6. **패키지 설정**을 선택하고 다음 패키지 설정을 입력합니다.

	이름 | 필수 | 설명
--- | --- | ---
ConnectionString | 예 | DB2 클라이언트 연결 문자열 (e.g., "Network Address=servername;Network Port=50000;User ID=username;Password=password;Initial Catalog=SAMPLE;Package Collection=NWIND;Default Schema=NWIND").
테이블 | 예 | 쉼표로 구분된 OData 작업에 요구되는 테이블, 뷰 및 별칭 이름 목록 및 예제를 사용한 swagger 문서 생성(예: "*NEWORDERS*").
프로시저 | 예 | 쉼표로 구분된 프로시저 목록 및 함수 이름 (예: "SPORDERID").
OnPremise | 아니요 | Azure 서비스 버스 릴레이를 사용하여 온-프레미스 배포.
ServiceBusConnectionString | 아니요 | Azure 서비스 버스 릴레이 연결 문자열.
PollToCheckData | 아니요 | 논리 앱 트리거와 함께 사용하는 SELECT COUNT 문 (예: "SHIPDATE가 NULL인 NEWORDERS에서 개수(*) 선택")
PollToReadData | 아니요 | 논리 앱 트리거와 함께 사용하는 SELECT 문 (예: "SHIPDATE가 업데이트에 대해 NULL인 NEWORDERS에서 * 선택")
PollToAlterData | 아니요 | 논리 앱 트리거와 함께 사용하는 UPDATE or 또는 DELETE 문.(예: “NEWORDERS 설정 SHIPDATE 업데이트 = &lt;CURSOR&gt;인 곳의 현재 날짜”)

7. **확인**을 선택하고 **만들기**를 선택합니다.
8. 완료되면 패키지 설정은 다음과 유사하게 됩니다. ![][1]


## DB2 커넥터 작업을 통해 데이터를 추가하는 논리 앱 ##
API 삽입 또는 OData 항목에 게시 작업을 사용하여 DB2 테이블에 데이터를 추가하기 위해 논리 앱 작업을 정의할 수 있습니다. 예를 들어 ID 열로 정의된 테이블에 대해 SQL INSERT 문을 처리하거나 논리 앱에 영향을 준 ID 값 또는 행을 반환하여 새 고객 주문 레코드를 삽입할 수 있습니다.(최종 테이블에서 ORDID 선택(NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) 값에 삽입(?,?,?,?,?,?)))

> [AZURE.TIP] DB2 연결 "*EntitySet에 게시*"는 ID 열 값을 반환하고 "*API 삽입*"은 영향을 받는 행을 반환합니다.

1. Azure 시작 보드에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. 이름(예: "NewOrdersDb2"), 앱 서비스 계획, 다른 속성을 입력한 다음 **만들기**를 선택합니다.
3. Azure 시작 보드에서 방금 만든 논리 앱, **설정**, **트리거 및 동작**을 차례로 선택합니다.
4. 트리거 및 동작 블레이드의 논리 앱 템플릿 내에서 **처음부터 새로 만들기**를 선택합니다.
5. API 앱 패널에서 **되풀이**를 선택하고 빈도 및 간격, **확인 표시**를 차례로 설정합니다.
6. API 앱 패널에서 **DB2 커넥터**를 선택하고 작업 목록을 확장하여 **NEWORDER에 삽입**을 선택합니다.
7. 매개 변수 목록을 확장하여 다음 값을 입력합니다.

	이름 | 값
--- | --- 
CUSTID | 10042
SHIPNAME | Lazy K Kountry Store 
SHIPADDR | 12 Orchestra Terrace
SHIPCITY | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. 작업 설정을 저장하려면 **확인 표시**, **저장**을 차례로 선택합니다.
9. 설정은 다음과 같이 표시되어야 합니다. ![][3]

10. **작업**의 **모두 실행** 목록에서 첫 번째 나열된 항목(가장 최근 실행)을 선택합니다.
11. **논리 앱 실행** 블레이드에서 **작업** 항목 **db2connectorneworders**을 선택합니다.
12. **논리 앱 작업** 블레이드에서 **입력 링크**를 선택합니다. DB2 커넥터는 입력을 사용하여 매개 변수가 있는 INSERT 문을 처리합니다.
13. **논리 앱 작업** 블레이드에서 **출력 링크**를 선택합니다. 입력은 다음과 같이 표시되어야 합니다. ![][4]

#### 알아야 하는 작업

- 논리 앱 작업 이름을 형성하는 경우 커넥터는 DB2 테이블 이름을 자릅니다. 예를 들어 작업 **NEWORDERS에 삽입**은 **NEWORDER에 삽입**에 대해 잘립니다.
- 논리 앱 **트리거 및 동작**을 저장한 후에 논리 앱은 작업을 처리합니다. 논리 앱이 작업을 처리하기 전까지 몇 초가 지연될 수 있습니다.(예: 3-5 초) 필요에 따라 **지금 실행**을 클릭하여 작업을 처리할 수 있습니다.
- DB2 커넥터는 멤버가 기본값 또는 생성된 열(예: ID)을 통해 DB2 열에 해당하는지 여부를 포함하여 특성을 가진 EntitySet 멤버를 정의합니다. 논리 앱에는 EntitySet 멤버 ID 이름 옆에 빨간색 별표를 표시하여 값을 필요로 하는 DB2 열을 나타냅니다. DB2 ID 열에 해당하는 ORDID 멤버에 대한 값을 입력하지 말아야 합니다. 기본 값으로 DB2 열에 해당하는 다른 선택적 멤버(ITEMS, ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY)에 대한 값을 입력할 수 있습니다.
- DB2 커넥터는 준비된 SQL INSERT 문에서 DRDA SQLDARD(SQL 데이터 영역 응답 데이터)에서 파생되는 ID 열에 대한 값을 포함하는 EntitySet에 게시에 있는 응답을 논리 앱에 반환합니다. DB2 서버는 기본값이 있는 열에 삽입된 값을 반환하지 않습니다.


## DB2 커넥터 작업을 통해 데이터를 대량으로 추가하는 논리 앱 ##
API 대량 삽입 작업을 사용하여 DB2 테이블에 데이터를 추가하기 위해 논리 앱 작업을 정의할 수 있습니다. 예를 들어 ID 열로 정의된 테이블에 대해 행 값의 배열을 사용하는 SQL INSERT 문을 처리하거나 논리 앱에 영향을 준 행을 반환하여 두 개의 새 고객 주문 레코드를 삽입할 수 있습니다.(최종 테이블에서 ORDID 선택(NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) 값에 삽입(?,?,?,?,?,?)))

1. Azure 시작 보드에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. 이름(예: "NewOrdersBulkDb2"), 앱 서비스 계획, 다른 속성을 입력한 다음 **만들기**를 선택합니다.
3. Azure 시작 보드에서 방금 만든 논리 앱, **설정**, **트리거 및 동작**을 차례로 선택합니다.
4. 트리거 및 동작 블레이드의 논리 앱 템플릿 내에서 **처음부터 새로 만들기**를 선택합니다.
5. API 앱 패널에서 **되풀이**를 선택하고 빈도 및 간격, **확인 표시**를 차례로 설정합니다.
6. API 앱 패널에서 **DB2 커넥터**를 선택하고 작업 목록을 확장하여 **NEW에 대량 삽입**을 선택합니다.
7. **행** 값을 배열로 입력합니다. 예를 들어 다음을 복사하여 붙여넣습니다.

	```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
	```

8. 작업 설정을 저장하려면 **확인 표시**, **저장**을 차례로 선택합니다. 설정은 다음과 같이 표시되어야 합니다. ![][6]

9. **작업**의 **모두 실행** 목록에서 첫 번째 나열된 항목(가장 최근 실행)을 클릭합니다.
10. **논리 앱 실행** 블레이드에서 **작업** 항목을 클릭합니다.
11. **논리 앱 작업** 블레이드에서 **입력 링크**를 클릭합니다. 출력은 다음과 같이 표시되어야 합니다. [][7]
12. **논리 앱 작업** 블레이드에서 **출력 링크**를 클릭합니다. 출력은 다음과 같이 표시되어야 합니다. ![][8]

#### 알아야 하는 작업

- 논리 앱 작업 이름을 형성하는 경우 커넥터는 DB2 테이블 이름을 자릅니다. 예를 들어 작업 **NEWORDERS에 대량 삽입**은 **NEW에 대량 삽입**에 대해 잘립니다.
- ID 열(예: ORDID), nullable 열(예: SHIPDATE) 및 기본 값이 있는 열(예: ORDDATE, REQDATE, SHIPID, FREIGHT, SHIPCTRY)을 생략하여 DB2 데이터베이스가 값을 생성합니다.
- "현재" 및 "내일"을 지정하여 DB2 커넥터는 "현재 날짜" 및 "현재 날짜 + 1일"(예: REQDATE)을 생성합니다.


## DB2 커넥터 트리거를 통해 데이터를 읽기, 변경 또는 삭제하려는 논리 앱 ##
API 데이터 폴링 복합 작업을 사용하여 DB2 테이블에서 데이터를 폴링하고 읽도록 논리 앱 트리거를 정의할 수 있습니다. 예를 들어 논리 앱에 레코드를 반환하여 한 개 이상의 새 고객 주문 레코드를 읽을 수 있습니다. DB2 연결 패키지/앱 설정을 다음과 같이 표시되어야 합니다.

	App Setting | Value
--- | --- | ---
PollToCheckData | SHIPDATE가 NULL인 NEWORDERS에서 개수(*) 선택
PollToReadData | "SHIPDATE가 업데이트에 대해 NULL인 NEWORDERS에서 * 선택"
PollToAlterData | <지정된 값 없음>


또한 API 데이터 폴링 복합 작업을 사용하여 DB2 테이블에서 데이터를 폴링하고 읽으며 변경하도록 논리 앱 트리거를 정의할 수 있습니다. 예를 들어 논리 앱에 (업데이트 전에) 선택한 레코드를 반환하여 한 개 이상의 새 고객 주문 레코드를 읽고 행 값을 업데이트할 수 있습니다. DB2 연결 패키지/앱 설정을 다음과 같이 표시되어야 합니다.

	App Setting | Value
--- | --- | ---
PollToCheckData | SHIPDATE가 NULL인 NEWORDERS에서 개수(*) 선택
PollToReadData | "SHIPDATE가 업데이트에 대해 NULL인 NEWORDERS에서 * 선택"
PollToAlterData | NEWORDERS 설정 SHIPDATE 업데이트 = &lt;CURSOR&gt;인 곳의 현재 날짜


또한 API 데이터 폴링 복합 작업을 사용하여 DB2 테이블에서 데이터를 폴링하고 읽으며 이동하도록 논리 앱 트리거를 정의할 수 있습니다. 예를 들어 논리 앱에 (삭제 전에) 선택한 레코드를 반환하여 한 개 이상의 새 고객 주문 레코드를 읽고 행을 삭제할 수 있습니다. DB2 연결 패키지/앱 설정을 다음과 같이 표시되어야 합니다.

	App Setting | Value
--- | --- | ---
PollToCheckData | SHIPDATE가 NULL인 NEWORDERS에서 개수(*) 선택
PollToReadData | "SHIPDATE가 업데이트에 대해 NULL인 NEWORDERS에서 * 선택"
PollToAlterData | &lt;CURSOR&gt;인 곳의 NEWORDERS 삭제

이 예제에서 논리 앱은 DB2 테이블의 데이터를 폴링하고 읽으며 업데이트한 다음 다시 읽습니다.

1. Azure 시작 보드에서 **+**(더하기 기호), **웹 + 모바일**, **논리 앱**을 차례로 선택합니다.
2. 이름(예: "ShipOrdersDb2"), 앱 서비스 계획, 다른 속성을 입력한 다음 **만들기**를 선택합니다.
3. Azure 시작 보드에서 방금 만든 논리 앱, **설정**, **트리거 및 동작**을 차례로 선택합니다.
4. 트리거 및 동작 블레이드의 논리 앱 템플릿 내에서 **처음부터 새로 만들기**를 선택합니다.
5. API 앱 패널에서 **DB2 커넥터**를 선택하고 빈도 및 간격, **확인 표시**를 차례로 설정합니다.
6. API 앱 패널에서 **DB2 커넥터**를 선택하고 작업 목록을 확장하여 **NEWORDERS에서 선택**을 선택합니다.
7. 작업 설정을 저장하려면 **확인 표시**, **저장**을 차례로 선택합니다. 설정은 다음과 같이 표시되어야 합니다. ![][10]
8. **트리거 및 동작** 블레이드를 닫으려면 클릭한 다음 **설정** 블레이드를 클릭합니다.
9. **작업**의 **모두 실행** 목록에서 첫 번째 나열된 항목(가장 최근 실행)을 클릭합니다.
10. **논리 앱 실행** 블레이드에서 **작업** 항목을 클릭합니다.
11. **논리 앱 작업** 블레이드에서 **출력 링크**를 클릭합니다. 출력은 다음과 같이 표시되어야 합니다. ![][11]


## DB2 커넥터 작업을 통해 데이터를 삭제하는 논리 앱 ##
API 삭제 또는 OData 항목에 게시 작업을 사용하여 DB2 테이블에서 데이터를 제거하도록 논리 앱 작업을 정의할 수 있습니다. 예를 들어 ID 열로 정의된 테이블에 대해 SQL INSERT 문을 처리하거나 논리 앱에 영향을 준 ID 값 또는 행을 반환하여 새 고객 주문 레코드를 삽입할 수 있습니다.(최종 테이블에서 ORDID 선택(NWIND.NEWORDERS (CUSTID,SHIPNAME,SHIPADDR,SHIPCITY,SHIPREG,SHIPZIP) 값에 삽입(?,?,?,?,?,?)))

## DB2 커넥터를 사용하여 데이터를 제거하는 논리 앱 만들기 ##
Azure 마켓플레이스 내에서 새 논리 앱을 만들고 DB2 커넥터를 작업으로 사용하여 고객 주문을 제거할 수 있습니다. 예를 들어 SQL DELETE 문을 처리하는 DB2 커넥터 조건부 삭제 작업을 사용할 수 있습니다. (DELETE FROM NEWORDERS WHERE ORDID >= 10000)

1. Azure **시작** 보드의 허브 메뉴에서 **+**(더하기 기호)를 클릭하고 **웹 + 모바일**을 클릭한 다음 **논리 앱**을 클릭합니다.
2. **논리 앱 만들기** 블레이드에서 **이름**을 입력합니다.(예: **RemoveOrdersDb2**)
3. 다른 설정(예: 서비스 계획, 리소스 그룹)에 대한 값을 선택하거나 정의합니다.
4. 설정은 다음과 같이 표시되어야 합니다. **만들기**를 클릭합니다. ![][12]
5. **설정** 블레이드에서 **트리거 및 동작**을 클릭합니다.
6. **트리거 및 동작** 블레이드의 **논리 앱 템플릿** 목록에서 **처음부터 새로 만들기**를 클릭합니다.
7. **트리거 및 동작** 블레이드의 리소스 그룹 내 **API 앱** 패널에서 **되풀이**를 클릭합니다.
8. 논리 앱 설계 화면에서 **되풀이** 항목을 클릭하고 **주파수** 및 **간격**을 설정한 다음(예: **일** 및 **1**) **확인 표시**를 클릭하여 되풀이 항목 설정을 저장합니다.
9. **트리거 및 동작** 블레이드의 리소스 그룹 내 **API 앱** 패널에서 **DB2 커넥터**를 클릭합니다.
10. 논리 앱 설계 화면에서 **DB2 커넥터** 작업 항목을 클릭하고 줄임표(**...**)를 클릭하여 작업 목록을 확장한 다음 **N에서 조건부 삭제**를 클릭합니다.
11. DB2 커넥터 작업 항목에서 **항목 하위 집합을 식별하는 식**에 **ORDID ge 10000**을 입력합니다.
12. 작업 설정을 저장하려면 **확인 표시**, **저장**을 차례로 클릭합니다. 설정은 다음과 같이 표시되어야 합니다. ![][13]
13. **트리거 및 동작** 블레이드를 닫으려면 클릭한 다음 **설정** 블레이드를 클릭합니다.
14. **작업**의 **모두 실행** 목록에서 첫 번째 나열된 항목(가장 최근 실행)을 클릭합니다.
15. **논리 앱 실행** 블레이드에서 **작업** 항목을 클릭합니다.
16. **논리 앱 작업** 블레이드에서 **출력 링크**를 클릭합니다. 출력은 다음과 같이 표시되어야 합니다. ![][14]

**참고:** 논리 앱 설계자는 테이블 이름을 자릅니다. 예를 들어 작업 **NEWORDERS에서 조건부 삭제**는 **N에서 조건부 삭제**에 잘립니다.


> [AZURE.TIP] 다음 SQL 문을 사용하여 샘플 테이블 및 저장 프로시저를 만듭니다.

다음 DB2 SQL DDL 문을 사용하여 샘플 NEWORDERS 테이블을 만들 수 있습니다.
 
 	CREATE TABLE ORDERS (  
 		ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
 		CUSTID INT NOT NULL ,  
 		EMPID INT NOT NULL DEFAULT 10000 ,  
 		ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
 		REQDATE DATE DEFAULT CURRENT DATE ,  
 		SHIPDATE DATE ,  
 		SHIPID INT NOT NULL DEFAULT 10000,  
 		FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
 		SHIPNAME CHAR (40) NOT NULL ,  
 		SHIPADDR CHAR (60) NOT NULL ,  
 		SHIPCITY CHAR (20) NOT NULL ,  
 		SHIPREG CHAR (15) NOT NULL ,  
 		SHIPZIP CHAR (10) NOT NULL ,  
 		SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
 		PRIMARY KEY(ORDID)  
 		)  
 
 	CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



다음 DB2 DDL 문을 사용하여 샘플 SPOERID 저장 프로시저를 만들 수 있습니다.
 
 	CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
 		DYNAMIC RESULT SETS 1  
 	P1: BEGIN  
 		DECLARE CURSOR1 CURSOR WITH RETURN FOR  
 			SELECT * FROM NWIND.NEWORDERS  
 				WHERE ORDID = ORDERID;  
 		OPEN CURSOR1;  
 	END P1  
 	') 


## 하이브리드 구성(선택 사항)

> [AZURE.NOTE] 이 단계는 방화벽 뒤의 DB2 커넥터 온-프레미스를 사용하는 경우에만 필요합니다.

앱 서비스는 하이브리드 구성 관리자를 사용하여 온-프레미스 시스템에 안전하게 연결합니다. 커넥터가 Windows용 온-프레미스 IBM DB2 Server를 사용하는 경우 하이브리드 연결 관리자가 필요합니다.

[하이브리드 연결 관리자 사용](app-service-logic-hybrid-connection-manager.md)을 참조하세요.


## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

REST API를 사용하여 API 앱을 만듭니다. [커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)를 참조하세요.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

<!---HONumber=AcomDC_0803_2016-->