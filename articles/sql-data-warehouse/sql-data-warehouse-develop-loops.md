---
title: "Azure SQL Data Warehouse의 T-SQL 루프 활용 | Microsoft Docs"
description: "솔루션 개발을 위한 Azure SQL 데이터 웨어하우스의 Transact-SQL 루프 및 커서 대체를 위한 팁."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f3384b81-b943-431b-bc73-90e47e4c195f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: d6409f1eb87787e5e023aa53b7b264116c9d8026
ms.lasthandoff: 01/24/2017


---
# <a name="loops-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스의 루프
SQL Data Warehouse는 문 블록을 반복 실행하기 위한 [WHILE][WHILE] 루프를 지원합니다. 이 루프는 지정한 조건이 true이거나 코드가 `BREAK` 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다. 루프는 SQL 코드에 정의된 커서를 대체하는 데 특히 유용합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 [WHILE] 루프는 무언가를 대체해야 하는 경우 좋은 대안입니다.

## <a name="leveraging-loops-and-replacing-cursors-in-sql-data-warehouse"></a>SQL 데이터 웨어하우스에서 루프 활용 및 커서 대체
그러나 계속 진행하기 전에 먼저 스스로 질문해 보아야 합니다. "이 커서를 집합 기반 작업을 사용하도록 다시 작성할 수 있는가?" 대부분의 경우에서 응답은 '그렇다'이며 보통 가장 좋은 방법입니다. 집합 기반 작업은 흔히 행별 반복 접근 방식보다 훨씬 더 빠르게 수행됩니다.

빠른 정방향 읽기 전용 커서는 루핑 구성으로 쉽게 대체할 수 있습니다. 다음은 간단한 예제입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령을 순차적으로 실행할 수 있습니다.

첫째, 개별 문을 식별하는 데 사용되는 고유한 행 번호를 포함하는 임시 테이블을 만듭니다.

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

이제 문을 반복하여 한 번에 하나씩 실행합니다.

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

마지막으로 첫 번째 단계에서 만든 임시 테이블을 삭제합니다.

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="next-steps"></a>다음 단계
더 많은 개발 팁은 [개발 개요][development overview]를 참조하세요.

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

