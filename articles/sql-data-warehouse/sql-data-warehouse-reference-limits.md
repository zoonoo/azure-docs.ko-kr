<properties
   pageTitle="SQL 데이터 웨어하우스에 용량 사양 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에 대한 최소 및 최대 용량 사양입니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/10/2016"
   ms.author="barbkess;jrj;sonyama"/>

# SQL 데이터 웨어하우스에 용량 사양

SQL 데이터 웨어하우스는 SQL Server와 매우 유사한 쿼리 처리 및 시스템 뷰에 대한 제한이 있습니다. 기본이 되고 확장성 있는 병렬적 아키텍처로 인해 일부 제한이 서로 다릅니다. SQL 데이터 웨어하우스에 대한 제한의 요약입니다.

## 연결

| Category | 설명 | 최대 |
| :---------------- | :------------------------------------------- | :----------------- |
| 세션 | 열린 동시 세션 | 1024<br/><br/>데이터 웨어하우스에 동시 요청을 전송할 수 있는 1024 동시 연결을 지원합니다. 동시에 실행할 수 있는 쿼리 수에 제한이 있습니다. 제한을 초과하는 경우 요청이 처리 될 때까지 대기하는 내부 큐로 이동합니다.|
| 세션 | 준비된 문에 대한 최대 메모리 | 20MB |
| 로그인 | 서버 당 로그인입니다. | 500,000 |


## 쿼리 처리

| Category | 설명 | 최대 |
| :---------------- | :------------------------------------------- | :----------------- |
| 쿼리 | 사용자 테이블에서 동시 쿼리입니다. | 32<br/><br/>추가 쿼리는 처리하기 위해 대기하는 내부 큐로 이동합니다. 동시에 실행하는 쿼리 수에 관계 없이 각 쿼리는 방대한 병렬 처리 아키텍처를 모두 사용할 수 있도록 최적화됩니다.|
| 쿼리 | 사용자 테이블에서 쿼리된 쿼리입니다. | 1000 |
| 쿼리 | 시스템 뷰에서 동시 쿼리입니다. | 100 |
| 쿼리 | 시스템 뷰에서 쿼리된 쿼리입니다. | 1000 |
| 쿼리 | 최대 매개 변수 | 2098 |
| 배치 | 최대 크기 | 65,536*4096 |


## 데이터 정의 언어(DDL)

| Category | 설명 | 최대 |
| :---------------- | :------------------------------------------- | :----------------- |
| 테이블 | 데이터베이스 당 테이블 | 20억 |
| 테이블 | 테이블 당 열 | 1024열 |
| 테이블 | 열 당 바이트 | 8000바이트 |
| 테이블 | 행 당 바이트, 정의된 크기 | 8060바이트<br/><br/>행 당 바이트 수는 페이지 압축이 있는 SQL server에서와 동일한 방식으로 계산됩니다. 8060바이트를 초과할 수 없습니다. 행 크기를 예측하려면 MSDN의 [클러스터형 인덱스의 크기 예측](https://msdn.microsoft.com/library/ms178085.aspx)에서 행 크기 계산을 참조하세요.<br/><br/>SQL 데이터 웨어하우스 데이터 형식 크기의 목록은 [테이블 만들기(Azure SQL 데이터 웨어하우스)](https://msdn.microsoft.com/library/mt203953.aspx)를 참조하세요. |
| 테이블 | 행 당 바이트, 이동하는 데이터에 대한 내부 버퍼 크기 | 32,768<br/><br/>SQL 데이터 웨어하우스는 내부 버퍼를 사용하여 분산된 SQL 데이터 웨어하우스 시스템 내에서 행을 이동합니다. 행을 이동하는 서비스는 DMS(데이터 이동 서비스)라고 하고 SQL Server와 다른 형식으로 행을 저장합니다.<br/><br/>행이 내부 버퍼에 맞지 않는 경우 쿼리 컴파일 오류 또는 내부 데이터 이동 오류가 발생합니다. 이 문제를 방지하려면 [DMS 버퍼 크기에 대한 세부 정보](#details-about-the-dms-buffer-size)를 참조하세요.|
| 테이블 | 테이블 당 파티션 | 15,000<br/><br/>고성능을 위해 비즈니스 요구 사항을 지원하면서도 필요한 파티션의 수를 최소화하는 것이 좋습니다. 파티션 수가 늘어날수록 DDL 및 DML 작업에 대한 오버 헤드가 증가하고 성능이 느려집니다.|
| 테이블 | 파티션 경계 값 당 문자.| 4000 |
| 인덱스 | 테이블 당 비클러스터형 인덱스. | 999<br/><br/>rowstore 테이블에만 적용됩니다.|
| 인덱스 | 테이블 당 클러스터형 인덱스. | 1<br><br/>rowstore 및 columnstore 테이블 모두에 적용됩니다.|
| 인덱스 | columnstore 인덱스 rowgroup의 행 | 1,024<br/><br/>각 columnstore 인덱스는 여러 columnstore 인덱스로 구현됩니다. SQL 데이터 웨어하우스 columnstore 인덱스에 1,024 행을 삽입하면 행의 일부는 동일한 행 그룹으로 이동하지 않습니다.|
| 인덱스 | 클러스터형 columnstore 인덱스의 동시 빌드. | 32<br/><br/>클러스터형 columnstore 인덱스는 다른 테이블에 빌드된 경우 적용됩니다. 테이블 당 하나의 클러스터형 columnstore 인덱스 빌드만이 허용됩니다. 추가 요청은 큐에 대기합니다.|
| 인덱스 | 인덱스 키 크기. | 900바이트.<br/><br/>rowstore 인덱스에만 적용됩니다.<br/><br/>인덱스를 만들 때 열에 있는 기존 데이터가 900바이트를 초과하지 않는 경우 최대 크기가 900바이트를 초과하는 varchar 열에 인덱스를 만들 수 있습니다. 그러나 나중에 전체 크기가 900바이트를 초과하는 열에서 삽입 또는 업데이트 동작이 실패합니다.|
| 인덱스 | 인덱스 당 키 열. | 16<br/><br/>rowstore 인덱스에 적용됩니다. 클러스터형 columnstore 인덱스는 모든 열을 포함합니다.|
| 통계 | 결합된 열 값의 크기. | 900바이트. |
| 통계 | 통계 개체 당 열. | 32 |
| 통계 | 테이블 당 열에 만든 통계. | 30,000 |
| 저장 프로시저 | 최대 수준의 중첩. | 8 |
| 보기 | 보기 당 열 | 1,024 |


## 데이터 조작 언어(DML)

| Category | 설명 | 최대 |
| :---------------- | :------------------------------------------- | :----------------- |
| 결과 선택 | 행 당 열 | 4096<br/><br/>결과 선택에서 행 당 열이 4096을 초과할 수 없습니다. 항상 4096이 있다고 보장할 수 없습니다. 쿼리 계획에 임시 테이블이 필요한 경우 테이블 당 최대 1024 열이 적용될 수 있습니다.|
| 결과 선택 | 행 당 바이트 | >8060<br/><br/>결과 선택에서 행 당 바이트 수는 테이블 행에 허용되는 최대치인 8060바이트를 초과할 수 있습니다. 선택 문에 대한 쿼리 계획에 임시 테이블이 필요한 경우 최대 8060바이트 테이블이 적용될 수 있습니다.|
| 결과 선택 | 열 당 바이트 | >8000<br/><br/>결과 선택에서 열 당 바이트 수는 테이블 열에 허용되는 최대치인 8000바이트를 초과할 수 있습니다. 선택 문에 대한 쿼리 계획에 임시 테이블이 필요한 경우 최대 8000바이트 테이블이 적용될 수 있습니다.|
| SELECT | 중첩된 하위 쿼리 | 32<br/><br/>선택 문에서 32보다 많은 중첩된 하위 쿼리를 가질 수 없습니다. 항상 32가 있다고 보장할 수 없습니다. 예를 들어 조인은 쿼리 계획에 하위 쿼리를 제공할 수 있습니다. 또한 사용 가능한 메모리에서 하위 쿼리의 수를 제한할 수 있습니다.|
| SELECT | 조인 당 열 | 1024 열<br/><br/>조인에서 1024보다 많은 열을 가질 수 없습니다. 항상 1024가 있다고 보장할 수 없습니다. 조인 계획에 조인 결과보다 많은 열을 가진 임시 테이블이 필요한 경우 1024 제한은 임시 테이블에 적용됩니다. |
| SELECT | 그룹화 기준 열 당 바이트. | 8060<br/><br/>그룹화 기준 절의 열은 최대 8060바이트를 가질 수 있습니다.|
| SELECT | 정렬 기준 열 당 바이트 | 8060바이트.<br/><br/>정렬 기준 절의 열은 최대 8060바이트를 가질 수 있습니다.|
| INSERT | 열 당 바이트, 고정 및 가변 너비. | 8000바이트. 열에 정의된 것보다 많은 바이트를 삽입하려고 하면 오류가 발생합니다.|
| INSERT | 행 당 바이트, 가변 너비 열. | >8060바이트. 8060바이트를 초과하는 데이터는 오버플로 저장소 영역에 배치됩니다.|
| UPDATE | 열 당 바이트, 고정 및 가변 너비. | 8000바이트. 열에 정의된 것보다 많은 바이트를필요로 하는 값에 열을 업데이트하려고 시도하면 오류가 발생합니다.|
| 식별자 및 문 당 상수 | 참조된 식별자 및 상수의 수. | 65,535<br/><br/>SQL 데이터 웨어하우스는 쿼리의 단일 식에 포함될 수 있는 식별자 및 상수의 수를 제한합니다. 이 제한은 65,535입니다. 이 숫자를 초과하면 SQL Server 오류 8632가 발생합니다. 자세한 내용은 [내부 오류: 식 서비스 제한에 도달했습니다](http://support.microsoft.com/kb/913050/)를 참조하세요.|

## 시스템 뷰

| 시스템 뷰 | 최대 행 |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | 가장 최근 1000 SQL 요청에 대한 DMS 작업자의 수. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10000 |
| sys.dm\_pdw\_errors | 10000 |
| sys.dm\_pdw\_exec\_requests | 10000 |
| sys.dm\_pdw\_exec\_sessions | 10000 |
| sys.dm\_pdw\_request\_steps | sys.dm\_pdw\_exec\_requests에 저장된 가장 최근 1000 SQL 요청에 대한 단계의 총 수입니다. |
| sys.dm\_pdw\_os\_event\_logs | 10000 |
| sys.dm\_pdw\_sql\_requests | sys.dm\_pdw\_exec\_requests에 저장된 가장 최근 1000 SQL 요청입니다. |


## DMS 버퍼 크기에 대한 세부 정보

SQL 데이터 웨어하우스는 내부 버퍼를 사용하여 백 엔드 계산 노드에서 행을 이동합니다. 행을 이동하는 서비스는 DMS(데이터 이동 서비스)라고 하고 SQL Server와 다른 저장소 형식을 사용합니다.

병렬 쿼리 성능을 향상시키려면 DMS는 SQL 데이터베이스 정의된 최대 크기에 모든 가변 길이 데이터를 채웁니다. 예를 들어 `nvarchar(2000) NOT NULL`에 대한 값 'hello'는 DMS 버퍼에 실제로 4002바이트를 사용합니다. 2000자 각각에 대해 2바이트 플러스 플러스 NULL 종결자에 대해 2바이트를 사용합니다.

> [AZURE.NOTE] DMS가 DMS 버퍼 크기인 32,768바이트를 초과하는 행을 이동하려고 할 때 내부 오류가 발생합니다. 행 크기가 DMS 버퍼 크기를 초과하면 DMS 버퍼에 행이 맞도록 테이블 정의를 수정해야 합니다.

### DMS 버퍼의 행 크기를 결정하는 방법
이 예제는 가변 길이 데이터의 DMS 정의된 크기를 설명하고 행에 대한 DMS 버퍼 크기를 계산하는 방법을 보여줍니다.
 
DMS 버퍼에서 가변 길이 데이터 크기는 다음의 합입니다.

- 문자에 대해 정의된 크기.
- NULL 형식은 NULL 표시기에 8바이트를 사용합니다.
- ASCII 형식은 NULL 종결자에 1개의 문자를 사용합니다.
- 유니코드 형식은 NULL 종결자에 2개의 문자를 사용합니다.
             
| 데이터 형식 | DMS 버퍼 크기 |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009바이트 = 1000 +8 + 1 |
| char(1000) NOT NULL | 1001바이트 = 1000 + 1 |
| nchar(1000 NULL | 2010바이트 = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009바이트 = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009바이트 = 1000 + 1 |
| nvarchar(1000) NULL | 2010바이트 = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010바이트 = 2000 + 2 |
                
DMS 버퍼에서 고정된 폭 열은 SQL Server의 기본 크기를 사용합니다. 형식이 null을 허용하는 경우 DMS은 추가로 8바이트가 필요합니다. SQL Server 크기에는 sys.types의 max\_length 필드를 참조하세요.

예:

| 고정 너비 데이터 형식 | DMS 버퍼 크기 |
| :-------------------- | :----------------- |
| int NULL | 12바이트 = 4 + 8 |
| int NOT NULL | 4바이트 = 4 + 0 | 
                
모두 배치하면 다음 행에 대해 DMS 버퍼 정의된 크기가 31,134바이트이며 이는 DMS 버퍼에 맞습니다.

| 열 | 데이터 형식 | 열 크기 |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2(7) NULL | 20바이트 = 8 + 8 |
| col2 | float(4) NULL | 12바이트 = 4 + 8 |
| col3 | nchar(6) NULL | 22바이트 = 12 + 8 + 2 |
| col4 | char(7000) NULL | 7009바이트 = 7000 + 8 + 1 |
| col5 | nvarchar(4000) | 8002바이트 = 8000 + 2. |
| col6 | varchar(8000) NULL | 8009바이트 = 8000 + 8 + 1 |
| col7 | varbinary(8000) | 8000바이트 |
| col8 | 이진(60) | 60바이트 |
                
              
### DMS 버퍼 크기를 초과하는 예

이 예제는 SQL 데이터 웨어하우스로 행을 삽입하는 방법을 성공적으로 보여주지만 DMS가 배포 호환되지 않는 조인에 대해 행을 이동해야 할 때 DMS 오버플로 오류가 발생합니다. 학습할 단원은 DMS 버퍼에 맞을 수 있는 더 작은 행을 만드는 것입니다.

다음 예에서 테이블 T1을 만듭니다. 모든 nvarchar 변수에 4000개의 유니코드 문자가 있는 경우 행의 가능한 최대 크기는 40,000바이트보다 크며 이는 DMS 버퍼의 최대 크기를 초과합니다.

nvarchar의 실제 정의된 크기가 26바이트를 사용하므로 행은 8060바이트보다 작고 SQL Server 페이지에 맞출 수 있습니다. 따라서 DMS가 DMS 버퍼에 이 행을 로드하려는 경우 실패하더라도 테이블 만들기 문은 성공합니다.

````
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
````
이 다음 단계에서는 테이블에 데이터를 삽입하는 데 삽입을 성공적으로 사용할 수 있다는 점을 보여줍니다. 이 문은 DMS를 사용하지 않고 SQL Server에 직접 데이터를 로드하므로 DMS 버퍼 오버플로 오류를 유발하지 않습니다. 또한 통합 서비스는 이 행을 성공적으로 로드합니다.</para>

````
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````

데이터 이동을 설명하도록 준비하려면 이 예에서는 배포 열에 CustomerKey로 두 번째 테이블을 만듭니다.

````
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
````
CustomerKey에서 두 테이블이 모두 배포되지 않으므로 CustomerKey에서 T1 및 T2 간에 조인은 호환되지 않는 배포입니다. DMS는 하나 이상의 행을 로드하고 다른 배포에 복사해야 합니다.

````
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
````

예상 대로 모든 nvarchar 열이 채워질 때 열이 32,768바이트의 DMS 버퍼 크기보다 크기 때문에 DMS는 조인을 수행하는 데 실패합니다. 다음 오류 메시지가 발생합니다.

````
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
````

## 다음 단계
자세한 참조 정보는 [SQL 데이터 웨어하우스 참조 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[SQL 데이터 웨어하우스 참조 개요]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0211_2016-->