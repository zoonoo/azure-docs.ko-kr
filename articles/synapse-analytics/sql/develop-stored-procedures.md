---
title: 저장 프로시저 사용
description: 솔루션 개발을 위해 Synapse SQL에서 저장 프로시저를 구현 하기 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 607060851a8afa48b9570dfcb17732279a3629ee
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286665"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Synapse SQL의 저장 프로시저 사용

Synapse SQL 프로 비전 및 서버 리스 풀을 사용 하면 복잡 한 데이터 처리 논리를 SQL 저장 프로시저에 넣을 수 있습니다. 저장 프로시저는 SQL 코드를 캡슐화 하 여 데이터 웨어하우스의 데이터에 가깝게 저장할 수 있는 좋은 방법입니다. 저장 프로시저를 통해 개발자는 코드를 관리 가능한 단위로 캡슐화 하 고 코드의 재사용 가능성을 높일 수 있으므로 개발자가 솔루션을 모듈화 수 있습니다. 각 저장된 프로시저로 매개 변수를 더 유연하게 할 수도 있습니다.
이 문서에서는 솔루션 개발을 위해 Synapse SQL 풀에서 저장 프로시저를 구현 하는 몇 가지 팁을 찾을 수 있습니다.

## <a name="what-to-expect"></a>필요한 항목

Synapse SQL은 SQL Server에서 사용 되는 다양 한 T-sql 기능을 지원 합니다. 무엇보다도 솔루션의 성능을 최대화하기 위해 사용할 수 있는 특정 기능 확장 사항이 있습니다. 이 문서에서는 저장 프로시저에 추가할 수 있는 기능에 대해 알아봅니다.

> [!NOTE]
> 프로시저 본문에서 Synapse SQL 노출 영역에서 지원 되는 기능만 사용할 수 있습니다. 저장 프로시저에서 사용할 수 있는 개체를 식별 하려면 [이 문서](overview-features.md) 를 검토 하세요. 이 문서의 예제에서는 서버 리스 및 프로 비전 된 노출 영역 둘 다에서 사용할 수 있는 일반 기능을 사용 했습니다. 이 문서의 끝부분에서 [프로 비전 및 서버를 사용 하지 않는 SYNAPSE SQL 풀의 추가 제한 사항을](#limitations) 참조 하세요.

SQL 풀의 규모와 성능을 유지 하기 위해 동작 차이가 있는 일부 기능과 기능이 지원 되지 않습니다.

## <a name="stored-procedures-in-synapse-sql"></a>Synapse SQL의 저장 프로시저

다음 예에서는 외부 개체가 데이터베이스에 있는 경우이를 삭제 하는 프로시저를 볼 수 있습니다.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

`EXEC`프로시저 이름 및 매개 변수를 지정할 수 있는 문을 사용 하 여 이러한 절차를 실행할 수 있습니다.

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL은 간소화 되 고 간소화 된 저장 프로시저 구현을 제공 합니다. SQL Server와 비교하여 가장 큰 차이점은 저장된 프로시저가 미리 컴파일된 코드가 아닙니다. 데이터 웨어하우스에서 컴파일 시간은 대용량 데이터에 대해 쿼리를 실행하는 데 걸리는 시간에 비해 작습니다. 저장 프로시저 코드가 대형 쿼리에 적절하게 최적화되었는지 확인하는 것은 더욱 중요합니다. 시간, 분 및 초(밀리초가 아닌)를 저장하는 것이 목표입니다. 그러므로 저장된 프로시저를 SQL 논리에 대한 컨테이너로 생각하는 것이 더욱 도움이 됩니다.

Synapse SQL에서 저장 프로시저를 실행 하는 경우 SQL 문은 런타임에 구문 분석, 변환 및 최적화 됩니다. 이 프로세스 중 각 명령문이 분산 쿼리로 변환됩니다. 데이터에 대해 실행되는 SQL 코드는 전송된 쿼리와 다릅니다.

## <a name="encapsulate-validation-rules"></a>유효성 검사 규칙 캡슐화

저장 프로시저를 사용 하면 SQL database에 저장 된 단일 모듈에서 유효성 검사 논리를 찾을 수 있습니다. 다음 예제에서는 매개 변수 값의 유효성을 검사 하 고 기본값을 변경 하는 방법을 볼 수 있습니다.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Sql 프로시저의 논리는 프로시저가 호출 될 때 입력 매개 변수의 유효성을 검사 합니다.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>중첩 저장 프로시저

저장 프로시저가 다른 저장 프로시저를 호출하거나 동적 SQL을 실행하면 내부 저장 프로시저 또는 코드 호출을 중첩된 것이라고 합니다.
다음 코드에서는 중첩 프로시저의 예를 보여 줍니다.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

이 프로시저는 일부 이름을 나타내는 매개 변수를 받아들인 다음이 이름을 사용 하 여 개체를 삭제 하는 다른 프로시저를 호출 합니다.
Synapse SQL 풀은 최대 8 개의 중첩 수준을 지원 합니다. 이 기능은 SQL Server와 약간 다릅니다. SQL Server의 중첩 수준은 32입니다.

상위 수준 저장 프로시저 호출은 중첩 수준 1과 같습니다.

```sql
EXEC clean_up 'mytest'
```

또한 저장 프로시저가 다른 EXEC 호출을 만드는 경우 중첩 수준이 2로 증가합니다.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

그 다음 두 번째 프로시저가 일부 동적 SQL을 실행하면 중첩 수준이 3으로 증가합니다.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL은 현재 [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)을 지원 하지 않습니다. 중첩 수준을 추적해야 합니다. 8개의 중첩 수준을 초과할 가능성은 없지만 초과하는 경우 이 한도 내에서 중첩 수준에 맞도록 코드를 다시 작업해야 합니다.

## <a name="insertexecute"></a>INSERT..EXECUTE

프로 비전 된 Synapse SQL 풀에서는 INSERT 문을 사용 하 여 저장 프로시저의 결과 집합을 사용할 수 없습니다. 사용할 수 있는 다른 방법이 있습니다. 예를 들어 프로 비전 된 Synapse SQL 풀의 [임시 테이블](develop-tables-temporary.md) 에 대 한 문서를 참조 하세요.

## <a name="limitations"></a>제한 사항

Synapse SQL에서 구현 되지 않는 Transact-sql 저장 프로시저의 몇 가지 측면은 다음과 같습니다.

| 기능/옵션 | 프로비전됨 | 서버를 사용하지 않음 |
| --- | --- |
| 임시 저장 프로시저 | 아니요 | 예 |
| 번호가 매겨진 저장 프로시저 | 아니요 | 아니요 |
| 확장된 저장 프로시저 | 아니요 | 아니요 |
| CLR 저장 프로시저 | 아니요 | 아니요 |
| 암호화 옵션 | 아니요 | 예 |
| 복제 옵션 | 아니요 | 아니요 |
| 테이블 반환 매개 변수 | 아니요 | 아니요 |
| 읽기 전용 매개 변수 | 아니요 | 아니요 |
| 기본 매개 변수 | 아니요 | 예 |
| 실행 컨텍스트 | 아니요 | 아니요 |
| Return 문 | 아니요 | 예 |
| INSERT INTO. EXEC | 아니요 | 예 |

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](develop-overview.md)를 참조하세요.
