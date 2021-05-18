---
title: T-SQL 루프 사용
description: Azure Synapse Analytics에서 Synapse SQL을 통한 T-SQL 루프 사용, 커서 바꾸기, 관련 솔루션 개발에 대한 팁입니다.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0d83e1305a851bf6bafb6c4c79f5caf73f8e44b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120889"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 Synapse SQL과 함께 T-SQL 루프 사용

이 문서에서는 Synapse SQL을 통한 T-SQL 루프 사용, 커서 바꾸기, 관련 솔루션 개발에 대한 필수 팁을 제공합니다.

## <a name="purpose-of-while-loops"></a>WHILE 루프의 목적

Synapse SQL은 명령문 블록을 반복 실행하기 위한 [WHILE](/sql/t-sql/language-elements/while-transact-sql?preserve-view=true&view=sql-server-ver15) 루프를 지원합니다. 이 WHILE 루프는 지정한 조건이 true이거나 코드가 BREAK 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다. 

Synapse SQL의 루프는 SQL 코드에 정의된 커서를 바꾸는 데 유용합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 WHILE 루프는 커서를 바꾸는 좋은 대안입니다.

## <a name="replace-cursors-in-synapse-sql"></a>Synapse SQL에서 커서 바꾸기

시작하기 전에 다음 질문을 고려해야 합니다. "이 커서를 집합 기반 작업을 사용하도록 다시 작성할 수 있나요?" 대부분의 경우 다시 작성할 수 있으며 자주 가장 좋은 방법이기도 합니다. 집합 기반 작업은 반복되는 행 단위 방법보다 더 빠르게 실행되는 경우가 많습니다.

빠른 정방향 읽기 전용 커서는 루핑 구성으로 쉽게 대체됩니다. 다음 코드는 간단한 예입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령이 순차적으로 실행됩니다.

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