---
title: 열 형식 테이블 스토리지 미리 보기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: 열 형식 스토리지(미리 보기)를 사용하여 데이터 압축
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023856"
---
# <a name="columnar-table-storage-preview"></a>열 형식 테이블 스토리지(미리 보기)

> [!IMPORTANT]
> 하이퍼스케일(Citus)의 열 형식 테이블 스토리지는 현재 미리 보기로 제공됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> [하이퍼스케일(Citus)의 미리 보기 기능](hyperscale-preview-features.md)에서 다른 새 기능의 전체 목록을 볼 수 있습니다.

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 분석 및 데이터 웨어하우징 워크로드에 대해 추가 전용 열 형식 테이블 스토리지를 지원합니다. 행이 아닌 열이 디스크에 연속적으로 저장되면 데이터를 더 압축할 수 있고 쿼리는 열의 하위 집합을 더 빨리 요청할 수 있습니다.

열 형식 스토리지를 사용하려면 테이블을 만들 때 `USING columnar`를 지정합니다.

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

하이퍼스케일(Citus)은 삽입하는 동안 행을 "스프라이프"의 열 형식 스토리지로 변환합니다. 각 스트라이프는 트랜잭션 하나에 해당하는 데이터 또는 행 150000개 중에서 더 작은 값을 보관합니다.  (열 형식 테이블의 스트라이프 크기 및 기타 매개 변수는 [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) 함수로 변경할 수 있습니다.)

예를 들어 다음 문은 5개의 행을 모두 동일한 스트라이프에 넣고 있는데 모든 값이 단일 트랜잭션에 삽입되기 때문입니다.

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

하이퍼스케일(Citus)은 스트라이프별로 열 형식의 데이터를 별도로 압축하기 때문에 가능하면 큰 스트라이프를 만드는 것이 좋습니다. `VACUUM VERBOSE`를 사용하여 압축률, 스트라이프 수, 스트라이프당 행 평균과 같은 열 형식 테이블에 대한 팩트를 볼 수 있습니다.

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

출력은 하이퍼스케일(Citus)에서 zstd 압축 알고리즘을 사용하여 1.31x 데이터 압축을 얻은 것을 보여 줍니다. 압축률은 a) 메모리에 스테이징된 삽입된 데이터의 크기와 b) 해당 데이터가 최종 스트라이프에서 압축된 크기를 비교합니다.

측정 방법으로 인해 한 테이블에 대한 행과 열 형식 스토리지 간의 크기 차이와 압축률이 일치할 수도 있고 일치하지 않을 수도 있습니다. 이러한 차이를 제대로 찾는 유일한 방법은 동일한 데이터를 포함하는 한 행으로 된 열 형식 테이블을 생성하여 비교하는 것입니다.

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

작은 테이블의 경우 열 형식 스토리지가 실제로 더 많은 공간을 사용하지만 데이터가 늘어남에 따라 압축의 이점을 활용할 수 있습니다.

## <a name="example"></a>예제

열 형식 스토리지는 테이블 분할이 잘 작동합니다. 예를 보려면 Citus 엔진 커뮤니티 설명서의 [열 형식 스토리지로 보관](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)을 참조하세요.

## <a name="gotchas"></a>알려진 내용

* 열 형식 스토리지는 스트라이프별로 압축합니다. 스트라이프는 트랜잭션별로 만들어지므로 트랜잭션별로 하나의 행을 삽입하면 단일 행이 자체의 스트라이프에 배치됩니다. 단일 행 스트라이프의 압축 및 성능이 행 테이블보다 더 나빠질 수 있습니다. 항상 열 형식 테이블에 대량으로 삽입합니다.
* 많은 양의 작은 스트라이프를 뒤섞어서 열 형식으로 만드는 경우 중단이 발생합니다.
  유일한 해결 방법은 열 형식 테이블을 새로 만들고 한 트랜잭션으로 원본에서 데이터를 복사하는 것입니다.
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* 기본적으로 압축할 수 없는 데이터는 특정 열을 선택할 때 열 형식 스토리지가 여전히 유용하더라도 문제가 될 수 있습니다. 나머지 열을 메모리에 로드할 필요가 없습니다.
* 행 파티션과 열 파티션이 혼합되어 있는 분할된 테이블에서는 업데이트 대상을 신중하게 지정해야 합니다. 행 파티션만 적중하도록 필터링합니다.
   * 작업이 특정 행 파티션(예: `UPDATE p2 SET i = i + 1`)을 대상으로 하는 경우 성공합니다. 지정된 열 파티션(예: `UPDATE p1 SET i = i + 1`)을 대상으로 하는 경우 실패합니다.
   * 작업이 분할된 테이블을 대상으로 하고 모든 열 형식 파티션을 제외하는 WHERE 절이 있는 경우(예: `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'`) 성공합니다.
   * 작업이 분할된 테이블을 대상으로 하지만 파티션 키 열에 대해 필터링하지 않는 경우 실패합니다. 열 형식만 있는 파티션에서 행과 일치하는 WHERE 절이 있는 경우라도 충분하지 않습니다. 파티션 키도 필터링해야 합니다.

## <a name="limitations"></a>제한 사항

이 기능에는 중요한 제한 사항이 여전히 많이 있습니다. [하이퍼스케일(Citus) 제한 및 제한 사항](concepts-hyperscale-limits.md#columnar-storage)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Citus [시계열 자습서](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage)(외부 링크)의 열 형식 스토리지 예를 참조하세요.
