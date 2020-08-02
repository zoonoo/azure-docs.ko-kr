---
title: T-sql 루프 사용
description: Synapse SQL에서 SQL 풀로 T-sql 루프를 사용 하 고 커서를 바꾸고 관련 솔루션을 개발 하기 위한 팁입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 9db7f2016de01edbedfa9e7d7254561fea957d2a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495304"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>Synapse SQL에서 T-sql 루프 사용
이 문서에서는 Synapse SQL에서 SQL 풀을 사용 하 여 T-sql 루프를 사용 하 고, 커서를 바꾸고, 관련 솔루션을 개발 하는 데 필요한 팁을 제공 합니다.

## <a name="purpose-of-while-loops"></a>WHILE 루프의 목적

Synapse SQL은 문 블록을 반복 실행 하기 위한 [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) 루프를 지원 합니다. 이 WHILE 루프는 지정한 조건이 true이거나 코드가 BREAK 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다. 

Sql 풀의 루프는 SQL 코드에 정의 된 커서를 대체 하는 데 유용 합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 [WHILE] 루프는 커서를 대체 하는 좋은 대안입니다.

## <a name="replace-cursors-in-sql-pool"></a>SQL 풀에서 커서 바꾸기

살펴보기 전에 다음 질문을 고려해 야 합니다. "set 기반 작업을 사용 하도록이 커서를 다시 작성할 수 있습니까?" 대부분의 경우 답은 예 이며 가장 좋은 방법입니다. 집합 기반 연산은 자주 반복 되는 행 방법 보다 더 빠르게 실행 됩니다.

빠른 전방 읽기 전용 커서를 루핑 구문으로 쉽게 바꿀 수 있습니다. 다음 코드는 간단한 예제입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령이 순차적으로 실행됩니다.

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

둘째, 루프를 실행 하는 데 필요한 변수를 초기화 합니다.

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
