---
title: "IDENTITY를 사용하여 서로게이트 키 만들기 | Microsoft Docs"
description: "IDENTITY를 사용하여 테이블에 서로게이트 키를 만드는 방법에 대해 알아봅니다."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 06/13/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3ab5d159e6eaeb830135fe134e108b0e4de4b7d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>IDENTITY를 사용하여 서로게이트 키 만들기
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [데이터 형식][Data Types]
> * [배포][Distribute]
> * [인덱스][Index]
> * [파티션][Partition]
> * [통계][Statistics]
> * [임시][Temporary]
> * [ID][Identity]
> 
> 

데이터 웨어하우스 모델을 설계하는 경우 많은 데이터 모델러는 해당 테이블에 서로게이트 키를 만들려고 합니다. 로드 성능에 영향을 주지 않고 간단하고 효과적으로 이 목표를 달성하기 위해 IDENTITY 속성을 사용할 수 있습니다. 

## <a name="get-started-with-identity"></a>IDENTITY 시작
다음 문과 유사한 구문을 사용하여 테이블을 처음 만드는 경우 테이블이 IDENTITY 속성을 가졌다고 정의할 수 있습니다.

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

테이블을 채우는 데 `INSERT..SELECT`를 사용할 수 있습니다.

## <a name="behavior"></a>동작
IDENTITY 속성은 로드 성능에 영향을 주지 않고 데이터 웨어하우스의 모든 배포에 확장하도록 설계되었습니다. 따라서 IDENTITY를 구현하여 이러한 목표를 달성합니다. 이 섹션에는 보다 완전하게 이해할 수 있도록 구현의 미묘한 차이를 강조 표시합니다.  

### <a name="allocation-of-values"></a>값 할당
IDENTITY 속성은 서로게이트 값을 할당한 순서를 보장하지 않습니다. 해당 순서는 SQL Server 및 Azure SQL Database의 동작을 반영합니다. 그러나 Azure SQL Data Warehouse에서 보장되지 않는다는 점이 더욱 분명합니다. 

다음 예제는 그림입니다.

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

앞의 예제에서 두 개의 행이 배포 1에서 연결되었습니다. 첫 번째 행에는 열 `C1`의 서로게이트 값 1이 있고 두 번째 행에는 서로게이트 값 61이 있습니다. 이 값은 모두 IDENTITY 속성에 의해 생성되었습니다. 그러나 값은 인접하게 할당되지 않습니다. 이 동작은 의도된 것입니다.

### <a name="skewed-data"></a>불균형 데이터 
데이터 형식 값의 범위는 배포에 균등하게 분산되어 있습니다. 배포된 테이블에 불균형 데이터가 발생한 경우 데이터 형식으로 사용할 수 있는 값의 범위는 중간에 소진될 수 있습니다. 예를 들어 모든 데이터가 단일 분포에서 끝난 경우 테이블은 데이터 형식 값의 1/6에만 효율적으로 액세스할 수 있습니다. 이러한 이유로 IDENTITY 속성은 `INT` 및 `BIGINT` 데이터 형식으로 제한됩니다.

### <a name="selectinto"></a>SELECT..INTO
다음 조건 중 하나가 true가 아닌 경우 새 테이블에서 기존 IDENTITY 열을 선택하면 새 열은 IDENTITY 속성을 상속합니다.
- SELECT 문이 조인을 포함합니다.
- UNION을 사용하여 여러 SELECT 문이 조인됩니다.
- IDENTITY 열이 SELECT 목록에 한 번 이상 나열됩니다.
- IDENTITY 열이 식의 일부입니다.
    
이러한 조건 중 하나라도 true인 경우 열은 IDENTITY 속성을 상속하지 않고 NOT NULL로 생성됩니다.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CTAS(CREATE TABLE AS SELECT)의 경우 SELECT..INTO에서 설명한 동일한 SQL Server 동작을 따릅니다. 그러나 문의 `CREATE TABLE` 부분에 있는 열 정의에서 IDENTITY 속성을 지정할 수 없습니다. CTAS의 `SELECT` 부분에서 IDENTITY 함수를 사용할 수 없습니다. 테이블을 채우려면 `CREATE TABLE`를 사용하여 `INSERT..SELECT` 다음의 테이블을 정의해야 합니다.

## <a name="explicitly-insert-values-into-an-identity-column"></a>IDENTITY 열에 값을 명시적으로 삽입 
SQL Data Warehouse는 `SET IDENTITY_INSERT <your table> ON|OFF` 구문을 지원합니다. 이 구문을 사용하여 명시적으로 값을 IDENTITY 열에 삽입할 수 있습니다.

많은 데이터 모델러는 해당 차원에 있는 특정 행에 미리 정의된 음수 값을 사용하려고 합니다. 예를 들어 -1 또는 "알 수 없는 멤버" 행입니다. 

다음 스크립트에서는 SET IDENTITY_INSERT를 사용하여 이 행을 명시적으로 추가하는 방법을 보여줍니다.

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>IDENTITY가 있는 테이블에 데이터 로드

IDENTITY 속성이 존재한다는 것은 데이터 로딩 코드와 몇 가지 관련이 있습니다. 이 섹션에서는 IDENTITY를 사용하여 테이블로 데이터를 로드하는 몇 가지 기본 패턴을 강조 표시합니다. 

### <a name="load-data-with-polybase"></a>PolyBase를 사용하여 데이터 로드
IDENTITY를 사용하여 테이블에 데이터를 로드하고 서로게이트 키를 생성하려면 테이블을 만든 다음 INSERT..SELECT 또는 INSERT..VALUES를 사용하여 로드합니다.

다음 예제에서는 기본 패턴을 강조 표시합니다.
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> 현재 IDENTITY 열이 있는 테이블에 데이터를 로드 하는 경우 `CREATE TABLE AS SELECT`를 사용할 수 없습니다.
> 

BCP(대량 복사 프로그램) 도구를 사용하여 데이터를 로드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [PolyBase로 로드하기][]
- [PolyBase 모범 사례][]

### <a name="load-data-with-bcp"></a>BCP를 사용하여 데이터 로드
BCP는 SQL Data Warehouse로 데이터를 로드하는 데 사용할 수 있는 명령줄 도구입니다. 해당 매개 변수(-E) 중 하나는 IDENTITY 열이 있는 테이블에 데이터를 로드할 때 BCP의 동작을 제어합니다. 

-E를 지정하는 경우 IDENTITY를 포함한 열의 입력 파일에 보관된 값이 유지됩니다. -E를 지정하지 *않는* 경우 이 열의 값은 무시됩니다. ID 열이 포함되지 않는 경우 데이터가 정상적으로 로드됩니다. 속성의 증분 및 초기값 정책에 따라 값이 생성됩니다.

BCP를 사용하여 데이터를 로드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

- [BCP로 로드하기][]
- [MSDN의 BCP][]

## <a name="catalog-views"></a>카탈로그 뷰
SQL Data Warehouse는 `sys.identity_columns` 카탈로그 뷰를 지원합니다. IDENTITY 속성이 있는 열을 식별하는 데 이 뷰를 사용할 수 있습니다.

이 예제에서는 데이터베이스 스키마를 보다 잘 이해할 수 있도록 다른 시스템 카탈로그 뷰와 `sys.identity_columns`를 통합하는 방법을 보여줍니다.

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>제한 사항
IDENTITY 속성은 다음과 같은 시나리오에서 사용할 수 없습니다.
- 열 데이터 형식이 INT 또는 BIGINT가 아닌 경우
- 열이 배포 키인 경우
- 테이블이 외부 테이블인 경우 

SQL Data Warehouse에서 다음과 같은 관련 함수가 지원되지 않습니다.

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>작업

이 섹션에서는 IDENTITY 열을 사용하여 작업할 때 일반적인 작업을 수행하는 데 사용할 수 있는 몇 가지 샘플 코드를 제공합니다.

> [!NOTE] 
> 열 C1은 다음과 같은 모든 작업의 IDENTITY입니다.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>테이블에 가장 높게 할당된 값을 확인합니다.
`MAX()` 함수를 사용하여 배포된 테이블에 할당된 가장 높은 값을 확인합니다.

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>IDENTITY 속성에 대한 초기값 및 증분을 찾습니다.
다음 쿼리를 사용하여 테이블에 대한 ID 증분 및 초기 구성 값을 검색하기 위해 카탈로그 뷰를 사용할 수 있습니다. 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>다음 단계

* 테이블 개발에 대해 자세히 알아보려면 [테이블 개요][Overview], [테이블 데이터 형식][Data Types], [테이블 배포][Distribute], [테이블 인덱싱][Index], [테이블 분할][Partition] 및 [임시 테이블][Temporary]을 참조하세요. 
* 모범 사례에 대한 자세한 내용은 [SQL Data Warehouse 모범 사례][SQL Data Warehouse Best Practices]를 참조하세요.  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[BCP로 로드하기]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[PolyBase로 로드하기]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[PolyBase 모범 사례]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[MSDN의 BCP]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
