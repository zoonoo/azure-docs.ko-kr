---
title: T-SQL 루프 사용
description: T-SQL 루프를 사용하고 Synapse SQL 풀에서 커서를 대체하는 솔루션 개발을 위한 팁입니다.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 72a39804931c0834233e91190aacffa8d35912df
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633471"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>시냅스 SQL 풀에서 T-SQL 루프 사용

이 문서에는 T-SQL 루프를 사용하고 커서를 대체하는 SQL 풀 솔루션 개발에 대한 팁이 포함되어 있습니다.

## <a name="purpose-of-while-loops"></a>WHILE 루프의 목적

Synapse SQL 풀은 반복실행 문 블록을 위한 [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 루프를 지원합니다. 이 WHILE 루프는 지정한 조건이 true이거나 코드가 BREAK 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다.

루프는 SQL 코드에 정의된 커서를 대체하는 데 유용합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 WHILE 루프는 커서를 교체하는 훌륭한 대안입니다.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Synapse SQL 풀에서 커서 교체

그러나 먼저 머리에서 다이빙하기 전에 다음과 같은 질문을 스스로에게 물어봐야합니다: "이 커서를 다시 작성하여 집합 기반 작업을 사용할 수 있습니까?"

대부분의 경우 대답은 '예'이며 가장 좋은 방법입니다. 집합 기반 작업은 흔히 행별 반복 접근 방식보다 더 빠르게 수행됩니다.

빠른 정방향 읽기 전용 커서는 루핑 구성으로 쉽게 대체할 수 있습니다. 다음 예제는 간단한 예제입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령이 순차적으로 실행됩니다.

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

둘째, 루프를 수행하는 데 필요한 변수를 초기화합니다.

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

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
