---
title: Azure Database for PostgreSQL 서버에서 대량 삽입 최적화
description: 이 문서에서는 Azure Database for PostgreSQL 서버에서 대량 삽입 작업을 최적화할 수 있는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: 9d2bfcddc649e4fff68bdba49df0945e88067036
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545239"
---
# <a name="optimizing-bulk-inserts-and-use-of-transient-data-on-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버에서 임시 데이터의 사용 및 대량 삽입 최적화 
이 문서에서는 Azure Database for PostgreSQL 서버에서 임시 데이터의 사용 및 대량 삽입 작업을 최적화할 수 있는 방법을 설명합니다.

## <a name="using-unlogged-tables"></a>기록되지 않은 테이블 사용
임시 데이터를 포함하거나 큰 데이터 세트를 대량으로 삽입하는 워크로드 작업이 있는 고객의 경우 기록되지 않은 테이블을 사용하는 것이 좋습니다.

기록되지 않은 테이블은 대량 삽입을 최적화하는 데 효과적으로 사용할 수 있는 PostgreSQL 기능입니다. PostgreSQL은 기본적으로 ACID 속성 중 두 속성인 원자성 및 내구성을 제공하는 WAL(미리 쓰기 로깅)를 사용합니다. 기록되지 않은 테이블에 삽입한다는 것은 PostgreSQL이 그 자체로 I/O 작업인 트랜잭션 로그에 기록하지 않고 삽입을 수행하면서 이러한 테이블을 일반 테이블보다 훨씬 빠르게 작동시킨다는 것을 의미합니다.

기록되지 않은 테이블을 만들기 위한 옵션은 다음과 같습니다.
- `CREATE UNLOGGED TABLE <tableName>` 구문을 사용하여 기록되지 않은 새 테이블을 만듭니다.
- `ALTER <tableName> SET UNLOGGED` 구문을 사용하여 기록된 기존 테이블을 기록되지 않은 테이블로 변환합니다.  이 작업은 `ALTER <tableName> SET LOGGED` 구문을 사용하여 되돌릴 수 있습니다.

## <a name="unlogged-table-tradeoff"></a>기록되지 않은 테이블 단점
기록되지 않은 테이블은 충돌에서 안전하지 않습니다. 기록되지 않은 테이블은 충돌 후 자동으로 잘리거나 불완전하게 종료될 수 있습니다. 또한 기록되지 않은 테이블의 내용은 대기 서버로 복제되지 않습니다. 기록되지 않은 테이블에서 생성된 모든 인덱스는 자동으로 기록되지도 않습니다.  삽입 작업이 완료되면 삽입이 지속될 수 있도록 테이블을 기록되지 않은 테이블로 변환할 수 있습니다.

그러나 기록되지 않은 테이블을 사용할 경우 일부 고객 워크로드에서 성능이 약 15-20퍼센트 향상됐습니다.

## <a name="next-steps"></a>다음 단계
임시 데이터 및 대량 삽입을 사용하기 위해 워크로드를 검토합니다.  

다음 PostgreSQL 설명서 [테이블 SQL 명령 만들기](https://www.postgresql.org/docs/current/static/sql-createtable.html) 검토