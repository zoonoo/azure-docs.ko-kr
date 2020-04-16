---
title: T-SQL 루프 사용
description: T-SQL 루프 사용, 커서 교체 및 Synapse SQL에서 SQL 풀로 관련 솔루션을 개발하기 위한 팁
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429955"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>시냅스 SQL에서 T-SQL 루프 사용
이 문서에서는 T-SQL 루프사용, 커서 교체 및 Synapse SQL의 SQL 풀로 관련 솔루션을 개발하기 위한 필수 팁을 제공합니다.

## <a name="purpose-of-while-loops"></a>WHILE 루프의 목적

Synapse SQL은 반복실행 문 블록을 위한 [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) 루프를 지원합니다. 이 WHILE 루프는 지정한 조건이 true이거나 코드가 BREAK 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다. 

SQL 풀의 루프는 SQL 코드에 정의된 커서를 대체하는 데 유용합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 [WHILE] 루프는 커서를 대체할 수 있는 훌륭한 대안입니다.

## <a name="replacing-cursors-in-sql-pool"></a>SQL 풀에서 커서 교체

다이빙하기 전에 다음 질문을 고려해야 합니다. 대부분의 경우 대답은 '예'이며 가장 좋은 방법입니다. 집합 기반 작업은 행에 따라 반복적인 행보다 빠르게 실행되는 경우가 많습니다.

빨리 감기 전용 커서는 루핑 구문으로 쉽게 대체됩니다. 다음 코드는 간단한 예입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령이 순차적으로 실행됩니다.

첫째, 개별 문을 식별하는 데 사용되는 고유한 행 번호를 포함하는 임시 테이블을 만듭니다.

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

둘째, 루프를 실행하는 데 필요한 변수를 초기화합니다.

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

이제 문을 반복하여 한 번에 하나씩 실행합니다.

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

마지막으로 첫 번째 단계에서 만든 임시 테이블을 삭제합니다.

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](develop-overview.md)를 참조하세요.
