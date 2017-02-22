---
title: "Stretch Database에 대해 마이그레이션할 행 선택 - Azure | Microsoft Docs"
description: "필터 함수를 사용하여 마이그레이션할 행을 선택하는 방법을 알아봅니다."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: f5ef79d9-68ef-4394-a057-d7aac5706b72
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: fc27cd6e25de8e5c3e6b50a0d887755f70d634fd


---
# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>필터 함수를 사용하여 마이그레이션할 행 선택(Stretch Database)
콜드 데이터를 별도 테이블에 저장하는 경우 전체 테이블을 마이그레이션할 Stretch Database를 구성할 수 있습니다. 반면 테이블에 핫 및 콜드 데이터가 모두 포함된 경우 필터 함수를 지정하여 마이그레이션할 행을 선택할 수 있습니다. 필터 조건자는 인라인 테이블\-값 함수입니다. 이 항목은 마이그레이션할 행을 선택하는 인라인 테이블\-값 함수를 작성하는 방법을 설명합니다.

> [!NOTE]
> 제대로 수행되지 않는 필터 함수를 제공하는 경우 데이터 마이그레이션도 제대로 수행되지 않습니다. Stretch Database는 CROSS APPLY 연산자를 사용하여 테이블에 필터 함수를 적용합니다.
>
>

필터 함수를 지정하지 않으면 전체 테이블이 마이그레이션됩니다.

스트레치에 데이터베이스 사용 마법사를 실행할 때, 전체 테이블을 마이그레이션하거나 마법사에서 간단한 필터 함수를 지정할 수 있습니다. 다른 종류의 필터 함수를 사용하여 마이그레이션할 행을 선택하려면 다음 중 하나를 수행합니다.

* 마법사를 종료하고 ALTER TABLE 문을 실행하여 테이블에 대한 스트레치를 사용하도록 설정하고 필터 함수를 지정합니다.
* 마법사를 종료한 후 ALTER TABLE 문을 실행하여 필터 함수를 지정합니다.

함수를 추가하는 ALTER TABLE 구문은 이 항목의 뒷부분에서 설명합니다.

## <a name="basic-requirements-for-the-filter-function"></a>필터 함수에 대한 기본 요구 사항
Stretch Database 필터 조건자에 필요한 인라인 테이블\-값 함수는 다음 예제와 같습니다.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE <predicate>
```
함수의 매개 변수는 테이블의 열에 대한 식별자이어야 합니다.

스키마 바인딩은 필터 함수에 의해 사용되는 열이 삭제되거나 변경되는 것을 방지하기 위해 필요합니다.

### <a name="return-value"></a>반환 값
함수가 비어 있지\-않은 결과를 반환하는 경우 해당 행을 마이그레이션할 수 있습니다. 그렇지 않은 경우 \- 즉 함수가 결과를 반환하지 않는 경우에는 \- 해당 행을 마이그레이션할 수 없습니다.

### <a name="conditions"></a>조건
&lt;*조건자*&gt;는 하나의 조건 또는 AND 논리 연산자로 결합된 여러 조건으로 구성할 수 있습니다.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
그러면 각 조건은 하나의 기본 조건 또는 OR 논리 연산자로 결합된 여러 기본 조건으로 구성할 수 있습니다.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>기본 조건
기본 조건은 다음 비교 중 하나를 수행할 수 있습니다.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

* 함수 매개 변수와 상수 식을 비교합니다. 예: `@column1 < 1000`

  다음은 *날짜* 열의 값이 &lt; 2016/1/1 이전인지 확인하는 예제입니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
  GO

  ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```
* IS NULL 또는 IS NOT NULL 연산자를 함수 매개 변수에 적용합니다.
* IN 연산자를 사용하여 함수 매개 변수를 상수 값의 목록과 비교합니다.

  다음은 *shipment\_status* 열의 값이 `IN (N'Completed', N'Returned', N'Cancelled')`인지 확인하는 예제입니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
  GO

  ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```

### <a name="comparison-operators"></a>비교 연산자
다음 비교 연산자가 지원됩니다.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>상수 식
필터 함수에서 사용하는 상수는 함수를 정의할 때 계산될 수 있는 결정 식이 될 수 있습니다. 상수 식에는 다음을 포함할 수 있습니다.

* 리터럴 예: `N’abc’, 123`
* 대수 식 예: `123 + 456`
* 결정 함수 예: `SQRT(900)`
* CAST 또는 CONVERT를 사용하는 결정 변환 예: `CONVERT(datetime, '1/1/2016', 101)`

### <a name="other-expressions"></a>기타 식
BETWEEN과 NOT BETWEEN을 동등한 AND와 OR 식으로 대체한 후 결과 함수가 여기에 설명된 규칙을 준수하는 경우 BETWEEN 및 NOT BETWEEN 연산자를 사용할 수 있습니다.

하위 쿼리나 RAND() 또는 GETDATE() 등의 비\-결정 함수를 사용할 수 없습니다.

## <a name="add-a-filter-function-to-a-table"></a>테이블에 필터 함수 추가
**ALTER TABLE** 문을 실행하고 기존 인라인 테이블\-값 함수를 **FILTER\_PREDICATE** 매개 변수 값으로 지정하여 필터 함수를 테이블에 추가합니다. 예:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
함수를 조건자로 테이블에 바인딩한 후에는 다음과 같은 것들은 true가 됩니다.

* 다음 번 데이터 마이그레이션 수행 시, 함수가 비어 있지\-않은 값을 반환하는 행만 마이그레이션됩니다.
* 함수에 의해 사용되는 열은 스키마 바운드됩니다. 테이블이 필터 조건자로 함수를 사용하는 한 이들 열을 변경할 수 없습니다.

테이블이 필터 조건자로 함수를 사용하는 한 인라인 테이블\-값 함수를 삭제할 수 없습니다.

> [!NOTE]
> 필터 함수의 성능을 향상시키려면 해당 함수에서 사용되는 열에 대해 인덱스를 만듭니다.
>
>

### <a name="passing-column-names-to-the-filter-function"></a>필터 함수에 열 이름 전달
테이블에 필터 함수를 할당하는 경우 필터 함수에 전달되는 열 이름을 한 부분으로 된 이름으로 지정합니다. 열 이름을 전달할 때 세 부분으로 된 이름을 지정하면 스트레치\-지원 테이블에 대한 후속 쿼리가 실패합니다.

예를 들어, 다음 예제와 같이 세 부분으로 이루어진 열 이름을 지정하면 문이 성공적으로 실행되지만 테이블에 대한 후속 쿼리는 실패합니다.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

대신에 다음 예제와 같이 한 부분으로 이루어진 열 이름을 사용하여 필터 함수를 지정합니다.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="a-nameaddafterwizaadd-a-filter-function-after-running-the-wizard"></a><a name="addafterwiz"></a>마법사를 실행한 후 필터 함수 추가
**스트레치에 데이터베이스 사용** 마법사에서 만들 수 없는 함수를 사용하려는 경우 마법사를 종료한 후에 ALTER TABLE 문을 실행하여 함수를 지정할 수 있습니다. 그렇지만 함수를 적용하려면 먼저 이미 진행 중인 데이터 마이그레이션을 중지하고 마이그레이션된 데이터를 다시 가져와야 합니다. (이 작업이 필요한 이유에 대한 자세한 내용은 [기존 필터 함수 대체](#replacePredicate)를 참조하세요.  

1. 마이그레이션 방향을 반대로 바꾸고 이미 마이그레이션된 데이터를 불러옵니다. 이 작업은 일단 시작되면 취소할 수 없습니다. 또한 Azure에서 아웃바운드 데이터 전송\(송신\)에 따른 비용이 발생합니다. 자세한 내용은 [Azure 가격 책정 방식](https://azure.microsoft.com/pricing/details/data-transfers/)을 참조하세요.  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  
2. 마이그레이션이 완료될 때까지 기다립니다. SQL Server Management Studio에서 **Stretch Database 모니터**에서 상태를 확인하거나 **sys.dm_db_rda_migration_status** 뷰를 쿼리할 수 있습니다. 자세한 내용은 [데이터 마이그레이션 모니터링 및 문제 해결](sql-server-stretch-database-monitor.md) 또는 [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx)를 참조하세요.  
3. 테이블에 적용할 필터 함수를 만듭니다.  
4. 테이블에 함수를 추가하고 Azure에 대한 데이터 마이그레이션을 다시 시작합니다.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>날짜별로 행 필터링
다음 예제에서는 **date** 열에 2016년 1월 1일 이전 값이 포함된 경우 행을 마이그레이션합니다.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>상태 열 값으로 행 필터링
다음 예제에서는 **status** 열에 지정된 값 중 하나가 포함된 경우 행을 마이그레이션합니다.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>슬라이딩 윈도우를 사용하여 행 필터링
슬라이딩 윈도우를 사용하여 행을 필터링하려면 필터 함수에 대한 다음 요구 사항을 염두에 두어야 합니다.

* 함수는 명확해야 합니다. 따라서 시간이 지남에 따라 슬라이딩 윈도우를 자동으로 다시 계산하는 함수를 만들 수 없습니다.
* 함수에서 스키마 바인딩을 사용해야 합니다. 따라서 슬라이딩 윈도우를 이동하기 위해 **ALTER FUNCTION** 을 호출하여 매일 "현재 위치"에서 함수를 업데이트할 수 없습니다.

다음 예제와 같이 **systemEndTime** 열에 2016년 1월 1일 이전 값이 포함된 행을 마이그레이션하는 필터 함수로 시작합니다.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

테이블에 필터 함수를 적용합니다.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

슬라이딩 윈도우를 업데이트하려는 경우에 다음을 수행합니다.

1. 새 슬라이딩 윈도우를 지정하는 새 함수를 만듭니다. 다음 예제에서는 2016년 1월 1일 대신 2016년 1월 2일 이전 날짜를 선택합니다.
2. 다음 예제와 같이 **ALTER TABLE**을 호출하여 이전 필터 함수를 새 필터 함수로 바꿉니다.
3. 필요에 따라 **DROP FUNCTION**을 호출하여 더 이상 사용하지 않는 이전 필터 함수를 삭제합니다. 이 단계는 예제에 표시되어 있지 않습니다.

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>유효한 필터 함수의 추가 예제
* 다음 예제는 AND 논리 연산자를 사용하여 두 개의 기본 조건을 결합한 것입니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
    WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
  GO

  ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```
* 다음 예제는 CONVERT를 통해 몇 가지 조건과 결정 변환을 사용한 것입니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
      WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
  GO
  ```
* 다음 예제는 수치 연산자와 함수를 사용한 것입니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < SQRT(400) + 10
  GO
  ```
* 다음 예제는 BETWEEN 및 NOT BETWEEN 연산자를 사용한 것입니다. BETWEEN과 NOT BETWEEN을 동등한 AND와 OR 식으로 대체한 후 결과 함수가 여기에 설명된 규칙을 준수하기 때문에 이렇게 사용하는 것은 올바릅니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 BETWEEN 0 AND 100
              AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
  GO
  ```
  BETWEEN과 NOT BETWEEN 연산자를 동등한 AND와 OR 식으로 대체하고 나면 선행 함수는 다음 함수와 동등하게 됩니다.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
  GO
  ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>올바르지 않는 필터 함수 예제
* 다음 함수는 비\-결정 변환을 포함하기 때문에 올바르지 않습니다.

  ```tsql
  CREATE FUNCTION dbo.fn_example5(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < CONVERT(datetime, '1/1/2016')
  GO
  ```
* 다음 함수는 비\-결정 함수 호출을 포함하기 때문에 올바르지 않습니다.

  ```tsql
  CREATE FUNCTION dbo.fn_example6(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < DATEADD(day, -60, GETDATE())
  GO
  ```
* 다음 함수는 하위 쿼리를 포함하기 때문에 올바르지 않습니다.

  ```tsql
  CREATE FUNCTION dbo.fn_example7(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
  GO
  ```
* 다음 함수는 함수를 정의할 때 대수 연산자 또는 기본\-제공 함수를 사용하는 식이 상수에 대해 계산해야 하기 때문에 올바르지 않습니다. 대수 식 또는 함수 호출에 열 참조를 포함시킬 수 없습니다.

  ```tsql
  CREATE FUNCTION dbo.fn_example8(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 % 2 =  0
  GO

  CREATE FUNCTION dbo.fn_example9(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE SQRT(@column1) = 30
  GO
  ```
* 다음 함수는 BETWEEN 연산자를 동등한 AND 식으로 대체한 후 여기에 설명된 규칙을 위반하기 때문에 올바르지 않습니다.

  ```tsql
  CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
  GO
  ```
  BETWEEN 연산자를 동등한 AND 식으로 대체하고 나면 선행 함수는 다음 함수와 동등하게 됩니다. 이 함수는 기본 조건이 OR 논리 연산자만 사용할 수 있기 때문에 올바르지 않습니다.

  ```tsql
  CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
  GO
  ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Stretch Database가 필터 함수를 적용하는 방법
Stretch Database는 CROSS APPLY 연산자를 사용하여 테이블에 필터 함수를 적용하고 해당 행을 결정합니다. 예:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
함수가 행에 대해 비어 있지\-않은 결과 반환할 경우 해당 행을 마이그레이션할 수 있습니다.

## <a name="a-namereplacepredicateareplace-an-existing-filter-function"></a><a name="replacePredicate"></a>기존 필터 함수 대체
**ALTER TABLE** 문을 다시 실행하고 **FILTER\_PREDICATE** 매개 변수에 대해 새 값을 지정하여 이전에 지정된 필터 함수를 대체할 수 있습니다. 예:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
새 인라인 테이블\-값 함수에는 다음과 같은 요구 사항이 있습니다.

* 새 함수는 이전 함수보다 덜 제한적이어야 합니다.
* 이전 함수에 존재하는 모든 연산자는 새 함수에 있어야 합니다.
* 새 함수는 이전 함수에 존재하지 않는 연산자를 포함할 수 없습니다.
* 연산자 인수의 순서를 변경할 수 없습니다.
* `<, <=, >, >=` 비교의 일부인 상수 값만이 함수를 덜 제한적으로 만드는 방식으로 변경될 수 있습니다.

### <a name="example-of-a-valid-replacement"></a>올바른 대체의 예제
다음 함수가 현재 필터 조건자임을 가정합니다.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
다음 함수는 새 날짜 상수(나중 구분 날짜를 지정)가 함수를 덜 제한적으로 만들기 때문에 올바른 대체입니다.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>올바르지 않은 대체의 예제
다음 함수는 새 날짜 상수(이전 구분 날짜를 지정)가 함수를 덜 제한적으로 만들지 않기 때문에 올바른 대체가 아닙니다.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
다음 함수는 비교 연산자 중 하나가 제거되었기 때문에 올바른 대체가 아닙니다.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
다음 함수는 AND 논리 연산자를 통해 새 조건이 추가되었기 때문에 올바른 대체가 아닙니다.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>테이블에서 필터 함수 제거
선택한 행이 아니라 테이블 전체를 마이그레이션하려면 **FILTER\_PREDICATE**를 null로 설정하여 기존 함수를 제거합니다. 예:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
필터 함수를 제거한 후 테이블의 모든 행은 마이그레이션할 수 있습니다. 따라서 먼저 Azure에서 테이블에 대한 모든 원격 데이터를 다시 가져오지 않는 한 나중에 같은 테이블에 대해 필터 함수를 지정할 수 없습니다. 이 제한 사항은 새 필터 함수를 제공할 때 마이그레이션할 수 없는 행이 Azure에 이미 마이그레이션되는 상황을 방지하기 위한 것입니다.

## <a name="check-the-filter-function-applied-to-a-table"></a>테이블에 적용된 필터 함수 확인
테이블에 적용된 필터 함수를 확인하려면 카탈로그 뷰 **sys.remote\_data\_archive\_tables**를 열고 **filter\_predicate** 열의 값을 확인합니다. 값이 null이면 전체 테이블을 보관할 수 있습니다. 자세한 내용은 [sys.remote_data_archive_tables(Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx)를 참조하세요.

## <a name="security-notes-for-filter-functions"></a>필터 함수에 대한 보안 정보
Db_owner 권한이 있는 손상된 계정은 다음 작업을 수행할 수 있습니다.  

* 많은 양의 서버 리소스를 소비하거나 장시간 대기하다가 서비스 거부를 초래하는 테이블 반환 함수를 만들고 적용합니다.  
* 사용자의 읽기 액세스가 명시적으로 거부된 테이블의 콘텐츠를 유추할 수 있도록 하는 테이블 반환 함수를 만들고 적용합니다.  

## <a name="see-also"></a>참고 항목
[ALTER TABLE(Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)



<!--HONumber=Jan17_HO4-->


