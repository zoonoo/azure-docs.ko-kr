---
title: 기본 키, 외국 및 고유 키
description: Azure 시냅스 분석의 시냅스 SQL 풀에서 테이블 제약 조건 지원
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 881e4f0110e3c0f35301e2ae6be40f2510f42539
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583488"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Synapse SQL 풀의 기본 키, 외래 키 및 고유 키

시냅스 SQL 풀의 기본 키, 외래 키 및 고유 키를 비롯한 테이블 제약 조건에 대해 알아봅니다.

## <a name="table-constraints"></a>테이블 제약 조건

Synapse SQL 풀은 다음 테이블 제약 조건을 지원합니다. 
- 기본 키는 비CLUSTERED 및 강제 적용되지 않는 둘 다 사용되는 경우에만 지원됩니다.    
- 고유 제약 조건은 NOT ENFORCED가 사용되지 않는 경우에만 지원됩니다.

시냅스 SQL 풀에서는 외래 키 제약 조건이 지원되지 않습니다.  

## <a name="remarks"></a>설명

기본 키 및/또는 고유 키를 사용하면 Synapse SQL 풀 엔진에서 쿼리에 대한 최적의 실행 계획을 생성할 수 있습니다.  기본 키 열 또는 고유한 제약 조건 열의 모든 값은 고유해야 합니다.

Synapse SQL 풀에서 기본 키 또는 고유한 제약 조건이 있는 테이블을 만든 후 사용자는 해당 열의 모든 값이 고유한지 확인해야 합니다.  이를 위반하면 쿼리가 부정확한 결과를 반환할 수 있습니다.  이 예제에서는 기본 키 또는 고유한 제약 조건 열에 중복 값이 포함된 경우 쿼리가 부정확한 결과를 반환하는 방법을 보여 주며 있습니다.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>예

기본 키가 있는 Synapse SQL 풀테이블 만들기: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
고유한 제약 조건을 사용하여 Synapse SQL 풀테이블을 만듭니다.

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>다음 단계

Synapse SQL 풀에 대한 테이블을 만든 후 다음 단계는 테이블에 데이터를 로드하는 것입니다. 로드 자습서의 경우 [시냅스 SQL 풀에 대한 데이터 로드를](load-data-wideworldimportersdw.md)참조하십시오.
