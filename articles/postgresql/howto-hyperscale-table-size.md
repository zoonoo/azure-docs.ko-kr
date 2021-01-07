---
title: 테이블 크기를 결정 합니다.-Hyperscale (Citus)-Azure Database for PostgreSQL
description: Citus (Hyperscale) 서버 그룹에서 분산 된 테이블의 실제 크기를 찾는 방법
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937682"
---
# <a name="determine-table-and-relation-size"></a>테이블 및 관계 크기 확인

PostgreSQL에서 테이블 크기를 검색 하는 일반적인 방법은 `pg_total_relation_size` Citus (Hyperscale)에서 분산 된 테이블의 크기를 보고 합니다.
이 모든 함수는 Citus (Hyperscale) 서버 그룹에 대해 코디네이터 노드의 테이블 크기를 표시 합니다.  실제로 분산 테이블의 데이터는 코디네이터가 아니라 작업자 노드 (분할)에 상주 합니다. 분산 테이블 크기의 실제 측정값은 분할 크기의 합계로 얻어집니다. Hyperscale (Citus)은이 정보를 쿼리 하는 도우미 함수를 제공 합니다.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>기능</th>
<th>반환</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>테이블의 실제 데이터 크기 ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">main 포크</a>")입니다.</li>
<li>관계는 테이블이 나 인덱스의 이름일 수 있습니다.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size 더하기:</p>
<blockquote>
<ul>
<li><a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">사용 가능한 공간 맵의</a> 크기</li>
<li><a href="https://www.postgresql.org/docs/current/static/storage-vm.html">표시 유형 지도의</a> 크기</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size 더하기:</p>
<blockquote>
<ul>
<li>인덱스 크기</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

이러한 함수는 노드에 연결할 수 없는 경우를 제외 하 고는 세 가지 표준 PostgreSQL [개체 크기 함수와](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE)유사 합니다.

## <a name="example"></a>예제

모든 분산 테이블의 크기를 나열 하는 방법은 다음과 같습니다.

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

출력:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>다음 단계

* 더 많은 데이터를 저장 하도록 [서버 그룹의 크기를 조정 하는](howto-hyperscale-scale-grow.md) 방법을 알아봅니다.
* Citus (Hyperscale) 서버 그룹의 [테이블 형식을](concepts-hyperscale-nodes.md) 구분 합니다.
