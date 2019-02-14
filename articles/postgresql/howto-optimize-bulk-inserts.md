---
title: Azure Database for PostgreSQL 서버에서 대량 삽입 최적화
description: 이 문서에서는 Azure Database for PostgreSQL 서버에서 대량 삽입 작업을 최적화하는 방법을 설명합니다.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: fba109e04369c05f98e863b7dd0fa3d51f40d0ad
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810244"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL 서버에서 대량 삽입을 최적화하고 임시 데이터 사용 
이 문서에서는 Azure Database for PostgreSQL 서버에서 대량 삽입 작업을 최적화하고 임시 데이터를 사용하는 방법을 설명합니다.

## <a name="use-unlogged-tables"></a>기록되지 않는 테이블 사용
임시 데이터를 포함하거나 큰 데이터 세트를 대량으로 삽입하는 워크로드 작업이 있는 경우 기록되지 않는 테이블을 사용하는 방안을 고려해 보세요.

기록되지 않은 테이블은 대량 삽입을 최적화하는 데 효과적으로 사용할 수 있는 PostgreSQL 기능입니다. PostgreSQL는 WAL(미리 쓰기 로그)을 사용합니다. 기본적으로 원자성 및 내구성을 제공합니다. 원자성, 일관성, 격리 및 내구성이 모여서 ACID 속성을 구성합니다. 

기록되지 않는 테이블을 삽입한다는 것은 PostgreSQL이 트랜잭션 로그에 쓰지 않고 삽입을 수행한다는 의미이며, 그 자체가 I/O 작업입니다. 따라서 기록되지 않는 테이블은 일반 테이블보다 훨씬 빠릅니다.

기록되지 않는 테이블을 만들려면 다음 옵션을 사용합니다.
- `CREATE UNLOGGED TABLE <tableName>` 구문을 사용하여 기록되지 않는 테이블을 새로 만듭니다.
- `ALTER <tableName> SET UNLOGGED` 구문을 사용하여 기존의 기록된 테이블을 기록되지 않는 테이블로 변환합니다.  

프로세스를 역방향으로 수행하려면 `ALTER <tableName> SET LOGGED` 구문을 사용합니다.

## <a name="unlogged-table-tradeoff"></a>기록되지 않은 테이블 단점
기록되지 않는 테이블은 충돌로부터 안전하지 않습니다. 기록되지 않은 테이블은 충돌 후 자동으로 잘리거나 불완전하게 종료될 수 있습니다. 또한 기록되지 않는 테이블의 콘텐츠는 대기 서버로 복제되지 않습니다. 기록되지 않은 테이블에서 생성된 모든 인덱스는 자동으로 기록되지도 않습니다. 삽입 작업이 완료되면 삽입이 지속될 수 있도록 테이블을 기록되는 테이블로 변환합니다.

기록되지 않는 테이블을 사용할 경우 일부 고객 워크로드의 성능이 약 15-20퍼센트 향상되었습니다.

## <a name="next-steps"></a>다음 단계
임시 데이터 및 대량 삽입을 사용하기 위해 워크로드를 검토합니다. 다음 PostgreSQL 설명서를 살펴봅니다.
 
- [Create Table SQL 명령](https://www.postgresql.org/docs/current/static/sql-createtable.html)